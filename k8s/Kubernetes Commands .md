#Команда				Что делает
	
```kubectl version```					Показать версию kubectl client'a и сервера  
```kubectl version --client```			Показать версию kubectl client'a  
```kubectl get componentstatuses```		Показать Состояние K8s Cluster'a  
```kubectl cluster-info```				Показать Информацию о K8s Cluster'e  
```kubectl get nodes```					Показать все Серверы K8s Cluster'a  

```kubectl version```					Показать версию kubectl client'a и сервера  
```kubectl version --client```			Показать версию kubectl client'a  
```kubectl cluster-info```				Показать Информацию о K8s Cluster'e  
```kubectl get nodes```					Показать все Серверы K8s Cluster'a  
	
```kubectl version --client```			Показать версию kubectl client'a  
```kubectl cluster-info```				Показать Информацию о K8s Cluster'e  
```kubectl get nodes```					Показать все Серверы K8s Cluster'a  
```kubectl version --client```			Показать версию kubectl client'a  
```kubectl cluster-info```				Показать Информацию о K8s Cluster'e  
	
```kubectl get nodes```					Показать все Серверы K8s Cluster'a  
```kubectl get nodes --show-labels```	Показать все Labels у Серверов K8s Cluster'a  
```kubectl label node node2 node-role.kubernetes.io/worker=```	Установить Label на worker у сервера node2  

```kubectl get pods```										Показать все Pods  
```kubectl run hello --generator=run-pod/v1 --image=nginx:latest --port=80```	Создать Pod из DockerImage nginx:latest и открыть порт 80  
```kubectl port-forward hello 7777:80``` 					Порт 7777 нашего компа теперь Порт 80 нашего Pod  
```kubectl describe pods hello```							Показать все данные  Pod hello  
```kubectl delete pods hello```								Стереть Pod hello   
```kubectl logs hello```									Показать лог из Pod hello  
```kubectl exec my-web date```								Запустить команду date на Pod my-web  
```kubectl exec -it my-web bash```							Запустить команду bash интерактивно на Pod my-web  
```kubectl apply -f myfile.yaml```							Создать объекты в K8s из Манифест Файла myfile.yaml  
```kubectl delete -f myfile.yaml```							Удалить объекты из K8s из Манифест Файла myfile.yaml  

```kubectl get deployments```													Показать все Depoyments  
```kubectl get rs```															Показать все ReplicaSets  
```kubectl create deployment denis-deployment --image httpd:latest```			Создать Deployment из DockerImage httpd:latest  
```kubectl describe deplyoments denis-deployment```								Показать все данные о Deployments denis-deployment  
```kubectl scale deployment denis-deployment --replicas 4```					Создать ReplicaSets  
```kubectl autoscale deployment denis  --min=10 --max=15 --cpu-percent=80```	Создать AutoScaling для Deployment denis  
```kubectl get hpa```															Показать все HPA - HorizontalAutoScalers  
	
```kubectl set image deployment/denis-deployment k8sphp=adv4000/k8sphp:version2 --record```	Заменить Deployment denis-deployment Image на новый   
```kubectl rollout status deployment/denis-deployment``` 									Показать статус Обновления  
```kubectl rollout history deployment/denis-deployment``` 									Показать историю Обновлении  
```kubectl rollout undo deployment/denis-deployment``` 										Вернуться на предидущую версию  
```kubectl rollout undo deployment/denis-deployment --to-revision=2```						Вернуться на указанную версию  
```kubectl rollout restart deployment/denis-deployment``` 									Передеплоить текущую версию  
	
```kubectl delete deployments denis-deployment``` 	Стереть Deployment denis-deployment  

```kubectl create deployment denis-deployment --image adv4000/k8sphp:latest```	Создать Deployment из Docker Image adv4000/k8sphp:latest  
```kubectl get deployment```													Показать все Depoyments  
```kubectl scale deployment denis-deployment --replicas 4```					Создать ReplicaSets  
```kubectl expose deployment denis-deployment --type=ClusterIP --port 80```		Создать Service типа ClusterIP для Deployment  
```kubectl expose deployment denis-deployment --type=NodePort --port 80```		Создать Service типа NodePort для Deployment  
```kubectl expose deployment denis-deployment --type=LoadBalancer --port 80```	Создать Service типа LoadBalancer  для Deployment  
```kubectl get services```														Показать все Services  
```kubectl get svc```															Показать все Services  
```kubectl describe nodes | grep ExternalIP```									Показать External IP со всех Worker Nodes  
	
```kubectl delete services  my-webserver```	Удалить Service my-webserver  

