
* L’utilisateur souhaite, via l’application web de son navigateur, accéder à des données protégées d’un service cible. 
* L’utilisateur s’authentifie premièrement auprès de Pro Santé Connect via le proxy LPS/API de son fournisseur de services.
* Une fois authentifié, le proxy LPS/API du fournisseur de services émet une requête auprès du serveur d'autorisation du service cible (`subject_token + scope`) pour accéder à ses ressources protégées.

Si le `subject_token` et les `scopes` sont jugés valides par le serveur d'autorisation, ce dernier fournit un `access_token` au proxy LPS/API du fournisseur de services.

Le proxy LPS/API du fournisseur de services, s'assure de la validité de `l'access_token` à sa réception puis envoie une requête auprès du service cible avec `l'access_token` contenu dans l’entête. Le service cible introspecte `l'access_token` auprès du serveur d’autorisation afin vérifier la validité de l’access_token et des scopes.<br>

Le proxy LPS/API du fournisseur de services utilise `l'access_token` et les `scopes` auprès du service cible pour accéder aux ressources protégées.


<div style="text-align: center;">{%include diag_api_prosanteconnectee_web_general.svg%}</div>

**Description du workflow :**

**1.	Depuis le navigateur, l’utilisateur souhaite accéder aux ressources protégées d’un service cible**

Origine : Navigateur
Cible : proxy LPS/API du Fournisseur de services
Méthode : GET

**Exemple de requête :**
 `GET /application/ressource_privee
Host: applicationserver.appelant.org
`


**2.	Le proxy LPS/API du fournisseur de services redirige le navigateur vers Pro Santé Connect pour authentifier l’utilisateur**

Origine : proxy LPS/API du Fournisseur de services
Cible : Navigateur

Le proxy LPS/API du fournisseur de services renvoie un code de redirection 302 avec l’URL de Pro Santé Connect

**Exemple de requête :** 
```sh
302 
Location: https://wallet.esw.esante.gouv.fr/auth?client_id=${client id}&redirect_uri= ${callback uri} &response_type=code&state=<random string>&scope= openid+scope_all&acr=eidas1&nonce=<random string> 
```
 

**3.	L’utilisateur accède à la mire d’authentification Pro Santé Connect**

L’authentification est décrite dans la documentation officielle de PSC [4]


**4.	Authentification de l’utilisateur sur Pro Santé Connect au moyen du MIE PSC**

L’authentification est décrite dans la documentation officielle de PSC [4]

**5.	Pro Santé Connect génère un authorization code PSC et redirige l’utilisateur vers le navigateur**

Origine : Pro Santé Connect
Cible : Navigateur

Une fois l’authentification terminée, PSC renvoie un code HTTP 302 pour demander au navigateur d’envoyer l’authorization code au FS.

**Exemple de requête :**

```sh
302 Location: https://applicationserver.appelant.fr/application/callback?code=3159339c2f1326f9fa128&state= <random_string>
```



Pro Santé Connect redirige l’utilisateur vers la callback URI avec en paramètre de la requête :

<table width="628">
<tbody>
<tr>
<td width="83">
<p>Param&egrave;tres</p>
</td>
<td width="141">
<p>Obligatoire d&rsquo;apr&egrave;s la documentation officielle PSC</p>
</td>
<td width="141">
<p>Obligatoire pour l&rsquo;interop&eacute;rabilit&eacute; des SIS</p>
</td>
<td width="262">
<p>Valeur</p>
</td>
</tr>
<tr>
<td width="83">
<p>Code</p>
</td>
<td width="141">
<p>Oui</p>
</td>
<td width="141">
<p>Oui</p>
</td>
<td width="262">
<p>&lsquo;code&rsquo;</p>
</td>
</tr>
<tr>
<td width="83">
<p>State</p>
</td>
<td width="141">
<p>Oui</p>
</td>
<td width="141">
<p>Oui</p>
</td>
<td width="262">
<p>Valeur envoy&eacute;e par le proxy LPS/API du Fournisseur de services lors de la demande d&rsquo;autorisation</p>
</td>
</tr>
</tbody>
</table>

**6.	Le navigateur envoie l’authorization code vers le proxy LPS/API du fournisseur de services**

Origine : Navigateur
Cible : proxy LPS/API du Fournisseur de services
Méthode : GET

**Exemple de requête :**
```sh
GET /application/callback ?code=3159339c2f1326f9fa128&state=<random string>
Host: applicationserver.appelant.fr
```
 

**7.	Le proxy LPS/API du fournisseur de services s’authentifie auprès de PSC avec l’authorization code PSC et son Client_ID_FS et certificat TLS**

Origine : proxy LPS/API du Fournisseur de services
Cible : Pro Santé Connect

