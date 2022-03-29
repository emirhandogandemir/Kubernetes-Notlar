# DashBoard ve diğer gui alternatifleri ( Ders 1 )
minikubede dashboard bir addon olarak bulunmakta . Minikube harici için zaten k8s/dahsboard altında var .
- `minikube addons enable dashboard`
- `minikube addons enable metrics-server`
- `minikube dashboard`

Lens dashboard için third party bir seçenek . tavsiye edilebilir 

# ImagePullPolicy ve Image Secret ( Ders 2 )
private registrye authentice olmayı ve ImagePullPolicy yapısını anlamlandıracağız . Genel olarak production ortamlarda da biz private repositoryler kullanırız. 

authenticate olabilmemiz için 👍🏻
- registry url
- username
- password ihtiyacımız bulunmakta . Bunlara sahip olduktan sonra bunları bir secret olarak kubernetese gönderiyoruz . bir secret yaratıyoruz . bu secret tanımlarını da kullanmak istediğimiz pod tanımlarında özel bir yeri var orada kullanarak imageları çekiyoruz . 

- `kubectl create secret docker-registry "secret_ismi" --docker-server="registry_url" --docker-username="kullanıcı_adi" --docker-password="şifre"`

peki bu şekilde secretimizi oluşturuk bunu pod tanımlarında nasıl kullanağımızın cevabı ise 


![image](https://user-images.githubusercontent.com/74687192/160274742-cd572af5-6cf2-4716-9657-1bf5173945bf.png)

- imagePullSecrets denilen bir anahtar açıyorum . bu oluşturduğum secreti da bu anahtara veriyorum .  
- image çekilirken takip edebileceği 3 tane anahtar mevcut .Bu anahtarları da `ImagePullPolicy altında tanımlıyoruz.`
- 1) `Always` => her container çalıştırılmaya çalışıldığında yani her pod oluşturulmaya çalışıldığında bu image yeniden registryden çekilecek 
- 2 ) `Never` => bu sefer de hiçbir zaman repositorye gidip bu image'ı çekmeye çalışmıycak . sadece kendi localinde bu image varsa o localinden bu image'ı kullanacak 
- 3 ) `IfNotPresent` => öncelikle locale bakacak varsa ordan çekecek yoksa eğer çekmeye çalışacak .

eğer latest tagli bir image kullanıyor isek k8s default olarak ImagePullPolicyi `Always` olarak set ediyor . latest dışında bir şey kullanıyorsam k8s onu `IfNotPresent` olarak defaultta set ediyor .

# Static Pod ( Ders 3 )
kubernetesde üzerinde bir tane daha pod oluşturma yöntemimiz var bunada static pod diyoruz. Kubelet çalıştığı nodeun üzerinde bir klasör belirleyebiliyorsunuz nu klasörün içerisine yaml dosyaları şeklinde pod tanımlarınızı koyarsanız kubelet o node'un üzerinde bu podları ayağa kaldırabiliyor .

kubeletin tek işi => container engine konuşarak container oluşturmak yani pod oluşturmak.

varsayılan olarak :
etcd-> kubernetes -> manifest 
biz bir makineye ilk önce kubeleti kuruyoruz . sonrasında kubelet izlediği folderdan kubernetesi ayağa kaldırıyor . işte biz buna static pod diyoruz .

# Network Policy ( Ders 4 )

-  k8s cluster altında her podun kendine özgü bir uniq idsi bulunmakta .
- bütün podlar birberleri arasında arada NAT olmadan haberleşebilmeleri gerekiyor . 
- bütün podlar diğer podlarla varsayılan olarak haberleşebiliyor olmaları gerekiyordu . 
- bunun dışında podlar worker nodeun erişebildiği her yerde erişebiliyor olmaları gerekiyordu .

biz buraya bir kısıtlama getirmek istiyor olabiliriz . mesela frontend podu sadece backend podu ile konuşabilsin vesaire gibi düşünebiliriz . biz yani k8s network altyapısında default olan ayarları bu Network Policy objesi ile değiştiriyor olacağız .
![image](https://user-images.githubusercontent.com/74687192/160276688-44c239ff-5fd7-4914-8e90-123ecc8fa829.png)

- biz bir network policy yaratırız , sonrasında bu network policyinin hangi podlar üzerinde etkili olacağına `podSelector`
ile seçeriz .
- öncelikle policyType belirliyoruz . 2 tane policyType belirliyoruz 
- 1 ) `Ingress` => benim selector ile seçtiğim podlara doğru gelen trafik anlamına geliyor .
- 2 ) `Egress` => podselector ile seçtiğim podlardan dış dünyaya doğru gidecek trafik ile ilgilidir .
ingress içerisinde 3 tane kullanım durumu var . örnekte bunların 3 ünüde koyulmuş lakin 3 ünüde kullanmak zorunda değiliz .
- 1) ipBlock  
- 2 ) namespaceSelector
- 3 ) podSelector
birden fazla policy varsa ne olacak sorusuna ise birlikte birleştirilerek assign edilir olarak düşünebiliriz .
bizim network policyleri kullanabilmemiz için ayrıyeten network policiy destekli bir CNI kullanmamız gerekir `calico gibi`

