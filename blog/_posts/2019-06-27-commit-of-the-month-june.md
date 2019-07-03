---
slug: june-tilt-commit-of-the-month
date: 2019-07-03T15:26:34.743Z
author: dmiller
layout: blog
title: "Tilt Commit of the Month"
subtitle: "June 2019 Edition"
images:
  - featuredImage.jpg
image_type: "contain"
tags:
  - docker
  - kubernetes
  - microservices
  - tilt
  - commit
keywords:
  - docker
  - kubernetes
  - microservices
  - tilt
---

Welcome to a new series of blog posts we're calling Tilt Commit of the Month. Commit of the Month is a lightweight way to highlight work that goes on in the Tilt project that might fly under the radar otherwise.

We’re already cheating: this month we’re going to highlight a bunch of commits related to better detecting and working better with different kinds of Kubernetes clusters.

Commits:
* [k8s: auto-detect microk8s registry](https://github.com/windmilleng/tilt/commit/d293a0ba0216711c855526100490e21733ada194)
* [docker: dynamically switch between local docker daemon and the in-cluster daemon](https://github.com/windmilleng/tilt/commit/af5a0a7e0c32c8b55117c9f22c28008f8af67d2d)
* [engine: fix exec syncing on busybox](https://github.com/windmilleng/tilt/commit/6678de099fd559b8e02240a3a807d3b06e65aff8)


## What they do
Combined these changes make Tilt better support KIND, microk8s and minikube. On microk8s we now detect whether we should use a registry built in microk8s, or an external one. Finally [`live_update`](https://docs.tilt.dev/live_update_reference.html) on KIND clusters no longer results in full rebuilds.

## Why they're important
Aside from making Tilt work better on various Kubernetes clusters for users these change also pave the way for us to run our integration tests across many different types of Kubernetes clusters, rather than just one.