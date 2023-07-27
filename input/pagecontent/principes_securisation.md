###	Confidentialité
Dans le volet transport synchrone pour application mobile : la confidentialité des échanges au niveau du transport est gérée par l’encapsulation du flux dans une connexion sécurisée TLS.
L’usage du **TLS version 1.3 ou supérieure est recommandé** et celui de la version 1.2 encore supporté.
**Les version 1.0 et 1.1 sont interdites**. L’usage de SSL 3.0 ou inférieure est interdit.

###	Intégrité
L’intégrité des échanges au niveau du transport est gérée par l’encapsulation du flux dans une connexion sécurisée TLS.

###	La sécurisation de l’architecture selon les cas d’usages
####	Les niveaux de sensibilité des données
La PGSSI-S met en avant des référentiels d’identification électronique pour personnes physiques et morales définis dans les documents suivants [11] et [12].

Dans le cadre des **API Pro Santé Connectées**, les données échangées sont des données sensibles qui nécessitent une architecture sécurisée. 

Dans le cadre des **API non Pro Santé Connectées** et du cas d’usage authentification d'une structure par 
`OAuth 2.0` , les données échangées sont des données non-sensibles et sont dites restreintes.

Dans le cadre d’une authentification par mTLS et par **mTLS + `OAuth 2.0`**, les données échangées sont des données sensibles qui nécessitent une architecture sécurisée.

**Le présent volet tranport du CI-SIS a pour objectif de présenter les méthodes pour sécuriser les échanges de données selon la sensibilité de la donnée échangée.**
 
####	La déclinaison des spécifications par cas d’usages et exigences de sécurité
L’objectif de la section présentée ci-dessous est de décrire les spécifications techniques permettant les échanges entre les différents SI Santé de façon sécurisée.

Les éléments permettant de déterminer le niveau de sensibilité de la donnée sont explicités dans la documentation de la PGSSI-S.

*	Si l’API appelée est **publique** : 

    *   L’appel aux ressources/services cibles se fait par clé API fournie par le service cible à la suite d’un enrôlement de l’application appelante. 
    *   Le service cible n’exige pas d’authentification en plus de la part du système initiateur, car les données exposées sont publiques.

*	Si l’API appelée est **privée** : 

    *   Cas de **l’authentification indirecte de l’utilisateur au sein d’une structure**


        On parle d'identification indirecte d'un professionnel de santé dans la mesure où le service cible s'appuie sur une authentification du professionnel réalisée localement par la structure.
        
        Dans le cas où seule la personne morale (structure) a besoin de s’authentifier, celle-ci est directe et réalisée par la **connexion mTLS avec le certificat de structure**.

        L’accès à l’API se fait avec **`OAuth 2.0` et/ou mTLS**. Si les données sont sensibles, l’utilisation d’une connexion mTLS à minima est nécessaire.

        Le mTLS permet d’authentifier le client par un **`Client_ID_AS` + mTLS** au lieu de s’authentifier avec son client_secret/Client_ID_AS. Dans ce cas le client_secret n’est pas nécessaire car l’authentification est portée par le mTLS (certificat client).

        La structure appelante doit fournir les informations utilisateur selon les besoins / cas d’usage métier.
        (ex : RPPS, profession, autres informations utilisateur).
        

    *   Cas **d’authentification directe d’un professionnel de santé**

        *	Cas d’authentification avec Pro Santé Connect :
            **NB : Ce cas est décrit dans cette version du CI-SIS**

            Dans le cas où l’utilisateur a besoin de s’authentifier avec Pro Santé Connect (pour accéder à des données sensibles), l’utilisateur s’authentifie avec son MIE PSC et l’accès à l’API du système cible se fait avec `OAuth 2.0`.

            *  Soit le PS s’authentifie à un FS avec PSC qui fournit lui-même des données de santé.

            * Soit le PS accède à un service avec PSC, et doit accéder à un autre service fournisseur de données de santé par des API Pro Santé connectées.

            Pour plus de détail sur le mode d'authentification sur les APIs Pro Santé Connectées, rendez-vous sur les pages :
            <a href="api_prosanteconnectee_web.html">Appel d'une API Pro Santé Connectée depuis une application web</a> et 
            <a href="api_prosanteconnectee_client_lourd.html">Appel d'une API Pro Santé Connectée depuis une application client lourd ou mobile</a>

        *   Cas d’authentification sans Pro Santé Connect :

            Dans le cas où l’utilisateur a besoin de s’authentifier physiquement avec un fournisseur d’identité tiers au service cible, l’utilisateur s’authentifie selon les modalités spécifiques requises et l’accès à l’API du système cible se fait avec `OAuth 2.0` avec `PKCE`.

            **Un serveur d’autorisation est mis à disposition à par la/les structure(s) cible(s)/appelée(s) dans le cadre de la réalisation du protocole `OAuth 2.0`.**

