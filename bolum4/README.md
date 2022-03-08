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
  ``- name : nginx
    image: nginx:latest
    ports:
    - containerPort: 80`


`kubectl apply -f pod1.yaml` => pod1 yaml dosyası olarak bir obje tanımladım benim için oluştur demek oluyor .

`kubectl edit pods firstpod` => k8s clusterindan yaml olarak çekip açar . burada ekleyip kaydedip çıkarsak k8s hemen bu değişikliği bu obje üzerinde uygular .
genel olarak apply komutu ile yaml dosyasından yapılır .

## Pod Yaşam Döngüsü ( Ders -7 )

`Pending` => siz veya sistemde başka bir bileşen yeni bir pod oluşturmuştur .

etcd ye pod yazılır
k8s scheduler sürekli olarak etcdyi izliyor . poda uygun bir node bilgisini ekler 

`Creating` => pod oluşturma aşamasına geçer .
kubelet de scheduler gibi sürekli etcdyi izler ve bulunduğu nodea atanmış podları gözler , yeni yaratılmış podu görür hemen işlemlere başlar .

`Running` => containerlar çalışmaya başladığı anda da pod running durumuna geçer .

![image](https://user-images.githubusercontent.com/74687192/156436478-9f8bd96e-023c-4b48-9358-b33b4b143202.png)
Podun içerisinde oluşturulan containerlara pod tanımı içerisinde Restart-policy tanımı yapılır . bunun da 3 durumu vardır 

- Always
- On-failure
- Never
- Succeed => Podun yaşam döngüsünü başarılı bir şekilde tamamlama sonucunu içerir .
- CrashLoopBackOff => containerin sürekli restart edildiğini bir şeylerin yanlış gittiğini belirtir .

![image](https://user-images.githubusercontent.com/74687192/156445594-8632def0-65fc-4162-bd0d-0d8563502754.png)

## Pod Yaşam Döngüsü | Uygulama (Ders-8)

`kubectl get podes -w` => wacth işlevini yerine getirir .
Farklı yaml dosyalarının farklı pod status değerlendirmeleri yapıldı.

## Çoklu container pod ( Ders -9)

container ile ilgili best practiselerden biri tek bir containerda tek bir uygulama çalışsın . Neden bu best practise sorusu sorulacak olursa ileride karşılaşılan scale olma sorunlarından dolayı olarak düşünebiliriz . % 99 biz bir podda bir container olacak şekilde mimariyi tasarlarız lakin biz eğer ki istersek pod içerisinde birden fazla container da çalıştırabiliriz . Peki k8s neden buna izin veriyor ? 

![image](https://user-images.githubusercontent.com/74687192/157220350-16b3c154-4e61-4ab4-b730-2a5b6046baac.png)
 
Eğer podlar arasında tam bağımlılık var ise fluentd gibi burada her zaman ayrı podlar içerisinde container yaratmaya devam ettiğimizde bağımlılık hissedilir . Sürekli bağımlılığa göre 2 ekler 2 siler gibi bir durum ile karşılaşılır . ( container loglarını kaydeden grafana burada düşünülebilir gibi ) 

![image](https://user-images.githubusercontent.com/74687192/157222002-bb26c379-968a-4b50-82ab-75b3fb37a0dd.png)

2 poda aynı local volume ile bağlabilabilmesi için o iki podun da aynı worker node üstünde çalışması gerekiyor .

![image](https://user-images.githubusercontent.com/74687192/157224127-1ac423a0-56e3-4a17-8759-6c12a62294dc.png)


k8s , ana uygulamaya bağımlı ve onunla network seviyesinde izolasyon olmadan ve gerektiği durumda ayni depolama altyapısını kullanabilecek uygulamaları pod içerisinde 2. bir container olarak çalıştırma imkanı sağlıyor . bizler birlikte scale etmesi gereken , birbirleri ile network ve storage seviyesinde erişmesi gereken uygulamaları aynı pod içerisinde ayrı ayrı containerlar olarak çalıştırabiliyoruz .

## Coklu container pod Uygulama ( Ders 10 ) 

![image](https://user-images.githubusercontent.com/74687192/157227091-204654ef-65b0-41c5-add8-b34718869301.png)

command ile bir shell çalıştır dedik . args ile de bu komuta gelecek opsiyonları belirtiyoruz .

`kubectl exec  -it multicontainer -c webcontainer -- bin/sh`
-c ile bağlanmak istediğimiz containerı belirtmiş oluyoruz normald buna gerek yoktu çünkü genel olarak tek podda tek container çalışmakta oluyordur .

- `apt udpate`
- `apt install net-tools`
- `ifconfig` ile 2 containerın aynı ip adreslerinde olduğunu gördük
`kubectl port-forward pod/multicontainer 8080:80`

## Init Container ( Ders 11 )
pod içerisinde birden fazla container çalıştırmanın birden fazla yöntemi vardır . ve biz buna init container diyoruz

![image](https://user-images.githubusercontent.com/74687192/157286888-74e71be6-18dd-4627-9e3e-af58a0e47b65.png)

![image](https://user-images.githubusercontent.com/74687192/157292651-1cd9e5cb-8076-437b-b7e4-51c43b13c5e5.png)

`watch -n 2 kubectl describe pod initcontainerpod`

`kubectl logs -f initcontainerpod -c initcontainer`

## Label ve Selector (Ders 12)


![image](https://user-images.githubusercontent.com/74687192/157297288-d857b159-daba-45b7-821c-283097f94128.png)


![image](https://user-images.githubusercontent.com/74687192/157297602-29bacf0c-1b6d-4263-8dca-b07803a9b724.png)

## Label ve Selector Uygulama ( Ders 13 )

k8s altında kaynaklarınızı illa ayrı ayrı yaml dosyaları olarak tanımlamak zorunda değiliz . Her tanımdan sonra 3 tane - koyup yeni bir tanım daha ekleyebiliriz . label tanımını metadata kısmında yapıyoruz . labellar bize gruplama ve tanımlama imkanı verir .

- `kubectl get pods -l  "app" --show-labels` => app anahtarına sahip bütün podları listele 

- `kubectl get pods -l  "app=firstapp" --show-labels`

- `kubectl get pods -l  "app=firstapp,tier=frontend" --show-labels`

- `kubectl get pods -l  "app=firstapp,tier=frontend" --show-labels`

- `kubectl get pods -l 'app in (firstapp)'`

- `kubectl get pods -l 'app notin (firstapp)'`


- `kubetcl get pods -l "app in (firstapp),tier notin (frontend)" --show-labels`

- `kubectl label pods pod9 app=thirdapp`
- `kubectl label pods pod9 app-`
- `kubectl label --overwrite pods pod9 team=team3`
- `kubectl label pods --all foo=bar`
 
nodeSelector altında labellar ile çalışacak nodeu belirtebiliyoruz aslında .ku

## Annotation ( Ders 14 )

Objelere metadata eklemek için kullanabileceğimiz bir opsiyon olan labelları önceki bölümlerde işledik. fakat labellar tek seçenek değil 

![image](https://user-images.githubusercontent.com/74687192/157313905-2223568d-bfb6-41ee-974c-f2309410c119.png)

![image](https://user-images.githubusercontent.com/74687192/157313935-8c2b4d27-e558-44e2-ad0c-95141c32a0af.png)

`kubectl annotate pods annotationpod foo=bar` => labellar gibi




