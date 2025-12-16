
# Kubernetes 1. Gün – Minikube Cheat Sheet (Genişletilmiş)

Bu doküman, eski sistemci bakış açısıyla, Ubuntu VM üzerinde Minikube kullanarak
Kubernetes’e ilk adımı atanlar için hazırlanmıştır.
Amaç ezber değil, doğru refleksi kazanmaktır.

---

## Büyük Resim (Fundamentals)

### Kubernetes Nedir?
Kubernetes, container’lı uygulamaların:
- Çalıştırılmasını
- İzlenmesini
- Bozulduğunda düzeltilmesini
- Yük arttığında çoğaltılmasını

otomatikleştiren bir sistemdir.

Kubernetes sana nasıl yapacağını değil, ne istediğini sorar.

---

### Desired State (İstenen Durum)
Kubernetes’in kalbi burasıdır.

Örnek:
- “Bu uygulamadan 3 tane çalışsın” dersin
- Kubernetes sürekli bakar:
  - 3 tane var mı?
  - Biri düştü mü?
  - Eksikse tamamlar

---

### Temel Kavramlar
Cluster = Site
Node = Apartman
Pod = Daire
Container = Dairenin içindeki eşyalar
Service = Apartman kapısı
kubectl = Uzaktan kumanda

Kubernetes container değil, Pod yönetir.

---

## Debug Refleksi
Önce bak, sonra anlat, en son müdahale et.

1. kubectl get
2. kubectl describe
3. kubectl logs

---

## Sistem Kontrol
```bash
lsb_release -a
free -h
df -h
```

---

## Docker Kontrol
```bash
docker ps
```
Permission hatası:
```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## Minikube
```bash
minikube start --driver=docker --cpus=2 --memory=4096
minikube status
```

---

## Kubernetes Kontrol
```bash
kubectl get nodes
kubectl get pods -A
```

---

## Pod Debug
```bash
kubectl describe pod <pod-adi>
kubectl logs <pod-adi>
```

---

## İlk Deployment
```bash
kubectl create deployment web --image=nginx
kubectl expose deployment web --type=NodePort --port=80
minikube service web --url
```

---



