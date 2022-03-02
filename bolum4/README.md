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

- `kubectl cluster-info`
 
  kubectl + fiil + üzerinde uygulanacak obje şeklinde kubectl kullanımı düşünülebilir .
  
 `kubectl delete pods testpod`
 
 kubectl + fiil + obje + obje üzerinde spesifik işlem yapmak istiyorsak yukarıdaki .
 
 kubectlde aksi belirtilmediği sürece tüm komutlar contextde set edilmiş namespace üzerinde uygulanır .
 set edilmemişse default namespace üzerinde uygulanır .
 
- `kubectl get pods -n kube-system`
- `kubectl get pods -A` = namespace ayrımı olmadan gösterim sağlıyor
- `kubectl get pods -A -o wide` -o ile output formatı değiştirilebiliyor .
- `kubectl get pods -A -o yaml` -o json , jsonpath , go-template , yaml , wide , custom-columns 
- `kubectl get pods -A -o json | jq -r ".items[].spec.containers[].name`

linuxda man in karşılığı => kubectl explain ile k8s objelerini check etmek için `kubectl explain pod` komutu kullanılabilir

`kubectl explain deployment`

Bu bölümde temel olarak kubectl i nasıl kullanabileceğimize odaklandık ve bilmemiz gereken temel opsiyonları öğrendik .
 

## Pod (Ders-3)

 
 
 
