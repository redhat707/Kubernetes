# Kubernetes Install with Minikube on Linux

Ez egy alap Kubernetes telepítési jegyzet tanulási célra.  
A cél: egy helyi, egynode-os Kubernetes cluster indítása `minikube` segítségével.

## Mit telepítünk?

- `kubectl` – Kubernetes parancssori eszköz
- `minikube` – helyi Kubernetes cluster tanuláshoz
- Docker vagy más container runtime – konténerek futtatásához

## 1. Rendszer ellenőrzése

```bash
uname -m
```

Elvárt architektúra általában:

```text
x86_64
```

Ellenőrizd, hogy van-e internetkapcsolat:

```bash
ping -c 3 google.com
```

## 2. Docker ellenőrzése

```bash
docker --version
```

Ha Docker fut:

```bash
systemctl status docker
```

Ha nincs Docker, először container runtime-ot kell telepíteni.

## 3. kubectl telepítése

```bash
curl -LO "https://dl.k8s.io/release/stable.txt"
```

A legfrissebb stabil verzió letöltése:

```bash
KUBECTL_VERSION=$(cat stable.txt)
curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl"
```

Telepítés:

```bash
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

Ellenőrzés:

```bash
kubectl version --client
```

## 4. minikube telepítése

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
```

Telepítés:

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Takarítás:

```bash
rm minikube-linux-amd64
```

Ellenőrzés:

```bash
minikube version
```

## 5. Kubernetes cluster indítása Docker driverrel

```bash
minikube start --driver=docker
```

Ellenőrzés:

```bash
minikube status
```

Node ellenőrzése:

```bash
kubectl get nodes
```

Ha jó, ilyesmit látunk:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane    ...   ...
```

## 6. Cluster információk

```bash
kubectl cluster-info
```

Minden namespace podjainak listázása:

```bash
kubectl get pods -A
```

## 7. Első teszt deployment

```bash
kubectl create deployment hello-nginx --image=nginx
```

Ellenőrzés:

```bash
kubectl get deployments
kubectl get pods
```

## 8. Service létrehozása

```bash
kubectl expose deployment hello-nginx --type=NodePort --port=80
```

Service ellenőrzése:

```bash
kubectl get services
```

Minikube service megnyitása:

```bash
minikube service hello-nginx
```

Ha szerveren vagy grafikus böngésző nélkül, akkor URL lekérése:

```bash
minikube service hello-nginx --url
```

## 9. Teszt törlése

```bash
kubectl delete service hello-nginx
kubectl delete deployment hello-nginx
```

Ellenőrzés:

```bash
kubectl get all
```

## 10. Minikube leállítása

```bash
minikube stop
```

Újraindítás:

```bash
minikube start
```

Teljes törlés:

```bash
minikube delete
```

## 11. Fontos ellenőrző parancsok

```bash
kubectl version --client
minikube version
minikube status
kubectl get nodes
kubectl get pods -A
kubectl get all
```

## 12. Gyakori hibák

### Docker nem fut

Hiba például:

```text
docker is not running
```

Ellenőrzés:

```bash
systemctl status docker
```

Indítás:

```bash
sudo systemctl enable --now docker
```

### User nincs docker csoportban

Ha jogosultsági hiba van Docker használatakor:

```bash
sudo usermod -aG docker $USER
```

Utána ki-be jelentkezés vagy reboot szükséges.

### Minikube nem indul

Törlés és újraindítás:

```bash
minikube delete
minikube start --driver=docker
```

## Rövid összefoglaló

A Kubernetes tanuláshoz a legegyszerűbb helyi megoldás:

```bash
kubectl
minikube
docker
```

Alap folyamat:

1. `kubectl` telepítése
2. `minikube` telepítése
3. Docker ellenőrzése
4. `minikube start --driver=docker`
5. `kubectl get nodes`
6. első deployment létrehozása
7. service létrehozása
8. törlés és takarítás
