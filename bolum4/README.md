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


![image](https://user-images.githubusercontent.com/74687192/156416447-f7eda80d-6174-4a38-8ae1-668a166ed909.png)
 
 kubernetes üzerinde her şey bir api objesidir . Pod kubernetesde oluşturup yönetebileceğimiz en küçük ve temel birimdir . Kubernetesde biz direk olarak container oluşturmayız yani direk git şu containeri çalıştır demeyiz .
 
 
![image](https://user-images.githubusercontent.com/74687192/156417297-05824dcb-6f2e-4e86-b397-ad4ded6a0113.png)

## Pod Oluşturma (Ders -4)

- `kubectl run firstpod --image=nginx --restart=Never` = image nginx olan düştüğünde restart olmıycak bir pod
- `kubectl describe` komutu bir objenin detaylarını görmemize imkan veriyor . events kısmı önemlidir .
- `kubectl describe deployments denemedeploy` 
- `kubectl logs firstpod` => podların loglarını gözlemlemek için
- `kubectl logs -f firstpod` => canlı olarak podların loglarına yapışmak için
- `kubectl exec firstpod -- ` => firstpodun içerisinde komut girmek için 
- `kubectl exec -it firstpod -- /bin/sh` =>firstpodun içerisindeki containerin içine bağlanmak için 
- `kubectl delete pods firstpod` => oluşturduğumuz podu silmek için 


## Yaml (Ders -5 )
Yaml bir veri serilizasyon standartıdır .

## Pod Yaml (Ders 6)
 
- apiVersion: => bu kısım bizim bu oluşturmak istediğimiz obje tipinin k8s APInda hangi endpoint üstünde ya da kaba tabirle hangi endpointte tanımlandığını belirttiğimiz kısımdır .
- kind:  => objenin ne objesi olduğunu burada tanımlarız .
- metadata: => bizim bu obje ile ilgili uniq bilgileri tanımladığımız yerdir .(namespace , isim , label , annotataionslar gibi )
- spec: => her obje tipine göre değişen kısımdır . spec kısmında oluşturmak istediğimiz objenin özelliklerini belirleriz .

`kubectl explain pods`

ilk 3 ü her k8s objesinde bulunması zorunlu üst seviye anahtarlardır.
sonuncusu specde objelerin çoğunda bulunur bazılarında bulunmayabilir.
`apiVersion: v1
kind: Pod
metadata:
  name : firstpod
  labels:
    app: front-end
spec:
  containers:
  - name : nginx
    image: nginx:latest
    ports:
    - containerPort: 80`


`kubectl apply -f pod1.yaml` => pod1 yaml dosyası olarak bir obje tanımladım benim için oluştur demek oluyor .

`kubectl edit pods firstpod` => k8s clusterindan yaml olarak çekip açar . burada ekleyip kaydedip çıkarsak k8s hemen bu değişikliği bu obje üzerinde uygular .
genel olarak apply komutu ile yaml dosyasından yapılır .

## Pod Yaşam Döngüsü ( Ders -7 )

