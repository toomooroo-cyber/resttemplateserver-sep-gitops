# Document from k8s.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: resttemplateserver-sep
  labels:
    app: resttemplateserver-sep
spec:
  replicas: 1
  selector:
    matchLabels:
      app: resttemplateserver-sep
  template:
    metadata:
      labels:
        app: resttemplateserver-sep
### Add the spec section to the deployment.yaml file from the kustomization.yaml file connenct with haror-secret
    spec:
      imagePullSecrets:
        - name: harbor-secret
----
      containers:
        - name: resttemplateserver-sep
          image: 210.90.24.172:8080/camit/toomooroo/resttemplateserver-sep:latest
          imagePullPolicy: Always
          ports:
            - containerPort: 8080
          env:
            - name: SPRING_DATASOURCE_URL
              value: "jdbc:mysql://mysql.default.svc.cluster.local:3306/bookdb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=Asia/Seoul&characterEncoding=UTF-8"
            - name: SPRING_DATASOURCE_USERNAME
              value: root
            - name: SPRING_DATASOURCE_PASSWORD
              value: root1234
          
---
apiVersion: v1
kind: Service
metadata:
  name: resttemplateserver-sep
spec:
  type: NodePort
  selector:
    app: resttemplateserver-sep
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30105 

## Run the command on power shell to create harbor secret

kubectl create secret docker-registry harbor-secret   --docker-server=210.90.24.172:8080   --docker-username=yklee2002   --docker-password=Byby3845//    --docker-email=yklee2002@gmail.com   -n default

on 
kubectl port-forward svc/argocd-server -n argocd 8080:443