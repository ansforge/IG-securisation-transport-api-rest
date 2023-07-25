# Le positionnement du volet transport du CI-SIS dans le corpus documentaire


Ce volet spécifie la couche Transport qui identifie les standards de transport des données et qui permet la communication des éléments d’identification et d’authentification des utilisateurs, physiques ou moraux, nécessaires à la mise en place des contrôles d’accès.

Il spécifie la couche Transport pour :

*	Un système cible offrant un service auquel il est possible de se connecter de façon synchrone.
*	Un système initiateur (application Web, client lourd, client natif) se connectant au service de façon synchrone.

<p style="text-align:center">
<object data="role_systeme.png" type="image/png"></object>
</p>

Le Cadre d'Interopérabilité des Systèmes d'Information de Santé (CI-SIS) établit les règles pour une informatique communicative dans le domaine de la santé, du médico-social et du social. Le CI-SIS préconise des normes techniques et sémantiques pour les acteurs de la santé impliqués dans des projets d'échange et de partage de données de santé. 

La mise en place des volets du cadre d'interopérabilité permet de minimiser les coûts d'intégration de nouvelles interfaces et les difficultés techniques d'interopérabilité, de même que de faciliter l'utilisation de produits d’éditeurs.

Les SI de santé en France sont composés de plusieurs entités indépendantes proposant un certain nombre d’applications et de services de nature hétérogène (client léger/web, client lourd, client applicatif). Les applications des différents Fournisseurs de Services (FS) sont susceptibles de communiquer entre elles. 

Afin de faciliter les échanges et d’assurer l’intégrité des données transmises d’un SI à l’autre, il est indispensable de standardiser les échanges. C’est ainsi qu’intervient le Cadre d’Interopérabilité des SI de Santé (CI-SIS) pour résoudre les problèmes d’interopérabilité des applications de santé.
 
<p style="text-align:center">
<object data="composants_flux_REST.png" type="image/png"></object>
</p>

Par ailleurs, ce volet du CI-SIS s'insère dans l'ensemble documentaire décrit ci-dessous :

<p style="text-align:center">
<object data="architecture_documentaire.png" type="image/png"></object>
</p>


En dehors du volet transport du CI-SIS, il existe des référentiels d'interopérabilité à la maille :
*	Sémantique : décrivent les normes et terminologies standardisées et partagées qui doivent être utilisées pour décrire différentes catégories de données (diagnostics, médicaments, examens de biologie médicale, etc.). [2]

*	Syntaxique : l'interopérabilité syntaxique permet à deux ou plusieurs systèmes de communiquer et d'échanger des données. Pour être efficace, la conception de toute solution d'interopérabilité doit tenir compte de la granularité des données à partager. 
Afin de sécuriser les échanges entre les SI de santé, le présent volet du CI-SIS définit les modalités d’autorisation d’accès à un service de santé et se base sur les protocoles OAuth 2.0 et OIDC. Les normes d’interopérabilité décrite dans le présent volet sont applicables aux applications Pro Santé Connectées et non Pro Santé Connectées.
*	Une application Pro Santé Connectée est une application qui permet aux professionnels de santé de se connecter à un réseau d'échange d'informations de santé, afin de faciliter la gestion des informations médicales via les MIE PSC compatibles.
*	Une application non Pro Santé Connectée est une application qui ne nécessite pas d’utiliser Pro Santé Connect pour accéder aux informations de santé.
Descriptif des acteurs et des composants du cadre

La PGSSI-S distingue :
*	Les personnes physiques : acteurs des secteurs sanitaire, médico-social et social et les usagers des SI santé.
-	Les usagers sont identifiés par :
	* L’identité INS (Identité Nationale de Santé).
    * L’identité locale fournie par un fournisseur de service.

-	Les acteurs des secteurs sanitaire, médico-social et social sont identifiés par leur :
    * Numéro RPPS, (Répertoire Partagé des Professionnels de Santé) à utiliser en priorité s’il existe pour la personne à identifier : utilisé dans les cartes CPx et e-CPS.
    * Numéros locaux composés d’identifiants privés à portée nationale (ex : identifiants portés dans le VIHF).

N.B. (en cours de migration) : Le numéro ADELI (Automatisation DEs LIstes) est toléré de façon transitoire jusqu’à son remplacement définitif par l’identifiant RPPS.

*	Les personnes morales : toutes les structures de soins, telles que les maisons et centres de santé, mais aussi les laboratoires de biologie médicale, les services de protection maternelle infantile ou encore les services de santé au travail. Ces dernières sont identifiées par :
    * Le numéro FINESS juridique (FINESS EJ) ou géographique (FINESS ET ou EG).
    * Le numéro SIREN ou SIRET.

Pour toute information sur les référentiels d’identification des personnes physiques ou morales, se référer au corpus documentaire [3]


Les systèmes en jeu dans le cadre de ce volet du CI-SIS sont les suivants :

<table width="680">
<tbody>
<tr>
<td width="196">
<p><strong>Composant</strong></p>
</td>
<td width="484">
<p><strong>Typologie de client</strong></p>
</td>
</tr>
<tr>
<td width="196">
<p><strong>Syst&egrave;me initiateur </strong></p>
<p><strong>(client)</strong></p>
</td>
<td width="484">
<p>o <strong>Client lourd</strong>&nbsp;: logiciel d&rsquo;un professionnel de sant&eacute; install&eacute; sur le poste de travail. Il peut &ecirc;tre chez un lib&eacute;ral ou bien un &eacute;tablissement de sant&eacute;.</p>
<p>o <strong>Application mobile ou native</strong> utilis&eacute;e par un PS</p>
<p>o <strong>Application web</strong> (client l&eacute;ger)&nbsp;: application disponible sur un navigateur web utilisable par un PS</p>
<p>o <strong>Client applicatif</strong>&nbsp;: serveur applicatif appartenant &agrave; une structure identifi&eacute;e (la personne morale). Ce client utilise une API propos&eacute;e par le syst&egrave;me cible</p>
</td>
</tr>
<tr>
<td width="196">
<p><strong>Syst&egrave;me cible</strong></p>
</td>
<td width="484">
<p>o <strong>Serveur applicatif</strong>&nbsp;: serveur applicatif appartenant &agrave; une structure identifi&eacute;e (la personne morale). Ce serveur expose une API</p>
</td>
</tr>
<tr>
<td width="196">
<p><strong>Fournisseur d&rsquo;identit&eacute; (Identity Provider ou IDP)</strong></p>
</td>
<td width="484">
<p>o <strong>Pro Sant&eacute; Connect (PSC)</strong>&nbsp;: Fournisseur d&rsquo;identit&eacute; permettant aux PS de s&rsquo;authentifier via les MIE PSC compatibles</p>
<p>o <strong>SI local</strong>&nbsp;: fournisseur d&rsquo;identit&eacute; propre &agrave; un service (applications non pro-sant&eacute; connect&eacute;e)</p>
</td>
</tr>
</tbody>
</table>