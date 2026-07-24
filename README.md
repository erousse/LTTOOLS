<div align="center">

# Leantime

Système open source de gestion de projet, conçu pour les personnes qui ne sont pas gestionnaires de projet de métier.

</div>

> ⚠️ **Note sur l'état de ce dépôt** : l'historique Git (`git show HEAD:...`) contient l'intégralité du projet Leantime (code applicatif, dépendances déclarées, tests, Docker, CI/CD, documentation). Cependant, **de nombreux fichiers présents dans l'historique Git ont été supprimés du répertoire de travail actuel** (working tree) et n'existent plus physiquement sur disque : `composer.json`, `package.json`, `tests/`, les workflows CI, les fichiers Docker, le `makefile`, `README.md` d'origine, etc. (`git status` les signale comme `D`). Ce README a été reconstitué à partir des informations disponibles dans l'historique Git du dépôt, mais **le répertoire de travail actuel ne contient plus tous ces fichiers**. Voir la section [Points à compléter](#points-à-compléter).

## Description

Leantime est un système de gestion de projet open source qui combine stratégie, planification et exécution dans une interface pensée pour être accessible aux équipes non spécialisées en gestion de projet (conçu en tenant compte du TDAH, de la dyslexie et de l'autisme, selon la documentation du projet).

- **Nom du package Composer** : `leantime/leantime`
- **Nom/version du package NPM** : `leantime` — `3.9.7`
- **Licence** : AGPL-3.0-only
- **Site officiel** : https://leantime.io

## Fonctionnalités

D'après la description du projet et l'organisation des modules métier (`app/Domain/`, 44 dossiers), Leantime couvre notamment :

- Gestion de tâches : Kanban, Gantt (Timeline), tableau, liste, calendrier (`Tickets`, `Calendar`)
- Planification de projets : tableaux de bord, rapports, objectifs (`Projects`, `Reports`, `Goalcanvas`)
- Sprints et suivi du temps (`Sprints`, `Timesheets`)
- Gestion de la connaissance : wiki, idées, commentaires (`Wiki`, `Ideas`, `Comments`)
- Modèles de canevas (Business Model Canvas, Lean Canvas, SWOT, analyse de risques) (`Canvas`, `Blueprints`, `Logicmodelcanvas`)
- Gestion des fichiers, avec stockage local ou S3 (`Files`)
- Authentification : LDAP, OIDC, double authentification (`Auth`, `Ldap`, `Oidc`, `TwoFA`)
- Système de plugins extensible (`Plugins`, `Modulemanager`)
- Notifications, tags, relations entre entités, audit (`Notifications`, `Tags`, `Entityrelations`, `Audit`)
- API JSON-RPC (`Api`)
- Internationalisation (fichiers `.ini` dans `app/Language/`)

## Technologies utilisées

| Domaine | Technologie |
|---|---|
| Langage backend | PHP ^8.2 |
| Framework | Laravel ^11.44 |
| Base de données | MySQL 8.0+ ou MariaDB 10.6+ (voir [Base de données](#base-de-données)) |
| Frontend / build | Webpack (Laravel Mix), Tailwind CSS, jQuery, HTMX, Tiptap, FullCalendar, Chart.js |
| Gestion de dépendances PHP | Composer |
| Gestion de dépendances JS | npm |
| Tests | Codeception ^5.1 (tests unitaires et d'acceptance) |
| Analyse statique | PHPStan, Laravel Pint (style de code), PHP_CodeSniffer |
| Conteneurisation | Docker / Docker Compose, Helm (Kubernetes) |
| CI/CD | GitHub Actions |

## Architecture

L'architecture est décrite dans `CLAUDE.md` (présent dans l'historique Git) comme une architecture orientée domaine (« domain-driven ») bâtie sur Laravel 11 :

- **`app/Core/`** : composants du framework et extensions Laravel (authentification, base de données, routage, middlewares, gestion des plugins, etc.)
- **`app/Domain/`** : ~44 modules métier, chacun suivant typiquement la structure Controllers → Services → Repositories → Models → Templates
- **`app/Plugins/`** : système de plugins (sous-module Git pointant vers un dépôt privé pour les extensions commerciales ; vide dans la version open source)
- **`app/Views/`** : vues partagées (layouts, composants Blade, sections communes)

Le projet est en migration progressive :
- de jQuery/AJAX vers **HTMX** (partiel),
- de templates legacy `.tpl.php` vers **Blade** `.blade.php` (partiel),
- d'événements basés sur des chaînes vers des événements typés par classe (partiel).

> Ces informations proviennent de `CLAUDE.md` dans l'historique Git ; ce fichier n'est plus présent sur disque dans le répertoire de travail actuel.

## Structure du projet

Arborescence attendue du projet telle que décrite par l'historique Git (à comparer avec l'état réel du répertoire de travail, voir avertissement en tête de document) :

```
.
├── app/
│   ├── Core/           # Composants du framework (auth, DB, routing, middlewares...)
│   ├── Domain/          # ~44 modules métier (Tickets, Projects, Users, Wiki...)
│   ├── Plugins/          # Système de plugins (sous-module Git, privé)
│   ├── Views/            # Vues partagées (layouts, composants Blade)
│   ├── Language/         # Fichiers de traduction (.ini)
│   └── Command/          # Commandes CLI (artisan étendu)
├── bin/                  # Exécutable CLI (bin/leantime)
├── bootstrap/            # Amorçage de l'application
├── config/               # Configuration (.env, sample.env)
├── database/             # Migrations / structure de base de données
├── public/               # Racine web (assets, dist, thèmes)
├── storage/               # Logs, cache, sessions
├── tests/                # Tests Codeception (Unit / Acceptance)
├── composer.json          # Dépendances PHP
├── package.json           # Dépendances JS
├── makefile               # Commandes de build/test/déploiement
└── userfiles/             # Fichiers utilisateurs uploadés
```

**Répertoire de travail actuel constaté** : seuls `app/`, `bin/`, `bootstrap/`, `config/`, `public/`, `storage/`, `userfiles/` et `vendor/` sont présents sur disque au moment de la rédaction de ce README.

## Prérequis

D'après le `README.md` original et `CLAUDE.md` (historique Git) :

- PHP 8.2 ou supérieur
- MySQL 8.0+ ou MariaDB 10.6+ (ou PostgreSQL, voir [Base de données](#base-de-données))
- Apache ou Nginx (IIS possible avec adaptations)
- Extensions PHP requises : BC Math (bcmath), Ctype, cURL, DOM, Exif, Fileinfo, Filter, GD, Hash, LDAP, Multibyte String (mbstring), MySQL, OPcache, OpenSSL, PCNTL, PCRE, PDO, Phar, Session, Tokenizer, Zip, SimpleXML
- Node.js et npm (pour la compilation des assets front-end)
- Composer

## Installation

> Les commandes ci-dessous proviennent du `makefile` et de `CLAUDE.md` présents dans l'historique Git. **Ces fichiers ne sont plus présents dans le répertoire de travail actuel** ; il faut les restaurer (`git checkout HEAD -- makefile composer.json package.json`) avant de pouvoir les exécuter tels quels.

### Installation manuelle (développement)

```bash
# Installer les dépendances (PHP + JS)
make install-deps-dev

# Compiler les assets pour le développement
make build-dev

# Pointer le serveur web vers le dossier public/
# Créer la base de données MySQL/MariaDB
# Copier config/sample.env vers config/.env et le configurer
# Se rendre sur <votre-domaine>/install
```

### Installation avec Docker (recommandé pour le développement)

```bash
# Construire l'environnement de développement
make clean build

# Démarrer les services (app, MailDev, phpMyAdmin, S3Ninja)
make run-dev
```

Services exposés d'après `CLAUDE.md` :

| Service | URL |
|---|---|
| Application Leantime | http://localhost:8090 |
| MailDev (test d'e-mails) | http://localhost:8081 |
| phpMyAdmin | http://localhost:8082 (identifiants : `leantime` / `leantime`) |
| S3Ninja (test S3) | http://localhost:8083 |

### Installation en production

> À compléter — le README d'origine décrit une installation locale et une installation via l'image Docker officielle (`leantime/leantime` sur Docker Hub), mais le détail complet de la procédure de production n'a pas été vérifié dans ce répertoire.

## Configuration

La configuration se fait via un fichier `.env` dans `config/` (`config/sample.env` sert de modèle, présent dans l'historique Git). Variables identifiées :

| Variable | Description | Obligatoire | Exemple |
|---|---|---|---|
| `LEAN_APP_URL` | URL de base, utile seulement pour une installation en sous-dossier | Optionnelle | `''` |
| `LEAN_APP_DIR` | Base de l'application sans slash final (utilisé pour les cookies) | Optionnelle | `''` |
| `LEAN_DEBUG` | Active le mode debug | Optionnelle | `0` |
| `LEAN_PERMISSIONS_ENFORCE` | Applique les refus de permissions `#[RequiresPermission]` (sinon mode audit) | Optionnelle | `true` |
| `LEAN_DB_HOST` | Hôte de la base de données | **Obligatoire** | `'localhost'` |
| `LEAN_DB_USER` | Utilisateur de la base de données | **Obligatoire** | — |
| `LEAN_DB_PASSWORD` | Mot de passe de la base de données | **Obligatoire** | — |
| `LEAN_DB_DATABASE` | Nom de la base de données | **Obligatoire** | — |
| `LEAN_DB_PORT` | Port de la base de données | **Obligatoire** | `'3306'` (5432 pour PostgreSQL) |
| `LEAN_DB_DEFAULT_CONNECTION` | Pilote de base de données : `mysql` ou `pgsql` | Optionnelle | `'mysql'` |
| `LEAN_DB_SCHEMA` | Schéma PostgreSQL (si `pgsql`) | Optionnelle | `'public'` |
| `LEAN_DB_SSLMODE` | Mode SSL PostgreSQL | Optionnelle | `'prefer'` |
| `LEAN_DB_PERSISTENT_CONNECTIONS` | Active le pool de connexions persistantes | Optionnelle | `true` |
| `LEAN_DB_MAX_CONNECTIONS` / `LEAN_DB_MIN_CONNECTIONS` | Bornes du pool de connexions | Optionnelle | `100` / `1` |
| `LEAN_DB_CONNECTION_TIMEOUT` / `LEAN_DB_IDLE_TIMEOUT` | Timeouts de connexion (secondes) | Optionnelle | `30` / `300` |
| `LEAN_SESSION_PASSWORD` | Sel utilisé pour les sessions (à remplacer par une valeur forte) | Optionnelle | *(valeur générée)* |
| `LEAN_SESSION_EXPIRATION` | Expiration de session en **minutes** | Optionnelle | `480` |
| `LEAN_SITENAME` | Nom du site | Optionnelle | `'Leantime'` |
| `LEAN_LANGUAGE` | Langue par défaut | Optionnelle | `'en-US'` |
| `LEAN_DEFAULT_TIMEZONE` | Fuseau horaire par défaut | Optionnelle | `'America/Los_Angeles'` |
| `LEAN_LOG_PATH` | Chemin du fichier de log | Optionnelle | `''` |
| `LEAN_LOG_CHANNELS` | Canaux de log (`single`, `syslog`, `sentry`, `stderr`) | Optionnelle | `'single,syslog,sentry'` |
| `LEAN_USE_S3` | Utiliser S3 au lieu du stockage local | Optionnelle | `false` |
| `LEAN_S3_KEY` / `LEAN_S3_SECRET` / `LEAN_S3_BUCKET` / `LEAN_S3_REGION` | Identifiants et configuration S3 | Optionnelle (si `LEAN_USE_S3=true`) | — |
| `LEAN_EMAIL_USE_SMTP` | Utiliser SMTP plutôt que `mail()` PHP | Optionnelle | `false` |
| `LEAN_EMAIL_SMTP_HOSTS` / `_AUTH` / `_USERNAME` / `_PASSWORD` / `_PORT` | Paramètres SMTP | Optionnelle (si SMTP activé) | — |
| `LEAN_LDAP_USE_LDAP` | Activer l'authentification LDAP | Optionnelle | `false` |
| `LEAN_LDAP_HOST` / `_PORT` / `_DN` / `_KEYS` | Paramètres de connexion LDAP | Optionnelle (si LDAP activé) | — |
| `LEAN_OIDC_ENABLE` | Activer OpenID Connect | Optionnelle | `true` |
| `LEAN_OIDC_CLIEND_ID` / `_CLIEND_SECRET` / `_PROVIDER_URL` | Paramètres OIDC | Optionnelle (si OIDC activé) | — |
| `LEAN_RATELIMIT_GENERAL` / `_AUTH` / `_API` | Limites de débit des requêtes | Optionnelle | `50000` |
| `LEAN_USE_REDIS` | Utiliser Redis pour le cache | Optionnelle | `false` |
| `CACHE_DRIVER` / `SESSION_DRIVER` / `QUEUE_CONNECTION` | Pilotes Laravel (cache, session, file d'attente) | Optionnelle | `file` / `file` / `sync` |
| `APP_ENV` / `APP_DEBUG` / `APP_KEY` / `APP_URL` | Variables standard Laravel | **Obligatoire** (`APP_KEY`) | voir `config/sample.env` |

> Liste non exhaustive : `config/sample.env` (historique Git, ~216 lignes) documente l'intégralité des options. Ce fichier n'est plus présent sur disque dans le répertoire de travail actuel.

## Utilisation

> Toutes les commandes ci-dessous proviennent du `makefile` (historique Git). Le `makefile` n'existe plus sur disque dans le répertoire de travail actuel — le restaurer avant utilisation.

### Développement

```bash
make install-deps-dev    # Installer les dépendances de développement (npm + composer)
make build-dev           # Build de développement (avec source maps)
make run-dev             # Démarrer l'environnement Docker de développement
npx mix                  # Build JS/CSS via webpack
```

### Production

```bash
make install-deps        # Installer les dépendances de production (composer --no-dev)
make build               # Build de production
make package             # Empaqueter une release (zip/tar.gz)
```

### Build

```bash
npx mix              # Build standard
npx mix --production # Build optimisé pour la production
```

### Lint / Format

```bash
make test-code-style   # Vérifier le style de code (Laravel Pint)
make fix-code-style    # Corriger le style de code (Laravel Pint)
make codesniffer       # Analyse PHP_CodeSniffer
make codesniffer-fix   # Correction automatique PHP_CodeSniffer
make phpstan           # Analyse statique (PHPStan, niveau 0)
```

### Tests

```bash
make unit-test              # Tests unitaires (Codeception, via Docker)
make acceptance-test        # Tests d'acceptance (Codeception + Selenium, via Docker)
make api-test                # Tests API (Codeception, via Docker)
make acceptance-test-ci      # Tests d'acceptance (variante CI)
make bearer-api-test-ci      # Tests API avec authentification Bearer (variante CI)
```

Groupes de tests d'acceptance disponibles (d'après `CLAUDE.md`) : `api`, `timesheet`, `login`, `ticket`, `user`.

```bash
docker compose --file .dev/docker-compose.yaml --file .dev/docker-compose.tests.yaml \
  exec leantime-dev php vendor/bin/codecept run -g api --steps
```

## API

Le projet expose une API interne organisée en JSON-RPC, d'après `CLAUDE.md` : toute méthode publique d'une classe de service peut être appelée via un point d'entrée de la forme `leantime.rpc.{domaine}.{service}.{méthode}`. L'annotation `@api` marque les méthodes destinées à l'API mais n'est pas appliquée strictement à l'exécution.

Le module `app/Domain/Api/` gère cette couche API.

> Description précise des routes, de l'authentification par jeton Bearer (`CreateBearerTokenCommand`) et du format des requêtes/réponses : à compléter — non déterminable avec certitude sans lecture approfondie du code (`app/Domain/Api/`, `tests/Httprequests/`).

## Base de données

- **Moteur** : MySQL 8.0+ ou MariaDB 10.6+, avec un support PostgreSQL apparaissant dans `config/sample.env` (`LEAN_DB_DEFAULT_CONNECTION = 'pgsql'`).
- **ORM / accès aux données** : couche d'abstraction propre au projet (`app/Core/Db/` : `Db`, `Repository`, `DbColumn`, `DatabaseHelper`), au-dessus de Laravel (Doctrine DBAL est une dépendance Composer : `doctrine/dbal`).
- **Migrations** : un dossier `database/` est présent dans l'historique Git ; commande CLI `MigrateCommand` disponible via `php bin/leantime`.
- **Seed** : Non déterminable à partir du dépôt (aucun mécanisme de seed explicite identifié dans les fichiers analysés).
- **Sauvegarde** : une commande `BackupDbCommand` (`app/Command/`) et une variable `LEAN_DB_BACKUP_PATH` existent pour la sauvegarde locale de la base.

## Docker

Fichiers Docker identifiés dans l'historique Git (non présents actuellement sur disque) :

- `leantime-3.9.7/.docker/Dockerfile` : image de production, build multi-stage sur `php:8.3-fpm-alpine`, avec extensions PHP (mysqli, pdo_mysql, pdo_pgsql, bcmath, mbstring, exif, gd, ldap...) compilées pour plusieurs plateformes.
- `leantime-3.9.7/.dev/dockerfile` et `leantime-3.9.7/.dev/docker-compose.yaml` : environnement de développement (app + MailDev + phpMyAdmin + S3Ninja).
- `leantime-3.9.7/.dev/docker-compose.tests.yaml` : surcouche pour l'exécution des tests.

```bash
# Démarrer l'environnement de développement
docker compose --file .dev/docker-compose.yaml up --detach --build --remove-orphans
```

Une image officielle est également publiée sur Docker Hub sous le nom `leantime/leantime` (mentionnée dans le badge du `README.md` d'origine).

> À compléter : ces fichiers Docker doivent être restaurés dans le répertoire de travail (`git checkout HEAD -- leantime-3.9.7/.docker leantime-3.9.7/.dev`) pour être utilisables.

## Déploiement

- Un chart **Helm** (`helm/`) est présent dans l'historique Git, avec templates de `deployment`, `service`, `hpa` (autoscaling horizontal), `ingress`, `pvc`, `serviceaccount`, suggérant un déploiement type **Kubernetes**.
- La cible `make package` du `makefile` construit une archive de release (`.zip` / `.tar.gz`) prête à être déployée sur un serveur classique (Apache/Nginx + PHP-FPM).
- Procédure détaillée de déploiement en production (au-delà de l'installation locale/Docker) : **Non déterminable à partir du dépôt.**

## CI/CD

Workflows GitHub Actions identifiés dans l'historique Git (`.github/workflows/`), non présents actuellement sur disque :

| Workflow | Rôle apparent |
|---|---|
| `acceptancetests.yml` | Exécution des tests d'acceptance |
| `api-bearer-tests.yml` | Tests de l'API avec authentification Bearer |
| `codeStyleAnalysis.yml` | Vérification du style de code |
| `makefile.yml` | Exécution de cibles du `makefile` en CI |
| `release-prepare.yml` | Préparation d'une release |
| `release.yml` | Publication d'une release |
| `security.yml` | Analyse de sécurité |
| `staticAnalysis.yml` | Analyse statique (PHPStan) |
| `unittests.yml` | Exécution des tests unitaires |
| `update-latest-tag.yml` | Mise à jour du tag `latest` |

> Le contenu détaillé de chaque workflow n'a pas été inspecté ligne à ligne ; les rôles ci-dessus sont déduits des noms de fichiers.

## Dépendances principales

### PHP (`composer.json`)

| Bibliothèque | Rôle |
|---|---|
| `laravel/framework` (^11.44) | Framework applicatif principal |
| `guzzlehttp/guzzle` | Client HTTP |
| `aws/aws-sdk-php`, `league/flysystem-aws-s3-v3` | Intégration stockage S3 |
| `phpmailer/phpmailer` | Envoi d'e-mails |
| `robthree/twofactorauth`, `endroid/qr-code` | Authentification à deux facteurs (2FA) |
| `laravel/sanctum` | Authentification API par jeton |
| `laravel/socialite` + `socialiteproviders/*` | Connexion via fournisseurs tiers (Google, GitHub, GitLab, Microsoft, Okta, Keycloak, SAML2...) |
| `symfony/dotenv`, `symfony/cache`, `symfony/yaml` | Composants Symfony (config, cache, YAML) |
| `sentry/sentry-laravel` | Suivi des erreurs (Sentry) |
| `stripe/stripe-php` | Paiements Stripe |
| `sabre/dav` | Support CalDAV/WebDAV |
| `prism-php/prism`, `inspector-apm/neuron-ai`, `hkulekci/qdrant` | Fonctionnalités liées à l'IA / recherche vectorielle |
| `laravel/mcp` | Support du protocole MCP |
| `phpseclib/phpseclib` | Cryptographie |

### JS (`package.json`)

| Bibliothèque | Rôle |
|---|---|
| `@tiptap/*` | Éditeur de texte riche (wiki, commentaires) |
| `fullcalendar`, `@fullcalendar/*` | Vues calendrier |
| `chart.js` | Graphiques et rapports |
| `htmx.org` | Mises à jour partielles de page (migration en cours, cf. architecture) |
| `mermaid` | Diagrammes (probable usage dans le wiki) |
| `gridstack`, `packery`, `isotope-layout`, `masonry-layout` | Mise en page en grille (tableaux de bord, kanban) |
| `jquery`, `jquery-ui-dist` | Framework JS legacy encore en usage |
| `tailwindcss`, `less` | Styles |
| `webpack`, `laravel-mix` | Build des assets |

### Outils de développement / qualité

| Outil | Rôle |
|---|---|
| `codeception/codeception` | Framework de tests (unitaires + acceptance) |
| `phpstan/phpstan` | Analyse statique |
| `laravel/pint` | Formatage du code PHP |
| `squizlabs/php_codesniffer` | Style de code PHP |
| `roave/security-advisories` | Blocage de dépendances vulnérables connues |

## Bonnes pratiques

D'après `CLAUDE.md` (historique Git) :

- Privilégier **HTMX** plutôt que jQuery/AJAX pour les nouvelles fonctionnalités asynchrones.
- Privilégier les templates **Blade** (`.blade.php`) plutôt que les templates legacy `.tpl.php` pour tout nouveau développement.
- Privilégier les **événements basés sur des classes** plutôt que les événements basés sur des chaînes lorsque c'est pratique.
- Le dossier `app/Plugins/` est un sous-module Git privé : il apparaît vide dans la version open source, ce qui est normal.

## Dépannage

Non déterminable à partir du dépôt : aucun fichier de type FAQ, `TROUBLESHOOTING.md` ou section dédiée n'a été identifié dans les fichiers analysés.

## Licence

**AGPL-3.0-only** (GNU Affero General Public License v3), telle que déclarée dans `composer.json` et le fichier `LICENSE` présent dans l'historique Git.

## Points à compléter

- **Cohérence du répertoire de travail** : de très nombreux fichiers versionnés dans Git (`composer.json`, `package.json`, `makefile`, `README.md` d'origine, `tests/`, `.github/workflows/`, fichiers Docker, `CLAUDE.md`, `LICENSE`, etc.) sont absents du répertoire de travail actuel (`git status` les marque `D`). Il conviendra de déterminer si cette suppression est intentionnelle, ou s'il faut restaurer ces fichiers (`git checkout HEAD -- <fichier>`).
- **Structure `leantime-3.9.7/`** : ce dossier de l'historique Git contient une copie complète et redondante du projet (mêmes fichiers `.dev`, `.docker`, `.github`, etc. qu'à la racine). Sa raison d'être n'est pas déterminable avec certitude à partir du dépôt seul — À compléter par l'équipe.
- **Procédure de déploiement en production détaillée** (au-delà de l'installation locale/Docker) — À compléter.
- **Documentation des routes/API JSON-RPC** (paramètres, authentification Bearer détaillée, exemples de requêtes) — À compléter.
- **Mécanisme de seed de base de données** — non identifié, à confirmer.
- **Section Dépannage / FAQ** — absente, à compléter si besoin.
- **Version actuelle exacte** : `package.json` indique `3.9.7` alors que `CLAUDE.md` mentionne « Current version: 3.6.2 » — incohérence à clarifier avec l'équipe.
