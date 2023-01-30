---
title: 'Why K8s Egress Traffic Policy Control is Critical to Security Architecture'
subtitle: 'An article discuss why and what way to control egress traffic in k8s'
summary: Managing outbound traffic policies in Kubernetes, a seemingly simple requirement, actually is not an easy task to do well.
authors:
- Jing Lin(林静)
tags:
- k8s
- egress
- security
categories: []
date: "2022-08-06T00:00:00Z"
lastmod: "2022-08-06T00:00:00Z"
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal point options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
image:
  caption: 'The picture from internet.'
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []

# Set captions for image gallery, use {{< gallery >}} to refrence in body.
gallery_item:
- album: gallery
  caption: Default
  image: theme-default.png
- album: gallery
  caption: Ocean
  image: theme-ocean.png
- album: gallery
  caption: Forest
  image: theme-forest.png
- album: gallery
  caption: Dark
  image: theme-dark.png
- album: gallery
  caption: Apogee
  image: theme-apogee.png
- album: gallery
  caption: 1950s
  image: theme-1950s.png
- album: gallery
  caption: Coffee theme with Playfair font
  image: theme-coffee-playfair.png
- album: gallery
  caption: Strawberry
  image: theme-strawberry.png
---

## Why Egress Traffic Policy Control

According to the 2021 CNCF survey, the proportion of users who use Kubernetes (K8s) in the production environment has reached 59.77%, and the number of European users has reached 68.98%. More and more users are migrating production services to the Kubernetes environment. Gartner 2021 Hype Cycle for Cloud Security also shows that container and Kubernetes security is already in the "slope of Enlightenment" stage. This shows that protecting application services in Kubernetes is becoming more and more important.

When we look at a large number of microservices running in Kubernetes, we can see that microservice security has the characteristics of typical micro-boundaries and need continuous security engineering. We need consider each microservice as a boundary and protect it, whether runtime, or north-south and east-west traffic. It is necessary for each microservice to start security considerations at the beginning of coding, and to move security to the left. Security methods, and policies should be adapted to developers and Kubernetes platform operators. It also requires the ability to gain insight into all traffic, collect all runtime logs, events and other data, analyze these data through a continuous security engineering system, aggregate rules and feed them back into security policy settings.

