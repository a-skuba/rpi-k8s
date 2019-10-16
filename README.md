# Azure Functions on a Raspberry Pi Kubernetes Cluster

![](https://raw.githubusercontent.com/gloveboxes/Raspberry-Pi-Kubernetes-Cluster/master/Resources/network.png)

|Author|Dave Glover, Microsoft Australia|
|----|---|
|Platform| Raspberry Pi, Kernel 4.19|
|Date|October 2019|

## Parts List

## Creating Raspberry Pi Boot SD Cards

## Kubernetes Master Installation

SSH to what will you will become the Kubernetes Master and run the following command:

```bash
bash -c "$(curl https://raw.githubusercontent.com/gloveboxes/Raspberry-Pi-Kubernetes-Cluster/master/setup.sh)"
```

The installation **setup.sh** bash script will first install **git** on the Raspberry Pi, this git repository will then cloned to the device, and then you will be prompted to install the Kubernetes Master or Node. Select **Master**

The installation will performance the following operations:

1. The Raspberry Pi will be renamed to **k8smaster**
2. Various optimizations/prerequisites set (tmpfs, GPU memory, 64bit kernel enabled, swap diabled, cgroups for k8s, iptables set to legacy mode)
3. Network settings configured (Static address for eth0, and packet routing defined)
4. DHCP Server and Docker installed
5. The Raspberry pi will reboot after Docker installation
6. Reconnect as **ssh pi@k8smaster.local**
7. The installation will restart
8. Kubernetes will be installed
9. [Flannel CNI](https://kubernetes.io/docs/concepts/cluster-administration/networking/#the-kubernetes-network-model) (Cluster Networking) installed
10. [MetalLB LoadBalance](https://metallb.universe.tf/) installed
11. Kubernetes Dashboard installation and configuration for admin access

## Kubernetes Node Set Up


Ensure the k8smaster and the Raspberry Pi that will be the first Kubernetes node are powered on and connected to the Network Switch. The DHCP Server running on the k8smaster will allocate an IP Address to the Raspberry Pi that will be the Kubernetes node.

![](resources/k8s-first-node.png)

1. Reconnect to the k8smaster **ssh pi@k8smaster.local**
2. From the k8smaster device **ssh pi@raspberry.local**
3. Run the following command from the SSH terminal:

    ```bash
    bash -c "$(curl https://raw.githubusercontent.com/gloveboxes/Raspberry-Pi-Kubernetes-Cluster/master/setup.sh)"
    ```

## Installing kubectl on your Desktop Computer

1. [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
2. Open a terminal window on your desktop computer
3. Change directory to your home directory
    * macOS, Linux, and Windows Powershell `cd ~/`, Windows Command Prompt `cd %USERPROFILE%`
4. Copy Kube Config from **k8smaster.local**

    ```bash
    scp -r pi@k8smaster.local:~/.kube .kube
    ```

## Kubernetes Dashboard

Acknowledgements:

* [Creating admin user to access Kubernetes dashboard](https://medium.com/@kanrangsan/creating-admin-user-to-access-kubernetes-dashboard-723d6c9764e4)

1. From the Kubernetes Master (ssh pi@k8smater.local), create a Dashboard access token

    ```bash
    kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
    ```

On Desktop computer start the Kubernetes Proxy

```bash
kubectl proxy
```

From your web browser, link to:

**http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/overview?namespace=default** 

![Kubernetes Dashboard](https://raw.githubusercontent.com/gloveboxes/RaspberryPiKubernetesCluster/master/Resources/KubernetesDashboard.png)

## Raspberry Pi Cluster

![Raspberry Pi Kubernetes Cluster](https://raw.githubusercontent.com/gloveboxes/RaspberryPiKubernetesCluster/master/Resources/rpi-kube-cluster.jpg)
