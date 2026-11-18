# Rendu — Atelier Kubernetes

> Complétez ce fichier au fil de l'atelier, puis faites un commit et un push (voir la section « Rendu » du README).

**Nom / Prénom :** PERESSONI Matthieu
**Groupe :** ING3-APP-RS2

---

## Exercice 1 — Cluster

1. Combien de nœuds affiche `kubectl get nodes` ? Quel est le rôle de chacun ?
   > En faisant kubectl get nodes j'observe qu'il y a 3 noeuds
2. Que montre `docker ps` ? Qu'est-ce qu'un « nœud » dans k3d, concrètement ?
   > docker ps affiche 4 conteneurs (k3d-lab-serverlb, k3d-lab-agent-1, k3d-lab-agent-0, k3d-lab-server-0). Dans kubernetes un noeud est un conteneur Docker qui fait tourner kubernetes chaque nœud Kubernetes correspond à un conteneur

## Exercice 2 — Premier déploiement

3. Quels objets Kubernetes ont été créés par `kubectl create deployment` ? (indice : `kubectl get all`)
   > kubectl create deployment a créé un deployment, un replicaset, un pod et un service 
4. Vous avez supprimé le pod `web-...` : que s'est-il passé, et pourquoi ?
   >Le pod a été supprimé et Kubernetes en a recréé un automatiquement avec un nouveau nom car le Deployment demande toujours 1 pod

## Exercice 3 — Modifier un conteneur en direct

5. Après suppression du pod, votre message « Bienvenue... » a-t-il survécu ? Pourquoi ?
   > Non le message n'a pas survécu. Le nouveau pod repart de l'image nginx d'origine car le système de fichiers d'un conteneur est éphémère.
6. Pourquoi le `port-forward` a-t-il cessé de fonctionner après la suppression du pod ?
   > le port-forward est attaché à un seul pod donc quand ce pod est supprimé le tunnel perd sa connexion et s'arrête

## Exercice 4 — Déclaratif

7. Quelle différence entre `kubectl create` et `kubectl apply` ?
   > kubectl create crée une ressource et échoue si elle existe déjà. kubectl apply lit un fichier yaml et crée ou met à jour la ressource pour qu'elle corresponde au fichier
8. À quoi sert le `selector` d'un Service ? Que se passe-t-il s'il ne correspond à aucun pod ?
   > le selector du service représente les pods ciblés via leurs labels. s'il ne correspond à aucun pod le service n'a aucun endpoint et le trafic n'arrive nulle part

## Exercice 5 — Scaling & mises à jour

9. Sur quels nœuds se trouvent vos 3 réplicas ? Collez la sortie de `kubectl get pods -l app=site -o wide`.
   ```
NAME                   READY   STATUS    RESTARTS   AGE     IP          NODE               NOMINATED NODE   READINESS GATES
site-f986cd679-4jt9s   1/1     Running   0          7m41s   10.42.2.7   k3d-lab-agent-1    <none>           <none>
site-f986cd679-wgkw9   1/1     Running   0          7m47s   10.42.0.9   k3d-lab-server-0   <none>           <none>
site-f986cd679-z8z46   1/1     Running   0          7m31s   10.42.1.7   k3d-lab-agent-0    <none>           <none>
   ```
10. Avec l'image `nginx:9.99`, quel statut avait le pod ? Quelle commande vous a donné la cause ?
    > le pod était en ImagePullBackOff (ErrImagePull) la commande kubectl describe pod m'a donné la cause : l'image nginx:9.99 n'existe pas
11. Pendant la panne, le site restait-il accessible ? Pourquoi ?
    > oui le site restait accessible. le rolling update garde les anciens pods tant que le nouveau n'est pas prêt, donc il n'y a pas de coupure

## Exercice 6 — Portainer & Mario

12. URL publique de votre Mario :
    > https://fictional-enigma-gx69qgggq763wx47-8585.app.github.dev/
13. Retrouvez le Deployment `mario` en ligne de commande. Portainer et `kubectl` parlent-ils au même composant ? Lequel ?
    > oui, le deployment mario est visible avec kubectl get deploy mario. il est identique aux autres avec seulement des labels io.portainer.* ajoutés par portainer. portainer et kubectl parlent au même composant l'api server du control plane (k3d-lab-server-0)

## Bonus (facultatif)

14. Bonus réalisés et remarques :
    >bonus a (ingress) : le site reste accessible sur le port 8081 même quand on supprime des pods
    bonus b (partiel) : readinessProbe et resources ajoutées au deployment, mais je n'ai pas réussi le test avec le chemin /nope
    bonus c (drain) : les pods de k3d-lab-agent-0 ont été recréés sur les autres nœuds et ne reviennent pas après uncordon. 
    bonus d (namespaces) : une seconde copie du site tourne dans le namespace prod.

---
Score : 18 / 18  (l'exercice 3 n'est pas vérifiable automatiquement)
**Score obtenu avec `./scripts/check.sh` :** ... / ...
