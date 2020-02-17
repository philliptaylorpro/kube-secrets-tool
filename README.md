
# secret-tool

A tiny python utility for working with Kubernetes secrets.

# examples

Given a small secrets file like this:

```text
```

You can easily base64 encode it like this:

```bash
cat mysecrets.yaml | secrets-tool -e
```

Then those secrets are ready to load into Kubernetes.

Secret decode can work with data straight out of Kubernetes
which means you never need to keep local secrets around. Here
are some examples:

```bash

# view unencrypted secrets directly from kube (to check they are correct)

kube get secret mysecret -o yaml | secrets-tool -d               # view decode data easily from kubernetes

kubectl get secret mysecret -o yaml > encrypted-locally          # save something from kubernetes locally
cat encrypted-locally | secrets-tool -d > decrypted-locally      # decode it
sed -i 's/ssl=true/ssl=false' decrypted-locally                  # work with the decoded version freely
cat decrypted-locally | secrets-tool -e                          # encode it easily again

# do it all inline

kubectl get secret mysecret -o yaml | secrets-tool -d | sed -i 's/ssl=true/ssl=false' | secrets-tool -e > /tmp/new-secrets-to-upload
```

Secrets tool only works with stdin and stdout. It only have two arguments `-d` and `-e` to encode/decode the data keys accordingly. It's
roughly 20 lines of python

```bash
secrets-tool < in.yaml > out.yaml
```
