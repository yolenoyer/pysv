pysv  v0.01
===========

Permet de gérer facilement des raccourcis vers des dossiers ou des fichiers.
Ces raccourcis sont utilisables via des commandes shell.

Des raccourcis peuvent être définis:
 * soit globalement (pour un utilisateur donné),
 * soit au sein d'un projet spécifique.


Fonctionnalités
---------------

* Déplacement rapide vers des raccourcis utilisateur

* Création, suppression, listing et recherche des raccourcis

* Possibilité de créer automatiquement des alias et des variables bash portant le nom (adaptable)
  de chaque raccourci, ce qui permet de faciliter l'accès à tous les répertoires usuels

* Génération de données json décrivant les raccourcis actuels

* La portée des raccourcis peut être:
 - soit globale (commande `cv`, cherchant le fichier `$HOME/.svinfo.global`)
 - soit locale à un projet (commande `sv`, cherchant le fichier `.svinfo` à la racine du projet)

* Expérimental: complétion bash personnalisée



INSTALLATION / CONFIGURATION
============================

En se plaçant à la racine du dossier d'installation, lancez en que root:

	# ./install.sh

Cela installera les fichiers suivants:

* l'exécutable `/usr/local/bin/pysv`
* le dossier `/usr/local/lib/pysv`


## Configuration dans .bashrc

Ajoutez la ligne suivante dans votre `~/.bashrc`:

	. /usr/local/lib/pysv/pysv_load --full

*NOTE*: pour profiter immédiatement des fonctionnalités, rechargez la session bash:

	$ exec bash



GUIDE RAPIDE D'UTILISATION:
===========================================

## Raccourcis globaux

	$ cd /usr/bin
	/usr/bin $ cva ub         # <== AJOUTER LE RACCOURCI 'ub' VERS '/usr/bin'
	/usr/bin $ cd /etc
	/etc $ cv ub              # <== ALLER VERS '/usr/bin' GRÂCE AU NOUVEAU RACCOURCI
	/usr/bin $

## Raccourcis par projet

	$ cd /myproject
	/myproject $ sv -I        # <== INITIALISATION DU PROJET POUR PYSV
	Fichier .svinfo initialisé dans le dossier courant.
	/myproject $ cd src
	/myproject/src $ sva s    # <== AJOUTER LE RACCOURCI 's' VERS '[PROJECT_ROOT]/src'
	Raccourci 's => src' ajouté.
	/myproject/src $ sv /     # <== ALLER À LA RACINE DU PROJET PYSV
	/myproject $ sv s         # <== ALLER VERS '[PROJECT_ROOT]/src' GRÂCE AU NOUVEAU RACCOURCI
	/myproject/src $



COMMANDE `cv` (raccourcis globaux)
===========================================

La commande `cv` permet de manipuler des raccourcis globaux.


## Ajouter un raccourci

Exemple: créer un racourci pour le répertoire global `/usr/bin`:

	~ $ cd /usr/bin
	/usr/bin $ cv -A <RACCOURCI>

Ou pour un répertoire donné:

	~ $ cv -A <RACCOURCI> /usr/bin

L'alias `cva` est également fourni:

	~ $ cva <RACCOURCI> /usr/bin


## Utiliser un raccourci existant

Pour se placer dans le répertoire spécifié par un raccourci, il suffit de lancer:

	~ $ cv <RACCOURCI>

Exemple: utiliser un raccourci `ub` => `/usr/bin` précédemment créé:

	/etc $ cv ub
	/usr/bin $

Avec la configuration `.bashrc` proposée ci-dessus, un alias rapide est aussi disponible,
commençant par une virgule et portant le nom du raccourci, ainsi qu'une variable locale à
la session:

	~ echo $ub     <-- une variable du même nom que le racourci porte sa valeur:
	/usr/bin
	~ $ ,ub        <-- alias équivalent à:  $ cd /usr/bin
	/usr/bin $


## Supprimer un raccourci

Pour supprimer un raccourci:

	~ $ cv -r <RACCOURCI>


## Lister les raccourcis

	~ $ cv -l


## Chercher un raccourci existant pour un répertoire donné

Il est possible de chercher les raccourcis disponibles pour un répertoire donné.

Par exemple, si un raccourci `ub` => `/usr/bin` a été défini, mais que l'on cherche à se rappeler du
nom de ce raccourci (`ub`), on peut faire:

	~ $ cv -s /usr/bin
	ub

Ou:

	~ $ cd /usr/bin
	/usr/bin $ cv -s
	ub



COMMANDE `sv` (raccourcis par projet)
===============================================

La commande `sv` permet de manipuler des raccourcis vers des répertoires locaux, au sein d'un projet
spécifique. Lorsque le répertoire courant se situe au sein d'un projet préalablement initialisé, il
est possible d'accéder à n'importe quel sous-répertoire à l'aide d'un raccourci.


## Prérequis: initialiser un fichier de raccourcis au sein d'un projet

