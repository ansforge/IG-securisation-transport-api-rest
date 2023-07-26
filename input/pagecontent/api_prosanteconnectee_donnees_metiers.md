**Certains services cibles Pro Santé Connectés nécessitent l’envoi de données métiers complémentaires concernant le LPS, le proxy API ou l’activité du PS.**

*   Des services existants historiques assurent ce besoin grâce à **une assertion SAML** appelée **VIHF** et souhaitent minimiser l’impact de l’intégration du protocole `OAuth 2.0`. 
*   Pour les autres services souhaitant répondre à ce besoin, ils devront à terme utiliser le paramètre `actor_token` spécifié dans le protocole `OAuth 2.0`. 
    

### Services cibles existants utilisant une assertion SAML

**Pour les services historiques utilisant l’assertion SAML et qui voudraient minimiser les impacts côté client éditeurs, il est possible de créer une architecture de transition qui utlise l’assertion SAML pour transmette les données métier.**

*   Le schéma ci-dessous présente l’intégration du requêtage par protocole SOAP dans un flux OAuth 2.0
*   Les flux 13 et 14 représentent l’accès à l’API avec la requête SOAP et l’assertion SAML. 
    Le Client lourd LPS crée et peuple l’assertion SAML et génère la requête SOAP. Il est préconisé d’utiliser une connexion mTLS lors du flux 14.

**Pour plus précisions pour plus de précisions concernant le flux 13 se référer au document CI-SIS volet transport synchrone Client Lourd et au document CI AMO volet transport synchrone pour les services de l’Assurance Maladie [1]**.

* Le flux 17 représente l’accès au service cible avec **la requête SOAP et l’assertion SAML** avec l’`access_token` de l’API passé à l'en-tête `Authorization : Bearer` de la requête.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_client_lourd_CIBA_SAML.svg%}</div>

### Nouveaux Services cibles (ce paragraphe est en cours de rédaction)

L’ajout du paramètre actor_token [12] dans la requête token exchange permet de transmettre au serveur d’autorisation du service cible des données métiers complémentaires.

**L’actor_token_type doit alors y être spécifié conjointement**.

**Certains services cibles historiques ont besoin des données suivantes :**
*   données identifiantes du LPS (lps_nom, lps_id, lps_version…),
*   Situation d’exercice choisie par le PS sur son LPS.


<table width="652">
<tbody>
<tr>
<td width="132">
<p>Param&egrave;tres</p>
</td>
<td width="104">
<p>Obligatoire d&rsquo;apr&egrave;s la documentation officielle (OAuth 2.0)</p>
</td>
<td width="113">
<p>obligatoire pour le service cible</p>
</td>
<td width="302">
<p>Valeur</p>
</td>
</tr>
<tr>
<td width="132">
<p>grant_type</p>
</td>
<td width="104">
<p>Oui</p>
</td>
<td width="113">
<p>Oui</p>
</td>
<td width="302">
<p>urn:ietf:params:oauth:grant-type:token-exchange</p>
</td>
</tr>
<tr>
<td width="132">
<p>subject_token</p>
</td>
<td width="104">
<p>Oui</p>
</td>
<td width="113">
<p>Oui</p>
</td>
<td width="302">
<p>Access Token PSC</p>
</td>
</tr>
<tr>
<td width="132">
<p>subject_token_type</p>
</td>
<td width="104">
<p>Oui</p>
</td>
<td width="113">
<p>Oui</p>
</td>
<td width="302">
<p>urn:ietf:params:oauth:token-type:jwt</p>
</td>
</tr>
<tr>
<td width="132">
<p>actor_token</p>
</td>
<td width="104">
<p>Non</p>
</td>
<td width="113">
<p>Non</p>
</td>
<td width="302">
<p>JWT identifiant&nbsp;:</p>
<p>&Oslash; Proxy LPS API</p>
<p>&Oslash; Le LPS</p>
<p>&Oslash; L&rsquo;activit&eacute; du PS</p>
</td>
</tr>
<tr>
<td width="132">
<p>actor_token_type</p>
</td>
<td width="104">
<p>Non</p>
</td>
<td width="113">
<p>Non</p>
</td>
<td width="302">
<p>urn:ietf:params:oauth:token-type:jwt<br>
<b>ou bien</b><br>
urn:ietf:params:oauth:token-type:access_token</p>
</td>
</tr>
<tr>
<td width="132">
<p>scope</p>
</td>
<td width="104">
<p>Non</p>
</td>
<td width="113">
<p>Oui</p>
</td>
<td width="302">
<p>A d&eacute;terminer par les &eacute;quipes m&eacute;tiers</p>
<p>= scopes metier</p>
</td>
</tr>
</tbody>
</table>

**Exemple de claims du JWT décodé correspondant à l’actor_token**

```json
  {
    "iss":"https://original-issuer.example.net",
    "exp": 1441910060,
    "sub": admin@example.net,
     "lps_nom": <lps_nom>,
     "lps_id": <lps_id>,
     "lps_version": <lps_version>,
     "situation_exercice”: <situation_exercice >
  }
```
*   Selon les nécessités du service cible, l’`access_token` délivré peut être enrichi avec les claims de l’`actor_token` fournis lors de la requête.

*   Le JWT `actor_token` dans le cas d'une **API Pro Santé Connectée** pourrait signé par la clé privée du certificat cachet serveur de la structure porteuse du fournisseur de services.           
**Cependant, le cas mTLS couvre ce besoin rendant la signature non nécessaire**.

<div style="text-align: center;">{%include diag_api_prosanteconnectee_client_lourd_web_popup_actor_token.svg%}</div>
