# Reproduce Linkerd rejecting AuthorizationPolicy with Gateway API HTTPRoute

This repository contains a minimal example to reproduce an issue with Linkerd rejecting an `AuthorizationPolicy` when using the Gateway API `HTTPRoute` instead of the Linkerd `HTTPRoute`.

## Steps to reproduce

TL;DR: `task setup reproduce`

1. Install Kind
  * `kind create cluster`
  * `kubectx kind-kind`
2. Install Linkerd
  * `linkerd install --crds | kubectl apply -f -`
  * `linkerd install | kubectl apply -f -`
  * `linkerd viz install | kubectl apply -f -`
3. Install the "booksapp" example
  * `kubectl create namespace booksapp`
  * `kubectl annotate namespace booksapp "linkerd.io/inject=enabled"`
  * `kubens booksapp`
  * `kubectl apply -f https://run.linkerd.io/booksapp.yml`
4. Apply the Kustomizations
  * `kubectl apply -k .`
  * `kubectl apply -k linkerd`
    * This uses the Linkerd version of HTTPRoute - accepted
  * `kubectl delete -k linkerd`
  * `kubectl apply -k gateway`
    * This uses the Gateway API version of HTTPRoute - rejected
  * `kubectl delete -k gateway`

## Result

```
httproute.gateway.networking.k8s.io/authors-get-route created
httproute.gateway.networking.k8s.io/authors-probe-route created
Error from server: error when creating "gateway": admission webhook "linkerd-policy-validator.linkerd.io" denied the request: invalid targetRef kind: HTTPRoute.gateway.networking.k8s.io
Error from server: error when creating "gateway": admission webhook "linkerd-policy-validator.linkerd.io" denied the request: invalid targetRef kind: HTTPRoute.gateway.networking.k8s.io
```
