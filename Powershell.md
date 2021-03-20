# Bienvenue sur notes Powershell

## [POWERSHELL] Création d’un utilisateur AD

Nous allons voir comment créer un utilisateur AD via POWERShell.
Il est toujours intéressant de savoir faire ce genre de chose, et surtout, le faire en masse, mais cela sera pour plus tard.

`
####### Definition des variables
$VILLE = 'Karakura'
 
####### Création Utilisateur dans l'AD  

New-ADUser -Name "Ichigo KUROSAKI" -GivenName "Ichigo" -Surname "KUROSAKI" -SamAccountName "Ichigo.KUROSAKI"`
 -UserPrincipalName "Ichigo.KUROSAKI@shinpo.fr" -Path "OU=Utilisateurs,OU=HOME,DC=shinpo,DC=fr" -City $VILLE `
 description 'Shinigami Remplacant' -AccountPassword(Read-Host -AsSecureString "Input Password") -Enabled $true
`

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