railgun-relayer-helm
=========

Railgun Relayer for Kubernetes. Not suitable for production (yet).

## TL;DR;

* Edit values.yaml and fill in your node values
* Create a Railgun namespace - `kubectl create ns railgun`
* Create a config.toml configmap - `kubectl create configmap railgun-config --from-file config.toml -n railgun`
* Create your secret credentials (TODO: Sealed secrets)
```
kubectl create secret generic credentials \
  --from-literal=DB_ENCRYPTION_KEY='0x0123456789abcdef0123456789' \
  --from-literal=MNEMONIC='one two three four five six seven eight nine ten eleven twelve'
```
* Create a TLS certificate named "tls-railgun-relayer" that is valid for your DNS name. Here's an example of how I created mine:
```
---
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: tls-railgun-relayer
  namespace: railgun
spec:
  secretName: tls-railgun-relayer
  commonName: relayer04.windowpa.in
  dnsNames:
  - relayer04.windowpa.in
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
```

Wait for a certificate to be issued:

```
wings:railgun-relayer-helm/ (main✗) $ kubectl get cert -n railgun
NAME                  READY   SECRET                AGE
tls-railgun-relayer   True    tls-railgun-relayer   10s
```

Enough preparation, let's do this:

```console
$ cd railgun-relayer-helm
$ helm install railgun-relayer . -f values.yaml -n railgun --create-namespace
```