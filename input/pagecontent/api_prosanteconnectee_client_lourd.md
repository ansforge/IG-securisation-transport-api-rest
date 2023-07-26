
**Pour faciliter la rédaction de cette section, on ne mentionnera que le client lourd mais cette section s’applique également au mobile**.
Un appel depuis un client lourd, comme un LPS ou un mobile, peut se faire via deux flux distincts : 

*   Flux CIBA : permet à l’utilisateur de s’authentifier via son device (ex : mobile) à Pro Santé Connect – uniquement pour les MIE compatibles Actuellement, Pro Santé Connect ne permet pas une authentification avec les cartes CPS en CIBA. La cible serait de le permettre. 
*   Navigateur extérieur (pop-up web) : ouverte en parallèle du client lourd (pop-up indépendante ou onglet du navigateur système), elle permet l’authentification de l’utilisateur sur Pro Santé Connect.

### Authentification par un flux CIBA 

<div style="text-align: center;">{%include diag_api_prosanteconnectee_client_lourd_CIBA.svg%}</div>

**Description du workflow :**

**1.	Le client lourd initie une demande de jeton applicatif/cookie web auprès du proxy LPS API**

Origine : Client lourd
Cible : Proxy LPS API



**2.	Le proxy LPS API génère le jeton applicatif et l’envoie au client lourd**

Origine : Proxy LPS API
Cible : Client lourd

Le client lourd conserve le jeton applicatif. 
Ce jeton applicatif sera utilisé pour chaque requête depuis le client lourd vers le proxy LPS API.


**3.	Depuis le client lourd, l’utilisateur souhaite accéder à des données protégées d’un service cible**

Origine : Client Lourd
Cible : Proxy LPS FS
Méthode : GET

Exemple : 

```sh
GET /application/ressource_privee
Host: applicationserver.appelant.org

```


**4.	Authentification CIBA client lourd suivant la documentation du flux CIBA [5]**

Valable pour les flux 4 à 8.


**9.	[UserInfo - OPTIONNEL] Le FS requête le userinfo avec un subject_token en entête au Userinfo Endpoint de PSC**

Origine : Proxy LPS FS
Cible : Userinfo Endpoint PSC
Méthode : GET

**10.	[UserInfo - OPTIONNEL] Le FS récupère le jeton UserInfo et renvoie une réponse de succès au client lourd**

Origine : Userinfo Endpoint PSC
Cible : Proxy LPS FS
 

**11.	[UserInfo - OPTIONNEL] Le Client Lourd récupère le jeton UserInfo**

Origine : Proxy LPS FS
Cible : Client Lourd

**12.	Le Proxy LPS FS fournit au proxy LPS API les tokens PSC qu’il a obtenus à l’issue de l’authentification de l’utilisateur**

Origine : Proxy LPS FS
Cible : Proxy LPS API

Les Tokens PSC sont : ID Token PSC, AT PSC et Refresh Token PSC

**13.	Le PS depuis, son client lourd initie, un accès API sur le proxy LPS API avec le jeton applicatif préalablement fourni par le proxy LPS API**

Origine : Client lourd
Cible : Proxy LPS API
Méthode : GET
 

**14.	Le proxy LPS API s’authentifie et demande un échange de jetons auprès du serveur d’autorisation avec le subject_token, certificat de structure et les scopes métiers.**

Origine : Proxy LPS API
Cible : Serveur d’autorisation
Méthode : POST

Lors de ce flux, le fournisseur de services effectue une connexion **mTLS** avec le **certificat d'authentification de la structure** auprès du serveur d’autorisation puis s’authentifie et envoie sa requête d’échange de jetons selon le protocole OAuth 2.0. 

Concernant l’échange de jetons, il suit la **RFC 8693 token exchange [12]**. 
Elle est nativement en voie d’adoption par les éditeurs du marché (méthode Delegation Token Exchange). 
Le fournisseur de services envoie dans les paramètres de sa requête le `subject_token` et les `scopes` métiers auxquels il souhaite accéder.
Ces derniers permettent d’authentifier la personne physique ou la personne morale appelante et de contrôler l’accès aux ressources requêtées.
Une fois ces éléments introspectés et validés, le serveur d’autorisation renvoie un `access_token` pour permettre au fournisseur de services d’accéder aux ressources du service cible.

<table width="633">
<tbody>
<tr>
<td width="132">
<p>Param&egrave;tres</p>
</td>
<td width="94">
<p>Obligatoire d&rsquo;apr&egrave;s la documentation officielle (OAuth 2.0)</p>
</td>
<td width="113">
<p>Obligatoire pour l&rsquo;interop&eacute;rabilit&eacute; des SIS</p>
</td>
<td width="293">
<p>Valeur</p>
</td>
</tr>
<tr>
<td width="132">
<p>grant_type</p>
</td>
<td width="94">
<p>Oui</p>
</td>
<td width="113">
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
<td width="94">
<p>Oui</p>
</td>
<td width="113">
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
<td width="94">
<p>Oui</p>
</td>
<td width="113">
<p>Oui</p>
</td>
<td width="293">
<p>urn:ietf:params:oauth:token-type:jwt<br>
<b>ou bien</b><br>urn:ietf:params:oauth:token-type:access_token</p>
</td>
</tr>
<tr>
<td width="132">
<p>scope</p>
</td>
<td width="94">
<p>Non</p>
</td>
<td width="113">
<p>Oui</p>
</td>
<td width="293">
<p>A d&eacute;terminer par les &eacute;quipes m&eacute;tiers</p>
<p>= scopes m&eacute;tier</p>
</td>
</tr>
</tbody>
</table>




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

 