![image-20220708090843383.png](https://community.f5.com/t5/image/serverpage/image-id/18348i39592BD75177EE79/image-size/large?v=v2&px=999)

The microservices in Kubernetes will not only run closed inside the cluster, it needs to access applications, databases, third-party API services, Internet services, etc. outside the cluster. The outgoing traffic may include the business traffic, open source component update traffic, or even traffic from the compromised application to the C2. Therefore, it is necessary to actively control the egress traffic of microservices in Kubernetes to ensure its security and compliance. Under the digital transformation driven by cloud-native architecture, enterprises will adopt a large number of open source technologies, which may be the easiest place to introduce security risks. Regardless of whether there is a clear open source whitelist mechanism, enterprises should pay enough attention to these open source technologies. These open source may take the initiative to visit the outside, we need control it well, and ensure safety.

Managing outbound traffic policies in Kubernetes, a seemingly simple requirement, actually is not an easy task to do well. This article will analyze the challenges of Kubernetes outbound policies with you, analyze the advantages and disadvantages of current common solutions, and think about how enterprises should manage and control Kubernetes egress traffic policies.

## Existing challenges

### Dynamic

From a technical point of view, this is the first challenge that exists. In a Kubernetes environment, the pods of the microservice will be highly dynamic and decentralized. IPs, network segments and physical locations will change over time. Therefore, it will be impossible to directly use IP and other characteristics for static policy setting. The strategy must rely on other abstract application labels, service names or namespaces, etc., and can dynamically perceive changes.

### Granularity

In a traditional application environment, the management and control of an application's outbound traffic policy generally only requires policy setting for a small number of deployments involved in the application. However, in the environment of microservices and containerization, an application may be composed of many microservices, and a microservice contains many pods. Different microservice units will require different outbound policy rules. For example, payment needs to connect to a third-party interface, while the comment service does not need to actively access third-party services. This means that the granularity of policy setting needs to be fine-grained to each microservice unit and ensure that all relevant pods are controlled. It can be seen that the policy control granularity is finer, the workload is larger, and the complexity is higher.

### Collaboration

When we want to deploy egress policies for different microservice, who should be responsible for this, the application development department? Application operation and maintenance department? PlatformOps division of Kubernetes? Or the security department? When we look at this with a security-left shift, it is obvious that the application department should consider whether his microservices need to actively access which external services. However, if the application developer is in charge, can the platform or security department just let it go? Obviously not, application developers set up security policies for the applications they are responsible for. Global basic security policies setting, and how to quickly remedy the wrong policies set by application developers, These still need to be taken care of by other teams. In order for the development department to practice the idea of security shift left, Platform ops team or the security department must provide friendly tools and integrate the security policy setting into the DevSecOps pipeline. If the tools or methods lead to a decline in development efficiency, developers will not be happy to use it. Therefore, this is not the independent work of a certain department, it requires the cooperation of multiple teams/departments to ensure safety.

### Data driven

As stated at the beginning of the article, security is a continuous engineering effort, meaning that any outgoing access behavior and events should be logged into the security engineering system for analysis to ensure adequate visibility and insight. Egress security control is not only a simple policy setting, but also requires the ability to output complete logs, behaviors, and events.

## Industry solution analysis

Next, we will analyze the current industry solutions for egress policy control one by one. First, we will divide them into 6 categories of solutions, and then analyze them one by one:

| **Category **      | **Solutions or products **                                   | **Description **                                             |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Platform based     | Kubernetes Network policy Openshift EgressIP Openshift Egress Router pod Openshift Egress Firewall Openshift EgressNetworkPolicy | A specific feature of a platform provider                    |
| CNI based          | Calico Egress pod Calico Enhanced Network policy Cilium Enhanced Network policy Kube-ovn | The capability of CNI                                        |
| Service Mesh       | NGINX Service Mesh Istio                                     | A function of Service Mesh                                   |
| Micro segmentation | PrismaNeu Vector                                             | From ZTA perspective or use enforcer container to control egress |
| Fusion             | F5 CES(Container Egress Service) Fortinet                    | Use k8s native method to integrate exist security assets to k8s |
| Others             | DNS interception Proxy pod                                   | Intercept coredns or use a proxy pod as forward proxy        |

### Platform based

The Network policy that comes with Kubernetes is the easiest way to think about egress security policy control. It is the native ability of K8s, which has natural affinity for developers or PlatformOps personnel, and can well adapt to the idea of security left shift. But Network policy needs CNI support. Some other disadvantages are:

- No cluster global policy capability, independent policies must be maintained under different namespaces
- No selection capability based on k8s svc name (can be changed to use pod label, but inflexible)
- No explicit rejection ability, through the isolation characteristics of the policy, and then impose a specific whitelist
- Rules without priority concept
- No dedicate external access rule type, external target services can only rely on broad ipblock
- pure four-layer, no seven-layer control capability
- No policy execution debugging capability
- No policy execution log
- The "isolation" feature of Networkpolicy makes maintenance very troublesome. For example, it only wants to control its access to the Internet, but because of isolation, additional maintenance is required All outbound (east-west) access of the pod within the cluster
- Does not solve the problem of k8s working with external security devices. Just imagine, after Network policy has made rule control, can an external security device open a default allow rule for the cluster?

Openshift has four features related to Egress, namely standard Network Policy, Egress IP, Egress Router, Egress Firewall and Egress NetworkPolicy.

- Network Policy, which is fully supported when Openshift uses OVN-Kubernetes as the CNI, while the traditional Openshift SDN CNI is only partially supported. It is no different from standard Kubernetes, and its advantages and disadvantages will not be analyzed here.
- EgressIP is a function to use deterministic source IP when Pods traffic leaves the cluster. When using Openshift SDN CNI, this function applies the Egress IP to the specified nodes as secondary IP and is used for SNAT. When using OVN-Kubernetes CNI, the snat rules are executed for specific pods through OVS. Using EgressIP itself is not a direct method for egress security policy control, but by specifying a certain source IP for different namespaces, some policies can be deployed on security devices outside the cluster to perform control. Obviously, this policy control method is relatively extensive, and cannot achieve the fine-grained granularity of different services. If the pods are scattered on different nodes, there will also be the problem that the outgoing cluster traffic of the pods must first traverse between different nodes, adding extra delay. In addition, the EgressIP must belong to the same network segment as the node's main network address, and a node cannot have more than one EgressIP. EgressIP also does not support public cloud and Redhat Openstack Platform.
- Egress Router Pod, which is a special pod with two network interfaces, and uses MacVlan technology to directly connect one of the container network interface to the external network. All pods out-of-cluster traffic will pass through this pod. Different CNIs (SDN or OVN-Kubernetes) have different functions for the pod. Only redirection operations are supported under OVN-Kubernetes CNI. Generally speaking, this is not suitable for large-scale use. From the perspective of Egress security policy setting, it is still impossible to distinguish between different services, and the centralized Egress pod can easily become a performance bottleneck.
- EgressFirewall, which is actually a feature of OVN-Kubernetes. Allows setting outbound access rules for a project or namespace, which can be based on protocols, ports, IP, FQDN and other dimensions. The protocol only supports TCP, UDP, SCTP, and cannot support other protocol control. It only allows setting based on the namespace level. Only one rule file is allowed to be set in a namespace, and cannot set different rules for different services in the cluster. Also it limits each namespace/project to a maximum of 8000 rules. Observables or events are also not supported.
- Egress NetworkPolicy, similar to EgressFirewall, supports this CRD when using Openshift SDN CNI. But the Egress NetworkPolicy is more restrictive, for example, each namespace/project supports a maximum of 1000 rules, and the network policy or multitenant mode must be turned on.

**>> [Click here](https://community.f5.com/t5/technical-articles/what-you-should-know-about-k8s-egress-traffic-policy-control/ta-p/297895) for continue reading(2/2)**