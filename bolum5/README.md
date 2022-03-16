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
