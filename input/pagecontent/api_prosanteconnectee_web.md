<p>
L’utilisateur souhaite, via l’application web de son navigateur, accéder à des données protégées d’un service cible. L’utilisateur s’authentifie premièrement auprès de Pro Santé Connect via l’application web de son fournisseur de services.<br>
Une fois authentifié, le fournisseur de services émet une requête auprès du serveur d'autorisation (subject_token + scope) pour accéder aux ressources.<br>
Si le subject_token et les scopes sont jugés valides par le serveur d'autorisation, ce dernier fournit un access_token au fournisseur de services.<br>

Le fournisseur de services, s'assure de la validité de l'access_token à sa réception puis envoie une requête auprès du service cible avec l’access_token contenu dans l’entête. Le service cible introspecte l’access_token auprès du serveur d’autorisation afin vérifier la validité de l’access_token et des scopes.<br>

Le fournisseur de services utilise l’access_token et les scopes auprès du service cible pour accéder aux données protégées.
</p>