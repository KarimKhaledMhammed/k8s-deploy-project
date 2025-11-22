# Java App (Spring PetClinic) — Source + Manifests

Overview

This repository now contains `java-app-source-code/` Spring PetClinic and the lightweight Kubernetes manifest files in `java-app-manifest-files/`.

Top-level folders

- `java-app-source-code/` — Spring PetClinic Java project:
  - `pom.xml`, `mvnw`, `.mvn/` — Maven build files and wrapper
  - `src/` — Java source, resources, templates
  - `target/` — build output (do not commit)

- `java-app-manifest-files/` — lightweight Kubernetes manifests (Secrets, MySQL manifest, `java-app` deployment and HPA)

Goals of this README

- Describe the current repository state (Java source only + manifests in `java-app-manifest-files/`)
- Provide minimal steps to build, run, test, and deploy

Prerequisites

- Java 17+ (OpenJDK)
- Maven (or use the included `./mvnw` wrapper)
- `kubectl` and access to a Kubernetes cluster (Minikube / kind / cloud) — only if you plan to deploy manifests
- Git for source control

Quick step-by-step

1) Build the Java project (local JAR)

```bash
cd java-app-source-code
./mvnw -DskipTests package
```

2) Run locally (without Docker)

```bash
cd java-app-source-code
java -jar target/*.jar
# then open http://localhost:8080
```

3) Run unit tests

```bash
cd java-app-source-code
./mvnw test
```

4) Deploy manifests (only `java-app-manifest-files/`)

```bash
# start minikube if needed
minikube start --memory=4096 --cpus=2

# apply lightweight manifests (order matters for secrets/db)
kubectl apply -f java-app-manifest-files/secret.yml
kubectl apply -f java-app-manifest-files/mysql/mysql.yml
kubectl apply -f java-app-manifest-files/java-app/configmap.yml
kubectl apply -f java-app-manifest-files/java-app/java.yml
kubectl apply -f java-app-manifest-files/java-app/hpa.yml

# check status
kubectl get pods,svc,deploy,hpa
kubectl logs -l app=spring-petclinic
```

Port / access example

```bash
kubectl port-forward svc/<service-name> 8080:8080
# then open http://localhost:8080
```

Cleanup examples

```bash
kubectl delete -f java-app-manifest-files/java-app/java.yml
kubectl delete -f java-app-manifest-files/mysql/mysql.yml
kubectl delete -f java-app-manifest-files/secret.yml
```

Notes & recommendations

- `target/` contains build artifacts — do not commit it. If it was accidentally committed, remove it and add to `.gitignore`.
- Replace secrets before deploying to production. Use a secret manager for production credentials.


