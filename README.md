# VLAN
Notion de Réseau local Virtuel (Norme IEEE 802.1Q)

### Qu'est-ce qu'un VLAN?

```
Un VLAN est ni plus ni moins qu’un réseau virtuel, orchestré par un commutateur réseau tel qu’un Switch
de niveau 2(N2 ou N3). 
```

#### Avantages

```
-Segmentation logique du réseau afin de garantir des performances optimales.
-Gain en sécurité
-Réduction de la diffusion du traffic sur le réseau
```

### VLAN TAGGED
```
Un port de switch configuré en “tagged” signifie que l'équipement branché derrière est capable de
traiter les tags 802.1q et qu'il est configuré pour les traiter. Le port du switch envoie le trafic
sans avoir retiré le tag (identifiant) du VLAN.
```
![tag](https://user-images.githubusercontent.com/83721477/163605373-6f6bfccf-8390-455f-a4dd-eca7504d4e04.png)


1. Un hôte enverra une trame sans tag
2. La trame entre dans un port non taggué sur le commutateur 1, configuré avec le VLAN 10 dans ce cas. Le commutateur ajoute la balise VLAN à la trame
3. Le commutateur 1 détermine que le port 2 doit envoyer cette trame au commutateur 2. Il s'agit d'un port "TAGGED", il vérifie donc que le VLAN 10 est autorisé sur ce port. Si c'est le cas, il laisse le tag intacte et envoie la trame. Si le VLAN 10 n'est pas autorisé, il abandonne la trame
4. Le commutateur 2 reçoit la trame sur le port étiqueté 1. Ce commutateur détermine également si le VLAN 10 est autorisé sur ce port et le supprime si ce n'est pas le cas. Le commutateur 2 détermine que le port 2 doit envoyer la trame
5. Étant donné que le port 2 est un port UNTAGGED, il supprime le tag de la trame, puis l'envoie
6. L'hôte B reçoit la trame sans tag

### VLAN UNTAGGED
```
Un port de switch configuré en untagged signifie que la notion de VLAN est totalement transparente pour
l'équipement branché derrière. C'est-à-dire qu'il ignore son VLAN de rattachement. C'est le switch qui
utilise l'id VLAN associé pour son traitement interne pour la distribution des paquets sur ses ports.
Les paquets ne sont pas taggués 802.1q en entrée et en sortie des ports du switch configurés en untagged
```
![untag](https://user-images.githubusercontent.com/83721477/163424492-4481089d-c438-4632-b92e-6d4b55762bc3.png)

1. L'hôte A envoie le trafic au commutateur. Le trafic n'a pas de Tag VLAN
2. La trame est reçue sur le port 1 du commutateur. Il s'agit d'un port untagged, configuré avec l'ID VLAN 10. Le commutateur insère ensuite le Tag dans la trame
3. Le commutateur détermine que la trame doit être transférée hors du port 2. Il s'agit également d'un port untagged, donc le Tag est retirée de la trame
4. L'hôte B reçoit la trame non étiquetée normalement

<hr>

### PVID
Port Vlan ID. C'est le VLAN auquel un port est associé. La notion de PVID permet donc de savoir dans quel VLAN se situe ce port. Un port ne peut avoir qu'un seul PVID.
Par défaut, chaque port est associé au PVID 1, soit le VLAN 1

### VLAN natif
La notion de VLAN natif entre en compte dans le cas d'association de VLAN par port. Cela correspond au PVID sur port trunk. Ainsi lorsqu'une trame non taggué arrive sur un port trunk, elle sera associé à un VLAN en fonction du PVID du port. On dit alors que la trame est associé au VLAN natif du port.
Sur les ports de type trunk, le vlan natif permet d'assurer l'inter-opérabilté avec les équipements qui ne supportent pas le 802.1Q c'est pour ça que les trames ne sont pas étiquetées
<hr>

## Trame ethernet
| adresse MAC dst | adresse MAC source | Taille de la trame/EtherType	| Data | FCS |
| :-: | :-: | :-: | :-: | :-: |

## Trame ethernet modifiée
| adresse MAC dst | adresse MAC source | Tag (inséré) | Taille de la trame/EtherType	| Data | FCS (modifié) |
| :-: | :-: | :-: | :-: | :-: | :-: |

*_Le champ FCS est recalculé après l'insertion de la balise de VLAN._

## Contenu du champ "Tag:IEEE 802.1Q"
| TPID (16 bits) | TCI (16 bits) |
| :-: | :-: |

## Tag protocol identifier, TPID
```
Les 16 premiers bits sont utilisés pour identifier le protocole de la balise insérée. Dans le cas de la
balise 802.1Q la valeur de ce champ est fixée à 0x8100.
```

## Contenu du champ TCI (Tag control information)
| Priority (3 bits) | CFI (1 bit) | Vlan ID, VID (12 bits) |
| :-: | :-: | :-: |

## Priorité ( PCP : Priority Code Point )
Ce champ de 3 bits fait référence au standard IEEE 802.1p. Sur 3 bits on peut coder 8 niveaux de priorité de 0 à 7. Ces 8 niveaux sont utilisés pour fixer une priorité aux trames d'un VLAN relativement aux autres VLAN. La notion de priorité dans les VLAN (niveau 2) est indépendante des mécanismes de priorité IP (niveau 3).

## Canonical Format Identifier, CFI
Ce champ codé sur 1 bit assure la compatibilité entre les adresses MAC Ethernet et Token Ring. Un commutateur Ethernet fixera toujours cette valeur à 0. Si un port Ethernet reçoit une valeur 1 pour ce champ, alors la trame ne sera pas propagée puisqu'elle est destinée à un port «sans balise» (untagged port).

## VLAN Id, VID
Ce champ de 12 bits sert à identifier le virtual lan (VLAN) auquel appartient la trame. Il est possible de coder 4094 VLAN (de 1 à 4094) avec ce champ. La valeur "0" signifie qu'il n'y a pas de VLAN, et la valeur 4095 est réservée. Les valeurs 1002 à 1005 sont réservées pour des protocoles de niveau 2 différents d'Ethernet.

# Types de Port

## Port de type Access
```
Par défaut, le type de port est configuré en Access et sur le VLAN ayant pour identifiant le numéro 1.
Un port de type Access doit être configuré lorsqu’un seul VLAN transite par ce port et qu’il envoie du
trafic Untagged. On utilise ce type de configuration pour connecter un appareil qui n’est pas en mesure
d’identifier les paquets Ethernets. Un ordinateur par exemple.
```

## Port de type Hybrid
```
Un port de type Hybrid, autorise le trafic Untagged et Tagged de plusieurs VLANs. On peut utiliser ce
type de configuration lorsque l’on connecte un téléphone (Tagged sur l’ID du VLAN) en série avec un
ordinateur (Untagged sur l’ID du VLAN).
```
## Port de type Trunk
```
Un port de type Trunk transporte le trafic en VLAN Tagged à l’exception du PVID qui sera Untagged.
Ce type de port est utilisé pour interconnecter des switchs.
```

## Qu'est-ce qu'une SVI?
```
Une interface SVI est une interface virtuelle configurée dans un commutateur multicouche, comme
illustré dans la figure. Une interface SVI peut être créée pour chaque VLAN existant sur le 
commutateur. Une interface SVI est considérée comme virtuelle, car aucun port physique n'est dédié
à l'interface. Elle peut assurer les mêmes fonctions pour le VLAN qu'une interface de routeur et peut
être configurée à peu près de la même manière (adresse IP, listes de contrôle d'accès d'entrée et de
sortie, etc.). L'interface SVI du VLAN assure le traitement de couche 3 des paquets vers ou depuis tous
les ports de commutateur associés à ce VLAN.
```

## Créer une SVI:
```cisco
interface vlan <number>
```
