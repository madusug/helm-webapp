
# HELM

For this project, I deployed a simple web application in a kubernetes cluster using Helm. I was able to utilize Helm charts to customize deployments with templates and values, installing and running helm and integrating helm in a basic ci/cd pipeline.

# Install Helm

```
choco install kubernetes-helm
```

# Create the helmchart

```
helm create webapp1
```
![webapp](./img/1%20helm%20create.jpg)


# Explore the "webapp1" directory

The "helm create" command created the following folders:

1. chart.yaml
2. values.yaml
3. templates folder

Next, I removed the contents of the values folder and used this instead:

```
appName: myhelmapp

namespace: default

configmap:
  name: helmappconfigmapv1.1
  data:
    CUSTOM_HEADER: "This app was deployed with Helm and Ugo"

image:
  name: devopsjourney1/mywebapp
  tag: latest
```

With that change, I proceeded to change the following manifest files to point to my values file.

### configmap.yaml

```
kind: ConfigMap 
apiVersion: v1 
metadata:
  name: {{.Values.configmap.name}}
  namespace: {{.Values.namespace}}
data:
  BG_COLOR: '#12181b'
  FONT_COLOR: '#FFFFFF'
  CUSTOM_HEADER: {{.Values.configmap.data.CUSTOM_HEADER}}
```

### deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{.Values.appName}}
  namespace: {{.Values.namespace}}
  labels:
    app: {{.Values.appName}}
spec:
  replicas: 5
  selector:
    matchLabels:
      app: {{.Values.appName}}
      tier: frontend
  template:
    metadata:
      labels:
        app: {{.Values.appName}}
        tier: frontend
    spec: # Pod spec
      containers:
      - name: mycontainer
        image: "{{.Values.image.name}}:{{.Values.image.tag}}"
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: {{.Values.configmap.name}}
        resources:
          requests:
            memory: "16Mi" 
            cpu: "50m"    # 50 milli cores (1/20 CPU)
          limits:
            memory: "128Mi" # 128 mebibytes 
            cpu: "100m"

```

### service.yaml

```
apiVersion: v1
kind: Service
metadata:
  name: {{.Values.appName}}
  namespace: {{.Values.namespace}}
  labels:
    app: {{.Values.appName}}
spec:
  ports:
  - port: 80
    protocol: TCP
    name: flask
  selector:
    app: {{.Values.appName}}
    tier: frontend
  type: NodePort
```


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

To deploy in different environments, I created a dev and prod namespace as follows:

```
kubectl create namespace dev
kubectl create namespace prod
```

I then proceeded to create a values-dev.yaml manifest file to change the namespace and configmap greeting as the helm chart is installed in the 'dev' namespace:

```
namespace: dev

configmap:
  data:
    CUSTOM_HEADER: "Welcome to Dev - Courtesy Ugo"

```

and I did the same thing for the values-prod.yaml manifest file:

```
namespace: prod

configmap:
  data:
    CUSTOM_HEADER: "Welcome to prod - Courtesy Ugo"

```
To install in the dev namespace I created earlier, I used:

```
helm install mywebapp-release-dev webapp1/ --values webapp1/values.yaml -f webapp1/values-dev.yaml -n dev
```
To install in the prod namespace I created earlier, I used:

```
helm install mywebapp-release-prod webapp1/ --values webapp1/values.yaml -f webapp1/values-prod.yaml -n prod
```

To see my helm releases in all namespaces:

```
helm ls --all-namespaces
```
I am also using this for Jenkins test.