### Cas d'erreur 1a : un utilisateur déjà Pro Santé Connecté a besoin du Refresh Token PSC pour poursuivre sa navigation et accéder à une ressource

Dans ce cas, **l’utilisateur est déjà authentifié auprès de Pro Santé Connect et souhaite accéder à une ressource d’un service cible, mais son subject_token a expiré**. 
Afin de poursuivre son parcours, le fournisseur de services a besoin de récupérer un nouveau `subject_token` grâce à son `Refresh Token` PSC encore valide (<2 min).

Ici, lorsque le serveur d’autorisation effectue une introspection auprès de PSC pour vérifier la validité des jetons, PSC détecte que le `subject_token` n’est plus valide (**durée de validité limitée à 2 minutes** [4] ).
Le fournisseur de services effectue alors une demande de renouvellement de jetons auprès de Pro Santé Connect. Il utilise son `Refresh Token`  [4] encore valide (<30 minutes) afin d’obtenir un nouveau `subject_token`.

Une fois le `subject_token` récupéré, le serveur d’application relance une nouvelle cinématique OAuth 2.0.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_web_AT_expired.svg%}</div>

### Cas d'erreur 1b : La session PSC est expirée, l’utilisateur doit se réauthentifier sur la mire d’authentification

**L’utilisateur est déjà authentifié auprès de Pro Santé Connect et souhaite accéder à une ressource d’un service cible mais sa session a expiré (cookie de session > 4 heures ou 30 min d’inactivité) [4]**.
Par conséquent, son `subject_token` ainsi que son `Refresh Token` PSC sont expirés 
(`subject_token` > 2min et `Refresh Token` > 30min).


#### Cas d’usage 1b.1 : Le cookie de session de l’utilisateur est expiré et redirection vers mire authentification - Application Web

Dans la navigation, si l’utilisateur a besoin des ressources nécessitant d’être connecté à **PSC**, le fournisseur de services va vérifier que la session est valide auprès de **PSC** via **cookie de session**. 
Lors du contrôle du cookie de session par PSC, ce dernier ne valide pas son cookie de session car il a expiré (>4 heures). 
PSC renvoie une réponse d’erreur et l’URL de sa mire d’authentification afin que le fournisseur de services redirige l’utilisateur vers PSC pour s’authentifier à nouveau.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_web_session_expired.svg%}</div>

#### Cas d’usage 1b.2 : Tentative d’accès aux ressources avec un subject_token expiré, suivi d’une tentative de nouvellement avec Refresh Token PSC lui aussi expiré, puis redirection vers la mire authentification - Application Web 


Lors de l’introspection du `subject_token`, **PSC** ne le valide pas car il a expiré. PSC renvoie une réponse d’erreur au fournisseur de services qui tente de renouveler son `subject_token` avec son `Refresh Token` **PSC**.
Cependant, ce dernier est lui aussi expiré, amenant PSC à rediriger le PS vers l’URL de la mire d’authentification PSC pour s’authentifier de nouveau.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_web_session_RT_expired.svg%}</div>