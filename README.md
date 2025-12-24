# Sealed Secrets Controller Setup and Usage

This guide provides the essential steps to install the Bitnami Sealed Secrets controller and use the `kubeseal` CLI for secure, offline secret management.

## 1. Controller Installation

This step installs the server-side component responsible for decrypting secrets in your Kubernetes cluster.

### 1.1 Deploy the Controller

Use `kubectl apply` to deploy the controller and its necessary Custom Resource Definitions (CRDs) into the `kube-system` namespace.

```bash
kubectl apply -f [https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.33.1/controller.yaml](https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.33.1/controller.yaml)
```

Note: Replace v0.33.1 with the latest stable version if necessary.

### 1.2 Verify the Controller Key

The controller automatically generates an asymmetric key pair. Verify that the Secret containing the private key has been created successfully.
```Bash
kubectl get secret -n kube-system -l [sealedsecrets.bitnami.com/sealed-secrets-key](https://sealedsecrets.bitnami.com/sealed-secrets-key)
```
## 2. Retrieve the Public Key Certificate

The public key is required to encrypt (seal) secrets locally. This file is safe to store locally or commit to Git.
### 2.1 Fetch the Certificate

For Linux/macOS:
```Bash
kubeseal --fetch-cert \
  --controller-name sealed-secrets-controller \
  --controller-namespace kube-system \
  > pub-cert.pem
```

For Windows PowerShell:
```shell
kubeseal --fetch-cert `
  --controller-name sealed-secrets-controller `
  --controller-namespace kube-system `
  > pub-cert.pem
```

## 3. Offline Secret Sealing

Once you have the pub-cert.pem file, you can seal secrets offline without any active cluster connection.
### 3.1 Sealing Command

Use the --cert flag to point to your saved public key file, and specify the input and output files.
```Bash
kubeseal --cert pub-cert.pem -f <input.yaml> -w <output.yaml>
```

# Database operators installation

## 1. Postgres (CNPG)

```bash
helm repo add cnpg https://cloudnative-pg.github.io/charts
helm upgrade --install cnpg cnpg/cloudnative-pg --namespace cnpg-system --create-namespace
```