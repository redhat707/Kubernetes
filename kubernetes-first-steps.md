# Kubernetes First Steps with Minikube kubectl

Ez egy alap Kubernetes jegyzet Linux admin / DevOps irányú tanuláshoz.

Ebben a jegyzetben a `kubectl` parancsot a Minikube-on keresztül használjuk:

```bash
minikube kubectl -- PARANCS
```

Példa:

```bash
minikube kubectl -- get pods
```

Ez akkor is működhet, ha a rendszerre külön nincs telepítve a `kubectl`.

---

## Mi az a Kubernetes?

A Kubernetes konténerek futtatására és kezelésére szolgáló rendszer.

Tipikus feladatai:

- konténerek indítása
- alkalmazások skálázása
- szolgáltatások elérése hálózaton keresztül
- hibás konténerek újraindítása
- konfigurációk kezelése
- deploymentek frissítése

---

## 1. Alapfogalmak

| Fogalom | Jelentés |
|---|---|
| Pod | A legkisebb futtatási egység Kubernetesben |
| Node | Egy gép, amin podok futnak |
| Cluster | Több node együtt |
| Deployment | Podok kezelése, frissítése, újraindítása |
| Service | Hálózati elérés biztosítása podokhoz |
| Namespace | Logikai elkülönítés a clusteren belül |
| ConfigMap | Konfigurációs adatok tárolása |
| Secret | Érzékeny adatok tárolása |
| kubectl | Parancssori eszköz Kubernetes kezeléséhez |
| Minikube | Helyi, tanulásra alkalmas Kubernetes cluster |

---

## 2. Minikube kubectl alap parancsok

Cluster információ:

```bash
minikube kubectl -- cluster-info
```

Node-ok listázása:

```bash
minikube kubectl -- get nodes
```

Podok listázása:

```bash
minikube kubectl -- get pods
```

Minden fontos objektum listázása:

```bash
minikube kubectl -- get all
```

Namespace-ek listázása:

```bash
minikube kubectl -- get namespaces
```

Minden namespace podjainak listázása:

```bash
minikube kubectl -- get pods -A
```

---

## 3. Első Pod létrehozása

Fájl neve:

```text
nginx-pod.yaml
```

Tartalom:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

Alkalmazás:

```bash
minikube kubectl -- apply -f nginx-pod.yaml
```

Fontos: a `--` jel kell a `minikube kubectl` után.

Rossz:

```bash
minikube kubectl apply -f nginx-pod.yaml
```

Jó:

```bash
minikube kubectl -- apply -f nginx-pod.yaml
```

Ellenőrzés:

```bash
minikube kubectl -- get pods
minikube kubectl -- describe pod nginx-pod
```

Törlés:

```bash
minikube kubectl -- delete pod nginx-pod
```

---

## 4. Deployment példa

Fájl neve:

```text
nginx-deployment.yaml
```

Tartalom:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          ports:
            - containerPort: 80
```

Alkalmazás:

```bash
minikube kubectl -- apply -f nginx-deployment.yaml
```

Ellenőrzés:

```bash
minikube kubectl -- get deployments
minikube kubectl -- get pods
```

Részletes ellenőrzés:

```bash
minikube kubectl -- describe deployment nginx-deployment
```

---

## 5. Service példa

A Service ad hálózati elérést a podokhoz.

Fájl neve:

```text
nginx-service.yaml
```

Tartalom:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

Alkalmazás:

```bash
minikube kubectl -- apply -f nginx-service.yaml
```

Ellenőrzés:

```bash
minikube kubectl -- get services
minikube kubectl -- describe service nginx-service
```

---

## 6. NodePort Service példa Minikube-hoz

Minikube alatt teszteléshez gyakran egyszerűbb `NodePort` service-t használni.

Fájl neve:

```text
nginx-nodeport-service.yaml
```

Tartalom:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: NodePort
```

Alkalmazás:

```bash
minikube kubectl -- apply -f nginx-nodeport-service.yaml
```

Ellenőrzés:

```bash
minikube kubectl -- get services
```

URL lekérése:

```bash
minikube service nginx-nodeport-service --url
```

---

## 7. Logok és hibakeresés

Pod részletes információ:

```bash
minikube kubectl -- describe pod nginx-pod
```

Pod logok:

```bash
minikube kubectl -- logs nginx-pod
```

Deployment részletes információ:

```bash
minikube kubectl -- describe deployment nginx-deployment
```

Események listázása:

```bash
minikube kubectl -- get events
```

Minden namespace eseményei:

```bash
minikube kubectl -- get events -A
```

---

## 8. Objektumok törlése

Pod törlése:

```bash
minikube kubectl -- delete pod nginx-pod
```

Deployment törlése:

```bash
minikube kubectl -- delete deployment nginx-deployment
```

Service törlése:

```bash
minikube kubectl -- delete service nginx-service
```

NodePort Service törlése:

```bash
minikube kubectl -- delete service nginx-nodeport-service
```

YAML fájl alapján törlés:

```bash
minikube kubectl -- delete -f nginx-deployment.yaml
minikube kubectl -- delete -f nginx-service.yaml
minikube kubectl -- delete -f nginx-nodeport-service.yaml
```

---

## 9. Fontos parancsok röviden

```bash
minikube kubectl -- get nodes
minikube kubectl -- get pods
minikube kubectl -- get deployments
minikube kubectl -- get services
minikube kubectl -- get all
minikube kubectl -- describe pod POD_NEVE
minikube kubectl -- logs POD_NEVE
minikube kubectl -- apply -f fajl.yaml
minikube kubectl -- delete -f fajl.yaml
```

---

## 10. Rövid összefoglaló

Az első Kubernetes lépések Minikube alatt:

1. Minikube cluster indítása
2. `minikube kubectl --` használata
3. Pod létrehozása
4. Deployment létrehozása
5. Service létrehozása
6. Logok és hibák ellenőrzése
7. Objektumok törlése

A Kubernetes tanulásnál a legfontosabb első körben megérteni:

- mi a Pod,
- mi a Deployment,
- mi a Service,
- hogyan működik a YAML alapú konfiguráció,
- hogyan kell hibát keresni `describe`, `logs` és `get events` segítségével.

---

## 11. Megjegyzés a parancsformáról

Ha külön telepítve van a `kubectl`, akkor használható így:

```bash
kubectl get pods
```

Ha nincs külön `kubectl`, akkor Minikube-on keresztül:

```bash
minikube kubectl -- get pods
```

Ebben a jegyzetben végig ezt a formát használjuk:

```bash
minikube kubectl -- ...
```
