#	(REST, OAUTH 2.0 ET OIDC)

##	Les API REST
###	Généralités sur le concept d’API REST et bonnes pratiques
Une API REST est une interface de programmation qui suit les principes de l'architecture REST.
Elle permet aux clients (des applications web ou mobiles) de communiquer avec un serveur en utilisant des requêtes HTTP. Les ressources cibles sont identifiées par des URLs.
Dans une API REST, les opérations CRUD (Create, Read, Update, Delete) sont implémentées via les méthodes dans une requête : POST, GET, PUT, et DELETE respectivement. 
Les réponses à ces requêtes sont retournées en format JSON.
<p style="text-align:center">
<object data="exemple_req_REST_bearer.png" type="image/png"></object>
</p>


###	Liaison avec le protocole de transport ou binding
Le protocole HTTP1.1 encapsulé dans une connexion sécurisée TLS doit être utilisé.



##	Le standard OpenID Connect pour la gestion de l’authentification des applications Pro Santé Connectées 
Pro Santé Connect est le fournisseur d’identité de la santé pour les acteurs de santé professionnels en France. Il s’agit d’un service basé sur le protocole standard OpenID Connect. Il permet aux professionnels de santé de s’identifier de manière simple, sécurisée et unifiée. Ils se connectent aux services numériques en santé, en passant de l’un à l’autre de manière fluide.

Une application Pro Santé Connectée est une application web ou client lourd qui permet aux professionnels (PS) de s’authentifier à l’aide d’un MIE PSC compatible. Ces applications sont généralement utilisées par des établissements de santé/e-santé pour la gestion de leurs dossiers médicaux.-

Pour accéder au service d’authentification de PSC permettant d’accéder à des API Pro Santé connectées, deux cinématiques sont disponibles :

*	Via le flux authorization code [4] 
*	Via le flux Client Initiated Backchannel Authentication (CIBA) [5] 

Description du protocole OpenID Connect

OpenID Connect (OIDC) est un protocole d'authentification qui s'appuie sur OAuth 2.0 pour fournir une identification sécurisée et basée sur des jetons pour les applications Web et mobiles. 

OIDC fournit une couche d'authentification supplémentaire au-dessus d’OAuth 2.0, qui permet aux clients d'obtenir des informations d'identification et de profil sur les utilisateurs en utilisant des ID tokens.

Ce protocole utilise des jetons JSON Web Tokens (JWT) pour échanger des informations d'identification entre l'application cliente, le fournisseur d'identité et le service/système cible.

La documentation technique Pro Santé Connecté [4] fournit des détails sur le fonctionnement du protocole OIDC au sein de PSC. 
 


##	Le standard OAuth 2.0 pour la gestion des autorisations et accès
OAuth 2.0 est un protocole standard d'autorisation qui permet à une application tierce d'accéder à des ressources protégées. Le protocole OAuth 2.0 définit des étapes à suivre pour obtenir et utiliser un jeton d'accès (access_token). Ce jeton est délivré par le serveur d’autorisation et permet d’accéder aux ressources d’une application protégée. Les services de e-santé utilisent ce standard OAuth 2.0 pour la gestion des autorisations et des accès.


###	Description du serveur d’autorisation du système cible

OAuth 2.0 est un protocole d'autorisation qui permet à une application (le client) de se connecter aux ressources protégées sur service cible en utilisant un access_token émis par un serveur d’autorisation. Ce dernier est responsable du contrôle d’accès du client, de l'émission et de la gestion de ces jetons d'accès et de la vérification du jeton d’accès.

D’autre part, le serveur d’autorisation va effectuer le contrôle d’accès sur les scopes de l’application appelante à l’aide de l’identifiant du client (Client_ID_AS) et des scopes associés dans la requête.
À l’issue du contrôle d’accès, le serveur d’autorisation va délivrer un jeton d’accès (access_token) permettant d’accéder aux ressources du système cible.

Le serveur d'autorisation est un composant essentiel du flux OAuth 2.0 qui permet de garantir la sécurité et la confidentialité des données de l'utilisateur et doit être implémenté côté service/système cible.

La règle générale d'autorisation portera toujours sur une structure et son service. Une structure est soit une structure de santé ou une structure autorisée (proxy éditeur). 


