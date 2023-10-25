## Lock Down Inter-Pod Communication with NetworkPolicies
![image](https://github.com/emirhandogandemir/Kubernetes-Notlar/assets/74687192/b4c90ea9-6716-4a25-849f-28c78d25eee9)
![image](https://github.com/emirhandogandemir/Kubernetes-Notlar/assets/74687192/1f26942c-a697-4944-b3d4-9fdf7c2fcbd4)
### Solution
![image](https://github.com/emirhandogandemir/Kubernetes-Notlar/assets/74687192/ef35b24b-72c6-42f2-8934-1ebbb98b7e35)
![image](https://github.com/emirhandogandemir/Kubernetes-Notlar/assets/74687192/e73fa6b9-6977-4d46-aa0b-0a492182f84c)

- `k get pods -n web-auth --show--labels`
- `k label namespace test role=auth`