Avant de pouvoir exploiter la commande `sv`, il faut d'abord créer un fichier `.svinfo` à la racine
du projet souhaité.

Pour cela, placez-vous à la racine du projet et lancez `sv -I`:

	~ $ cd ~/MyProject
	~/MyProject $ sv -I
	Fichier .svinfo initialisé dans le dossier courant.

Ou pour un répertoire donné en paramètre:

	~ $ sv -I ~/MyProject
	Fichier .svinfo initialisé dans le dossier /home/bob/MyProject.


## Ajouter un raccourci

Exemple: créer le racourci `s` pour le répertoire `src`:

	~ $ cd ~/MyProject/src
	~/MyProject/src $ sv -a s
	Raccourci 's => src' ajouté.

Ou plus rapidement, en utilisant l'alias `sva`:

	~ $ cd ~/MyProject/src
	~/MyProject/src $ sva s

Si on ne se situe pas dans le bon répertoire, on peut en donner un spécifique:

	~ $ sv -a s ~/MyProject/src
	Raccourci 's => src' ajouté.

La commande ci-dessus a trouvé le projet `~/MyProject` (précédemment initialisé) à la racine du dossier
cible `~/MyProject/src`, et elle a donc ajouté le raccourci `s` au projet concerné, bien que le dossier
courant (`~`) soit externe au projet.


## Aller vers un raccourci existant

Exemple: utiliser le raccourci `s` précédemment créé:

	~/MyProject/any/path $ sv s
	~/MyProject/src $

Avec la configuration `.bashrc` proposée ci-dessus, et une fois lancé `sv` au moins une fois:

 * un alias rapide est aussi disponible, commençant par une virgule et portant le nom du raccourci,
 * ainsi qu'une variable locale à la session, portant simplement le nom du raccourci

Exemple d'utilisation d'un alias rapide et d'une variable bash de raccourci:

	~/MyProject/any/path $ sv        <-- lancer sv au moins une fois auparavant, dans un répertoire du projet

	~/MyProject/any/path $ echo $s   <-- une variable du même nom que son raccourci contient son chemin
	~/MyProject/src

	~/MyProject/any/path $ ,s        <-- alias équivalent à:  $ cd ~/MyProject/src
	~/MyProject/src $


## Supprimer un raccourci

Exemple: supprimer le raccourci `s`:

	~/MyProject/any/path $ sv -r s
	Raccourci 's => /home/bob/MyProject/src' retiré.


## Lister les raccourcis

	~/MyProject $ sv -l


## Chercher un raccourci existant pour un répertoire donné

	~/MyProject $ sv -s [DIR]


## Afficher le nom du fichier .svinfo correspondant à un projet

	~/MyProject/sub/path $ sv -i
	/home/bob/MyProject/.svinfo



BASH: ALIAS ET VARIABLES DE RACCOURCIS
======================================

Lorsque cette fonctionnalité est activée dans la session bash active, chaque appel à `cv` ou `sv`
crée/actualise des alias et des variables bash correspondant à chaque raccourci défini,
automatiquement.

Les noms de ces alias et variables sont apparentés au nom de base du raccourci, mais ils sont
légèrement transformables (casse, préfixe, suffixe, cf. plus bas), afin de pouvoir contrôler les
éventuels conflits que cela pourrait générer avec d'autres alias/variables existant(e)s.

Par défaut:

* les variables générées portent le nom de leur raccourci
* les alias générés portent le nom de leur raccourci préfixé par une virgule

Par exemple, par défaut un raccourci global `ub` => `/usr/bin` génèrera l'équivalent des deux
commandes bash suivantes:

	ub="/usr/bin"
	alias ,ub="cd /usr/bin"

La variable `$ub` créée peut alors s'utiliser dans n'importe quelle commande bash, par exemple:

	~ $ du -hs $ub    # Affiche la taille totale du répertoire /usr/bin

De même l'alias `,ub` permet d'accéder très rapidement au répertoire `/usr/bin`:

	~ $ ,ub
	/usr/bin $


## (Alias et variables bash) Activer la fonctionnalité

Si les instructions d'installation ont été suivies à la lettre, la fonctionnalité est déjà
installée; en prime, les variables et alias globaux (gérés par `cv`) sont générés automatiquement au
lancement de bash.

Sinon, pour activer cette fonction il faut définir la variable `$PYSV_PARSE_CONF` à `1`, le plus
simple étant de la définir dans `~/.bashrc`:

	PYSV_PARSE_CONF=1

