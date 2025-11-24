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
![Alias](./screenshots/parte1/habilitacion-addons/01-habilitar-addons.png)

- Habilitar MetalB (reemplaza el ranfo de las IPs de la red local)
```bash
microk8s enable metallb:10.0.0.100-10.0.0.110
```
![Alias](./screenshots/parte1/habilitacion-addons/02-metalb-addons.png)

- Verificación que todos estén activos
```bash
microk8s status
```
![Alias](./screenshots/parte1/habilitacion-addons/03-verificacion-addons.png)

### 1.4 Instalación Git y Docker
- Instalación Git
```bash
sudo apt install git -y
```
![Alias](./screenshots/parte1/git-docker/01-git-instalacion.png)

- Instalación Docker
```bash
sudo apt install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
```
![Alias](./screenshots/parte1/git-docker/02-docker-instalacion.png)

- Verificación instalación docker
```bash
docker --version
docker run hello-world
```
![Alias](./screenshots/parte1/git-docker/03-docker-verificacion.png)

- Login en Docker Hub
```bash
docker login
```
![Alias](./screenshots/parte1/git-docker/04-login-docker.png)
