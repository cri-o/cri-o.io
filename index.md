---
layout: default
---

## What is cri-o?

CRI-O is an implementation of the kubernetes CRI (Container Runtime Interface) to enable using OCI (Open Container Initiative) compatible runtimes. It allows kubernetes to use any OCI compliant runtime as the container runtime for running pods. Today it supports runc and Clear Containers as the container runtimes but any OCI conformant runtime can be plugged in principle. CRI-O uses simple libraries as building blocks to implement the CRI.

CRI-O supports OCI container images and can pull from any container registry. It is a lightweight alternative to using docker or rkt as the runtime for kubernetes.

It is developed by maintainers and contributors from Red Hat, Intel, SUSE, Hyper, IBM and others.  We are a fully open project and contributors are always welcome:
https://github.com/kubernetes-incubator/cri-o

<script type="text/javascript" src="https://asciinema.org/a/124131.js" id="asciicast-124131" async></script>

## Features and Benefits

* Optimized architecture for Kubernetes through less daemon overhead(?)
* Composable library
* Stable release roadmap

## Architecture

TBA diagram

## FAQ

TBA

**Feature roadmap**

1. Basic pod/container lifecycle, basic image pull (already works)
1. Support for tty handling and state management
1. Basic integration with kubelet once client side changes are ready
1. Support for log management, networking integration using CNI, pluggable image/storage management
1. Support for exec/attach
1. Target fully automated kubernetes testing without failures


<a class="twitter-timeline"  href="https://twitter.com/hashtag/%22cri-o%22" data-widget-id="862337196434632704">#"cri-o" Tweets</a>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","twitter-wjs");</script>