Il faut ensuite lancer au moins une fois `cv` (et/ou `sv` dans le cas d'un projet spécifique), avec ou
sans paramètre:

 - Si aucun paramètre n'est donné, cela génèrera les alias et variables pour la session bash active,
   sans rien faire d'autre. Exemple:
     
    ~ $ cv       # Génère les alias globaux et les variables globales... C'est tout.

 - Si des paramètres sont donnés, cela génèrera également les alias et variables, tout en effectuant
   les actions demandées. Exemple:

	~ $ cv ub    # (Re)génère les alias globaux et les variables globales, puis va dans /usr/bin.
	/usr/bin $

À chaque fois que `cv` ou `sv` est invoqué, les alias et variables correspondants sont (re)générés
dans la session bash active.


## (Alias et variables bash) Exemple d'utilisation basique

	~ $ cva ub /usr/bin
	Raccourci 'ub => /usr/bin' ajouté.

	~ $ echo $ub       # <-- La variable $ub est désormais accessible
	/usr/bin

	~ $ ,ub            # <-- L'alias ",ub" est désormais disponible
	/usr/bin $


## (Alias et variables bash) Configuration

Par défaut, `cv` et `sv` utilisent la configuration située dans `/usr/local/lib/pysv/default_config/*`
pour générer les noms de variable et d'alias.

Pour modifier cette configuration, lancez la commande suivante et éditez les fichiers fraîchement
copiés:

	$ cp -r /usr/local/lib/pysv/default_config ~/.pysv

Dans le répertoire créé, on trouve les fichiers:

- `cv.conf`, qui contrôle le comportement de `cv`
- `sv.conf`, qui contrôle le comportement de `sv`

Dans chaque fichier, les paramètres suivant peuvent être définis, ou omis afin de garder
leur valeur par défaut:

- `BashVarsEnable` (`yes`/`no`) : spécifie si des variables doivent être générées ou non
- `BashVarsTemplate` : définit le template à utiliser pour les noms de variable (cf. plus bas)
- `BashVarsRootName` : définit le nom d'une variable qui fournira le chemin racine du projet (utile
  seulement pour `sv`: pour `cv` le chemin serait toujours le dossier `$HOME`)
- `BashAliasesEnable` (`yes`/`no`) : spécifie si des alias doivent être générés ou non
- `BashAliasesTemplate` : définit le template à utiliser pour les noms d'alias (cf. plus bas)


## (Alias et variables bash) Templates

Les templates permettent de transformer le nom des variables et alias bash générés automatiquement,
par rapport au nom du raccourci auquel ils sont liés.
Les templates définissent en une seule ligne le suffixe, le préfixe et la casse à utiliser pour un nom
à transformer. Ils ont la syntaxe suivante:

	BashVarsTemplate = <PREFIX>(<FACTICE>)<SUFFIX>

 - `<PRÉFIXE>` est une chaîne à ajouter au début du nom du raccourci
 - `<FACTICE>` est un mot factice, qui permet de définir le type de casse du nom de raccourci à
   transformer, en donnant comme exemple sa propre casse:
	- s'il est tout en minuscules, les noms générés seront en minuscules
	- s'il est tout en majuscules, les noms générés seront en majuscules
	- si seulement la première lettre est en majuscules, les noms générés seront capitalisés
 - `<SUFFIXE>` est une chaîne à ajouter à la fin du nom de raccourci

Les parenthèses permettent de délimiter chaque partie.


## (Alias et variables bash) Exemples de templates

À définir dans `~/.pysv/cv.conf` ou `~/.pysv/sv.conf`:

	BashVarsTemplate = _(Rabbit)_

génèrera des noms de variable du type `$_Ub_`, `$_Foo_` ...

	BashAliasesTemplate = ,,(RABBIT),,

génèrera des noms d'alias du type `,,UB,,`, `,,FOO,,` ...


## (Alias et variables bash) Configuration par défaut = possibilité de "tuilage"!!

Par défaut et pour rester simple, la configuration des templates par défaut est la même pour
`sv.conf` et `cv.conf`:

	BashVarsTemplate = (name)        <-- Template pour les noms de variable
	BashAliasesTemplate = ,(name)    <-- Template pour les noms d'alias

Cela signifie qu'en utilisant les commandes `sv` *et* `cv` durant la même session bash, des
variables et alias de même nom peuvent se tuiler et interférer, dans le cas où ils ont été définis
globalement *ainsi* qu'au sein d'un projet.

Exemple montrant les problèmes de "tuilage":

	~ $ cva u /usr
	Raccourci 'u => /usr' ajouté.

	~ $ mkdir -p ~/MyProject/util
	~ $ cd ~/MyProject
	~/MyProject $ sv --init
	Fichier .svinfo initialisé dans le dossier courant.
	~/MyProject $ sva u util
	Raccourci 'u => /home/bob/MyProject/util' ajouté.

	~/MyProject $ sv; echo $u
	/home/bob/MyProject/util
	~/MyProject $ cv; echo $u
	/usr
	~/MyProject $ sv; echo $u
	/home/bob/MyProject/util
	~/MyProject $ cv; echo $u
	/usr

La solution à ce problème est simplement de personnaliser les fichiers `~/.pysv/sv.conf` et
`~/.pysv/cv.conf`, afin que leurs templates ne collisionnent pas.

