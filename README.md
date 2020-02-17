
# secret-tool

A tiny python utility for working with Kubernetes secrets.

# examples

Given a small secrets file like this:

```text
apiVersion: v1
kind: Secret
metadata:
  name: 'mysecret'
type: Opaque
data:
  some_password: 'whysostupid'
  hello: 'www.artifactory.com/some/repo'
  db_connection_string: 'mongo://user:pass@mongo?ssl=true'
```

You can easily base64 encode it like this:

```bash
secrets-tool -e < mysecret > mysecret-encoded
```

Then those secrets are ready to load into Kubernetes.

`secret-tool` can work with output straight out of kubectl
which means you never need to store local secrets around. Here
are some examples:

```bash

# view unencrypted secrets directly from kube (to check they are correct)

kube get secret mysecret -o yaml | secrets-tool -d                              # view secrets decoded (to check they are correct)

kubectl get secret mysecret -o yaml | secrets-tool -d > /tmp/mysecret.yaml      # store locally as decoded.
sed -i 's/ssl=true/ssl=false' /tmp/mysecret                                     # work with it in the decoded version freely
secrets-tool -e < /tmp/mysecret > /tmp/encoded-for-upload                       # encode it easily again

# do it all inline

kubectl get secret mysecret -o yaml | secrets-tool -d | sed -i 's/ssl=true/ssl=false' | secrets-tool -e | kubectl ...
```

`secret tool` only works with stdin and stdout. It only has two arguments `-d` and `-e` to encode/decode the data keys accordingly. Omitting this leaves if in the same format it's already in, making the invocation of the command equivalent to just pretty printing it. It's roughly 20 lines of Python.

```bash
secrets-tool < in.yaml > out.yaml
```
