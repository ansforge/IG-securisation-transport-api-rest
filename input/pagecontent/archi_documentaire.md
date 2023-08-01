Ce volet du CI-SIS s'insère dans l'ensemble documentaire décrit ci-dessous :

<p style="text-align:center">
<object data="architecture_documentaire.png" type="image/png"></object>
</p>

### Descriptif des acteurs et des composants du volet transport

La PGSSI-S distingue :
*	Les personnes physiques : acteurs des secteurs sanitaire, médico-social et social et les usagers des SI santé.
*	Les usagers sont identifiés par :
	*   L’identité INS (Identité Nationale de Santé).
    *   L’identité locale fournie par un fournisseur de service.

*	Les acteurs des secteurs sanitaire, médico-social et social sont identifiés par leur :
    *   Numéro RPPS, (Répertoire Partagé des Professionnels de Santé) à utiliser en priorité s’il existe pour la personne à identifier : utilisé dans les cartes CPx et e-CPS.
    *   Numéros locaux composés d’identifiants privés à portée nationale (ex : identifiants portés dans le VIHF).

**N.B. (en cours de migration) : Le numéro ADELI (Automatisation DEs LIstes) est toléré de façon transitoire jusqu’à son remplacement définitif par l’identifiant RPPS.**

*	Les personnes morales : toutes les structures de soins, telles que les maisons et centres de santé, mais aussi les laboratoires de biologie médicale, les services de protection maternelle infantile ou encore les services de santé au travail. Ces dernières sont identifiées par :
    *   Le numéro FINESS juridique (FINESS EJ) ou géographique (FINESS ET ou EG).
    *   Le numéro SIREN ou SIRET.

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