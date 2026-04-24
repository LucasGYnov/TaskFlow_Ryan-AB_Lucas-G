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