

```markdown
# 🚀 DevOps Academy Local - GitOps Pipeline

Este proyecto es un laboratorio completo de DevOps que implementa un flujo moderno basado en:

- Docker
- Kubernetes (Minikube)
- Helm
- GitHub Actions (CI)
- Argo CD (CD - GitOps)
- Prometheus + Grafana (Observabilidad)

---

# 🧠 Arquitectura General

El sistema sigue un modelo **GitOps**, donde Git es la fuente de verdad.

```

git push
↓
GitHub Actions (CI)
↓
Build + Push imagen Docker (GHCR)
↓
Update Helm values.yaml (tag)
↓
Commit automático
↓
Argo CD detecta cambio
↓
Deploy automático en Kubernetes

```

---

# 📦 Componentes

## 🔹 App (Node.js)
- API simple con Express
- Endpoint `/health`
- Endpoint `/metrics` (Prometheus)
- Tests + ESLint

---

## 🔹 Docker
La app se empaqueta como imagen:

```

ghcr.io/manubarrionuevo/devops-academy-app:<tag>

```

Cada build genera un tag único basado en el commit.

---

## 🔹 Kubernetes (Minikube)

Recursos principales:

- Deployment
- Service
- ConfigMap
- ServiceMonitor

---

## 🔹 Helm

Ubicación:

```

helm/devops-app/

````

Define cómo se despliega la app.

Ejemplo clave:

```yaml
image:
  repository: ghcr.io/manubarrionuevo/devops-academy-app
  tag: "abc1234"
````

👉 Este `tag` es el punto crítico del redeploy.

---

## 🔹 GitHub Actions (CI)

Archivo:

```
.github/workflows/ci.yml
```

Pipeline ejecuta:

1. Checkout
2. Setup Node
3. npm ci
4. Lint
5. Tests
6. Build Docker image
7. Push a GHCR
8. Update `values.yaml`
9. Commit automático

---

## 🔹 Argo CD (CD - GitOps)

Archivo:

```
k8s/argocd/application.yaml
```

Argo CD monitorea:

```
helm/devops-app
```

Y compara constantemente:

```
Git (estado deseado)
vs
Cluster (estado actual)
```

---

# 🔥 ¿Por qué Argo CD redeploya?

El redeploy ocurre cuando cambia esto:

```yaml
tag: "abc1234" → "def5678"
```

Ese cambio lo hace el CI.

Entonces:

1. Git cambia
2. Argo CD detecta diferencia
3. Renderiza Helm → YAML
4. Aplica cambios en Kubernetes
5. Kubernetes detecta cambio en Deployment
6. Ejecuta rolling update

---

# 🔄 Rolling Update (Kubernetes)

Cuando cambia la imagen:

* Se crea un nuevo ReplicaSet
* Se levantan nuevos Pods
* Los Pods viejos se eliminan progresivamente

👉 Sin downtime si las probes están bien configuradas

---

# 🧠 Conceptos clave

## GitOps

* Git es la fuente de verdad
* No se hacen deploys manuales
* El cluster se sincroniza automáticamente

---

## Helm

* Define cómo se despliega
* Usa templates + values.yaml

---

## CI vs CD

| Etapa | Responsable    |
| ----- | -------------- |
| CI    | GitHub Actions |
| CD    | Argo CD        |

---

## Importante

❌ CI NO deploya
❌ Helm NO deploya
✔ Argo CD deploya

---

# 📊 Observabilidad

Incluye:

* Prometheus (metrics scraping)
* Grafana (visualización)
* ServiceMonitor para integración automática

---

# 🧪 Cómo probar el flujo

```bash
git commit --allow-empty -m "test pipeline"
git push
```

Resultado esperado:

1. CI corre
2. Imagen nueva se construye
3. `values.yaml` se actualiza
4. Argo CD sincroniza
5. Pods se redeployan automáticamente

---

# ⚙️ Comandos útiles

```bash
kubectl get pods -n dev
kubectl rollout status deployment/devops-app -n dev
kubectl describe pod -n dev
```

---

# 🎯 Estado actual del proyecto

✔ CI funcionando
✔ Build + push imagen
✔ Git actualizado automáticamente
✔ Argo CD sincronizando
✔ Deploy automático
✔ Observabilidad integrada

---

# 🚀 Próximos pasos (mejoras)

* Seguridad (Trivy / Semgrep)
* Autoscaling (HPA)
* Ingress + dominio
* Multi-environment (dev / prod)
* Secrets management
* Alerting

---

# 🧠 Resumen final

Este proyecto implementa un flujo DevOps moderno donde:

👉 Git define el estado
👉 Argo CD lo ejecuta
👉 Kubernetes lo mantiene

---

# 👨‍💻 Autor

Manu Barrionuevo

````

---

# 🚀 Ahora pushealo

```bash
git add README.md
git commit -m "add devops gitops documentation"
git push
````

---

# 🧠 Nota final (importante)

Lo que escribiste en el README es exactamente esto:

👉 Argo CD redeploya porque cambia el **tag de la imagen en Helm (values.yaml)**
👉 Ese cambio lo hace el CI
👉 Eso dispara todo el flujo GitOps

---