L’authentification du proxy LPS/API est décrite dans la documentation [4]


**8.	Pro Santé Connect vérifie l’authorization code et génère un ID Token, un access token PSC et un Refresh Token (PSC)**

Origine : Pro Santé Connect
Cible : Proxy LPS/API du Fournisseur de services

Les détails de cette étape et des jetons délivrés sont dans la documentation [4]

**exemple de réponse :**
```xml
{
'access_token': ${access_token},
'token_type': 'Bearer',
'refresh_token': ${refresh_token},
‘expires_in': ${expiration},
 'id_token': ${id_token}
}
```
 


**9.	Le proxy LPS/API du fournisseur de services s’authentifie et demande un échange de jetons auprès du serveur d’autorisation avec un subject_token, certificat de structure et scopes métier**
NB : La section OAUTH2.0 Flow n’est pas dépendante du type de FS

Origine : Proxy LPS/API du Fournisseur de services 
Cible : Serveur d’autorisation
Méthode : POST

Lors de ce flux, le proxy LPS/API du fournisseur de services effectue une connexion mTLs avec le certificat de structure, auprès du serveur d’autorisation puis s’authentifie et envoie sa requête d’échange de jetons selon le protocole OAuth 2.0. 

Concernant l’échange de jetons, il suit le cadre définit par le Token Exchange dans la **RFC 8693 [12].** Elle est nativement **en voie d’adoption par les éditeurs du marché (méthode Delegation Token Exchange).**

Le proxy LPS/API du fournisseur de services envoie dans les paramètres de sa requête le subject_token et les scopes métiers auxquels il souhaite accéder.
Ces derniers permettent d’authentifier la personne physique ou la personne morale appelante et de contrôler l’accès aux ressources requêtées.
Une fois ces éléments introspectés et validés, **le serveur d’autorisation renvoie un access_token pour permettre au proxy LPS/API du fournisseur de services d’accéder aux ressources du service cible.**

<table width="633">
<tbody>
<tr>
<td width="132">
<p>Param&egrave;tres</p>
</td>
<td width="102">
<p>Obligatoire d&rsquo;apr&egrave;s la documentation officielle (OAuth 2.0)</p>
</td>
<td width="106">
<p>Obligatoire dans ce volet du CI-SIS</p>
</td>
<td width="293">
<p>Valeur</p>
</td>
</tr>
<tr>
<td width="132">
<p>grant_type</p>
</td>
<td width="102">
<p>Oui</p>
</td>
<td width="106">
<p>Oui</p>
</td>
<td width="293">
<p>urn:ietf:params:oauth:grant-type:token-exchange</p>
</td>
</tr>
<tr>
<td width="132">
<p>subject_token</p>
</td>
<td width="102">
<p>Oui</p>
</td>
<td width="106">
<p>Oui</p>
</td>
<td width="293">
<p>Access Token PSC</p>
</td>
</tr>
<tr>
<td width="132">
<p>subject_token_type</p>
</td>
<td width="102">
<p>Oui</p>
</td>
<td width="106">
<p>Oui</p>
</td>
<td width="293">
<p>urn:ietf:params:oauth:token-type:jwt<br></p>
<p><b>ou bien</b> urn:ietf:params:oauth:token-type:access_token</p>
</td>
</tr>
<tr>
<td width="132">
<p>Scope</p>
</td>
<td width="102">
<p>Non</p>
</td>
<td width="106">
<p>Oui</p>
</td>
<td width="293">
<p>A d&eacute;terminer par les &eacute;quipes m&eacute;tiers</p>
<p>= scopes m&eacute;tier</p>
</td>
</tr>
<tr>
<td width="132">
<p>cnf</p>
</td>
<td width="102">
<p>Non</p>
</td>
<td width="106">
<p>Oui</p>
</td>
<td width="293">
<p>Empreinte du certificat TLS client utilis&eacute; pour la requ&ecirc;te token exchange et pour la requ&ecirc;te m&eacute;tier</p>
</td>
</tr>
</tbody>
</table>

**Exemple d'attribut cnf :**

```json
{
"x5t#S256": "YTIzYThiYThjNWNmNTYyZWVkMjE3YmRhM2Y1OWM1MzA3NzAyOWQyMWY4YzY4MjZiYzU2OGY4ODU0ODZhN2RhMA=="
}
```

**Exemple de requête :**

```sh
 POST /as/token.oauth2 HTTP/1.1
 Host: systemecible.example.com
 Content-Type: application/x-www-form-urlencoded
&grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Atoken-exchange
&subject_token=accVkjcJyb4BWCxGsndESCJQbdFMogUC5PbRDqceLTC
&subject_token_type= urn%3Aietf%3Aparams%3Aoauth%3Atoken-type%3Aaccess_token
&scope=
``` 



