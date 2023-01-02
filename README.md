# WIK-DPS-TP04

---

- **Pour chaque étape il faut produire les fichiers YAML nécessaires et les conserver pour les communiquer lors du rendu.**

---

- **Créer un Pod pour déployer l'image registry.cluster.wik.cloud/public/echo (c'est l'image créée lors du TP WIK-DPS-TP02) et le tester sur minikube en local.**

- Créer un fichier `pod.yaml`

- Lancer les commandes ci dessous :
  `kubectl apply -f pod.yaml` --> _créer la ressource à partir de pod.yaml_
  `kubectl get pod` --> _voir si il est bien actif_

```
 NAME                    READY   STATUS        RESTARTS   AGE
publicecho              1/1     Running       0          6s
```

- **Pour le tester vous devez faire un port-forwarding entre le port du Pod sur lequel votre API écoute et un port sur votre hôte.**

`kubectl port-forward publicecho  8080:8080` --> _port-forwarding_

- Résultat `localhost/ping` :

```
{
   "host":"localhost:8080",
   "connection":"keep-alive",
   "sec-ch-ua":"\"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"108\", \"Google Chrome\";v=\"108\"",
   "sec-ch-ua-mobile":"?0",
   "sec-ch-ua-platform":"\"Windows\"",
   "upgrade-insecure-requests":"1",
   "user-agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36",
   "sec-purpose":"prefetch;prerender",
   "purpose":"prefetch",
   "accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
   "sec-fetch-site":"none",
   "sec-fetch-mode":"navigate",
   "sec-fetch-user":"?1",
   "sec-fetch-dest":"document",
   "accept-encoding":"gzip, deflate, br",
{
```

- Pour le delete : `kubectl delete pod publicecho`

---

- **Remplacer le Pod par un ReplicaSet afin de déployer 4 réplicas du Pod créé précédemment.**

- Créer un fichier `replicaset.yaml`

- Lancer les commandes ci dessous :
  `kubectl apply -f replicaset.yaml` --> _créer la ressource à partir de replicaset.yaml_
  `kubectl get pods` --> _voir si il est bien actif_

```
 NAME                   READY   STATUS              RESTARTS   AGE
part-2-service-8n5kk   0/1     ContainerCreating   0          5s
part-2-service-fpj9w   0/1     ContainerCreating   0          5s
part-2-service-lls8b   0/1     ContainerCreating   0          5s
part-2-service-xtl55   0/1     ContainerCreating   0          5s
```

`kubectl port-forward replicaset.apps/part-2-service 8080:8080` --> _port-forwarding_

- Résultat `localhost/ping` :

```
{
   "host":"localhost:8080",
   "connection":"keep-alive",
   "cache-control":"max-age=0",
   "sec-ch-ua":"\"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"108\", \"Google Chrome\";v=\"108\"",
   "sec-ch-ua-mobile":"?0",
   "sec-ch-ua-platform":"\"Windows\"",
   "upgrade-insecure-requests":"1",
   "user-agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36",
   "accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
   "sec-fetch-site":"none",
   "sec-fetch-mode":"navigate",
   "sec-fetch-user":"?1",
   "sec-fetch-dest":"document",
   "accept-encoding":"gzip, deflate, br",
   "accept-language":"fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7"
}
```

- Pour le delete : `kubectl delete replicaset.apps/part-2-service`

---

- **Remplacer le ReplicaSet par un Deployment afin de pouvoir définir une stratégie d'update en RollingRelease (50% en maxUnavailable).**

- Créer un fichier `deployment.yaml`

- Lancer les commandes ci dessous :
  `kubectl apply -f deployment.yaml` --> _créer la ressource à partir de deployment.yaml_
  `kubectl  get services` --> _voir si il est bien actif_

```
NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
deployment-service    ClusterIP   10.107.4.214   <none>        80/TCP    5m19s
```

`kubectl port-forward deployment.apps/part-3-deployment  8080:8080` --> _port-forwarding_

- Résultat `localhost/ping` :

```
{
   "host":"localhost:8080",
   "connection":"keep-alive",
   "cache-control":"max-age=0",
   "sec-ch-ua":"\"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"108\", \"Google Chrome\";v=\"108\"",
   "sec-ch-ua-mobile":"?0",
   "sec-ch-ua-platform":"\"Windows\"",
   "upgrade-insecure-requests":"1",
   "user-agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.0.0 Safari/537.36",
   "accept":"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9",
   "sec-fetch-site":"none",
   "sec-fetch-mode":"navigate",
   "sec-fetch-user":"?1",
   "sec-fetch-dest":"document",
   "accept-encoding":"gzip, deflate, br",
   "accept-language":"fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7"
}
```

- Pour le delete : `kubectl delete deployment.apps/part-3-deployment`

---

- **Créer un Service pour pouvoir communiquer avec les Pod du ReplicaSet créé précédemment, pour le tester vous devez faire un port-forwarding entre le port du Service sur lequel votre API écoute et un port sur votre hôte.**

```
apiVersion: v1
kind: Service
metadata:
  name: service-ingress-part-04
spec:
  selector:
    app: publicecho
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 8080
```

- **Activer le plugin ingress nginx sur minikube et créer un Ingress (nom de domaine au choix) pour communiquer avec le Service créé précédemment.**

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-ingress-part-04
spec:
  ingressClassName: nginx
  rules:
    - host: toto.xyz
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: service-ingress-part-04
                port:
                  number: 8080
```

- **Tester en ajoutant le nom de domaine choisi dans /etc/hosts afin de résoudre localement vers le service nginx de minikube.**

Il faut se rendre, sur Windows, dans `C:\Windows\System32\drivers\etc\host` :

`127.0.0.1 trotrorigolo.fr`

- Lancer les commandes ci dessous :
  `kubectl apply -f service.yaml` --> _créer la ressource à partir de service.yaml_

- Pour le delete : `kubectl delete deployment.apps/part-04`

- **Vous pouvez alors accéder via votre navigateur au service créé précédemment Faites une capture d'écran de la page sur votre navigateur avec le nom de domaine de votre choix pour votre service.**
