---
title: 'Container Egress Service(CES)'
subtitle: 'What is CES and why CES'
summary: CES is a solution. It is used to help users better manage the outgoing traffic of k8s pod/container. It solves the egress challenge in high dynamic IP scenarios, and provides a wealth of outgoing control capability. 
authors:
- Jing Lin(林静)
tags:
- F5
- k8s
- ces
- egress traffic controlling
- afm
categories: []
date: "2022-02-01T00:00:00Z"
lastmod: "2022-02-01T00:00:00Z"
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal point options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
image:
  caption: 'My tech blog https://myf5.net'
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

## Solution architecture

### Components

The CES solution includes the following components:

- CES controller: a container running in k8s. This component is the control plane, responsible for converting the outbound policies that deployed in k8s into the external data plane component(here is F5 AFM).
- F5 BIG-IP AFM: Data plane components running outside of k8s. Accept the configuration issued by the CES controller and execute specific access control rules, such as access control lists, bandwidth limiting, traffic programming, etc.
- CNI: CNI is a choice of the user environment itself and is not included in the CES plan. However, different CNIs will have different effects on the functions of the CES solution. Use [kube-ovn](https://github.com/kubeovn/kube-ovn/) CNI to get the full functionality of CES.

### Architecture diagram

![high-level-arch](img/high-level-arch.jpg)

### Policy scope and role

CES provides three policy scopes `cluster global` `namespace` and `service`. Its meaning and user role relationship are as follows:

| scope          | meaning                                                      | Adaptation role                                         |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------- |
| Cluster global | It is the global level policy of the cluster, which is used to control the general and overall access control of the cluster. For example, the cluster's access to basic public services such as NTP and DNS of the enterprise. The scope policy is applied to the outbound access control of all services in the cluster. | Cluster administrator, Security team                    |
| namespace      | This policy level is effective for a single namespace or project. It is used to control the access of all services in specific NS or project to access the services out of the cluster. Policies in different namespaces or projects do not affect each other. *This function requires the support of CNI. | Project team, application operation team                |
| service level  | The policy control the k8s servcies to the external services. Only valid for specific services. So if the CNI can not support namespace level policy, set svc level policy is an alternative way. | Project team, application operation, microservice owner |

### Tenant isolation

The CES solution supports strong isolation of `network + namespace`. Supports the administrative isolation of configuration objects on the data plane, and also supports strict traffic isolation at the network level. Support different namespaces to use overlapping CIDR.

```
Need CNI support network isolation. For example kube-ovn's per ns subnet
```

## Solution value

### Challenges solved

- High-frequency changes in outbound traffic caused by container IP dynamics
- Different role groups have different requirements for the scope setting of the policy, and the policy needs to match the role in multiple dimensions
- Dynamic bandwidth limit requirements for outbound traffic
- Protocol in-depth security inspection requirements
- Advanced requirements for flow programmable based on access control events
- Visualization requirements for outbound traffic

### Provided capabilities

- Dynamic IP ACL control with Cluster/Pod/NS granularity
- Cluster/Pod/NS granular FQDN ACL control
- Time-based access control
- Matched flow event trigger and programmable
- Matched traffic redirection
- Protocol security and compliance testing
- IP intelligence
- Traffic matching log
- Traffic matching visualization report
- Protocol detection visual report
- TCP/IP Errors report
- NAT control and logging
- Data flow visualization tracking
- Visual simulation of access rules
- Transparent detection mode
- High-speed log outgoing

```
Partial functions will evolve along with version iterations
```

**Next step:**

[Understanding CES installation](https://github.com/f5devcentral/container-egress-service/wiki/EN_2_CES_Installation)