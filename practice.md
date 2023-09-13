## kubernetes (k8s)
![preview](./Images/k8s1.png)

#### Manual steps to install kubernetes
* Create 3 vms and login
* consider 1vm as master and remaining as nodes
---Steps to be done in all the vms---
1. Install DOCKER in all vms

* `curl -fsSL https://get.docker.com -o install-docker.sh`
* `sh install-docker.sh` 
   * `now docker info` ( unix:///var/run/cri-dockered.sock)
   * now check docker version -server version will be denied.
    * `docker version` 
* add ubuntu user to docker group
 * `sudo usermod -aG docker ubuntu`
 * logout & login
  * `logut`
2. To install CRI-docker and get the latest releases  

  * wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/ cri-dockerd_0.3.4.3-0.ubuntu-jammy_amd64.deb
    * sudo dpkg -i cri-dockerd_0.3.4.3-0.ubuntu-jammy_amd64.deb (### dabean package )  
3. Insatall kubeadm, kubectl & kubelet on all "Vms" 

* Update the apt package index and install packages needed to use the Kubernetes apt repository:

 * `sudo apt-get update`
#### apt-transport-https may be a dummy package; if so, you can skip that package

 * `sudo apt-get install -y apt-transport-https ca-certificates curl`
* Download the public signing key for the Kubernetes package repositories. The same signing key is used for all repositories so you can disregard the version in the URL:
 * `curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg`

* Add the appropriate Kubernetes apt repository:

#### This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
 * ` echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list`

 * Update the apt package index,install kubelet,kubeadm and kubectl, and pin their version:
  
  *  sudo apt-get update
   • `sudo apt-get install -y kubelet kubeadm kubectl`
   • `sudo apt-mark hold kubelet kubeadm kubectl`

#### ---Execute the following on master node---
4. Now lets create a k8s cluster using kubeadm

5. Lets initialize the cluster using the following command as a root user

   * `sudo -i`
   * `kubeadm init --pod-network-cidr "10.244.0.0/16" --cri-socket "unix:///var/run/cri-dockerd.sock"`
6. On the master node to run kubectl as regular user execute the following

   • `mkdir -p $HOME/.kube`
   • `sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`
   • `sudo chown $(id -u):$(id -g) $HOME/.kube/config`
7. Now as a regular user execute kubectl get nodes
 * `kubectl get nodes`

 #### ---Execute the command in worker node---
 
8. Now as a root user in workernode execute the join command

####  we will get after initialise step
 * ` kubeadm join 172.31.13.69:6443 --token t0hkv6.es6qexws9lhbbi5x \ --discovery-token-ca-cert-hash sha256:d80f37bfb0dc701380dc1fb816cda39b91e84b841b3263d954c141507ae6929a \ --cri-socket "unix:///var/run/cri-dockerd.sock"`

#### ---execute in master node---

9. Now execute kubectl get nodes from master node
 * `kubectl get nodes`

10. Now kuberentes needs CNI plugin so that pod-network is enabled. Till this is done the DNS doesnot work, services don't work so nodes are shown as NotReady.

11. We can choose among wide range of CNI plugins, For this lets use flannnel. Execute the following on master node
  * ` kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml`

12. Now execute kubectl get nodes -w & wait for all the nodes to get to ready state 
   * `kubectl get nodes -w` 


[Refer here](https://directdevops.blog/2023/09/07/devops-classroomnotes-07-sep-2023/)

   






   