```kubectl apply -f https://projectcontour.io/quickstart/contour.yaml```	Создать Ingress Controller Contour  
```kubectl get services -n projectcontour envoy -o wide```					Показать Ingess Controller Load Balancer данные  
```kubectl create deployment main    --image=adv4000/k8sphp:latest``` 	Создать Deployment  
```kubectl create deployment web1    --image=adv4000/k8sphp:version1```	Создать Deployment  
```kubectl create deployment web2    --image=adv4000/k8sphp:version2```	Создать Deployment  
```kubectl scale deployment main    --replicas 2```						Создать ReplicaSets  
```kubectl scale deployment web1    --replicas 2```						Создать ReplicaSets  
```kubectl scale deployment web2    --replicas 2```						Создать ReplicaSets  
```kubectl expose deployment main   --port 80   # --type=ClusterIP  DEFAULT```	Создать Service, по умолчанию тип ClusterIP  
```kubectl expose deployment web1   --port 80```						Создать Service, по умолчанию тип ClusterIP  
```kubectl expose deployment web2   --port 80```						Создать Service, по умолчанию тип ClusterIP  
```kubectl expose deployment tomcat --port 8080```						Создать Service, по умолчанию тип ClusterIP  
	
```kubectl get services -o wide```	Показать данные всех Services  
	
```kubectl apply -f ingress-hosts.yaml```	Создать Ingress Rules из файла  
```kubectl apply -f ingress-paths.yaml```	Создать Ingress Rules из файла  
```kubectl get ingress```					Показать все созданные Ingress Rules  
```kubectl describe ingress```				Показать все созданные Ingress Rules подробно  
```kubectl delete ns projectcontour```		Стереть полностью Ingress Controller Contour  

```minikube version```	Показать версию minikube  
```minikube start```	Создать и Запустить VM с K8s Cluster с параметрами по умолчанию  
```minikube stop```		Остановить VM с нашим K8s Cluster  
```minikube delete```	Удалить VM с нашим K8s Cluster  
```minikube ssh```		Сделать Login на VM с нашим K8s cluster  
```minikube start --cpus=4 --memory=8gb   --disk-size=5gb``` 		Создать и Запустить VM с K8s Cluster в нашими параметрами  
```minikube start --cpus=2 --memory=6000mb --disk-size=4000mb```	Создать и Запустить VM с K8s Cluster в нашими параметрами  
```minikube start -p MYSUPERCLUSTER```								Создать и Запустить VM с K8s Cluster с нашим именем  

```helm version```	Пока версию Helm  
```helm list```		Показать все задеплоенные Helm Releases  
```helm search hub```	Показать Helm Chart с общего списка Hub  
```helm search repo```	Показать Helm Chart из добавленных Repos  
```helm install app1 Denis-Chart/```												Задеплоить Helm Chart app1 из директории Denis-Chart  
```helm upgrade app1 Denis-Chart/ --set container.image=adv4000/k8sphp:version2```	Обновить Деплоймент app1  
```helm create MyChart```						Сделать скелет Helm Chart в директории MyChart  
```helm package Denis-Chart/```					Запаковать Helm Chart в tgz архив  
```helm install app2 App-HelmChart-0.1.0.tgz```	Задеплоить Helm Chart app2 из архива  
```helm delete app1```							Удалить Деплоймент Helm Chart app1  
```helm uninstall app1```						Удалить Деплоймент Helm Chart app1  
```helm repo add bitnami https://charts.bitnami.com/bitnami```	Добавить Helm Chart Repo от bitnami  
```helm install my_website bitnami/apache -f my_values.yaml```	Задеплоить Helm Chart bitnami/apache с нашими переменными  

```docker build -t myk8sapp```								Создать DockerImage из локального Dockerfile  
```docker login```											Зайти в DockerHub  
```docker tag  myk8sapp:latest  adv4000/k8sphp:latest```	Переименовать Docker Image  
```docker push	```											Загрузить Image в Repository  
```docker images```											Показать все локальные Docker Images  
```docker rmi XXXXXXXXX -f```								Удалить локальный DockerImage с ID XXXXXXXX  
```docker run -it -p 1234:80 adv4000/k8sphp:latest```		Запустить Container на порту 1234 с нашим DockerImage  

```gcloud version```			Показать версию gcloud  
```gcloud init```				Настроить gcloud с вашим Google Cloud  
```gcloud services enable container.googleapis.com```	Включить создание K8s в вашем Google Cloud проекте  
```gcloud container clusters create denis```				Создать и Запустить K8s Cluster с параметрами по умолчанию  
```gcloud container clusters create denis --num-nodes=4```	Создать и Запустить K8s Cluster с нашими параметрами  
```gcloud container clusters get-credentials denis```		Настроить kubectl для работы с нашим K8s Cluster  
```gcloud container clusters delete denis```				Стереть наш K8s Cluster  

```eksctl version```				Показать версию eksctl  
```eksctl create cluster```			Создать и Запустить K8s Cluster с параметрами по умолчанию  
```eksctl delete cluster``` 		Удалить наш K8s Cluster  
```eksctl create cluster -f mycluster.yaml```	Создать и Запустить K8s Cluster с параметрами из файла  
```eksctl delete cluster -f mycluster.yaml```	Удалисть наш K8s Cluster с параметрами из файла  






