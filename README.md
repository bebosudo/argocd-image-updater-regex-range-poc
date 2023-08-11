# argocd-image-updater-regex-range-poc
Repro to test that regex with ranges aren't supported by ArgoCD Image Updater.
Reported at https://github.com/argoproj-labs/argocd-image-updater/issues/606.

Clone this repo and install the sample app in your cluster already running ArgoCD and ArgoCD Image Updater with:

```console
git clone https://github.com/bebosudo/argocd-image-updater-regex-range-poc.git
cd argocd-image-updater-regex-range-poc/
kubectl apply -f app/application.yaml
```

Wait a couple of minutes for ArgoCD Image Updater to pick up changes, you can keep an eye on it with:
```console
kubectl -n argocd logs deploy/argocd-image-updater --tail 20 -f
```

Notice how the new tag applied by ArgoCD Image Updater is `shorttag123` instead of the more recent `amuchlongertagwithnumbers1234`:
```console
kubectl -n argocd-regex-range-poc describe deploy/poc-deploy
```

(the former is a `nginx:stable` image, while the latter is a `nginx:alpine` image, just to make sure their hashes are different)

What's curious is that the utility `argocd-image-updater test` correctly parses the latest image with the same regex defined in the ArgoCD app manifest:
```console
kubectl -n argocd exec -it deploy/argocd-image-updater -- argocd-image-updater test docker.io/bebosudo/argocd-image-updater-regex-range-poc --update-strategy latest --allow-tags 'regexp:^[a-z\d]{11,}$'
```
