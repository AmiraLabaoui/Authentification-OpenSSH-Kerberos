# Authentification-OpenSSH-Kerberos
# Kerberos : qu’est-ce que c’est ?
Kerberos est un service d’authentification qui est utilisé sur les réseaux informatiques ouverts ou non sécurisés. Le protocole de sécurité authentifie les demandes de service entre deux hôtes de confiance, ou plus, via un réseau non approuvé comme Internet. Pour l’authentification d’applications client-serveur et la vérification de l’identité de l’utilisateur, il utilise le chiffrement cryptographique et un tiers de confiance (Trusted Third Party en anglais).
![Image](https://user-images.githubusercontent.com/100439283/236642493-8a987ff6-f16d-43e1-8a55-ec4c164c4bb0.jpg)   
Kerberos est un projet open source du consortium Kerberos. Ce protocole a été développé dans les années 1980 au Massachusetts Institute of Technology (MIT) pour le projet Athena. Aujourd’hui, Kerberos est la technologie d’autorisation standard de Microsoft Windows. L’implémentation de Kerberos est également possible pour d’autres systèmes d’exploitation comme Apple OS, FreBSD, UNIX et Linux. Microsoft a lancé sa propre version du protocole Kerberos avec Windows 2000. Par la suite, il s’est également développé comme protocole standard pour les sites Web et implémentations d’authentification unique (SSO) sur différentes plateformes.
 # Principe de fonctionnement
![Image](https://user-images.githubusercontent.com/100439283/236642692-0fb2146a-0e5d-4559-a2a1-47a5c754a83b.png)
 # Comment fonctionne l’authentification Kerberos ?
Pour comprendre comment se déroule exactement l’authentification Kerberos, nous allons, dans cette section, revenir sur ses principaux composants. Voici les principaux composants qui sont impliqués dans un processus Kerberos standard :
Client : le client agit pour le compte de l’utilisateur et initie la communication lors d’une demande de service.
Serveur hôte : il s’agit du serveur qui héberge le service auquel l’utilisateur veut accéder.
Serveur d’authentification (AS) : l’AS effectue l’authentification du client. Lorsque l’authentification est réussie, l’AS émet un ticket au client, le TGT (Ticket Granting Ticket). Ce ticket indique aux autres serveurs que le client a été authentifié.
Serveur d’émission de tickets(TGS) : le TGS est un serveur d’application qui émet des tickets de service.
Centre de distribution de clés (KDC) : le KDC est composé d’un serveur d’authentification (AS) et d’un serveur d’émission de tickets (TGS).
Voici comment se déroule le protocole illustré sur l’image :
1re étape : le client envoie une requête chiffrée au serveur d’authentification. Lorsque l’AS reçoit la requête, il cherche dans la base de données Kerberos le mot clé à l’aide de l’identifiant utilisateur. Si l’utilisateur a donné le bon mot clé, l’AS déchiffre la requête.
2e étape : après la vérification de l’utilisateur, l’AS émet un Ticket Granting Ticket (TGT) qui est envoyé au client.
3e étape : le client envoie ensuite le TGT à un serveur d’émission de tickets. Avec le TGT, le client « explique » la raison de l’accès au serveur hôte. Le TGS déchiffre le ticket avec une clé secrète que l’AS et le TGS ont en commun.
4e étape : si le TGT est valide, le TGS émet un ticket de service pour le client.
5e étape : le client envoie le ticket de service au serveur hôte. Celui-ci déchiffre le ticket avec la clé secrète qu’il partage avec le TGS.
6e étape : 
si la clé secrète correspond, le serveur hôte autorise l’accès du service au client. Le ticket de service détermine combien de temps l’utilisateur peut utiliser le service. Dès que l’autorisation d’accès au service expire, il peut renouveler le processus d’authentification Kerberos grâce à une [commande Kinit .
# Nom d'hôte et adresses IP
Nous aurons besoin de trois machines. Dans mon cas, j'utilise deux  machines Ubuntu ,kali  : ma machine physique et les deux  machines virtuelles à l'intérieur de VirtualBox. la machine Ubuntu  sera le KDC  et l'autre machines sera le serveur de service et le client.
Les machines virtuelles ont un adaptateur NAT par défaut, mais pour attribuer des adresses IP à ces machines, nous devrons ajouter manuellement un adaptateur hôte uniquement .
Commençons par créer une nouvelle machine virtuelle. Sous Fichier , accédez à Gestionnaire de réseau hôte... puis cliquez sur Créer .
Il est maintenant temps de connecter ces machines virtuelles à notre nouveau réseau virtuel. Allez dans les Paramètres de chacune des machines virtuelles et sous Réseau activez un second adaptateur : 
![Image](https://user-images.githubusercontent.com/100439283/236911267-b591a685-6994-4bdd-af9d-e48becd6417f.png)
Spécifiez le type de l'adaptateur ( adaptateur hôte uniquement ) et le réseau virtuel auquel se connecter (celui que nous venons de créer).
Nous pouvons vérifier les adresses IP des trois machines en exécutant hostname -Ichacune d'elles.
Dans mon cas :
L'adresse IP de la machine cliente (machine physique) est 192.168.112.129
L'adresse IP de la machine KDC (l'autre machine virtuelle) est 192.168.112.130 
![Image](https://user-images.githubusercontent.com/100439283/236911761-42a4bc89-18ad-414c-b59f-f5800a16c245.png)
## La communication SSH entre les machines : 
![Image](https://user-images.githubusercontent.com/100439283/236914285-ac770359-93c7-4fa1-96ea-490be8aaef01.png)
![Image](https://user-images.githubusercontent.com/100439283/236914286-f0466cd6-a4ba-4550-b949-207b83f9e3d1.png)
![Image](https://user-images.githubusercontent.com/100439283/236914287-0cfeb104-f9bb-4738-a4c9-78fb9208df19.png)
 ## vérification de la date : 
![Image](https://user-images.githubusercontent.com/100439283/236916630-c4810cea-68f0-4130-8fbc-66096a7739bc.png)
Maintenant que nous avons ajouté des adresses IP aux machines virtuelles, nous allons commencer par définir des noms d'hôte pour chaque machine :
Machine KDC
hostnamectl --static set-hostname kdc.universitecentrale.tn 
Ordinateur client
hostnamectl --static set-hostname client.universitecentrale.tn
![Image](https://user-images.githubusercontent.com/100439283/236912762-8aec3ea2-6829-412f-b245-7ea823efac37.png)
On peut vérifier le nom d'hôte d'une machine en exécutant la commande :hostname
![Image](https://user-images.githubusercontent.com/100439283/236913332-e2647589-1dbb-43a7-8d19-9d7f5370c2ab.png)
Maintenant, nous devrions définir les informations ci-dessous sur /etc/hosts pour les trois machines :
![Image](https://user-images.githubusercontent.com/100439283/236913408-f3d99cd6-91e7-40b5-9673-bbf719e9846a.png)
# Configuration de la machine du centre de distribution de clés
  $ sudo apt-get update
   $ sudo apt-get install krb5-kdc krb5-admin-server krb5-config
Lors de l'installation, il nous sera demandé la configuration de :
*le domaine  'UNIVERSITECENTRALE.TN' (doit être tout en majuscule )
![Image](https://user-images.githubusercontent.com/100439283/236915345-3bfb7adf-cd54-4d9e-b72e-84c5b17f4e7c.png)
*le serveur Kerberos : 'kdc.universitecentrale.tn'
![Image](https://user-images.githubusercontent.com/100439283/236915907-5558c041-d2c7-4c83-911f-5aa89e878755.png)
*le serveur d'administration : 'kdc.universitecentrale.tn'
![Image](https://user-images.githubusercontent.com/100439283/236916126-7ff72edd-e6c1-45b7-ba83-61f6b3af5874.png)
![Image](https://user-images.githubusercontent.com/100439283/236916127-c398b81d-9706-4ef5-9402-85d671a8d213.png)
### Le domaine est un réseau logique, similaire à un domaine, auquel appartiennent tous les utilisateurs et serveurs partageant la même base de données Kerberos. 
La clé principale de cette base de données KDC doit être définie une fois l'installation terminée :
sudo krb5_newrealm 
![Image](https://user-images.githubusercontent.com/100439283/236917104-faedc780-db8b-4755-aa65-4db023877eb6.png)
Les utilisateurs et les services d'un domaine sont définis comme un principal dans Kerberos. Ces principaux sont gérés par un utilisateur admin que nous devons créer manuellement : $ sudo kadmin.local ////kadmin.local:  add_principal root/admin
![Image](https://user-images.githubusercontent.com/100439283/236919519-bb31d3e1-5a1d-4fed-97dc-22274eebe5e8.png)
Nous pouvons vérifier si l'utilisateur root/admin a bien été créé en exécutant la commande : kadmin.local: list_principals. Nous devrions voir le principal 'root/ [admin@UNIVERSITECENTRALE.TN] listé avec les autres principaux par défaut.
![Image](https://user-images.githubusercontent.com/100439283/236919937-cba34915-e54e-4e65-9cb0-716644171ce4.png)
Ensuite, nous devons accorder tous les droits d'accès à la base de données Kerberos à l'administrateur principal root/admin à l'aide du fichier de configuration /etc/krb5kdc/kadm5.acl .
sudo vi /etc/krb5kdc/kadm5.acl
Dans ce fichier, nous devons ajouter la ligne suivante :
*/admin@UNIVERSITECENTRALE.TN    *
![Image](https://user-images.githubusercontent.com/100439283/236923548-3c2dc2c4-c5e0-43fb-a51d-fb95f8463197.png)
# Créer un principal pour le client  $ sudo kadmin.local    kadmin.local:  add_principal utilisateur
![Image](https://user-images.githubusercontent.com/100439283/236925113-09129c14-70dd-4e17-b9b6-e4467239605d.png)
# Créer un principal pour le serveur de service
![Image](https://user-images.githubusercontent.com/100439283/236925603-373de20b-97de-4420-b726-351b7a272e0b.png)
Nous pouvons vérifier la liste des principaux en exécutant la commande :kadmin.local: list_principals
![Image](https://user-images.githubusercontent.com/100439283/236926040-0281b9c2-1929-49a4-8022-8233a36ca858.png)
# Configuration de la machine du serveur de service
## Configuration de Kerberos
### Installation de packages
Voici les packages qui doivent être installés sur la machine du serveur de service :
$ sudo apt-get update
$ sudo apt-get install krb5-user libpam-krb5 libpam-ccreds
Lors de l'installation, il nous sera demandé la configuration de :
* le domaine : 'UNIVERSITECENTRALE.TN' (doit être tout en majuscule )
* le serveur Kerberos : 'kdc.universitecentrale.tn'
* le serveur d'administration : 'kdc.universitecentrale.tn'  
### Préparation du fichier keytab
Nous devons extraire le principal du service de la base de données des principaux KDC dans un fichier keytab.

Dans la machine KDC exécutez la commande suivante pour générer le fichier keytab dans le dossier courant :
   $ ktutil 
   ktutil:  add_entry -password -p 
   ktutil:  wkt kdm5.keytab
  ![Image](https://github.com/AmiraLabaoui/Authentification-OpenSSH-Kerberos/blob/main/c21%20creation%20de%20keytab.png)
   Envoyez le fichier keytab de la machine KDC à la machine du serveur de service :
Dans la machine serveur Postgres créez les répertoires suivants :

mkdir -p /home/kdm5/data

Dans la machine KDC, envoyez le fichier keytab au serveur kdm5 :

! Nous avons besoin d'avoir le paquet openssh-server installé sur le serveur de service : sudo apt-get install openssh-server.
![Image](https://github.com/AmiraLabaoui/Authentification-OpenSSH-Kerberos/blob/main/c16%20database%20varlib%20....png)

Vérifiez que le principal du service a bien été extrait de la base de données KDC :

Lister la liste de clés actuelle

ktutil:  list

Lire un keytab krb5 dans la liste de clés actuelle

ktutil:  read_kt pgsql/data/kdm5.keytab

Lister à nouveau la liste de clés actuelle

ktutil:  list

![Image](https://github.com/AmiraLabaoui/Authentification-OpenSSH-Kerberos/blob/main/c23%20kerberos%20et%20openssh.png)