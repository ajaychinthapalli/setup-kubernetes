# About My Mac (macOS High Sierra 10.13.6)
```text
Model Name           :	MacBook Pro
Model Identifier     :	MacBookPro14,2
Processor Name       :	Intel Core i5
Processor Speed      :	3.1 GHz
Number of Processors :	1
Total Number of Cores:	2
L2 Cache (per Core)  :	256 KB
L3 Cache             :	4 MB
Memory               :	8 GB
```
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

# Build, deploy and run an image on your local k8s setup
First setup a local registry, so Kubernetes can pull the images from there
```text
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```
```text
bash-5.2$ docker run -d -p 5000:5000 --restart=always --name registry registry:2
Unable to find image 'registry:2' locally
2: Pulling from library/registry
ca7dd9ec2225: Pull complete
c41ae7ad2b39: Pull complete
1ed0fc8a6161: Pull complete
21df229223d2: Pull complete
626897ccab21: Pull complete
Digest: sha256:ce14a6258f37702ff3cd92232a6f5b81ace542d9f1631966999e9f7c1ee6ddba
Status: Downloaded newer image for registry:2
7efa3289fa506fb581ceb4fb252f346c8bdeec2a1875d7ce5398af7a405ba06b
```
## Build
- Build the Dockerfile below locally
```text
bash-5.2$ docker build . --tag my-app
Sending build context to Docker daemon  102.4kB
Step 1/2 : FROM httpd:2.4-alpine
2.4-alpine: Pulling from library/httpd
c158987b0551: Pull complete
af0dc97e3e7a: Pull complete
7a4f45a5d61d: Pull complete
3b6adf47f20b: Pull complete
a7c0837131ea: Pull complete
82cf25aabba6: Pull complete
Digest: sha256:86ed18b4670b3be349e62f05c34bf0c28f3e0a73732969c417fd53e04af807f4
Status: Downloaded newer image for httpd:2.4-alpine
 ---> 4e7c9ee81ce6
Step 2/2 : COPY ./index.html /usr/local/apache2/htdocs/
 ---> ff1c26ada38c
Successfully built ff1c26ada38c
Successfully tagged my-app:latest
```
- You should now have an image named 'my-app' locally, check by using docker images (or your own image of course). You can then publish it to your local docker registry
```text
docker tag my-app localhost:5000/my-app:0.1.0
```
- Running `docker images` should now output the following:
```text
bash-5.2$ docker images
REPOSITORY                                TAG                 IMAGE ID            CREATED             SIZE
localhost:5000/my-app                     0.1.0               ff1c26ada38c        3 minutes ago       56.9MB
my-app                                    latest              ff1c26ada38c        3 minutes ago       56.9MB
httpd                                     2.4-alpine          4e7c9ee81ce6        11 days ago         56.9MB
registry                                  2                   81c944c2288b        4 weeks ago         24.1MB
registry.k8s.io/kube-apiserver            v1.25.2             97801f839490        2 months ago        128MB
registry.k8s.io/kube-scheduler            v1.25.2             ca0ea1ee3cfd        2 months ago        50.6MB
registry.k8s.io/kube-controller-manager   v1.25.2             dbfceb93c69b        2 months ago        117MB
registry.k8s.io/kube-proxy                v1.25.2             1c7d8c51823b        2 months ago        61.7MB
registry.k8s.io/pause                     3.8                 4873874c08ef        5 months ago        711kB
registry.k8s.io/etcd                      3.5.4-0             a8a176a5d5d6        6 months ago        300MB
registry.k8s.io/coredns/coredns           v1.9.3              5185b96f0bec        6 months ago        48.8MB
k8s.gcr.io/pause                          3.6                 6270bb605e12        15 months ago       683kB
gcr.io/k8s-minikube/storage-provisioner   v5                  6e38f40d628d        20 months ago       31.5MB
```

## Deploy & Run
- Creates Kubernetes objects:
```text
bash-5.2$ kubectl create -f my-app.yml
deployment.apps/my-app created
service/my-app created
```
- You should now see your pod and your service:
```text
bash-5.2$ kubectl get all
NAME                          READY   STATUS    RESTARTS   AGE
pod/my-app-674df799d7-fsdn8   1/1     Running   0          4m4s

NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        49m
service/my-app       NodePort    10.108.149.3   <none>        80:31385/TCP   4m5s

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/my-app   1/1     1            1           4m5s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/my-app-674df799d7   1         1         1       4m6s
```
- The configuration exposes my-app outside of the cluster, you can get the address to access it by running:
```text
bash-5.2$ minikube service my-app --url
http://127.0.0.1:61249
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
```
This should give an output like `http://127.0.0.1:61249` (the port will most likely differ). Go there with your favorite browser, you should see "Hello World!". You just accessed your application from outside of your local Kubernetes cluster!
![hello-world.png](images%2Fhello-world.png)