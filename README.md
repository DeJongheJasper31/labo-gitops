# labo-gitops

GitOps-repo voor het labo Containers & Orchestration: kube-prometheus-stack
(Prometheus, Alertmanager, Grafana) installeren via Argo CD.

## Structuur

```
bootstrap/
  root.yaml                  # "app of apps": de enige manifest die je zelf toepast
apps/
  kube-prometheus-stack.yaml # Argo CD Application voor de Helm-chart (release "kps")
```

- `bootstrap/root.yaml` maakt de Application `root`. Die volgt de map `apps/`
  op de branch `main` van deze repo.
- Elke Application in `apps/` wordt zo automatisch door Argo CD aangemaakt en gesynchroniseerd.
- `kube-prometheus-stack` installeert chartversie 92.15.0 in de namespace `monitoring`,
  met `ServerSideApply=true` omdat de CRD's anders te groot zijn.

Een wijziging aan het cluster doe je met een commit en een push naar `main`, niet met `kubectl`.

## Commando's (PowerShell)

```powershell
# Eenmalig: bootstrap toepassen
kubectl apply -f bootstrap/root.yaml

# Status opvolgen
kubectl get applications -n argocd
kubectl get pods -n monitoring

# Grafana-wachtwoord (gebruiker: admin)
$b64 = kubectl get secret kps-grafana -n monitoring -o jsonpath="{.data.admin-password}"
[Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($b64))

# Grafana openen via http://localhost:3000
kubectl port-forward svc/kps-grafana -n monitoring 3000:80
```