###	Gestion et contrôle des accès
####	Le contrôle d’accès avec les scopes
*	Définition d’un scope dans le protocole OAuth 2.0 

Dans le protocole OAuth 2.0, les scopes sont utilisés par le client pour demander un accès limité aux données du propriétaire de la ressource. 

Les scopes sont utilisés pour définir les autorisations que le client demande auprès du service cible propriétaire de la ressource. Ils décrivent les actions que l'application sera autorisée à effectuer ou le périmètre de données sur lequel l'application sera autorisée d'agir.

<p style="text-align:center">
<object data="schema_autorisations_scopes.png" type="image/png"></object>
</p>


Afin d’illustrer l’usage du scope au sein d’un processus fonctionnel, on peut prendre l’exemple simplifié d’un utilisateur qui souhaite accéder aux ressources protégées d’un service de e-santé.

Dans le cas des API Pro Santé Connectées, le Professionnel de Santé (PS) est redirigé vers PRO Santé CONNECT par le fournisseur de service. Une fois le PS authentifié sur PSC, celui-ci envoie les tokens PSC au fournisseur de services.
L’authentification du fournisseur de services auprès du serveur d’autorisation se fait selon la RFC 7235 [11] c’est-à-dire avec un Client_ID_AS dans le header de sa requête suivant la méthode « Authentification Basic » qui encode les credentials en base 64.
Le fournisseur de service envoie une requête auprès du serveur d’autorisation qui contient comme paramètres, un subject_token (dans le cas des API ProSantéConnectées : subject_token = Access Token PSC), un certificat de structure et des scopes métier.
Une fois le contrôle d’accès est réalisé par le serveur d’autorisation, le PS peut accéder au service de e-santé.

<p style="text-align:center">
<object data="schema_scope_cas_metier.png" type="image/png"></object>
</p>




####	Les normes de conservation de l’access token
La sécurité liée au stockage de l’access token et à sa conservation est définie par l’ANSSI dans le document Recommandations pour la sécurisation de la mise en œuvre du protocole OpenID Connect dans la section 3.4 Recommandation R23 à R26. [6]

Recommandation : 

Une fois envoyé, les attributs de validité de l’access_token pourront être conservés par le système cible, notamment au sein d’un cache au niveau du serveur d’autorisation.
Afin de résoudre le problème de l’éventuelle révocation de l’access_token, la recommandation est de définir la durée de validité de l’access_token, émis par le serveur d’autorisation, entre 1h et 4h. Il est également fortement recommandé de vérifier la validité de l’access_token toutes les demi-heures en utilisant le service d'introspection du serveur d’autorisation. 
Si l’access_token est un jeton autoporteur JWS l’introspection n’est pas nécessaire, mais si c’est un jeton JWT opaque, elle l’est.
 

####	Les données de l’access token
Les données de l’access token peuvent être accessibles soit dans un jeton JWT signé par le serveur d'autorisation, soit via un appel à un service d'introspection.

Les paramètres de l’access token sont définis par les champs suivants :

<table>
<tbody>
<tr>
<td width="113">
<p><strong>Champs</strong></p>
</td>
<td width="519">
<p><strong>Description</strong></p>
</td>
</tr>
<tr>
<td width="113">
<p>iss</p>
</td>
<td width="519">
<p>Identifiant de l'&eacute;metteur du jeton</p>
</td>
</tr>
<tr>
<td width="113">
<p>sub</p>
</td>
<td width="519">
<p>Identifiant de l'utilisateur pour lequel le jeton a &eacute;t&eacute; &eacute;mis</p>
</td>
</tr>
<tr>
<td width="113">
<p>aud</p>
</td>
<td width="519">
<p>Identifiant ou liste d'identifiants des ressources qui peuvent &ecirc;tre accessibles avec ce jeton</p>
</td>
</tr>
<tr>
<td width="113">
<p>nonce</p>
</td>
<td width="519">
<p>Cha&icirc;ne de caract&egrave;res al&eacute;atoires unique qui peut &ecirc;tre utilis&eacute;e pour identifier les &eacute;changes entre le client et le serveur</p>
</td>
</tr>
<tr>
<td width="113">
<p>exp</p>
</td>
<td width="519">
<p>Heure d'expiration du jeton, apr&egrave;s laquelle il ne sera plus valide.</p>
<p>Il est de la responsabilit&eacute; des services de d&eacute;terminer la dur&eacute;e de vie de l&rsquo;access token (une dur&eacute;e de vie pas trop courte est &agrave; privil&eacute;gier)</p>
</td>
</tr>
<tr>
<td width="113">
<p>iat</p>
</td>
<td width="519">
<p>Heure &agrave; laquelle le jeton a &eacute;t&eacute; &eacute;mis</p>
</td>
</tr>
<tr>
<td width="113">
<p>scope</p>
</td>
<td width="519">
<p>D&eacute;finit le type et le p&eacute;rim&egrave;tre des ressources octroy&eacute;es par le serveur d&rsquo;autorisation. Sur cette base, le serveur d'autorisation authentifie l'utilisateur et recueille son consentement pour la transmission des ressources</p>
</td>
</tr>
</tbody>
</table>

