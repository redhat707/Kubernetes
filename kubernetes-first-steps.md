# Kubernetes First Steps

Ez egy alap Kubernetes jegyzet Linux admin / DevOps irányú tanuláshoz.

## Mi az a Kubernetes?

A Kubernetes konténerek futtatására és kezelésére szolgáló rendszer.

Tipikus feladatai:

- konténerek indítása
- alkalmazások skálázása
- szolgáltatások elérése hálózaton keresztül
- hibás konténerek újraindítása
- konfigurációk kezelése
- deploymentek frissítése

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

## 2. kubectl alap parancsok

Cluster információ:

```bash
kubectl cluster-info
```

Node-ok listázása:

```bash
kubectl get nodes
```

Podok listázása:

```bash
kubectl get pods
```

Minden fontos objektum listázása:

```bash
kubectl get all
```

Namespace-ek listázása:

```bash
kubectl get namespaces
```

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
kubectl apply -f nginx-pod.yaml
```

Ellenőrzés:

```bash
kubectl get pods
kubectl describe pod nginx-pod
```

Törlés:

```bash
kubectl delete pod nginx-pod
```

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
kubectl apply -f nginx-deployment.yaml
```

Ellenőrzés:

```bash
kubectl get deployments
kubectl get pods
```

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
kubectl apply -f nginx-service.yaml
```

Ellenőrzés:

```bash
kubectl get services
kubectl describe service nginx-service
```

## 6. Logok és hibakeresés

Pod részletes információ:

```bash
kubectl describe pod nginx-pod
```

Pod logok:

```bash
kubectl logs nginx-pod
```

Deployment részletes információ:

```bash
kubectl describe deployment nginx-deployment
```

Események listázása:

```bash
kubectl get events
```

## 7. Objektumok törlése

Pod törlése:

```bash
kubectl delete pod nginx-pod
```

Deployment törlése:

```bash
kubectl delete deployment nginx-deployment
```

Service törlése:

```bash
kubectl delete service nginx-service
```

YAML fájl alapján törlés:

```bash
kubectl delete -f nginx-deployment.yaml
kubectl delete -f nginx-service.yaml
```

## 8. Fontos parancsok röviden

```bash
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get all
kubectl describe pod POD_NEVE
kubectl logs POD_NEVE
kubectl apply -f fajl.yaml
kubectl delete -f fajl.yaml
```

## 9. Rövid összefoglaló

Az első Kubernetes lépések:

1. `kubectl` használata
2. pod létrehozása
3. deployment létrehozása
4. service létrehozása
5. logok és hibák ellenőrzése
6. objektumok törlése

A Kubernetes tanulásnál a legfontosabb első körben megérteni:

- mi a Pod,
- mi a Deployment,
- mi a Service,
- hogyan működik a YAML alapú konfiguráció,
- hogyan kell hibát keresni `kubectl describe` és `kubectl logs` segítségével.
