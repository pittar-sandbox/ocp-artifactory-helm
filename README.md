# JFrog Artifactory Helm Chart

## Andrew's Notes

1. Download latest tgz from here (I used 107.41.6): https://charts.jfrog.io/
2. Extract to dir of your choice
3. Made following edits to "values.yaml"

Lines 18-19:

```
  joinKeySecretName: joinkey-secret
  masterKeySecretName: masterkey-secret
```

Line 107-115:

```
serviceAccount:
  create: false
  ## The name of the ServiceAccount to use.
  ## If not set and create is true, a name is generated using the fullname template
  name: jfrog-anyuid
  ## Service Account annotations
  annotations: {}
  ## Explicitly mounts the API credentials for the Service Account
  automountServiceAccountToken: false
```

Lines 517-524:
```
  license:
    ## licenseKey is the license key in plain text. Use either this or the license.secret setting
    licenseKey:
    ## If artifactory.license.secret is passed, it will be mounted as
    ## ARTIFACTORY_HOME/etc/artifactory.lic and loaded at run time.
    secret: artifactory-cluster-license
    ## The dataKey should be the name of the secret data key created.
    dataKey: art.lic
```

Lines 1569-1571:
```
# Nginx
nginx:
  enabled: false
```

Lines 1829-1836:
```
  http:
    enabled: true
    externalPort: 80
    internalPort: 8080
  https:
    enabled: true
    externalPort: 443
    internalPort: 8443
```

Lines 1934-1938:
```
postgresql:
  enabled: true
  serviceAccount:
    enabled: true
    name: jfrog-anyuid
```

4. Create required RBAC (serviceaccount/rolebinding) and secrets (join/master key, license)

```
oc apply -f rbac.yaml -n jfrog-helm
oc apply -f secrets.yaml -n jfrog-helm
```

Note:  I created the joinkey, masterkey, and license secrets based on the process in the Artifcatory docs:
https://jfrog.com/knowledge-base/artifactory-installation-quick-start-guide-helm/


5. Install the helm chart:

```
helm install -f artifactory/values.yaml artifactory artifactory
```

6. Create a route:

```
oc apply -f route.yaml
```

