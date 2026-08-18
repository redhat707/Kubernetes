# Minikube Install with Podman on Rocky Linux 

Ez a jegyzet egy alap Kubernetes tanulókörnyezet telepítését mutatja be Rocky Linux alatt.

A cél:

- Podman telepítése container runtime-ként
- Minikube telepítése
- Minikube indítása Podman driverrel
- Első Kubernetes ellenőrzés

---

## 1. Mi kell hozzá?

A Minikube egy helyi Kubernetes clustert indít.

Ehhez kell egy container runtime, ami ténylegesen futtatja a konténereket.

Rocky / RHEL vonalon erre jó választás a Podman.

Egyszerűen:

```text
Minikube = helyi Kubernetes cluster
Podman = konténerek futtatása
kubectl = Kubernetes parancssori kezelés
```

---

## 2. Rendszer frissítése

```bash
dnf update -y
```

---

## 3. Podman telepítése

```bash
dnf install -y podman
```

Ellenőrzés:

```bash
podman --version
```

Teszt:

```bash
podman info
```

Ha ezek működnek, akkor a Podman telepítve van.

---

## 4. Minikube letöltése

Fontos: a letöltést és az install parancsot külön sorban kell futtatni.

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
```

---

## 5. Minikube telepítése

```bash
install minikube-linux-amd64 /usr/local/bin/minikube
```

Takarítás:

```bash
rm -f minikube-linux-amd64
```

Ellenőrzés:

```bash
minikube version
```

---

## 6. Minikube indítása Podman driverrel

Ha rootként dolgozol:

```bash
minikube start --driver=podman --force
```

Normál userként:

```bash
minikube start --driver=podman
```

Megjegyzés: rootként a `--force` azért kellhet, mert a Minikube alapból nem szereti, ha rootként indítják.

---

## 7. Minikube állapot ellenőrzése

```bash
minikube status
```

Ha minden jó, ilyesmit kell látni:

```text
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

## 8. Kubernetes node ellenőrzése

Ha van `kubectl` telepítve:

```bash
kubectl get nodes
```

Ha nincs külön `kubectl`, akkor Minikube-on keresztül is használható:

```bash
minikube kubectl -- get nodes
```

Jó eredmény például:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane    1m    v1.xx.x
```

---

## 9. Podok ellenőrzése minden namespace-ben

```bash
minikube kubectl -- get pods -A
```

Ez megmutatja a Kubernetes rendszerpodokat is.

---

## 10. Első teszt deployment

```bash
minikube kubectl -- create deployment hello-nginx --image=nginx
```

Ellenőrzés:

```bash
minikube kubectl -- get deployments
minikube kubectl -- get pods
```

---

## 11. Service létrehozása

```bash
minikube kubectl -- expose deployment hello-nginx --type=NodePort --port=80
```

Ellenőrzés:

```bash
minikube kubectl -- get services
```

URL lekérése:

```bash
minikube service hello-nginx --url
```

---

## 12. Teszt törlése

```bash
minikube kubectl -- delete service hello-nginx
minikube kubectl -- delete deployment hello-nginx
```

Ellenőrzés:

```bash
minikube kubectl -- get all
```

---

## 13. Minikube leállítása

```bash
minikube stop
```

Újraindítás:

```bash
minikube start --driver=podman --force
```

Teljes törlés:

```bash
minikube delete
```

---

## 14. Gyakori hibák

### minikube: command not found

A Minikube nincs telepítve vagy nincs benne a PATH-ban.

Ellenőrzés:

```bash
which minikube
```

Telepítés után ennek ezt kell mutatnia:

```text
/usr/local/bin/minikube
```

---

### Could not resolve host: install

Ez akkor történik, ha a `curl` és az `install` parancs egy sorba lett írva.

Rossz:

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64 install minikube-linux-amd64 /usr/local/bin/minikube
```

Jó:

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
install minikube-linux-amd64 /usr/local/bin/minikube
```

---

### Root user hiba

Ha rootként indítod a Minikube-ot, kellhet a `--force` opció:

```bash
minikube start --driver=podman --force
```

---

## 15. Fontos ellenőrző parancsok

```bash
podman --version
podman info
minikube version
minikube status
minikube kubectl -- get nodes
minikube kubectl -- get pods -A
```

---

## Rövid összefoglaló

Teljes alap telepítési sorrend:

```bash
dnf update -y
dnf install -y podman

curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
install minikube-linux-amd64 /usr/local/bin/minikube
rm -f minikube-linux-amd64

minikube version
minikube start --driver=podman --force
minikube status
minikube kubectl -- get nodes
```

Ha a node `Ready` állapotú, akkor a helyi Kubernetes cluster működik.
