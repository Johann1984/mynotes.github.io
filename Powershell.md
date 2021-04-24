# Bienvenue sur notes Powershell

(1-Creation_Utilisateur_AD)[#1-CReation_Utilisateur_AD]

## 1-Création d’un utilisateur AD

Nous allons voir comment créer un utilisateur AD via POWERShell.
Il est toujours intéressant de savoir faire ce genre de chose, et surtout, le faire en masse, mais cela sera pour plus tard.

```
####### Definition des variables
$VILLE = 'Karakura'
 
####### Création Utilisateur dans l'AD  

New-ADUser -Name "Ichigo KUROSAKI" -GivenName "Ichigo" -Surname "KUROSAKI" -SamAccountName "Ichigo.KUROSAKI"`
 -UserPrincipalName "Ichigo.KUROSAKI@shinpo.fr" -Path "OU=Utilisateurs,OU=HOME,DC=shinpo,DC=fr" -City $VILLE `
 description 'Shinigami Remplacant' -AccountPassword(Read-Host -AsSecureString "Input Password") -Enabled $true
```

Explications

'#' : Permet de commenter ce qu’il y a après.
'$' : Permet de définir une variable
New-ADUser : CMDLET qui permet de créer un nouveau compte utilisateur
    -Name : Nom d’affichage
    -GivenName : Prénom
    -Surname : NOM
    -SamAccountName : Nom de session de l’utilisateur
    -UserPrincipalName : UPN de l’utilisateur
    -Path : Chemin où sera créé l’utilisateur dans l’AD
    -City : Ville
    -description : Description
    -AccountPassword : Création d’un mot de passe pour l’utilisateur fraîchement créé
    -Enabled $true : Active le compte (il est désactivé par défaut)

Et voilà, ce n’est pas sorcier.
Bien sûr, cette cmdlet a beaucoup plus d’options que je vous laisserai découvrir par vous-même.  
  
    
## 2-Création de Groupe de Sécurité  
  
Hello à vous,  
Oui, la création de groupe de sécurité être très facile graphiquement.  
Mais vu que nous pouvons le faire en PowerShell, pourquoi s’en priver ?  
La commande est simple.  
La voici :  
```
#####Création d'un Groupe de Sécurité
New-ADGroup -SamAccountName "SHINPOGroupeTEST" -DisplayName "[SHINPO] Groupe TEST" -GroupCategory Security -GroupScope Global -Name "[SHINPO] Groupe TEST" -Path "OU=Groupes,OU=HOME,DC=shinpo,DC=fr" -Description "Groupe de TEST"
```  
  
## 3-Création d'utilisateurs en masse  
Donc oui, créer un utilisateur, c’est bien !  
Mais en créer plusieurs d’un clic, c’est mieux !
  
Car oui, on peut nous demande 10, 20, 50 … comptes pour un projet particulier.
Des comptes de Services pour du SQL, IIS, Accès BDD, etc… et franchement, se les taper à la mano….ca serai fatiguant.

Je vous laisse le script :

```
#####Définition des variables
$USERS = Import-Csv -path 'C:\TEMP\CreateADUsers\UsersAD.csv' -Delimiter ','
$PATH = "OU=Utilisateurs,OU=HOME,DC=shinpo,DC=fr"

#####Création d'une boucle pour traiter toutes les lignes présentes du fichier
foreach ($USER in $USERS) 
    {
New-ADUser -Name $USER.Name  -GivenName $USER.Prenom -Surname $USER.Nom -SamAccountName $USER.SamAccountName -UserPrincipalName $USER.UPN -Path $PATH -description $USER.description -Enabled $true -AccountPassword (ConvertTo-SecureString "P@ssw0rd" -AsPlainText -force) -passThru
    }
```  
Après exécution de ce script, tous les utilisateurs présents dans le fichier CSV seront ajoutés dans notre AD.

Création du fichier CSV.
La 1ere ligne de votre fichier DOIT contenir les en-têtes (vous les choisissez, dès l’instant que vous vous retrouvez. Nous pourrions très bien mettre TATA, TOTO, TUTU et LaRéponseDJeanPierre) :
Nom,Prenom,SamAccountName,Name,UPN,Description
Tout est explicite sauf “Name”, j’aurai pu choisir ‘Nom d’affichage’ mais c’était très long à écrire, bref.

Voici le reste du fichier CSV.  
RORONOA,Zoro,Zoro.RORONOA,Zoro RORONOA,Zoro.RORONOA@shinpo.fr,Compte de Pirate
USHIWA,Sasuke,Sasuke.USHIWA,Sasuke USHIWA,Sasuke.USHIWA@shinpo.fr, Survivant des USHIWA  
VEGATA,VEGATA,VEGATA,VEGETA,VEGATA@shinpo.fr,Roi de la planete VEGETA  
HATATE,Kakashi,Kakashi.HATATE,Kakashi HATATE,Kakashi.HATATE@shinpo.fr,Utilisateur du Sharingan  
LIGHT,Yagami,Yagami.LIGHT,Yagami LIGHT,Yagami.LIGHT@shinpo.fr,Possesseur du DeathNote

Nous avons notre contenu (mais nous aurions pu aller plus loin en ajoutant des adresses Mails, postales, numéro de TEL, le service, l’organisation, et encore d’autres attributs à ne plus savoir qu’en faire.  
  
Notre fichier CSV terminé, passons aux explications du script PowerShell.

Commençons par la boucle FOREACH :  
```  
foreach ($USER in $USERS)
```  
FOREACH crée une variable pour chaque ligne (USER) du fichier CSV (USER)dufichierCSV(USERS).
Nous utiliserons juste cette nouvelle variable : $USER.”l’option que nous avons besoin”.
Nous pourrons voir que cette option fait référence aux colonnes de notre fichier CSV.
Donc, nous pouvons mettre ce que nous souhaitons dans ce fichier.

Je vous laisse découvrir les autres options, qui ne sont pas essentielles mais bien pratique.
Maintenant, vérifions que nos utilisateurs ont bien été créer avec notre script.
Nous avons 2 méthodes, aller dans l’AD et vérifier la présence de nos utilisateurs.
Ou juste faire une petit ligne de commande pour vérifier cela.  

```
$PATH = "OU=Utilisateurs,OU=HOME,DC=shinpo,DC=fr"
Get-ADUser -SearchBase $PATH -Filter * -Properties * | Select-Object SamAccountName,UserPrincipalName,description  
```