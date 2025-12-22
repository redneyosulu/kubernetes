
# Kubernetes Eğitimi – 2. Gün
## Detaylı Anlatım & Saha Rehberi (Field Manual)

> Hedef kitle: Eski sistemciler  
> Ortam: Online, browser yok, VM + minikube + curl  
> Amaç: Kubernetes’i çalıştıran değil, yöneten seviyeye gelmek

---

## 1. Günün Kısa Hatırlatması

Pod çalışır, Deployment yönetir, Service trafiği alır.
Bugün bu yapının ağı, diski, kaynağı ve güncellemesini ele alıyoruz.

Kontrol:
```bash
minikube status
kubectl get nodes
kubectl get pods -A
```

---

## Service & Networking

### Günlük Hayat Benzetmesi
- Pod: dükkân çalışanı
- Service: sabit telefon numarası
- ClusterIP: dahili hat
- NodePort: binanın dış kapısı

Pod IP’leri geçicidir, Service sabittir.

### ClusterIP
```bash
kubectl create deployment internal --image=nginx
kubectl expose deployment internal --type=ClusterIP --port=80
```

Cluster içinden test:
```bash
kubectl run curlpod --image=busybox -it --rm -- sh
wget -qO- http://internal | head
exit
```

### NodePort
```bash
kubectl expose deployment internal --type=NodePort --port=80 --name=internal-np
NODEPORT=$(kubectl get svc internal-np -o jsonpath='{.spec.ports[0].nodePort}')
curl http://localhost:$NODEPORT | head
```

---

## DNS & Ingress

Service isimleri DNS ile IP’ye çevrilir.

```bash
kubectl run dns --image=busybox -it --rm -- sh
nslookup internal
exit
```

Ingress = HTTP router.

```bash
minikube addons enable ingress
```

Ingress test (Host header):
```bash
curl -H "Host: demo.local" http://$(minikube ip)/one
```

---

## Storage & Stateful

Pod = kiracı  
PVC = depo

PVC Pod’dan bağımsızdır.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: web-pvc
spec:
  accessModes: [ReadWriteOnce]
  resources:
    requests:
      storage: 1Gi
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-pvc-pod
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: data
  volumes:
  - name: data
    persistentVolumeClaim:
      claimName: web-pvc
```

---

## Resource Management

request = rezervasyon  
limit = üst sınır

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

OOMKilled = koruma mekanizması.

---

## Scaling, Update & Rollback

```bash
kubectl scale deployment whoami --replicas=5
kubectl set args deployment/echo -- --text="v2"
kubectl rollout undo deployment/echo
```

---

## Debug

Altın sıra:
1. kubectl get
2. kubectl describe
3. kubectl logs

Örnek:
```bash
kubectl describe pod broken-pod
kubectl logs broken-pod
```

---

## Gün Sonu Mesajı

Bugün Kubernetes’i zorladık çünkü production zor.
Artık trafiği, veriyi, kaynağı ve hatayı yönetebiliyoruz.

---

## Akılda Kalacaklar
- Pod geçicidir
- Deployment yönetir
- Service sabit kapıdır
- Veri PVC’dedir
- Panik yok, debug var
