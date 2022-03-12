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

## NameSpaces ( Ders 15 )

kubernetes clusteri bu örnekteki fileserver olarak düşünürsek . Namespaceleride buranın altındaki folderlar olarak düşünebiliriz .

![image](https://user-images.githubusercontent.com/74687192/157393359-94a59e9a-49bb-4f04-b810-a256e3400ac4.png)

her kubernetes kurulumunda varsayılan olarak 4 namespace oluşturulur .
- kube-system => kubernetes tarafından oluşturulan objelerin tutulduğu namespacedir
- kube-public => kimliği doğrulanmamış olanlarda dahil tüm kullanıcılar tarafından erişilmesine ihtiyaç duyulan objelerin oluştutulabileceği yerdir .
- kube-node-lease => 
- default => biz aksini belirtmediğimiz sürece objeler burada oluşturulur 
- `kubectl get pods --namespace kube-system`
- `kubectl get pods --all-namespaces` 
-  `kubectl create namespace app1`
- `kubectl get pods -n development`
-  `kubectl exec -it namespacepod -n development -- /bin/sh`
biz istersek kubectl.configimizde varsayılan olarak başka bir namespacei tanımlayabiliriz .
- `kubectl config set-context --current --namespace=development`

## Deployment ( Ders 16 )
Bizler genelde k8s üstünde singleton olarak adlandırdığımız tekil ,yönetilmeyen podlar yaratmayız . Podları yöneten üst seviye objeler yaratırız ve podlar bu objeler tarafından yaratılıp , yönetilir . Bu bölümde bu objelerin en sık kullanılanlardan bir tanesi olan `Deployment` objesine göz atacağız . 

TEKİL POD:

![image](https://user-images.githubusercontent.com/74687192/157398722-2a6db9eb-ecc7-47e4-a2c7-1cebddfcc224.png)

DEPLOYMENT:

![image](https://user-images.githubusercontent.com/74687192/157399321-b2f8c995-0a61-4faa-bc9f-54df9ce36dbc.png)

## Deployment Uygulama ( Ders 17 )

- `kubectl create deployment firstdeployment --image=nginx:latest --replicas=2`
- `kubectl get deployment`
 tek bir pod bile yaratacak olsak bunu deployment ile yaratmanın daha doğru olduğu söyleniyor .
- `kubectl set image deployment/firstdeployment nginx=httpd`
set komutu ile objelerde birçok değeri değiştirebiliriz .
- `kubectl scale deployment firstdeployment --replicas=5`

![image](https://user-images.githubusercontent.com/74687192/157403378-753ed0f9-34bd-4ee9-a3a5-52acda06868d.png)
- replicas => kaç replica oluşturması gerektiğini söylüyoruz .
- selector => bak deployment senin yöneteceğin podlar bu lable özelliğine sahio olacak demektir .
- template => deployment taradınfan oluşturulacak podların özelliklerini belirlediğimiz kısımdır .

![image](https://user-images.githubusercontent.com/74687192/157403695-eb655ee5-6190-4eb3-9d25-554e2949a1df.png)

metadata kısmı ve alt kısmının hepsini kopyala ve templatein sağ tarafına geçir . ve son olarakda metadata altındaki name anahtarını sil .

![image](https://user-images.githubusercontent.com/74687192/157403996-4bab4b92-e610-4b02-b247-d5d77a8b17e1.png)

her deployment objesinde farklı label ve selector kullanmamız gerekecek . 

## ReplicaSet (Ders 18)

bizlere podları direk yaratmaz Deployment gibi daha üst seviye objeler kullanırız ve bu objeler istediğimiz özellikte podlar yaratır

![image](https://user-images.githubusercontent.com/74687192/158015130-ea5c0384-96a0-4d0a-963c-e276e31f9fa6.png)

`kubectl get replicaset`
bizim tüm podlarımızı replicaset  objesi yönetiyor.
eski replicatset podları yavaş yavaş sildi . Eskisi duruyor ama .
`kubectl rollout undo deployment firstdeployment`

![image](https://user-images.githubusercontent.com/74687192/158015359-ad8d343a-7d61-4f38-a0db-d6ae5fc55f02.png)

replicaset sadece tek başına olanı değiştirme işlemini yapmaz , sadece yeni bir pod yaratacak ise o zaman podları yeni imajdan yaratır.


## Rollout ve Rollback 

![image](https://user-images.githubusercontent.com/74687192/158015516-66d3ab05-219e-4c5f-a1c4-0558c752b504.png)
 spec kısmı altında strategy kısmı altınde type key : value su bulunmakta biz burada . Biz burada deployment güncellendiği zaman rollout işlemlerinin nasıl yapılacağını ayarlıyoruz .

2 Adet rollout stratejisi bulunmakta 
- recreate = recreate şu anlama gelir , ben bu deploymentta bir değişiklik yaparsam öncelikle tüm mevcut podları sil ve bu işlem tamamlandıktan sonra yeni podları oluştur . major değişikliklerde recreate stratejisi seçilerek öncelikle tüm eski versiyonların sistemden kaldırılması ve yeni podların 
yaratılması istenebilir .
![image](https://user-images.githubusercontent.com/74687192/158016225-b9314ae6-b1a0-40dd-b9fb-333228029496.png)

- rolling => siz eğer yaml dosyanızda bir strateji berlitmesseniz de default olarak rolling update kullanılır . Rolling update , recreatein tam tersidir . Ben bir değişiklik yaptığım zaman tüm podları silip yenisini oluşturma bunun yerine bu işi aşamalı olarak yap 
- maxUnavailable => ben bir deploymentta bir değişiklik yaptığım zaman (geçiş sırasında)en fazla burada belirttiğim sayıda kadar podu sil .
- maxSurge => bu geçiş sırasında toplam pod sayısının en fazla kaç olabileceğini gösterir .

- `kubectl set image deployment rolldeployment nginx=httpd:alpine`

- `kubectl rollout history deployment rolldeployment`
- `kubectl rollout deployment rolldeployment --to-revision=3` => istediğim haline listede olana dönebiliyoruz .
işte bizler bu sayede deployment üstünde yaptığımız güncellemeleri kataloglama ve sonrasındada geri dönme imkanına kavuşuruz 
- `kubectl rollout status deployment rolldeployment -w` =>bazı durumlarda deploymentımızda canlı olarak neler olduğunu görmek isteyebiliriz o canlı görmek için de kullanacağımız yapı `status`dur.
- `kubectl rollout pause deployment rolldeployment` => rolloutun ortasında bu komutu yazarsam olan o andaki rollout durduruluyor .

## Kubernetes Ağ altyapısı (Ders 20 )

Uygulamalarımıızn birbirleri ile ve dış dünyadaki kaynaklar ile haberleşmesi gerekiyor .

![image](https://user-images.githubusercontent.com/74687192/158017155-8e338f53-622b-4c94-9f2b-625d50146852.png)

![image](https://user-images.githubusercontent.com/74687192/158017356-6a4e5f7c-bde3-4092-94b5-fb37cf122d4a.png)

![image](https://user-images.githubusercontent.com/74687192/158017443-7ebf68c3-1aa5-40bd-a8df-3d6646818e0f.png)

![image](https://user-images.githubusercontent.com/74687192/158017524-3c2ce261-35f2-4cf7-ac41-a3fde8dd9852.png)
Burada işlenenler aslında k8s içerisinde olan ingress trafiğidir 


## Service ( Ders 21)
şuanda öğreneceğimiz objemiz işin network tarafı ile ilgili .

![image](https://user-images.githubusercontent.com/74687192/158018240-609b944c-90bd-497e-9581-b4057e35857a.png)

![image](https://user-images.githubusercontent.com/74687192/158018255-50daccf7-1282-4360-9111-ed6fb7add01f.png)

![image](https://user-images.githubusercontent.com/74687192/158018448-c38f4672-205f-41d8-b7a5-faf7230a0db2.png)

![image](https://user-images.githubusercontent.com/74687192/158018456-52b58ccb-90a2-4e7a-a6a4-933593227084.png)

![image](https://user-images.githubusercontent.com/74687192/158018595-6ddb0d83-d325-463b-84e6-bbb302f2742d.png)

CLusterIp serviceleri kubernetes altında basitde olsa , service discovery , load balancing hizmetleri sunan obje tipidir 

![image](https://user-images.githubusercontent.com/74687192/158018698-d99461a0-8a91-4964-9958-531d32dc51b3.png)

Service Types :
- ClusterIp
- NodePort
- LoadBalancer
- External Name

## Service uygulama ( Ders 22)

![image](https://user-images.githubusercontent.com/74687192/158019108-b5b29553-6850-403a-8ce7-22a15c95987b.png)

biz selector kısmında app : backend valuesunu tanımlayarak service'e şunu diyoruz . git bu anahtar :değer veri eşleniğine sahip olan podları bul ve trafiği onlara yönlendir .yani service hangi podlara hizmet edeceğini service ile seçiyor .
- port => bu serviceın dinleyeceği port
- targetPort => podlarımızın expose ettiği porttur. 

- `nslookup backend`
k8s içinde core dns tabanlı bir dns hizmeti bulunur . Tüm podlar sorgularını buraya gönderir .Siz bir service oluşturduğunuz zaman onun isim ve ip adresi buraya kayıt olunur ve dolayısıyla tüm podlar bu isme air ip adresini öğrenebilirler .
![image](https://user-images.githubusercontent.com/74687192/158019500-3c624288-27fa-4158-9a30-36b2bb846002.png)

- `curl backend:5000`
- `minikube service --url frontend` => bu nodePorta bir tünel açacak ve bize başka bir port üstünden buraya erişme imkanı verecek  
- `curl http...`
- `kubectl c
- onfig use-context minikube`
### kubectl aracılığı ilede oluşturabiliyoruz
- `kubectl expose deployment backend --type=ClusterIP --name=backend`
- `kubectl expose deployment frontend --type=NodePort --name=frontend`

biz bir service oluşturduğumuz zaman bu service endpoit adında bir obje oluşturur ve bu endpoint objesi bizim belirlediğimiz selector tarafından seçilen podların IP adreslerini üstünde barındırır. Service trafiği nereye yönlendireceğini bu listeye göre düzenler .

- `kubectl get endpointsku`
- `kubectl describe endpoints frontend`

![image](https://user-images.githubusercontent.com/74687192/158020330-352c4f4e-2831-4e9d-abcb-b6793a409e6d.png)

`kubectl scale deployment frontend --replicas=5`