Exemple de contenu d’un access_token dont la durée de vie est d’une heure : 

<table width="100%">
<tbody>
<tr>
<td>
<p>{<br /> "iss": "https://server.example.com",<br /> "sub": "identifiant_national",<br /> "aud": [Ressource A, Ressource B], <br /> "nonce": "n-0S6_WzA2Mj",<br /> "exp": 1626921770,<br /> "iat": 1311280970,<br /> "scope": &ldquo;scope 2 scope 3&rdquo;,<br /> }</p>
</td>
</tr>
</tbody>
</table>

####	L’intégration du mTLS dans le cadre du workflow OAuth 2.0

Dans le cadre d’un workflow OAuth 2.0, l’utilisation du mTLS permet :

*	La sécurisation des échanges (chiffrement)
*	L’authentification du client vis-à-vis du serveur
*	L’authentification du serveur vis-à-vis du client

Ci-dessous une illustration de l’utilisation du mTLS avec OAuth 2.0.

<div style="text-align: center;">{%include diag_mtls_oauth2_structure.svg%}</div>

Le détail des flux OAUH 2.0 + mTLS est défini dans la partie 7.6.3 Cas OAuth 2.0 + mTLS : accès à des données sensibles

Le protocole TLS est assuré sur la couche transport. Le certificat client utilisé dans le mTLS peut être transmis au serveur d’autorisation. Le protocole OAUTH est réalisé sur la couche applicative et permet de faire le contrôle du certificat client et de générer un access_token. 
 
####	Le lien entre un certificat TLS (champ Organisational Unit), sa requête et la réponse de l’access token.

Rappel sur les certificats émis une IGC Santé : 

Un certificat de personne morale (également appelé certificat d'entreprise) est un document électronique qui identifie une personne morale, telle qu'une entreprise, une organisation gouvernementale ou une association, auprès d'une autorité de certification (AC). Il sert à garantir l'authenticité et l'intégrité des communications électroniques émises par la personne morale, en fournissant une signature électronique associée à son identité.

Un certificat serveur (également appelé certificat SSL/TLS) est un document électronique qui identifie un serveur web auprès d'une autorité de certification (AC). Il est utilisé pour garantir l'authenticité et la confidentialité des communications entre un navigateur web et un serveur, en chiffrant les données échangées. Les certificats serveur sont utilisés pour sécuriser les connexions HTTPS, qui sont devenues la norme pour les sites web.

Recommandation :

*	L’attribut OU du certificat issu de l’IGC Santé contient un identifiant de type struct_IdNat qui lui-même est constitué d’un préfixe suivi de l’identifiant SIRET ou FINESS.
*	Pour une structure de santé, le FINESS pourra être le FINESS juridique.
Par exemple, les hôpitaux et les EHPAD génèrent des certificats sur leur FINESS juridique

Pour plus de précisions sur le contenu du certificat, voir le document [7]

Pour plus de précisions sur le contenu du struct_IdNat voir la section 5.5 [8]

Si certaines structures souhaitent indiquer le FINESS géographique, elles le pourront, mais la recommandation reste le FINESS juridique.












