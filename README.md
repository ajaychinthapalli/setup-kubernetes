# Requirements
Minikube requires that VT-x/AMD-v virtualization is enabled in BIOS. To check that this is enabled on OSX / macOS run:
```text
sysctl -a | grep machdep.cpu.features | grep VMX
```
If there's output, you're good!
```text
bash-5.2$ sysctl -a | grep machdep.cpu.features | grep VMX
machdep.cpu.features: FPU VME DE PSE TSC MSR PAE MCE CX8 APIC SEP MTRR PGE MCA CMOV PAT PSE36 CLFSH DS ACPI MMX FXSR SSE SSE2 SS HTT TM PBE SSE3 PCLMULQDQ DTES64 MON DSCPL VMX EST TM2 SSSE3 FMA CX16 TPR PDCM SSE4.1 SSE4.2 x2APIC MOVBE POPCNT AES PCID XSAVE OSXSAVE SEGLIM64 TSCTMR AVX1.0 RDRAND F16C
```

# Prerequisites
- kubectl
- docker
- minikube
- virtualbox
```text
brew update
brew install kubectl
brew install --cask docker virtualbox
brew install minikube
```

# Verification
```text
bash-5.2$ docker --version
Docker version 19.03.13, build 4484c46d9d
bash-5.2$ docker-compose --version
docker-compose version 1.27.4, build 40524192
bash-5.2$ minikube version
minikube version: v1.27.1
commit: fe869b5d4da11ba318eb84a3ac00f336411de7ba
bash-5.2$ kubectl version --client
WARNING: This version information is deprecated and will be replaced with the output from kubectl version --short.  Use --output=yaml|json to get the full version.
Client Version: version.Info{Major:"1", Minor:"25", GitVersion:"v1.25.3", GitCommit:"434bfd82814af038ad94d62ebe59b133fcb50506", GitTreeState:"clean", BuildDate:"2022-10-12T10:47:25Z", GoVersion:"go1.19.2", Compiler:"gc", Platform:"darwin/amd64"}
Kustomize Version: v4.5.7
```

# Start
```text
minikube start
```
It will take a while, expected output:
```text
bash-5.2$ minikube start
😄  minikube v1.27.1 on Darwin 10.13.6
🎉  minikube 1.28.0 is available! Download it: https://github.com/kubernetes/minikube/releases/tag/v1.28.0
💡  To disable this notice, run: 'minikube config set WantUpdateNotification false'

✨  Automatically selected the docker driver. Other choices: hyperkit, ssh
💨  For improved Docker Desktop performance, Upgrade Docker Desktop to a newer version (Minimum recommended version is 20.10.0, minimum supported version is 18.09.0, current version is 19.03.13)
📌  Using Docker Desktop driver with root privileges
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.25.2 preload ...
    > preloaded-images-k8s-v18-v1...:  385.41 MiB / 385.41 MiB  100.00% 15.09 M
    > gcr.io/k8s-minikube/kicbase:  387.11 MiB / 387.11 MiB  100.00% 8.39 MiB p
    > gcr.io/k8s-minikube/kicbase:  0 B [________________________] ?% ? p/s 53s
🔥  Creating docker container (CPUs=2, Memory=1987MB) ...
🐳  Preparing Kubernetes v1.25.2 on Docker 20.10.18 ...
    ▪ Generating certificates and keys ...
    ▪ Booting up control plane ...
    ▪ Configuring RBAC rules ...
🔎  Verifying Kubernetes components...
    ▪ Using image gcr.io/k8s-minikube/storage-provisioner:v5
🌟  Enabled addons: storage-provisioner, default-storageclass
🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
```
Great! You now have a running Kubernetes cluster locally. Minikube started a virtual machine for you, and a Kubernetes cluster is now running in that VM.

# Check k8s
```text
bash-5.2$ kubectl get nodes
NAME       STATUS   ROLES           AGE     VERSION
minikube   Ready    control-plane   3m19s   v1.25.2
```

