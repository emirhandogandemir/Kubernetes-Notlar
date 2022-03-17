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
