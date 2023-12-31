### Introduction

Ce document présente les modalités de sécurisation des **API REST** afin de garantir l’interopérabilité des systèmes d’information de santé en France. 

**Il concerne la couche de transport et est indépendant des cas d’usages fonctionnels traités par ces API.**

L’interopérabilité des Systèmes d’Information (SI) est un enjeu majeur pour les services de la e-santé en France. L’objectif de l’interopérabilité est de faciliter les échanges entre les différents SI tout en garantissant la bonne sécurisation des données partagées entre ces SI.

Ce volet du Cadre d’Interopérabilité des Systèmes d’Information (CI-SIS) prend en compte les éléments suivants : 

*	Les exigences de sécurité les plus élevées, en particulier celles qui concernent la protection des `access token` et des informations sensibles.
*	La conformité aux normes d’architecture technique et la conformité aux exigences de la **Politique Générale de Sécurité des Systèmes d’Information de Santé (PGSSI-S)**.
*	Les spécificités sectorielles de la e-santé en France, telles que les **identifiants sectoriels, Pro Santé Connect ou encore IGC Santé.**
*	Les standards techniques de sécurité actuels, tels que `OAuth 2.0, OpenID Connect, l’API REST`, etc.


### Description Générale des flux du volet transport

Ce volet transport du cadre d’interopérabilité doit permettre de garantir l’intégration de ces nouvelles technologies. 
Cela passe notamment par la mise en place de protocoles d’authentification, de contrôle d’accès et de gestion des identités.

À noter que les technologies existantes, telles que le **SAMLV2, l’API SOAP**, etc. ne sont plus préconisées en cible. 

Elles sont toutefois utilisées dans l'architecture provisoire Services cibles existants utilisant une assertion SAML.

**Ces technologies sont documentées dans les volets transports suivants : CI-SIS Volet Transport Synchrone Client Lourd et CI AMO Volet Transport Synchrone pour les services de l’Assurance Maladie [1].**

Ce document est destiné à un public disposant de compétences techniques avancées. 
Une compréhension des standards `OAuth 2.0 et OpenID Connect` est notamment recommandée pour la lecture de ce document. 

 
### Description des flux spécifiques à une API ProSantéConnectée
Ce volet spécifie les protocoles de transport à utiliser pour les flux entre : 

* un système cible offrant une API ProSantéConnectée à laquelle il est possible de se connecter de façon synchrone.
* un système initiateur, pouvant être une application web serveur, une application client lourd, une application mobile native ou web, se connectant à une API ProSantéConnectée de façon synchrone.   

Pour en savoir plus sur les flux relatifs à une API ProSantéConnectée, rendez-vous sur les pages accessible via le menu "API ProSantéConnectée".