# Use minikube's built-in docker daemon:
```text
eval $(minikube docker-env)
```
Add this line to `.bash_profile` or `.zshrc` or ... if you want to use minikube's daemon by default (or if you do not want to set this every time you open a new terminal).

You can revert back to the host docker daemon by running:
```text
eval $(docker-machine env -u)
```
If you now run `docker ps`, it should now output something like:
```text
bash-5.2$ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
c7b879609c60        6e38f40d628d           "/storage-provisioner"   7 minutes ago       Up 7 minutes                            k8s_storage-provisioner_storage-provisioner_kube-system_c410ca2c-cecf-410d-bb13-38e969048f92_0
cfa5f44183de        5185b96f0bec           "/coredns -conf /etc…"   7 minutes ago       Up 7 minutes                            k8s_coredns_coredns-565d847f94-zr6pd_kube-system_f28ff7fa-8899-4860-bdb5-6229fa339304_0
60ba3882a370        k8s.gcr.io/pause:3.6   "/pause"                 7 minutes ago       Up 7 minutes                            k8s_POD_storage-provisioner_kube-system_c410ca2c-cecf-410d-bb13-38e969048f92_0
328aad89849c        1c7d8c51823b           "/usr/local/bin/kube…"   7 minutes ago       Up 7 minutes                            k8s_kube-proxy_kube-proxy-tzmgb_kube-system_601e548d-87fe-4dfb-b7ca-126ba8e267e2_0
c5b1f26169e2        k8s.gcr.io/pause:3.6   "/pause"                 7 minutes ago       Up 7 minutes                            k8s_POD_coredns-565d847f94-zr6pd_kube-system_f28ff7fa-8899-4860-bdb5-6229fa339304_0
df9e76992434        k8s.gcr.io/pause:3.6   "/pause"                 7 minutes ago       Up 7 minutes                            k8s_POD_kube-proxy-tzmgb_kube-system_601e548d-87fe-4dfb-b7ca-126ba8e267e2_0
afb8d718de0e        dbfceb93c69b           "kube-controller-man…"   8 minutes ago       Up 8 minutes                            k8s_kube-controller-manager_kube-controller-manager-minikube_kube-system_2e75927e0fd852156c6691ad3046836e_1
d38816460fb5        97801f839490           "kube-apiserver --ad…"   8 minutes ago       Up 8 minutes                            k8s_kube-apiserver_kube-apiserver-minikube_kube-system_0043f0e06f8033573472a91b2322f830_0
5730cd42e0d7        ca0ea1ee3cfd           "kube-scheduler --au…"   8 minutes ago       Up 8 minutes                            k8s_kube-scheduler_kube-scheduler-minikube_kube-system_d9e39786b2e03d044abb6c68064d5241_0
c9d592d3afb3        a8a176a5d5d6           "etcd --advertise-cl…"   8 minutes ago       Up 8 minutes                            k8s_etcd_etcd-minikube_kube-system_bd495b7643dfc9d3194bd002e968bc3d_0
36a4c8d23d9a        k8s.gcr.io/pause:3.6   "/pause"                 8 minutes ago       Up 8 minutes                            k8s_POD_kube-controller-manager-minikube_kube-system_2e75927e0fd852156c6691ad3046836e_0
0dec781dbb16        k8s.gcr.io/pause:3.6   "/pause"                 8 minutes ago       Up 8 minutes                            k8s_POD_etcd-minikube_kube-system_bd495b7643dfc9d3194bd002e968bc3d_0
f6a4e6053b1d        k8s.gcr.io/pause:3.6   "/pause"                 8 minutes ago       Up 8 minutes                            k8s_POD_kube-scheduler-minikube_kube-system_d9e39786b2e03d044abb6c68064d5241_0
305e62608a38        k8s.gcr.io/pause:3.6   "/pause"                 8 minutes ago       Up 8 minutes                            k8s_POD_kube-apiserver-minikube_kube-system_0043f0e06f8033573472a91b2322f830_0
```
