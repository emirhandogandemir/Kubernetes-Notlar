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
