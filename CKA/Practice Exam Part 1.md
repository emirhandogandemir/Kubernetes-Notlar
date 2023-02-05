
Normal workloads= worker node olarak ele alınır.
1) `kubectl describe node node_name`
2) `kubectl logs -n backend data-handler -c proc | grep -i "error" > deneme.txt`
3) `k top pod -n web --selector app=auth`
