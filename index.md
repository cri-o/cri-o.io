---
layout: default
---

{:.features}
* {:.feature} ![](assets/images/features-designed.svg) **Designed** Optimized for Kubernetes
* {:.feature} ![](assets/images/features-stable.svg) **Stable** Committed to passing Kubernetes tests
* {:.feature} ![](assets/images/features-any.svg) **Any Image, Any Registry** Pull from *any* [compliant registry](#container-images); run *any* [OCI-compliant container](#oci-compatible-runtimes)

## Try Now

* **Minikube**: Use [CRI-O](https://minikube.sigs.k8s.io/docs/reference/runtimes/#cri-o) as the container runtime
* **kubeadm**: Checkout this [kubeadm guide](https://github.com/cri-o/cri-o/blob/master/tutorials/kubeadm.md) to setup `kubeadm` to use CRI-O
* **Kubic**: Configured to use CRI-O [out of the box](https://kubic.opensuse.org/blog/2018-09-17-crio-default/) in both its `kubeadm` and `microOS` system roles

Join #crio on [Kubernetes Slack](https://slack.k8s.io/)

### Distribution Packaging

* **Fedora**: Available on all supported Fedora versions.
	* Fedora 30 and later
        Before installing CRI-O, it is recommended to list all the versions available for the current distro release.
        Example:
	```shell
	dnf module list cri-o
	VERSION=1.18
	dnf module enable cri-o:$VERSION
	dnf install cri-o
	```

* **openSUSE**: Available on Tumbleweed and [Kubic](https://kubic.opensuse.org) (installed by default on Kubic)
    ```shell 
    zypper in cri-o
    ```

* **Centos**: Available on the following versions

To install on the following operating systems, set the environment variable `$OS` as the appropriate field in the following table:

| Operating system | $OS               |
| ---------------- | ----------------- |
| Centos 8         | `CentOS_8`        |
| Centos 8 Stream  | `CentOS_8_Stream` |
| Centos 7         | `CentOS_7`        |

Then, set `$VERSION` to be the cri-o version matching your kubernetes version.
For instance, if you want to install cri-o 1.17, `VERSION=1.17`
We also support pinning to a particular release. To install 1.17.3, `VERSION=1.17:1.17.3`

And then run the following as root:
```shell
curl -L -o /etc/yum.repos.d/devel:kubic:libcontainers:stable.repo https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/devel:kubic:libcontainers:stable.repo
curl -L -o /etc/yum.repos.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.repo https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/devel:kubic:libcontainers:stable:cri-o:$VERSION.repo
yum install cri-o
```

* **Ubuntu/Debian**: 
To install on the following operating systems, set the environment variable `$OS` as the appropriate field in the following table:

| Operating system | $OS               |
| ---------------- | ----------------- |
| Debian Unstable  | `Debian_Unstable` |
| Debian Testing   | `Debian_Testing`  |
| Ubuntu 20.04     | `xUbuntu_20.04`   |
| Ubuntu 19.10     | `xUbuntu_19.10`   |
| Ubuntu 19.04     | `xUbuntu_19.04`   |
| Ubuntu 18.04     | `xUbuntu_18.04`   |

Then, set `$VERSION` to be the cri-o version matching your kubernetes version.
For instance, if you want to install cri-o 1.17, `VERSION=1.17`
We also support pinning to a particular release. To install 1.17.3, `VERSION=1.17:1.17.3`

And then run the following as root:
```shell
echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /" > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$VERSION/$OS/ /" > /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$VERSION.list

curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$VERSION/$OS/Release.key | apt-key add -
curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | apt-key add -

apt-get update
apt-get install cri-o cri-o-runc
```
   
* **RHEL**: Available with OpenShift

For more information on installation, visit our [install guide](https://github.com/cri-o/cri-o/blob/master/install.md).

### CRI-O Running Kubernetes Pods

<script type="text/javascript" src="https://asciinema.org/a/124131.js" id="asciicast-124131" async></script>

## What is CRI-O?

CRI-O is an implementation of the Kubernetes CRI (Container Runtime Interface) to enable using
OCI (Open Container Initiative) compatible runtimes.
It is a lightweight alternative to using Docker as the runtime for kubernetes.
It allows Kubernetes to use any OCI-compliant
runtime as the container runtime for running pods. Today it supports runc and Kata Containers
as the container runtimes but any OCI-conformant runtime can be plugged in principle.

CRI-O supports OCI container images and can pull from any container registry.
It is a lightweight alternative to using Docker, Moby or rkt as the runtime for Kubernetes.

## Contributors

{:.contributors}
* {:.contributor} Red Hat
* {:.contributor} Intel
* {:.contributor} SUSE
* {:.contributor} Hyper
* {:.contributor} IBM

CRI-O is developed by maintainers and contributors from these companies and others.
It is a community-driven, open source project. Feedback, users, and of course, contributors, are always welcome via the [cri-o/cri-o](https://github.com/cri-o/cri-o) GitHub project.

## Architecture

![CRI-O architecture](assets/images/architecture.png)

The architectural components are as follows:

- Kubernetes contacts the kubelet to launch a pod.
  - Pods are a kubernetes concept consisting of one or more containers sharing the same IPC, NET and PID `namespaces` and living in the same cgroup.
- The kubelet forwards the request to the CRI-O daemon VIA kubernetes CRI (Container runtime interface) to launch the new POD.
- CRI-O uses the `containers/image` library to pull the image from a container registry.
- The downloaded image is unpacked into the container's root filesystems, stored in COW file systems, using containers/storage library.
- After the rootfs has been created for the container, CRI-O generates an OCI runtime specification json file describing how to run the container using the OCI Generate tools.
- CRI-O then launches an OCI Compatible Runtime using the specification to run the container proceses. The default OCI Runtime is runc.
- Each container is monitored by a separate `conmon` process. The conmon process holds the `pty` of the PID1 of the container process.  It handles logging for the container and records the exit code for the container process.
- Networking for the pod is setup through use of [CNI](https://github.com/containernetworking/cni), so any CNI plugin can be used with CRI-O.

## Components

CRI-O is made up of several components that are found in different GitHub repositories.

* [OCI compatible runtime](https://github.com/opencontainers/runtime-tools)
* [containers/storage](https://github.com/containers/storage)
* [containers/image](https://github.com/containers/image)
* [networking (CNI)](https://github.com/containernetworking/cni)
* [container monitoring (conmon)](https://github.com/containers/conmon)
* security is provided by several core Linux capabilities

### OCI compatible runtimes

CRI-O supports any [OCI compatible runtime](https://github.com/opencontainers/runtime-tools).
We test with runc and Clear Containers today.

### Storage

The [containers/storage](https://github.com/containers/storage) library is used for managing layers
and creating root file-systems for the containers in a pod: `Overlayfs`, `devicemapper`, `AUFS` and `btrfs`
are implemented, with `Overlayfs` as the default driver.

Support for network based file system images (NFS, GlusterFS, CephFS) is on the development roadmap.

### Container images

The [containers/image](https://github.com/containers/image) library is used for pulling images from registries.
Currently, it supports Docker [schema 2/version 1](https://docs.docker.com/registry/spec/manifest-v2-1/)
as well as [schema 2/version 2](https://docs.docker.com/registry/spec/manifest-v2-2/).
It also passes all Docker and Kubernetes tests.

### Networking
The Container Network Interface [CNI](https://github.com/containernetworking/cni) is used for setting up networking for the pods.
Various CNI plugins such as Flannel, Weave and OpenShift-SDN have been tested with CRI-O and are working as expected.

### Monitoring

[conmon](https://github.com/containers/conmon) is a utility within CRI-O that is used to
monitor the containers, handle logging from the container process, serve attach clients and detects Out Of Memory (OOM)
situations.

### Security

Container security separation policies are provided by a series of tools including SELinux, Capabilities, seccomp,
and other security separation policies as specified in the OCI Specification.

### Contribute

You can find us at:

- [GitHub](https://github.com/cri-o/cri-o)
- Slack: [Kubernetes](https://slack.k8s.io/) #crio

<a class="twitter-timeline"  href="https://twitter.com/hashtag/%22cri-o%22" data-widget-id="862337196434632704">#"cri-o" Tweets</a>
<script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","twitter-wjs");</script>
