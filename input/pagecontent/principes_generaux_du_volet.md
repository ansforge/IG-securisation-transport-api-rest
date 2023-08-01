
Ce volet spécifie la couche Transport qui identifie les standards de transport des données et qui permet la communication des éléments d’identification et d’authentification des utilisateurs, physiques ou moraux, nécessaires à la mise en place des contrôles d’accès.

Il spécifie la couche Transport pour :

*	Un système cible offrant un service auquel il est possible de se connecter de façon synchrone.
*	Un système initiateur (application Web, client lourd, client natif) se connectant au service de façon synchrone.

<p style="text-align:center">
<object data="role_systeme.png" type="image/png"></object>
</p>

Le **Cadre d'Interopérabilité des Systèmes d'Information de Santé (CI-SIS)** établit les règles pour une informatique communicative dans le domaine de la santé, du médico-social et du social. Le CI-SIS préconise des normes techniques et sémantiques pour les acteurs de la santé impliqués dans des projets d'échange et de partage de données de santé. 

La mise en place des volets du cadre d'interopérabilité permet de minimiser les coûts d'intégration de nouvelles interfaces et les difficultés techniques d'interopérabilité, de même que de faciliter l'utilisation de produits d’éditeurs.

Les SI de santé en France sont composés de plusieurs entités indépendantes proposant un certain nombre d’applications et de services de nature hétérogène (client léger/web, client lourd, client applicatif). 
**Les applications des différents Fournisseurs de Services (FS) sont susceptibles de communiquer entre elles**. 

Afin de faciliter les échanges et d’assurer l’intégrité des données transmises d’un SI à l’autre, il est indispensable de standardiser les échanges. 

**C’est ainsi qu’intervient le Cadre d’Interopérabilité des SI de Santé (CI-SIS) pour résoudre les problèmes d’interopérabilité des applications de santé**.
 
<p style="text-align:center">
<object data="composants_flux_REST.png" type="image/png"></object>
</p>


### Les différents type de volets du CI-SIS

En dehors du volet transport du CI-SIS, il existe des référentiels d'interopérabilité à la maille :
*	**Sémantique :** décrivent les normes et terminologies standardisées et partagées qui doivent être utilisées pour décrire différentes catégories de données (diagnostics, médicaments, examens de biologie médicale, etc.). [2]

*	**Syntaxique :** l'interopérabilité syntaxique permet à deux ou plusieurs systèmes de communiquer et d'échanger des données. Pour être efficace, la conception de toute solution d'interopérabilité doit tenir compte de la granularité des données à partager.<br>
Afin de sécuriser les échanges entre les SI de santé, le présent volet du CI-SIS définit les modalités d’autorisation d’accès à un service de santé et se base sur les protocoles OAuth 2.0 et OIDC.<br>

### Typologie des appplications et services connernés par le volet transport du CI-SIS
Les normes d’interopérabilité décrite dans le présent volet sont applicables aux applications Pro Santé Connectées et non Pro Santé Connectées.
*	Une application Pro Santé Connectée est une application qui permet aux professionnels de santé de se connecter à un réseau d'échange d'informations de santé, afin de faciliter la gestion des informations médicales via les MIE PSC compatibles.
*	Une application non Pro Santé Connectée est une application qui ne nécessite pas d’utiliser Pro Santé Connect pour accéder aux informations de santé.