L'authentification mTLS se réalise dans le cadre d’une **authentification OAuth 2.0 et suit la RFC 8705 [13]**. 
Le fournisseur de service établit une connexion TLS mutuelle avec le serveur d’autorisation en présentant son certificat client, préalablement aux échanges applicatifs. 

Le `client_secret` est remplacé par la **preuve de possession de la clé privée qui est assurée par le mTLS**. 
En effet, le mTLS permet d’authentifier le client par un `Client_ID_AS` + mTLS au lieu de s’authentifier avec son couple `client_secret/Client_ID_AS`. 
Autrement dit : le `client_secret` n’est pas nécessaire car l’authentification est portée par le **mTLS (certificat client IGC Santé)**.

Dans la pratique, il est envisagé d’utiliser un **certificat IGC Santé ORG AUTH_CLI** qui identifiera le fournisseur de services auprès du serveur d’autorisation en plus de son `Client_ID_AS` lors de la requête.

Le contenu du certificat, en particulier le **DN**, est accessible au serveur d’autorisation.
Le **DN sujet** du certificat client possède un attribut **OU (Organizational Unit)** qui contient :
*   L’identifiant de la structure porteuse du fournisseur de services (Structure_ID) issu du référentiel d’identité utilisé par l’IGC Santé
*   Un attribut **CN (Common Name)** qui a une valeur libre, et qui fera le lien entre la structure et le certificat.

L’attribut **CN** du certificat peut permettre de lier un certificat à la granularité d'un service d’une structure.

**Afin de garantir la bonne gestion des accès, le serveur d’autorisation aura la charge de faire le mapping entre le DN sujet du certificat TLS client et le `Client_ID_AS` (créé à l’enrôlement auprès du serveur d’autorisation).** 
Si la corrélation échoue alors il doit y avoir un retour d’erreur.


**15.	Le serveur d’autorisation vérifie auprès de PSC la validité du subject_token (introspection)**

Origine : Serveur d’autorisation
Cible : PSC
Méthode : POST 

L’introspection du `subject_token` est défini dans la documentation PSC [4]. 
La réponse de l’introspection est définie par une structure de code HTTP standard.
 

**16.	Le serveur d’autorisation contrôle l’accès aux ressources selon le `Client_ID_AS` et ses 'scopes` puis délivre l’access_token au proxy LPS/API**

Origine : Serveur d’autorisation
Cible : Proxy LPS API

Le contrôle d’accès basé sur le contrôle de l’association entre `scopes` et `Client_ID_AS` contenus dans le serveur d’autorisation. 

Une fois le contrôle d’accès effectué et validé, le serveur d’autorisation émet un `access_token` permettant d’accéder au service cible et le délivre au proxy LPS API appelant.

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
 
**La réponse doit être conforme à la section 2.2 de la RFC 8693 Token Exchange [12].**
 

**17.	Le proxy LPS API requête la ressource protégée auprès du service cible**

Origine :  Proxy LPS API
Cible : Service cible
Type d’appel :  Requête de ressources protégées
Méthode : GET

Le proxy LPS API envoie une requête auprès du service cible avec l’`access_token` contenu dans l’entête. 
Le service cible introspecte l’`access_token` du serveur d’autorisation afin vérifier sa validité et ses scopes.

**Exemple de requête** :
```sh 
GET /resource/v1 HTTP/1.1
Host: https://apifournisseurdedonnees.com/scope1/resourceX
Authorization: Bearer oab3thieWohyai0eoxibaequ0Owae9oh


```
 
### Authentification via un navigateur extérieur (pop-up web)

L’utilisateur souhaite accéder à des ressources chez un service cible à partir de son **client lourd**. 
Afin d’accéder à ces ressources, l’utilisateur s’authentifie auprès de **Pro Santé Connect via un navigateur extérieur (pop-up web)**.

L’authentification par une **application mobile est similaire à une authentification via un navigateur extérieur (pop-up web)**.

Une fois authentifié auprès de Pro Santé Connect, le fournisseur de services émet une requête auprès du serveur d’autorisation (`subject_token + scope`) pour accéder aux ressources protégées. 
Une fois le contrôle des `scopes` effectué, le serveur d’autorisation fournit un `access_token` au fournisseur de services (proxy API). 
Ce dernier l’utilise dans l’entête de sa requête auprès du service cible pour accéder aux données protégées.

**Le partage d’un jeton applicatif est nécessaire en amont de la cinématique d’authentification pour permettre l’authentification du client lourd lors de la requête de ce dernier auprès du serveur d’autorisation.**

Si le PS, depuis son client lourd, a besoin de préciser des champs liés à ses activités pour requêter des données auprès du service cible, un flux de récupération du jeton `userinfo` est exécuté.
Les données passent par une application web serveur avec des redirections sur des URLs web. 
Les données du jeton `userInfo` peuvent remonter jusqu'à l'application client lourd. 

Se référer à la partie <a href="api_prosanteconnectee_web.html">appel depuis une application web</a> pour la description du flux.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_client_lourd_web_pop_up.svg%}</div>

### Cas dérogatoires

**Les modalités de sécurisations nécessitant de faire des échanges client-serveur entre le client lourd et le proxy LPS API fait l’objet d’un protocole HTTP.**
**Si les systèmes cibles utilisent des protocoles hors HTTP (par exemple SMTP) cela s’inscrit hors cadre du présent volet transport de ce CI-SIS.**
 

