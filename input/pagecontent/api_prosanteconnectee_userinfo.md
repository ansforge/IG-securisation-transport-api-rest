**Une fois l’utilisateur authentifié sur Pro Santé Connect, l’obtention du jeton `Userinfo` du PS est demandé par le service cible**.

### Cas d'une application web (navigateur) 

#### Cas où le fournisseur de services souhaite accéder au `userinfo`

La requête de récupération du jeton `UserInfo` [10] par le fournisseur de services est décrite dans la documentation PSC.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_web_userinfo_FS.svg%}</div>

#### Cas où le service cible souhaite accéder au userinfo

**Le service cible, par l’intermédiaire du serveur d’autorisation, souhaite récupérer le jeton userinfo PSC.**
*   Le jeton user info ne peut pas être récupéré directement auprès de PSC. 
    Dans ce cas c'est le serveur d'autorisation qui le récupère et qui le met en cache. 
*   Le serveur d’autorisation récupère le userinfo durant la durée de validité du `subject_token`.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_web_userinfo_API.svg%}</div>

### Cas d'une application client lourd 

#### Cas où le logiciel PS souhaite accéder au `userinfo` 

*   La requête de récupération du jeton `UserInfo` [10] par le fournisseur de services est décrite dans la documentation PSC.
*   Le jeton `userinfo` est récupéré par le proxy LPS.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_client_lourd_userinfo_FS.svg%}</div>

#### Cas où le service cible souhaite accéder au userinfo :
*   Le jeton user info ne peut pas être récupéré directement auprès de PSC. 
    Dans ce cas c'est le serveur d'autorisation qui le récupère et qui le met en cache. 
*   Le serveur d’autorisation récupère le userinfo durant la durée de validité du `subject_token`.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_client_lourd_userinfo_API.svg%}</div>