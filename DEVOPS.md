# Documentation DevOps

## Partie Docker

### Choix 1 :

Taille image Backend avec 18 alpine :  187.16 mb
Nombre de vulnérabilité trivy : 50 

Taille image Backend avec 20 alpine :  199.43 mb
Nombre de vulnérabilité trivy : 14 

Taille image Backend avec 18 slim :  284.8 mb
Nombre de vulnérabilité trivy : 166 

Je pars donc sur le 20 alpine car pas beaucoup plus lourd et avec le moins de faille donc plus sécu

## Choix 2 : 

Je décide d'utiliser unless-stopped car tant que quelqu'un ne décide pas de le couper, il se relancera. Cela laisse un contrôle supplémentaire pour être sûr de ce qui est lancé ou non. 
Il redémarre si il y a un crash mais ne redémarre pas si il est arrêté manuellement. Donc à 3h, si le backend crash, il repart automatiquement.
Et si on veut garder un service stop, on peut décider qu'il soit stopper.

### Choix 3 : Stratégie de déploiement Kubernetes

**Pour l'environnement de Staging (maxUnavailable: 1 / maxSurge: 0) :**
Je pars sur cette stratégie car en staging, l'objectif est de valider le code tout en économisant les ressources du cluster. Cette configuration éteint d'abord l'ancien pod avant d'allumer le nouveau. Cela provoque une courte interruption de service, mais c'est un compromis totalement acceptable pour un environnement de test en interne où l'on veut éviter de surcharger la machine.

**Pour l'environnement de Production (maxUnavailable: 0 / maxSurge: 1) :**
Je décide d'utiliser cette stratégie car en production, la priorité absolue est la disponibilité pour les utilisateurs finaux (zéro downtime). Avec cette configuration, Kubernetes crée d'abord le nouveau pod et attend qu'il soit pleinement opérationnel avant de couper l'ancien. Le déploiement est légèrement plus lent et demande temporairement plus de ressources, mais cela garantit qu'il n'y a aucune coupure de service lors des mises à jour.

### Choix 4 : Nombre de replicas

**Pour l'environnement de Staging (1 replica) :**
Je pars sur 1 seul replica car en staging, le trafic est très faible (uniquement l'équipe pour valider les développements). Un seul pod suffit amplement pour vérifier que l'application fonctionne. De plus, cela s'aligne parfaitement avec mon Choix 3 (économie de ressources et acceptation d'une courte interruption) et évite de consommer de la RAM et du CPU inutilement sur le cluster de test.

**Pour l'environnement de Production (3 replicas minimum) :**
Je décide de mettre 3 replicas car en production, la haute disponibilité et la tolérance aux pannes sont indispensables. Si un pod plante, il y en a toujours deux autres pour absorber le trafic des utilisateurs instantanément. C'est aussi le nombre idéal pour que mon "Rolling Update" (zéro downtime) se déroule de manière fluide, sans surcharger les pods restants pendant que K8s remplace l'ancienne version. Enfin, c'est une excellente base avant que l'autoscaler (HPA) ne prenne le relais et n'ajoute des pods si la charge CPU augmente.