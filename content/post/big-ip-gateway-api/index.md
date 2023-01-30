---
title: 'Project- BIG-IP K8S Gateway API'
subtitle: 'A project of k8s Gateway API'
summary: F5 BIG-IP kubernetes Gateway is an implementation of Kubernetes Gateway API. It uses F5 BIG-IP as the downstream implementation and integration. 
authors:
- Jing Lin(林静)
tags:
- F5
- k8s
- gateway api
categories: []
date: "2023-01-30T00:00:00Z"
lastmod: "2023-01-30T00:00:00Z"
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

## Name

**Product Name**: BIG-IP Kubernetes Gateway Controller

**Code Name**: bigip-kubernetes-gateway

## Description

[**Gateway API**](https://gateway-api.sigs.k8s.io/) is an open source project managed by the SIG-NETWORK community. It is a collection of resources that model service networking in Kubernetes. These resources - GatewayClass,Gateway, HTTPRoute, TCPRoute, Service, etc - aim to evolve Kubernetes service networking through expressive, extensible, and role-oriented interfaces that are implemented by many vendors and have broad industry support.

**F5 BIG-IP kubernetes Gateway** is an implementation of Kubernetes Gateway API. It uses F5 BIG-IP as the [downstream implementation and integration](https://gateway-api.sigs.k8s.io/implementations/).

See https://gateway-api.f5se.io for more information.

Source code repository: https://github.com/f5devcentral/bigip-kubernetes-gateway.

## Quick view

BIG-IP Kubernetes Gatewway enables the conversion of GatewayAPI resources to BIG-IP ADC capabilities automatically:

[![image](img/bigip-k8s-gateway-resource-mapping.png)]()

## Code

 [Github f5devcentral](https://github.com/f5devcentral/bigip-kubernetes-gateway/tree/master)

## Installation

The installation process is simple, see https://gateway-api.f5se.io/quick-start/ for details.

## Usage

As the quick getstart, see https://gateway-api.f5se.io/Use-Cases/simple-gateway/.

## Support

For support, please open a [GitHub issue](https://github.com/f5devcentral/bigip-kubernetes-gateway/issues). Note, the code in this repository is community supported and is not supported by F5.

Maintenance and F5 Technical Support of this F5 code is provided only if the software (i) is unmodified; and (ii) has been marked as F5 Supported in SOL80012344, (https://support.f5.com/csp/article/K80012344).

Support will only be provided to customers who have an existing BIG-IP support contract associated with a valid BIG-IP serial number. For information about support policies, see http://www.f5.com/about/guidelines-policies/ and http://askf5.com.

## Roadmap

The bigip-kubernetes-gateway versions are released on dockerhub as [Docker images](https://hub.docker.com/r/f5devcentral/bigip-kubernetes-gateway/tags).

For a list of supported Gateway API resources and features, see the [Gateway API Compatibility](https://github.com/f5devcentral/bigip-kubernetes-gateway/blob/master/docs/gateway-api-compatibility.md) doc. Check [here](https://github.com/kubernetes-sigs/gateway-api#status) for Gateway API's latest status.

## Contributing

We are open to contributions, if

- you are interested in participating in our project and
- you have experience in kubernetes and golang development,
- BIG-IP related applications or development experience is preferred.

## Authors and acknowledgment

Contributors for now：

- [zongzw f5zong](https://github.com/zongzw)
- [Niklaus-xie](https://github.com/Niklaus-xie)
- [myf5 Jing Lin](https://github.com/myf5)