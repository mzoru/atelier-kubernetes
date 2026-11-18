# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** UROZ Mattias
**Groupe :** RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > `kubectl get nodes` affiche 3 noeuds. `k3d-lab-server-0` agit ici commme un LoadBalancer, qui répartie la charge entre `k3d-lab-agent-0` et `k3d-lab-server-1`.
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > Un noeuf dans k3d est un simple container docker.

## Exercice 2 — Premier déploiement


3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > On peut voir que `kubectl create deployment` a créé un deployment, un service, un pod et un replicaset.
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   > Quand on a supprimé le pod `web-...` un autre pod a pris le relai. C'est normal et c'est l'objectif d'un replicaset, il va maintenir X pods en activité, ici on en a supprimé un donc il a dû en créer un nouveau. 

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non, notre message n'a pas survécu. Comme pour un container si le message n'est pas sur un volume persistant il ne survit pas à une suppression. Il faudrait le déclarer en dehors du pod à sa création.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > le `port-forward` est attaché à UN pod, on l'a supprimé, donc le tunnel cesse de fonctionner.

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > `create` c'est pour initialiser mais si on refait create on aura une erreur. Alors qu'`apply` permet de mettre à jour.
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > le `selector` permet d'indiquer au Service vers quel pod envoyer le trafic. Le service va se créer mais on ne pourra pas s'y connecter car il n'y a pas de pod au bout.

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
      NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE               NOMINATED NODE   READINESS GATES
      site-75775484b-ct9g9   1/1     Running   0          69s   10.42.1.6   k3d-lab-agent-1    <none>           <none>
      site-75775484b-nq9ls   1/1     Running   0          69s   10.42.2.5   k3d-lab-server-0   <none>           <none>
      site-75775484b-qrpsw   1/1     Running   0          15m   10.42.0.7   k3d-lab-agent-0    <none>           <none>
   ```
   Nos 3 replicas se trouve sur l'ensemble des noeuds, 1 par noeud.
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    > Le pod a un statut `ErrImagePull`. `kubectl get pods -l app=site` permet de trouver la raison du crash.
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > Non, le site n'était pas accessible. Le tunnel n'était plus ouvert, et on ne pouvait plus l'ouvrir car il est en attente des 3 replicas.

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://special-space-meme-64vxwv66w6fp6q-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    > Oui il parle au même composant, via `kube-apiserver`.

## Bonus (facultatif)

14. Bonus réalisés et remarques :
- A :
    > Le site reste accessible cette fois-ci.

- B : 
    > On reçoit simplement un code d'erreur 404.
   
- C : 
    > Les pods sont recréés mais cette fois-ci uniquement sur le master et le node k3d-lab-agent-1
    
    > Non, le replica est respecté, 3. Si un noeud tombe alors il pourra être recréé sur agent-0.

---

**Score obtenu avec `./scripts/check.sh` :** ... / ...