# Helm ( Ders 5 )

ben bir uygulamayı kubernetes üzerinde kurulabilecek şekilde bir paket haline getirseydim ve insanlar da bu paketi kubernetese kursalardı sanırım daha mantıklı olurdu .Helm bize kubernetes üzerinde paket yükleme imkanı sağlıyor .

brew => linuxa paket kurmamızı sağlıyordu . biz bunlara paket yöneticileri diyorduk . bu paket yöneticilerinin kubernetes için olanına da biz `helm` diyoruz . bizim uygulamalarımızı paketler haline getirmemizi ve bu paketleri tek bir komutla kubernetese yüklememizi sağlıyor .bre

- `brew install helm`
 ilk olarak helmde bilmemiz gereken önemli 3 tane kısım var 
- 1 ) helmde ilk bilmemiz gereken kavram `chart` kavramı , sizin kubernetes üzerine yükleyebildiğiniz uygulamanın paketlenmiş haline biz chart diyoruz . 
- 2 ) chart paketin kendi adı , releasede siz bunu kubernetes clusterınıza yüklediğinizde verilen isim .
- 3 ) repository dediğimiz şey bu helm chartların birlikte tutulduğu yerler .Mesela ben bir tane repository ekliyorum bu repository altına çeşitli helm chartlar ekleyebiliyorum . 

### Arama Kısmı 
- `helm search hub wordpress` => artifact hub
- `helm search repo wordpress` => bu bilgisayara eklenmiş repositoryler 
### Yükleme Kısmı
- ilk başta sistem hangi charı release edeceğini bilmesi gerektiği için sistemde repoyu eklemem gerekir 
- `helm install "release ismi" "kuracağım chart"`
### -
- `helm status wordpress`
- `helm show values bitnami/wordpress` => default ayarlar yerine kullanabileceğim ayarları bu komut ile görebiliyorum buna göre bi aksiyon izleyebiliyorum .
- `helm uninstall wordpress`
- `helm upgrade`
- `helm rollback revizyon adı` => hangi revizyona geri dönmek istiyorsak

## Monitoring (Prometheus Stack)

kubernetes özelişnde monitoring işleri için 4 farklı yer var olarak söyleyebiliriz özelliklede production ortamlarında.
- 1) kubernetes cluster durumu o an nasıl çalışıyor
- 2 ) objelerin mevcut durumu
- 3 ) nodeların izlenmesi
- 4 ) containerların içerisinde çalışan uygulamarın logları durumları

- `kubectl get pods`
- `kubectl get all -A`
- `kubectl describe pods firstpod` => spesifik podun durumu ile alakalı
-  `kubectl get events -A` => tüm eventsleri gözlemliyoruz cluster üzerinde
- `kubectl top node`
- `kubectl top pod`
- `kubectl logs "pod-ismi"` => şeklinde kullanılabilir 

ama tabi production ortamlarda biz bunları manuel olarak çekersek bunlar yönetilmez hale gelebilir . benim merkezi bir loglama sunucusu veya merkezi bir metric sunucusu bulup bu logları k8s clusterından bu sunucuya çekmem buna göre de artık görselleştirmem yada alert göndermem gibi olabilir . prometheus neredeyse k8s'in standart metric toplama aracı haline gelmiştir .

![image](https://user-images.githubusercontent.com/74687192/160699603-f36e4b64-e0e1-4a2f-b43c-9802148a07fb.png)

- `kubernetes metrics kurmamız gerekir bu kube api ile konuşarak objelerin mevcut durumu ile ilgili metricleri kubernetes apindan çekip prometheus için çekilebilecek duruma getiriyor.`
- `node'un cpu durumu node'un disk durumu node ile ilgili memory durumu vesaire node metriclerini de linux kernelinden çekmek istiyorsak bizim bu nodeların üstünde node exporter kurmamız gerekiyor.`

Grafanayıda görselleştirme aracı olarak düşünebiliriz ..

kube-prometheus-stack.md deki oluşturulan adımları izledim .
adminin yapacağı işi operatörler yazarak otomatik hale getiriyoruz .
