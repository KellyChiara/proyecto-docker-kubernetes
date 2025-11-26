# Proyecto Final - Docker & Kubernetes

Aprendiendo el flujo profesional de actualización y gestión de aplicaciones en Kubernetes, utilizando microk8s como entorno de desarrollo local que simula un cluster cloud.

**Alumno:** Kelly Amanda Chiara Flores

**Fecha:** 23 de noviembre de 2025

**Curso:** [Docker & Kubernetes DIACONIA - i-Quattro](https://iquattrogroup.com/course/view.php?id=116)

## Parte 1: Setup del Ambiente

### 1.1 Creación de Maquina Virtual
Recursos necesarios

- VirtualBox
- Imagen ISO de Ubuntu 24.04

Donde la guía de creación de la maquina virtual, la encuentras [aqui](./creacion-maquina-virtual/README.md)

- Verificamos el sistema operativo
```bash
lsb_release -a
```
![Sistema Operativo](./screenshots/parte1/verificacion-ambiente/so-verificacion.png)

- Verificamos la memoria
```bash
free -h
```
![Memoria](./screenshots/parte1/verificacion-ambiente/ram-verificacion.png)

- Verificamos número de CPU Cores
```bash
lscpu | grep 'CPU(s):'
```
![CPU(s)](./screenshots/parte1/verificacion-ambiente/cpu-verificacion.png)

### 1.2 Instalación microk8s
- Actualizamos el sistema
```bash
sudo apt update && sudo apt upgrade -y
```
![Actualización sistema](./screenshots/parte1/instalacion-microk8s/01-actualizacion-instalacion.png)

- Instalar microk8s
```bash
sudo snap install microk8s --classic
```
![Instalación microk8s](./screenshots/parte1/instalacion-microk8s/02-microk8s-instalacion.png)

- Agregar usuario al grupo
```bash
sudo usermod -a -G microk8s $USER
sudo chown -f -R $USER ~/.kube
newgrp microk8s
```
![Agregar usuario](./screenshots/parte1/instalacion-microk8s/03-add_grupo-instalacion.png)

- Verificación de la instalación
```bash
microk8s status --wait-ready
```
![Verificación](./screenshots/parte1/instalacion-microk8s/04-verificacion-instalacion.png)

- Creación de alias
```bash
echo "alias kubectl='microk8s kubectl'" >> ~/.bashrc
source ~/.bashrc
```
![Alias](./screenshots/parte1/instalacion-microk8s/05-alias-instalacion.png)

### 1.3 Habilitar Addons
- Habilitar addons necesarios
```bash
microk8s enable dns
microk8s enable storage
microk8s enable ingress
microk8s enable metrics-server
```
![Habilitar addons](./screenshots/parte1/habilitacion-addons/01-habilitar-addons.png)

- Habilitar MetalB (reemplaza el ranfo de las IPs de la red local)
```bash
microk8s enable metallb:10.0.0.100-10.0.0.110
```
![Habilitar metalb](./screenshots/parte1/habilitacion-addons/02-metalb-addons.png)

- Verificación que todos estén activos
```bash
microk8s status
```
![Status](./screenshots/parte1/habilitacion-addons/03-verificacion-addons.png)

### 1.4 Instalación Git y Docker
- Instalación Git
```bash
sudo apt install git -y
```
![Instalación Git](./screenshots/parte1/git-docker/01-git-instalacion.png)

- Instalación Docker
```bash
sudo apt install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
```
![Instalación docker](./screenshots/parte1/git-docker/02-docker-instalacion.png)

- Verificación instalación docker
```bash
docker --version
docker run hello-world
```
![Verificación docker](./screenshots/parte1/git-docker/03-docker-verificacion.png)

- Login en Docker Hub
```bash
docker login
```
![Login](./screenshots/parte1/git-docker/04-login-docker.png)

### 1.5 Obtener y Desplegar proyecto integrador v2.0
- Descargamos el zip, lo descomprimimos y empezamos a inicializarlo

- Seguimos la guía adjunta [DEPLOYMENT_GUIDE_MICROK8S.md](./proyecto-integrador-docker-k8s/k8s/DEPLOYMENT_GUIDE_MICROK8S.md)

- Verificación microk8s
```bash
microk8s status
```
![microk8s](./screenshots/parte1/microk8s/01-microk8s-status.png)

- Verificación de recursos desplegados
```bash
kubectl get all -n proyecto-integrador
```
![recursos desplegados](./screenshots/parte1/microk8s/02-recursos-desplegados.png)

- Vista frontend desde la web con la ip expuesta

![frontend](./screenshots/parte1/microk8s/03-frontend.png)

- Vista backend desde la web con la ip expuesta

![frontend](./screenshots/parte1/microk8s/04-backend.png)

![api users](./screenshots/parte1/microk8s/05-api-users.png)

![health](./screenshots/parte1/microk8s/06-health.png)

![registro usuario](./screenshots/parte1/microk8s/07-registro-usuario.png)

![lista usuarios](./screenshots/parte1/microk8s/08-lista-usuario.png)

## Parte 2: Iteración v2.1 - Modificar Backend

- Generación de un nuevo Endpoint

Agregamos el siguiente codigo, en el archivo [GreetingController.java](./proyecto-integrador-docker-k8s/src/main/java/dev/alefiengo/api/controller/GreetingController.java)

```bash
@GetMapping("/api/info")
public ResponseEntity<Map<String, Object>> getInfo() {
    Map<String, Object> info = new HashMap<>();
    info.put("alumno", "Kelly Amanda Chiara Flores");
    info.put("version", "v2.1");
    info.put("curso", "Docker & Kubernetes - i-Quattro");
    info.put("timestamp", LocalDateTime.now().toString());
    info.put("hostname", System.getenv("HOSTNAME"));
    return ResponseEntity.ok(info);
}
```
![nuevo endpoint](./screenshots/parte2/01-endpoint.png)

- Push a Docker Hub
```bash
docker push kellychiara/springboot-api:v2.1
```

[kellychiara/springboot-api](https://hub.docker.com/r/kellychiara/springboot-api/tags)

- Actualizamos [api-deployment.yaml](./proyecto-integrador-docker-k8s/k8s/05-backend/api-deployment.yaml)

![modificacion de archivo](./screenshots/parte2/02-modificar-archivo.png)

- Aplicación de cambios
```bash
kubectl apply -f k8s/05-backend/api-deployment.yaml
kubectl rollout status deployment/api -n proyecto-integrador
kubectl get pods -n proyecto-integrador -w
```
![aplicación de cambios](./screenshots/parte2/03-cambios.png)

- **Verificación de funcionamiento**
```bash
kubectl port-forward -n proyecto-integrador svc/api-service 8080:8080

curl http://localhost:8080/api/info
```
![verificación](./screenshots/parte2/04-verificacion.png)

![verificación terminal](./screenshots/parte2/05-verificacion-terminal.png)

![verificación desde la web](./screenshots/parte2/06-verificacion-ip.png)
