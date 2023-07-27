
Cas d’usage pour lequel le système cible nécessite une authentification de l’utilisateur par **Pro Santé Connect** pour l’accès à ses données. 
Pour ce faire, le fournisseur de services redirige d'abord l'utilisateur vers Pro Santé Connect pour l'authentifier.

Cependant, si le fournisseur de services dispose déjà d'un `subject_token` valide délivré par Pro Santé Connect (voir le cas d'utilisation 1a), la première étape d'authentification n'est pas nécessaire.

Une fois l’utilisateur authentifié, le fournisseur de services utilise le protocole `OAuth 2.0` pour initier une requête au nom de l’utilisateur pour récupérer ses données auprès du service cible au sein du système cible.
Le système initiateur peut être une application web ou un client lourd.

Dans le cadre du **token exchange RFC 8693 [12]**, le jeton échangé contre l’`access_token` délivré par le serveur d’autorisation `OAuth 2.0` permet d’identifier l’utilisateur, il se nomme `subject_token`.

**Afin de garantir une cohérence avec les paramètres du RFC et dans le cas des API Pro Santé connectées : `subject_token` = `Access Token PSC`**
