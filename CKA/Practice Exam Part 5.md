![image](https://user-images.githubusercontent.com/74687192/216829694-02c74c89-076e-40a4-831c-7d28717e29d2.png)

## 1 Control Plane
1) Dogru contextde oldugunu kontrol et
2) control plane sunucusunu drain'e çek k drain k8s-control --ignore-daemonsets
3) sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubeadm=1.22.2-00
4) sudo kubeadm upgrade plan v1.22.2
5) sudo kubeadm upgrade apply v1.22.2
6) sudo apt-get update && sudo apt-get install -y --allow-change-held-packages kubelet=1.22.2 kubectl=1.22.2
7) sudo systemctl daemon-reload
8) sudo systemctl restart kubelet

## 2 Worker Node