#### La définition des proxys
Le proxy est un serveur applicatif mis à disposition par le système initiateur qui souhaite accéder aux données du système cible. 

L’utilisation d’un proxy est motivée par des enjeux de :

**Sécurisation :**
*	Par la gestion des credentials pour s’authentifier sur PSC et sur le serveur d’autorisation du service cible. Chaque proxy détiendra un `Client_ID_AS` et un **certificat TLS client**.
*	Séparation de la gestion des credentials entre le module qui utilise l’`access_token` (Fournisseur des services pour le cas web et Proxy API/LPS pour le cas client lourd) et le module qui le demande (navigateur, mobile ou LPS). Le proxy évite donc la remontée de ces informations au niveau de l’instance qui les demandent. 
*	Un contrôle d’accès d’un proxy au service cible réalisé grâce aux `scopes`

**Découplage des requêtes au service cible et des requêtes vers PSC :**
*	L’`access token` PSC est utilisé une seule fois pour initier l’authentification sur le service cible lors de l’échange de jetons : `subject_token` (`access_token PSC`) contre un `access_token API`.
*	La durée de validité de l’`access_token API` est de 1h contre 2 minutes pour l’access_token PSC.
Cela évite une sursollicitation de PSC lors de l’envoie des requêtes vers le service cible.

<p style="text-align:center">
<object data="role_proxy.png" type="image/png"></object>
</p>

**Un proxy est soumis aux exigences de sécurité suivantes :**

*	Sécurisation de la connexion entre chaque instance qui demande les tokens (Navigateur ou LPS) et l’instance qui l’utilise (FS pour le cas web et proxy LPS FS/API pour le cas client lourd). 
*	Fiabilité dans le mapping entre l’instance demandant les tokens et celle qui l’utilise. 

Par exemple, on souhaite s’assurer que `subject_token` délivré par PSC est bien distingué par le proxy de l’`access_token` délivré par le serveur d’autorisation.


**L’outil utilisé pour la sécurisation des échanges et la bonne affection des jetons entre le client et le proxy varient selon le type de client :**

*	Si le client est un navigateur, un cookie applicatif avec une durée de vie limitée est initié par le proxy et est utilisé par le navigateur pour chaque nouvelle requête.
*	Si le client est un client lourd, un jeton applicatif avec une durée de vie limitée est initié par le proxy et est utilisé par le client lourd pour chaque nouvelle requête

**Dans les deux cas, un mapping par couple (clé,valeur) du type (ID jeton applicatif / ID cookie applicatif ; token ) est utilisé pour permettre au proxy qui manipulent ces tokens de les distinguer.**


Dans le cas des clients lourds, les **différences quantitatives de cardinalités** entre les instances LPS client, les proxys LPS et les serveurs d’autorisation sont décrites ci-dessous, du point de vue des éditeurs, pour l’accès à un seul service cible : 

<p style="text-align:center">
<object data="cardinalites_LPS_proxy_SA.png" type="image/png"></object>
</p>

Selon les éditeurs, il est possible qu’un seul proxy LPS FS soit mis à disposition et dans ce cas, la cardinalité entre le proxy LPS FS et le proxy LPS API est (1,1).

Du point de vue du serveur d’autorisation du service cible, les cardinalités sont décrites ci-dessous :

<p style="text-align:center">
<object data="cardinalites_SA_proxy.png" type="image/png"></object>
</p>

**Les proxys LPS API et LPS FS ont été rendus distincts car leurs rôles le sont. Il est toutefois possible pour un éditeur de regrouper ces deux fonctions au sein d’une même structure logicielle lors de l’implémentation de la sécurisation de l’architecture.**


 


