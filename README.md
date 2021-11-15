# kustomize-examples


Bu repo [blog yazısındaki](https://blog.viadee.de/raus-aus-dem-yaml-chaos-parametrisierte-kubernetes-deployments-mit-kustomize) kodları içerir ve Türkçeleştirilmiş ve özetlenmiştir.  

```
.
├── base
│   ├── deployment.yaml
│   └── kustomization.yaml
├── LICENSE
├── overlays
│   ├── custom-metadata-labels
│   │   ├── kustomization.yaml
│   │   ├── metadataLabelTransformer.yaml
│   │   ├── README.md
│   │   └── service.yaml
│   ├── dev
│   │   ├── deployment_env.yaml
│   │   └── kustomization.yaml
│   └── prod
│       ├── assets
│       │   └── ca.pem
│       ├── deployment_env.yaml
│       ├── deployment_replicas.yaml
│       ├── deployment_volume.yaml
│       └── kustomization.yaml
└── README.md

6 directories, 15 files

```

./base: Orijinal Kubernetes YAMl dosyaları için bir dizin olarak.
./overlays/env: Bindirmelerin ilgili değişikliklerle toplandığı her ortam için ayrı alt klasör.

```yaml
# File: ./base/kustomization.yaml

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
 
resources:
  - deployment.yaml

```

```yaml
# File: ./overlays/prod/kustomization.yaml

apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
 
bases:
- ../../base

```

kustomizaztion.yaml dosyasında hangi dosyaların kullanılacağını söylüyoruz. 

```yaml
# File: ./overlays/prod/kustomization.yaml
 
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
 
bases:
- ../../base
 
patchesStrategicMerge:
- deployment_replicas.yaml
- deployment_env.yaml
```


prod içerisindeki deployment_* içerisindeki tüm ayarları çıktıda gösterebilirsiniz. 

```sh

kubectl kustomize ./overlays/prod

```

elle bazı değerleri uygulayabiliriz. bu durumda prod altındaki  kustomization.yaml dosyasına bu değer eklenir. 

```sh
cd ./overlays/prod

kustomize edit set image alpine=alpine:3.13

```

Oluşturduklarımızı nasıl deploy ederiz.

```sh
# 1. yöntem
kubectl kustomize ./overlays/prod | kubectl apply -f -
 
# 2. yöntem (-k parametresi kustomize'ı çağırarak build eder.)
kubectl apply -k ./overlays/prod

```


