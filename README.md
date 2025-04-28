
# Install Helm
```
choco install kubernetes-helm
```

# Create the helmchart
```
helm create webapp1
```
![webapp](./img/1%20helm%20create.jpg)


# Follow along with the video
- Create the files per the video, copying and pasting from templates-original
- you can also use the files in the solution folder

# Install the first one
```
helm install mywebapp-release webapp1/ --values webapp1/values.yaml
```

# Upgrade after templating
```
helm upgrade mywebapp-release webapp1/ --values mywebapp/values.yaml
```

# Accessing it
```
minikube tunnel
servicename=$(k get service -l "app=myhelmapp" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward service/myhelmapp 8888:80 --namespace default
```

# Create dev/prod
```
k create namespace dev
k create namespace prod
helm install mywebapp-release-dev webapp1/ --values webapp1/values.yaml -f webapp1/values-dev.yaml -n dev
helm install mywebapp-release-prod webapp1/ --values webapp1/values.yaml -f webapp1/values-prod.yaml -n prod
helm ls --all-namespaces
```
