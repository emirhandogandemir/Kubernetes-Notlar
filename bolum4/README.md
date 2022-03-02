## Kubectl Config (Ders-1)
Kubectl bizlerin k8s clusterları ile iletişim kurmamızı sağlayan temel cli aracıdır .
kubectl bağlanacağı kubernetes cluster bilgisini config dosyalarından okur . Herhangi bir şekilde oluşturduğumuz config dosyasını parametre olarak girerek baplantı bilgilerine buradan bakmasını söyleyebiliriz .

default olarak `/home/username/.kube` altında config isimli dosyaya bakar .

context => kube config dosyasındaki bağlanmak istediğimiz clustera hangi userla bağlanacağımızı ve hangi namespace'e erişeceğimizi contextler yaratarak belirleriz.

![image](https://user-images.githubusercontent.com/74687192/156349464-0faaabd4-5317-4d1b-8521-ff29c49fd2db.png)


- `kubectl config` 
- `kubectl config get-contexts`
- `kubectl config current-context`
- `kubectl config use-context docker-desktop`


## Kubectl Kullanımı (Ders 2)
