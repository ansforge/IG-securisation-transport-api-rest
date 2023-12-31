Cet exemple d'Implementation Guide (IG) très simplifié sert de base pour le développement de nouveaux guides d'implémentation. La démarche d'élaboration d'un nouvel IG est expliquée dans le [wiki de ce repo](https://github.com/ansforge/IG-modele/wiki). 
Le README ci-dessous constitue un template à adapter et compléter pour chaque projet.

# Contexte

## Contexte métier du projet
La rédaction de ce volet est le résultat des travaux de co-construction menés avec l’Assurance Maladie, le GIE SESAM VITALE entre octobre 2022 et mai 2023.  

Ce volet décrit la sécurisation des APIs qui permettent d’accéder à des données de santé et qui nécessitent l’authentification des professionnels personnes physiques intervenant dans les secteurs sanitaire, social et médico-social via Pro Santé Connect.  

## Contexte technique du projet
L'objectif de ce projet est de décrire les architectures de référence permettant de sécuriser les APIs REST qui utilisent ProSantéConnect pour authentifier les professionnels de santé sur des services cibles à partir de leur logiciels métiers (LPS).

# CI/CD
Les workflows associés à ce repository (.github/workflows) permettent : 
* D'executer Sushi pour vérifier la grammaire
* De faire les tests avec le validator_cli
* De publier les pages : https://ansforge.github.io/IG-securisation-transport-api-rest/ig/main

# Notes
Ce repo "IG-modele" a été créé à partir du repo [sample-ig](https://github.com/FHIR/sample-ig) de l'organisation GitHub FHIR.

[A COMPLETER: notes supplémentaires pour le lecteur de la spec]
Un commentaire ? Une remarque ? Utilisez les GitHub [issues](https://docs.github.com/fr/issues) pour indiquer vos propositions d'amélioration et de correction.

## Acronymes

* IG : Implementation Guide
* FHIR : Fast Healthcare Interoperability Resources
* FIG : FHIR Implementation Guide
* HL7 : Health Level Seven
* API (application programming interface ou « interface de programmation d'application »)
* IDentity Provider ou Fournisseur d’identité
* MIE PSC Moyen d’Identification Electronique Pro Santé Connect
* OAUTH2 Open Authorization Protocole de sécurité mis en place pour permettre d'accéder aux ressources hébergées
* OIDC Openid connect
* PGSSIS Politique Générale de Sécurité des Systèmes d’Information de Santé
* REST Representational State Transfer Interface de programmation qui suit les principes de l'architecture REST.

