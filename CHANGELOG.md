Fichiers modifiés : `config/routes.json`

Ajout des guards `AdminGuard` sur les routes `/admin/user` et `/admin/user/new`
Ajout des guards `UserGuard` sur les routes membres `/habits` et `/habits/create`

Impact : Les utilisateurs non-admin ne peuvent plus accéder aux interfaces d'administration

Mots de passe stockés en clair

Fichiers modifiés : `src/Repository/UserRepository.php`, `src/Controller/SecurityController.php`

Implémentation du hachage des mots de passe avec `password_hash()` lors de l'insertion
Utilisation de `password_verify()` pour la vérification lors de la connexion

Impact : Les mots de passe sont maintenant sécurisés en base de données

**Injections XSS**

Fichiers modifiés :

`templates/admin/user/new.html.php`
`templates/admin/user/index.html.php`
`templates/register/index.html.php`
`templates/member/habits/new.html.php`
`templates/member/dashboard/index.html.php`
Ajout de `htmlspecialchars()` sur toutes les variables affichées
Impact : Protection contre l'injection de code JavaScript malveillant

**Injection SQL lors de la création d'habitudes**

Fichiers modifiés : `src/Repository/HabitRepository.php`

Remplacement de la concaténation directe par des requêtes préparées avec paramètres nommés
Utilisation de `execute()` avec un tableau de paramètres sécurisés
Impact : Protection contre les attaques par injection SQL

**Bugs Corrigés**

Erreur 404 sur `/habit/toggle`

Fichiers modifiés : `config/routes.json`

Ajout de la route manquante pour `/habit/toggle`

Fatal error sur `/api/habits`

Fichiers modifiés : `src/Controller/Api/HabitsController.php`

Correction de la déclaration de classe (héritage de `AbstractController`)
Impact : L'API retourne maintenant les habitudes au format JSON

_Redirection invalide après inscription_

Fichiers modifiés : `src/Controller/RegisterController.php`

Correction de la redirection vers `/dashboard` au lieu de `/home`
Ajout de `exit;` après `header()`
Impact : Redirection correcte après l'inscription

_Redirection invalide après connexion_

Fichiers modifiés : `src/Controller/SecurityController.php`

Correction de la redirection conditionnelle selon le rôle (admin/user)
Impact : Les admins sont redirigés vers `/admin/dashboard`, les users vers `/dashboard`

_Session admin définie après redirection_

Fichiers modifiés : `src/Controller/SecurityController.php`

Déplacement de la définition de `$_SESSION['admin']` avant le `header()`
Impact : La session admin est correctement définie avant la redirection

_Messages d'erreur toujours affichés_

Fichiers modifiés : `src/Controller/SecurityController.php`

Restructuration de la logique conditionnelle avec `else` approprié
Impact : Le message d'erreur ne s'affiche que lorsque nécessaire

_Variable session incorrecte_

Fichiers modifiés : `templates/member/dashboard/index.html.php`

Utilisation de `$_SESSION['user']['username']` au lieu de `$_SESSION['username']`
Impact : Le nom d'utilisateur s'affiche correctement sur le dashboard

_Lien vers route inexistante_

Fichiers modifiés : `templates/admin/dashboard/index.html.php`

Suppression du lien vers `/admin/habits` (route non implémentée)
Impact : Évite les erreurs 404 inutiles

_Logique de calcul de streak incorrecte_

Fichiers modifiés : `src/Repository/HabitRepository.php`

Correction de la logique de calcul des jours consécutifs
Vérification correcte de la date avec comparaison au format 'Y-m-d'
Impact : Le compteur de jours consécutifs fonctionne maintenant correctement

_Exit manquant après redirection_

Fichiers modifiés : `src/Controller/SecurityController.php`

Ajout de `exit;` après tous les `header()`
Impact : Empêche l'exécution de code après une redirection

_Clé JSON incorrecte dans l'API_

Fichiers modifiés : `src/Controller/Api/HabitsController.php`

Correction de la clé de retour JSON (`habits` au lieu de `data`)
Impact : Cohérence avec les attentes du frontend
