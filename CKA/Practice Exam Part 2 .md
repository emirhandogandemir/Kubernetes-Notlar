## 1
`k edit deployments web-frontend -n web`
`ports:`
 - containerPort:80
## 2
![image](https://user-images.githubusercontent.com/74687192/216811155-d842c886-eee1-4f50-b7e5-9484bdafa138.png)
`k get endpoints -n web web-frontend-svc`
## 3
`k scale deployment web-frontend -n web --replicas=5`
## 4
`k get svc -n web`
`calhost:30080/`
