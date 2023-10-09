1. Create a namespace for ArgoCD:

kubectl create namespace argocd

2. Install the ArgoCD Helm chart:
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm upgrade --install argocd argo/argo-cd --namespace argocd --create-namespace

❯ helm install argocd  argo/argo-cd -n argocd
NAME: argocd
LAST DEPLOYED: Thu Sep 21 19:17:47 2023
NAMESPACE: argocd
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
In order to access the server UI you have the following options:

1. kubectl port-forward service/argocd-server -n argocd 8080:443

    and then open the browser on http://localhost:8080 and accept the certificate

2. enable ingress in the values file `server.ingress.enabled` and either
      - Add the annotation for ssl passthrough: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-1-ssl-passthrough
      - Set the `configs.params."server.insecure"` in the values file and terminate SSL at your ingress: https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#option-2-multiple-ingress-objects-and-hosts


After reaching the UI the first time you can login with username: admin and the random password generated during the installation. You can find the password by running:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

❯ F7Ko3CsRzr766U53



kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

argoPass=$(kubectl -n argocd get secret argocd-initial-admin-secret \
    -o jsonpath="{.data.password}" | base64 -d)

argocd login --insecure --grpc-web k3s_master:32761 --username admin \
    --password $argoPass

0cWDQ69IqE59zknr

(You should delete the initial secret afterwards as suggested by the Getting Started Guide: https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)

3. Verify that ArgoCD is running:

kubectl get pods -n argocd

If everything is working correctly, we should see the ArgoCD server and associated pods listed.

4. Get the ArgoCD server password:

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

This command will output the password for the initial ArgoCD administrator account.

5. Open the ArgoCD UI:

kubectl -n argocd port-forward svc/argocd-server -n argocd 8080:443

This command forwards port 8080 on the local machine to port 443 on the ArgoCD server. We can then access the ArgoCD UI by opening a web browser and navigating to https://localhost:8080(opens in a new tab). When prompted, enter the username admin and the password we obtained in the previous step.