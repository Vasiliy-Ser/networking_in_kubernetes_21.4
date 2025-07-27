# Домашнее задание к занятию 21.4 «Сетевое взаимодействие в Kubernetes» - Падеев Василий


### Примерное время выполнения задания

120 минут

### Цель задания

Научиться настраивать доступ к приложениям в Kubernetes:  
- Внутри кластера через **Service** (ClusterIP, NodePort).  
- Снаружи кластера через **Ingress**.  

Это задание поможет вам освоить базовые принципы сетевого взаимодействия в Kubernetes — ключевого навыка для работы с кластерами.  
На практике Service и Ingress используются для доступа к приложениям, балансировки нагрузки и маршрутизации трафика. Понимание этих механизмов поможет вам упростить управление сервисами в рабочих окружениях и снизит риски ошибок при развёртывании.

------

## **Подготовка**  
### **Чеклист готовности**  
- Установлен Kubernetes (MicroK8S, Minikube или другой).  
- Установлен `kubectl`.  
- Редактор для YAML-файлов (VS Code, Vim и др.).  

------

### Инструменты, которые пригодятся для выполнения задания  

1. [Инструкция](https://microk8s.io/docs/getting-started) по установке MicroK8S.  
2. [Инструкция](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download) по установке Minikube.  
3. [Инструкция](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/)по установке kubectl.  
4. [Инструкция](https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vscode-kubernetes-tools) по установке VS Code   

### Дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) Deployment и примеры манифестов.  
2. [Описание](https://kubernetes.io/docs/concepts/services-networking/service/) Описание Service.  
3. [Описание](https://kubernetes.io/docs/concepts/services-networking/ingress/) Ingress.  
4. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.  

------

## **Задание 1: Настройка Service (ClusterIP и NodePort)**  
### **Задача**  
Развернуть приложение из двух контейнеров (`nginx` и `multitool`) и обеспечить доступ к ним:  
- Внутри кластера через **ClusterIP**.  
- Снаружи через **NodePort**.  

### **Шаги выполнения**  
1. **Создать Deployment** с двумя контейнерами:  
   - `nginx` (порт `80`).  
   - `multitool` (порт `8080`).  
   - Количество реплик: `3`.  
2. **Создать Service типа ClusterIP**, который:  
   - Открывает `nginx` на порту `9001`.  
   - Открывает `multitool` на порту `9002`.  
3. **Проверить доступность** изнутри кластера:  
```bash  
 kubectl run test-pod --image=wbitt/network-multitool --rm -it -- sh  
 curl <service-name>:9001 # Проверить nginx  
 curl <service-name>:9002 # Проверить multitool  
```  
4. **Создать Service типа NodePort** для доступа к `nginx` снаружи.  
5. **Проверить доступ** с локального компьютера:  
```bash    
 curl <node-ip>:<node-port>    
   ```  
 или через браузер.

### **Что сдать на проверку**  
- Манифесты:  
  - `deployment-multi-container.yaml`  
  - `service-clusterip.yaml`  
  - `service-nodeport.yaml`  
- Скриншоты проверки доступа (`curl` или браузер).  


### Решение:

Создал [Deployment](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/src/deployment.yaml) с 3 репликами и [Service](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/src/service.yaml) с доступом через порты 9001 и 9002. Выполним проверку:

![answer1](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/png/1.png)  
![answer2](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/png/2.png)  

Создал [NodePort Service](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/src/nodeport_service.yaml) для доступа к `nginx` снаружи. 

![answer3](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/png/3.png)  


---
## **Задание 2: Настройка Ingress**  
### **Задача**  
Развернуть два приложения (`frontend` и `backend`) и обеспечить доступ к ним через **Ingress** по разным путям.  

### **Шаги выполнения**  
1. **Развернуть два Deployment**:  
   - `frontend` (образ `nginx`).  
   - `backend` (образ `wbitt/network-multitool`).  
2. **Создать Service** для каждого приложения.  
3. **Включить Ingress-контроллер**:  
```bash  
 microk8s enable ingress  
   ```
4. **Создать Ingress**, который:  
   - Открывает `frontend` по пути `/`.  
   - Открывает `backend` по пути `/api`.  
5. **Проверить доступность**:  
```bash
 curl <host>/
 curl <host>/api
   ```
 или через браузер.

### **Что сдать на проверку**  
- Манифесты:  
  - `deployment-frontend.yaml`  
  - `deployment-backend.yaml`  
  - `service-frontend.yaml`  
  - `service-backend.yaml`  
  - `ingress.yaml`  
- Скриншоты проверки доступа (`curl` или браузер).  


### Решение:

Создал [deployment-backend](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/src/backend-deployment.yaml) c service, [ddeployment-frontend](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/src/frontend-deployment.yaml) c service и [ingress](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/src/ingress.yaml). Выполним проверку:


![answer4](https://github.com/Vasiliy-Ser/networking_in_kubernetes_21.4/blob/cfd16574e30789a3ee8f194f3a4f3923f2be85f9/png/4.png)  