Concernant la connexion mTLS, elle se réalise dans le cadre d’une authentification OAuth 2.0 et suit la **RFC 8705 [13].**  
Le proxy LPS/API du fournisseur de service établit une **connexion TLS mutuelle (mTLS)** avec le serveur d’autorisation en présentant son certificat client, préalablement aux échanges applicatifs. 

Le `client_secret` est remplacé par la preuve de possession de la clé privée qui est assurée par le mTLS. En effet, le mTLS permet d’authentifier le client par un `Client_ID_AS` + **mTLS** au lieu de s’authentifier avec son `client_secret`/`Client_ID_AS`. 
Dans ce cas le `client_secret` n’est pas nécessaire car l’authentification est portée par le mTLS (certificat client IGC Santé).

Dans la pratique, il est envisagé d’utiliser **Certificat IGC Santé ORG AUTH_CLI** qui identifiera le fournisseur de services auprès du serveur d’autorisation (`Client_ID_AS`) lors de la requête.

Le contenu du certificat, en particulier le **DN**, est accessible au serveur d’autorisation.
Le **DN** sujet du certificat client possède un attribut **OU (Organizational Unit)** qui lui contient :
*   L’identifiant de la structure porteuse du fournisseur de services (Structure_ID) issu du référentiel d’identité utilisé par l’IGC Santé
*   Un attribut **CN (Common Name)** qui a une valeur libre, et qui fera le lien entre la structure et le certificat

L’attribut CN du certificat peut permettre de lier un certificat à un service d’une structure.

Afin de garantir la cohérence et la bonne gestion des accès, le serveur d’autorisation aura la charge de faire le mapping entre le **DN sujet du certificat TLS client** et le `Client_ID_AS` (créé à l’enrôlement auprès du serveur d’autorisation). Si la corrélation échoue alors il doit y avoir un retour d’erreur.

**10.	Le serveur d’autorisation vérifie auprès de PSC la validité du `subject_token` (introspection)**

Origine : Serveur d’autorisation
Cible : Pro Santé Connect
Méthode : POST 

L’introspection du subject_token est définie dans la documentation PSC [8]. La réponse de l’introspection est définie par une structure de code HTTP standard.


**11.	Le serveur d’autorisation contrôle l’accès aux ressources selon le Client_ID_AS et les scopes du proxy LPS/API du fournisseur de services puis lui délivre l’access_token**

Origine : Serveur d’autorisation
Cible : Proxy LPS/API du Fournisseur de services

Le contrôle d’accès est basé sur le contrôle de l’association entre `scopes` et `Client_ID_AS` contenus dans le serveur d’autorisation. 

Une fois le contrôle d’accès effectué et validé, le serveur d’autorisation émet un `access_token` permettant d’accéder au service cible et le délivre au fournisseur de services appelant.


**Exemple de réponse :**
```xml
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-cache, no-store
{
"access_token":"eyJhbGciOiJFUzI1NiIsImtpZCI6IjllciJ9.eyJhdWQiOiJodHRwczovL2JhY2tlbmQuZXhhbXBsZS5jb20iLCJpc3MiOiJodHRwczovL2FzLmV.40y3ZgQedw6rxEQgU85AI9x3KmsPottVMLPIWvmDCMy5-kdXjwhw",
 "issued_token_type":
 	"urn:ietf:params:oauth:token-type:access_token",
 "token_type":"Bearer",
 "expires_in":60
}
```
 
La requête vers le serveur d'autorisation et la réponse doivent suivre la section 2.2 du RFC Token Exchange [12]
 

**12.	Le proxy LPS/API du fournisseur de services requête la ressource protégée auprès du service cible**

Origine : Proxy LPS/API du Fournisseur de services
Cible : Service cible
Type d'appel :  Requête de ressources protégées
Méthode : GET

Le proxy LPS/API du fournisseur de services envoie une requête auprès du service cible avec l’`access_token` contenu dans l’entête. Le service cible réalise les contrôles suivants :
* introspection de l’`access_token` auprès du serveur d’autorisation afin vérifier la validité de ce dernier,
* vérification des `scopes` associés à l’`access_token`,
* vérification du lien entre le certificat TLS client utilisé pour la requête au ressources protégées et l’`access_token` via l'attribut cnf selon la RFC 7519 [3].

**Exemple de requête :**

```sh
GET /resource/v1 HTTP/1.1
Host: 
Authorization: Bearer oab3thieWohyai0eoxibaequ0Owae9oh
```


Une fois l’introspection validée, le proxy LPS/API du fournisseur de services a accès aux ressources protégées du service cible.


