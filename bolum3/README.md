## Ders -1 (Kubectl Kurulumu)
Api server ile iletişim kurmak için ilk yöntem rest çağrılarıdır . Her isteğimizi rest çağrıları ile göndermek zordur.
Eğer ki biz istersek bir grafiksel arayüz ile de kubernetesi yönetebilir , onunla iletişim kurabiliriz .
Gui yerinden komut satırından çalışmak tercih edilir . 
K8s kubectl adında bir cli içerir . kubectl bizlere shell üzerinden çalıştırıp k8s api server ile iletişim kurarak ona komutlar gönderebileceğimiz kubernetes projesinin resmi cli aracıdır .

## Ders -2 K8s Kurulum Çeşitleri 

![image](https://user-images.githubusercontent.com/74687192/153710474-07a97d86-1ca7-415f-a8a8-283f8cb848e1.png)
![image](https://user-images.githubusercontent.com/74687192/153710477-7f94854d-7c84-482e-a241-85724cb99b04.png)
![image](https://user-images.githubusercontent.com/74687192/153710491-8114b543-830d-46da-86bf-1120561c40dc.png)

## Ders -3 Docker Desktop 
Single node bir k8s cluster ayağa kaldırabilme imkanı da sunuyor .

## Ders- 4 Minikube
Bir nevi k8s projesinin yan ürünlerinden bir tanesi . Minikube da ayni docker Desktop gibi bizim localimizde single node bir kubernetes cluster kurmamızı sağlıyor .
Minikube sisteme kurulduktan sonra sisteme bakıyor . sistemde docker varsa bu sanal makine işlerini o dockerı kullanarak hallediyor . eğer yoksa Hyper-v vmware virtualbox gibi sistemde olabilecek sanallaştırma katmanını tarıyor . Bulduğu sanallaştırma katmanını kullanarak arkada birkaç tane sanal makine ya da container ayağa kaldırarak k8si bunların üstünde ayağa kaldırıyor . Minikube kullanabilmem için sistemimde bu sanallaştırma katmanlarından birinin kurulu olması gerekiyor .

linux dağıtımlarından debian tabanlı ubuntu kullandığım için 

`brew install minikube`  komutu ile minikube' u kurdum .

`minikube status` ile kurulan minikube durumuna bakabiliriz.

`minikube start`

`minikube start --driver=hyper-v || minikube start --driver=docker`



