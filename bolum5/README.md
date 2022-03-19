## Volume ( Ders 1 )

Containerlar stateless uygulamalar için uygun bir yapıdadır .
![image](https://user-images.githubusercontent.com/74687192/158553825-99668bc3-63a5-4c31-b6de-3daef38e91a5.png)
![image](https://user-images.githubusercontent.com/74687192/158553958-d60b209c-6024-4fc6-a4f6-46b1d07c958b.png)
pod silinirse volume da silinir .
![image](https://user-images.githubusercontent.com/74687192/158554401-50a77423-adb1-4b95-bece-4d1c822a4f36.png)
volume tanımlamak için yapmamız gereken ilk şey spec altında volumes altında tanımını yapmaktır .
- `watch kubectl get pods`
-  `kubectl exec emptydir -c frontend -- rm -rf healthcheck` => livenssprobeun çökme senaryosunu burada işlemiş oluyoruz aslında
kubernetes liveness probedan dolayı restart işleminde aslında restart yapmıyor . yeni ve aynı bir containerı oluşturuyor aslında 
- `kubectl exec -it emptydir -c sidebar -- sh`
- empydir bize 2 imkanı sağlıyor
- 1) container yaşam süresinden daha uzun süre boyunca saklamak istediğimiz dosyaları bir tane emptydir tipinde volume yaratarak bunu ilgili foldera bağlayarak sağlıyabiliyoruz 
- 2) aynı podun üstünde birden fazla container varsa bunlar aynı dosyalar üzerinde çalışabilsin diye farklı farklı pathlere veya aynı pathlere volume'u mount ediyoruz ve bunlarda aynı dosyalara erişebiliyor .

fmrl volumelar bizim depolama sıkıntımızın tamamını çözmüyor.bazı durumlarda pod yaşam süresinden bağımsız şekilde saklamamız gereken dosyalar da oluyor .


## Secret ( Ders 2 )

image içerisine erişilmesi istenmeyen bilgileri gömmedik ama env kısmında bu seferde yaml içine gömmüş olduk . 

![image](https://user-images.githubusercontent.com/74687192/158569104-42b4263e-f601-4e32-a2db-65fbb94e457e.png)

![image](https://user-images.githubusercontent.com/74687192/158569347-0a2aed62-d8f5-462c-b5e3-7061b8b66d0d.png)
Oluşturduğunuz secretler ile atayacağınız podlar aynı namespacede olmalı .
type => 8 çeşit secret tipi bulunmakta 

- `kubectl get secrets`
- `kubectl get secrets`
- `kubectl create secret gener
ic mysecret2 --from-literal=db_server=db.example.com --from-literal=db_username=admin --from-literal=db_password=P@sw0rd!`
- `kubectl create secret gener
ic mysecret3 --from-file=db_server=server.txt --from-file=db_username=username.txt --from-file=db_password=password.txt`
- `kubectl create secret generic mysecret4 --from-file=config.json`

biz bu secretlaerı pod içerisinden okunabilmesi için oluşturduk peki o kısım nasıl yani bunları o poda nasıl ekleyebilirim . burada 2 seçenek var ya bunları o poda volume aracılığı ile aktarır ya da bu değerleri environment variable olarak geçerim.

secretlar bizlerin uygulama ile hassas verilerin ayrılmasına imkan tanır . secretlar vasıstası ile hassas verilerimizi container imagelarından ve yaml dosyalarından ayırabiliyoruz .

## ConfigMap ( Ders 3 )

![image](https://user-images.githubusercontent.com/74687192/158579247-0184d6b7-eff6-483d-a22a-432096ec4ce2.png)

- `kubectl create configmap my
configmap --from-literal=background=blue --from-file=a.txt`
- `kubectl get configmaps`
- `kubectl exec -it configmapp
od -- bash`



## Node affinity ( Ders 4 )

NodeSelector sayesinde biz bir podu istediğimiz bir node üstünde oluşturma imkanına sahip oluruz . Bu bölümde podlarımızın uygun  worker nodelar üstünde oluşturulması için kullanabileceğimiz ve nodeSelectorden daha fazla opsiyon sunan 
node affinity konusuna göz atacağız .

![image](https://user-images.githubusercontent.com/74687192/158590114-6f0d620f-05d5-46be-ad82-d97aa0e56b06.png)

![image](https://user-images.githubusercontent.com/74687192/158591809-fc9f533b-8893-4789-bd73-e8bb5287f03a.png)

![image](https://user-images.githubusercontent.com/74687192/158591833-5d5eeba0-1a58-477b-bd3d-6fdbf5dcd521.png)

required tanımı , mutlaka bu şart yerine getirilmeli demek oluyor .

preferred tanımı , ben burada bir tanım yapıyorum önceliğin buna göre bir node bulmak olsun ama bulamazsan da pending durumunda bekleme farklı bir worker node üzerinde oluştur .

IgnoredDuringExecution => pod bir kere schedule edildikten sonra çalışmaya devam etsin manasına gelmektedir .

- `kubectl get nodes`
- `kubectl label node minikube app=blue`




## Pod Affinity ( Ders 5 )

Bazı durumlarda podumuzun workernodeda çalışan başka bir podun olması ya da olmaması durumuna göre schedule edilmesini isteyebiliriz .

![image](https://user-images.githubusercontent.com/74687192/158600888-1b11ab36-0b40-4075-a469-e87a0874be50.png)

![image](https://user-images.githubusercontent.com/74687192/158601434-b6f95410-5d00-476e-aaf4-5fae8a02d170.png)

![image](https://user-images.githubusercontent.com/74687192/158601768-860e6223-d8b4-4ae4-bd4b-50404265da83.png)

clusterımız büyükse ve farklı farklı availability zonelar bulmam gerekiyorsa pod affinity kullanışlı olabiliyor . 

## Taint ve Toleration ( Ders 6 )

pod affinity ve node affinity poda göre bir tanımdır . ama blue olarak işaretlenmiş worker node da sadece blue uygulamasına ait kaynakların çalışmasını isterseniz bunu affinityler ile yapamazsınız . affinity tanımları podun nerede schedule edileceğini belirtir . 


![image](https://user-images.githubusercontent.com/74687192/158609142-f6f3b0e3-ea8e-4641-8ca1-5d33b3985e84.png)

- 1) NoSchedule
- 2) PreferNOSchedule
- 3) NoExecute
Taint kısıtı yok ise her podu kabul edecektir bu pod olarak anlayabiliriz .

- `kubectl taint node minikube
 platform=production:NoSchedule`

- `kubectl taint node minikube kubeplatform-`

![image](https://user-images.githubusercontent.com/74687192/158612275-de482ac8-0711-4adc-a576-103ef264882d.png)

- `kubectl taint node minikube color=blue:NoExecute`


## DaemonSet ( Ders 7 )

![image](https://user-images.githubusercontent.com/74687192/158660205-3904e531-1632-448e-8bb7-6dd9343b9b13.png)

varsayılan olarak her node üstünde bir pod oluşturulur .ama biz bunu değiştirerek sadece belirli tipler üstündeki nodelar üstünde oluşturulmasını sağlayabiliriz .
Örnek olarak log toplama uygulamaları gibi uygulamarı kolay şekilde deploy edilmesini sağlar .

![image](https://user-images.githubusercontent.com/74687192/158661056-5de0d519-1a02-4f1f-b3e8-b90611b8dc7f.png)

- `minikube node add` 

## Persistent Volume ve Persistent Volume Claim ( Ders 8 )

Bu bölümde podun yaşam süresinden bağımsız şekilde olan volumeleri göreceğiz .

podlar içerisinde sorun çıkması ve deployment objesi gibi yapıları ile containerın restart pozisyona gelmesi `Ephemeral volume` ile çözülebiliyordu zaten . peki ya podun çalıştığı worker node üzerinde bir sorun ile karşılaşırsak ne olacak ? deployment objesi podumuzun çalışabileceği yeni bir worker node'a yönlendirecek ve bizim ephemeral volume'umuz ortadan kaybolacak :( . bunun bir tek çözümü var bizim cluster dışında vuran ama tüm worker nodela tarafından ulaşılabilen bir yerde tutabilmemiz lazım . 

![image](https://user-images.githubusercontent.com/74687192/158760059-8bd3e42b-adc4-4b4c-9385-167e7a68b2b9.png)

![image](https://user-images.githubusercontent.com/74687192/158760182-7a9103a7-cde3-4a7b-b9e3-656d77158f92.png)

![image](https://user-images.githubusercontent.com/74687192/158760343-bc87f9d8-02f9-4289-8233-5611378486df.png)

K8s altında nfsdriverları bulunduğu için ek bir drive yüklememize gerek kalmadan bu iki ortam birbiri ile görüşebilir durumda .

Biz bir volume 'ü direkt olarak bir pod ile eşleştirme imkanına sahiğ değiliz fakat şunu bilmeliyiz ki bir volume'ü bir poda bağlamak için öncesinde persistent volume claim dediğimiz bir obje daha yaratmalıyız .

![image](https://user-images.githubusercontent.com/74687192/158762992-08571e4c-a521-4f7c-b1e0-62cd6428313c.png)

![image](https://user-images.githubusercontent.com/74687192/158763114-25608c16-7b1a-4646-9041-e268d81608fc.png)

## Pv ve Pvc Uygulama ( Ders 9)

Docker volume oluşturduk ve onun network ayarlarını gerçekleştirdik .

- `kubectl apply -f pv.yaml`
- `kubectl get pv`

persistent volume yaratılması ve onun öncesindeki kısım sistem yöneticilerini ilgilendiriyor . Persistent volume claim ve sonrası onun kullanımını developerlar ilgileniyor .

Access modeslar eşleşmek zorunda .

- `kubectl get pvc`

## Storage Class ( Ders 10 )

![image](https://user-images.githubusercontent.com/74687192/158794082-80700485-96b6-440f-a876-46c12e83bd53.png)

siz pvc oluşturduğunuz anda depolama ünitenizle haberleşerek gerekli ayarları yaparak size bir volume ve persistent volume objesi oluşturan yeni bir mekanizma geliştirdi . Bu mekanizmanın adı storage class . yani uzun lafın kısası persistent volume'u manuel oluşturmak yerine claim'e göre dinamik oluşturma imkanı getirdi .

- `kubectl get storageclass`

![image](https://user-images.githubusercontent.com/74687192/158797498-b1ba7646-ec21-4628-bf00-52160c2afb14.png)

![image](https://user-images.githubusercontent.com/74687192/158797548-175d8a86-83f1-4332-a87a-1c840132a869.png)

## StatefulSet ( Ders 11 )

Sorun state tutan yapılarda örneğin cassandra kurulumunda master pod olan ve diğer worker nodeların dağılımında 



![image](https://user-images.githubusercontent.com/74687192/159019794-f380168b-de4f-4898-b4cc-8c349992367f.png)

![image](https://user-images.githubusercontent.com/74687192/159019890-9e9369e0-a121-48ee-b0c5-f62e89e67ed2.png)

![image](https://user-images.githubusercontent.com/74687192/159020541-e0f0441a-6f9c-4d16-8933-3c1328ba5658.png)

![image](https://user-images.githubusercontent.com/74687192/159021495-213fdb99-dc82-4b59-b456-e1494868b09c.png)

her pod için bu özelliklerde bir pvc oluşturulmasını söylüyoruz . Bu pcvler de standart isimli storageclassı kullanarak her bir pod için birer pv oluşturacak . yani her podun aynı pv'ye bağlanması yerine her poda ayrı bir pv yaratılacak ve bunuda yaratacak pvcleride burada template ile yapıyoruz .

ClusterIp => none , buna headless service diyoruz .Bunu oluşturduğumuz anda clusterIP tipinde bir service oluşturulacak fakat ona bir Ip atanmayacak . Ben ne zaman bu service ismine gitmek istersem o bana bu servis altındaki podlardan bir tanesinin IP adresini dönecek bunun yanında her bir poda da 
`pod ismi.service ismi` şeklinde erişebilme imkanı da verecek .

- `kubectl scale statefulset cassandra --replicas=5`
- `kubectl delete pod cassandra-2`

- `Headless service`

## Job ( Ders 12 )

![image](https://user-images.githubusercontent.com/74687192/159025179-220f7902-4b28-4ec1-9501-f66e8cf994a6.png)

- 1) tek seferlik yapması gereken işi yapıp kapanması gereken uygulamalarımızı job olarak deploy ederiz .
- 2) bir kuyruk ya da bucketta işlenmesi gereken birçok işimiz olduğunda bunları eritmek adına bunlar eriyene kadar çalışacak uygulamaları job şeklinde deploy ederiz 


![image](https://user-images.githubusercontent.com/74687192/159026335-060c04b3-02f6-44de-b932-f6758abbc66f.png)

- `watch kubectl get jobs`

- backoffLimit = toplam kaç kere hata olursa tüm işlemleri bırakarak jobun fail etmesini istediğimizi söylediğimiz kısım .
- activeDeadlineSeconds => fail sayısı yerine saniye tarzından veriliyor .eğer bu görev 100 saniye içerisinde tamamlanmazsa anla ki sıkıntı var ve jobu fail et diyorum .

- `kubectl logs pod-name`

- `kubectl delete jobs.batch pi`

## CronJob ( Ders 13 )

![image](https://user-images.githubusercontent.com/74687192/159027235-22f11ab6-5970-4e9b-8c7b-8b58059e2d6b.png)

![image](https://user-images.githubusercontent.com/74687192/159027451-6731a3f7-5f63-4018-aecb-63a59c3c103c.png)

## Authentication ( Ders 14 )

![image](https://user-images.githubusercontent.com/74687192/159114692-ec872c97-d2e7-4619-8d00-33f5ef48f37f.png)

k8sde kullanıcı oluşturma ve kimlik doğrulama işi cluster dışında halledilecek şekilde tasarlanmıştır .KubeAPi server ayarlarında bu altyapılardan hangilerini kullanacağınızı belirlersiniz .Her k8s clusterında kök sertifika yetkilisi bulunur .Kimliğin doğrulanması bir kullanıcının cluster üzerinde her işi yapabileceği anlamına gelmez . 

varsayılan olarak minikube X509 client certificationları ile kimlik doğrulama yapacak şekilde tasarlanmıştır . 

Developer olarak sertifkiasyon yaratma işlemleri :
- `mkdir minikubecrt`
- `cd minikubecrt`
- `opensll genrsa -out emirhandogandemir.key 2048gn` => private key oluşturma işlemini bu şekilde gerçekleştirdim
- `ls`
- artık anahtarımız hazır 2.noktaya geçiyoruz . 2.aşama =>bu anahtarı kullanarak bir certificate signing request yani csr hazırlamam gerekicek .
- csr => bizlerin certificate authoritye sertifika sağlayıcıya gönderip bak bana bu özelliklerde bir dijital sertifika sağla dememize imkan veren dosyalardır . Bu dosyayı oluşturacak ve ardından bu dosyayı admine göndereceğiz . oda k8s'in certificate authoritysi ile bunu onaylayıp imzalayarak bizim sertifikamızı oluşturacak ve bize bir sertifika gönderecektir ve bizde bunu kullanarak kubernetese bağlanacağız . 
- `openssl req -new -key emirhandogandemi
r.key -out emirhandogandemir.csr -subj "/CN=emirhandgndmr51@gmail.com/O=DevMillennium"`
- Cn ile belirlenen kısım kullanıcı adımız , O ile belirlenen alan => bu kullanıcının hangi gruplara üye olacağını belirleyecek .
- şu aşamada developer olarak yapmam gerekeni yaptım bundan sonra k8s admine göndereceğim .
- `kubectl get csr`
- `kubectl certificate approve emirhandogandemir`
- `kubectl get csr emirhandogandemir -o`
- `kubectl get csr emirhandogandemir -o jsonpath='{status.certificate}' | base64 -d >> emirhandogandemir.crt`
- developera crt şeklinde elde ettiğimiz sertifikayı gönderiyoruz .
- sertifikam hazır artık bu sertifika ile kubectl config ayarlarını yaparsam kubernetes clusterına bağlanabilirim .
- `kubectl config set-credentials emirhandgndmr51@gmail.com --client-certicate=emirhandogandemir.crt --client-key=emirhandogandemir.key` =>emirhandogandemir adlı kulanıcı set edildi diyecektir bana .
- kullanıcıyı yarattık bir de bu kullanıcıyı minikube clusterımız ile ilişkilendirmemiz gerekiyor . onun için de bir tane context oluşturmam gerekiyor .
- `kubectl config set-context emirhandogandemir-context --cluster=minikube --user=emirhandgndmr51@gmail.com`
- `kubectl config use-context emirhandogandemir-context`

şu ana kadar yaptığımız işlemler bir kullanıcının doğrulanmasını yaparak kubernetese bağlanmasını sağlamak ile ilgiliydi . varsayılan olarak her kullanıcı 0 yetki ile gelir ve listeleme dahil hiçbir işlem yapamaz . 


## Role Based Access Control "RBAC"

![image](https://user-images.githubusercontent.com/74687192/159116177-5ee2fef0-ac82-4938-8c15-e15acaa16dab.png)

![image](https://user-images.githubusercontent.com/74687192/159116241-b83beb13-7942-4778-95d3-7ad2965df87d.png)

kullanıcılar sadece bind edildikleri rollere sahiptirler .

![image](https://user-images.githubusercontent.com/74687192/159116301-d5744dc3-dab1-45eb-aff1-28f3d36989fd.png)

![image](https://user-images.githubusercontent.com/74687192/159116308-f6934435-1bfd-411e-a3b7-b2e7c88eafb1.png)

![image](https://user-images.githubusercontent.com/74687192/159116314-ad3f8cfe-7ee5-4baf-848f-d9ab73092089.png)

![image](https://user-images.githubusercontent.com/74687192/159116432-6f7673d8-36c3-41c5-a8ea-b5b9da605fac.png)

tanımlar rules altında yapıllır . burada role'e atanan yetkiler konur .

![image](https://user-images.githubusercontent.com/74687192/159116551-7e7066e1-7d7e-4c67-a6b2-6cafd4a0eb04.png)

![image](https://user-images.githubusercontent.com/74687192/159116587-aed59eef-212f-4984-a316-db5b80dae201.png)

- `kubectl apply -f .`

## Service Account ( Ders 16 )

![image](https://user-images.githubusercontent.com/74687192/159117011-ffdb7f4d-1a73-4f58-851f-350dfe6522dd.png)

uygulamaların authenticate olması için service accountlar var .
![image](https://user-images.githubusercontent.com/74687192/159117118-f623f9ff-8360-4cec-86dd-698f5982f9a1.png)

- `kubectl get sa`
- `kubectl exec -it testpod -- bash`
- 
biz bir service account oluşturduğumuz zaman bu service account için bir secret oluşturulur .

- `curl --insecure https://kubernetes`
- `TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)`
- `curl --insecure https://kubernetes --header "Authorization:Bearer $TOKEN"`
- `curl --insecure https://kubernetes/api/v1/namespaces/default/pods --header "Authorization:Bearer $TOKEN"`
- `curl --insecure https://kubernetes/api/v1/namespaces/default/pods --header "Authorization:Bearer $TOKEN" | jq '.items[].metadata.name`

## Ingress ( Ders 17 )



- 1 ) her service için ayrı bir loadBalancer yaratmak ilk sorun


 bu iki sorunu da ingress controller ve ingress objeleri ile çözüyoruz .

- windows üzerine geçip çalışılan default driver olan dockerı değiştirdik 
- minikube start --driver=hyperv
ingress için bizim ilk başta bir ingress controller kurmamız gerekiyor (nginx,haproxy).
her ingress controllerın kurulumu farklıdır . o yüzden dökumanlarını inceleyip ona göre kurulum yapmamız gerekir .
- `minikube addons list`
- `minikube addons enable ingress`

artık clusterımızın giriş noktası burası . tüm servicelerimizi artık bunun üstünden dış dünyaya açıcaz ama nasıl ?

cluster içinden erişmek => clusterIp ama ben bunlara dış dünyadan erişilsin dersem bunları LoadBalancer tipine çeviricem . 

clustera deploy ettiğiniz ingress contrelların üstünde herhangi bir ayar yapmak isterseniz de bunu hemen hemen tüm ingress controller için annotationlar ile yapabilirsiniz .
tek bir ingress tanımlamak zorunda değiliz . birden fazla tanımlarsak ingress controller bunları görecek ve gerekli ayarlamalar yapılacak .
- `kubectl get ingress`
