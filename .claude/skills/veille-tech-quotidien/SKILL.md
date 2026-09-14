---
name: veille-tech-quotidien
description: >
  Veille technologique automatisée quotidienne, organisée par source (13 sites :
  5 axes tech — Modèles IA, Outils IA, Infra/Réseau, Cybersécurité, DATA — plus
  décideurs IT/DSI et conseil/gestion de projet). Conçue pour une exécution sans
  supervision (Routine Claude Code planifiée à 08h00 UTC+11), pas pour un usage
  conversationnel manuel. Distincte de `veille-marche` (10 axes, incluant les
  volets commerciaux NC) : ne pas fusionner les deux, ne pas publier sur la
  même page GitHub Pages.
---

# veille-tech-quotidien

Note quotidienne courte, publiée automatiquement sur une page GitHub Pages dédiée, distincte de la veille commerciale complète.

**Format (v2, depuis le 14/09/2026)** : la note est organisée **par source**, pas par axe thématique. Pour chaque site de la liste, on relève les articles actuellement en Une, on les résume en 2-3 phrases avec un lien vers l'article original, et on indique la fraîcheur :
- Si un article est clairement daté de moins de 24 h : le lister normalement avec sa date.
- Si rien n'est confirmé daté de moins de 24 h pour une source : afficher le bandeau `Aucun nouvel article dans les dernières 24 h — historique conservé` puis lister quand même les derniers articles connus de cette source (ne jamais laisser une section vide).
- Si une source est inaccessible (403, 503, DNS...) après 2 tentatives : l'indiquer explicitement (`Source inaccessible (HTTP {code}) — contenu non consulté, non inventé`), ne jamais inventer de contenu à sa place.
- Si une date de publication n'est pas affichée par le site (fréquent sur certains sites) : lister l'article quand même mais préciser `dates non affichées — fraîcheur non garantie` en tête de section plutôt que d'affirmer une fraîcheur non vérifiée.

**Fenêtre temporelle** : 24 heures glissantes (pas 48h — cadence quotidienne, pas de recouvrement voulu), calculée comme en étape 1.

**Accumulation** : contrairement à `veille-marche` (qui remplace tout à chaque run), cette note s'ajoute en tête de la page à chaque exécution. Les 14 dernières notes sont conservées ; au-delà, la plus ancienne est retirée. Si une note existe déjà pour la date du jour (ré-exécution le même jour), elle est **remplacée** (même ID, pas de doublon) plutôt que dupliquée.

---

## Périmètre (13 sources, en 4 catégories)

| Catégorie | Sources | Classe CSS |
|---|---|---|
| Modèles IA / Outils IA | intelligence-artificielle.com, usine-digitale.fr, journaldunet.com | `cat-ia` |
| Infra / Cybersécurité / DATA | lemondeinformatique.fr, lemagit.fr (rubrique virtualisation), silicon.fr, itpro.fr | `cat-tech` |
| Décideurs IT / DSI | Alliancy.fr, ITforBusiness.fr, Solutions Numériques | `cat-dsi` |
| Conseil / Gestion de projet | Consultor.fr, Manager GO!, PMI France | `cat-conseil` |

Axes de fond couverts par les sources tech (indicatif, ne structure plus la note) : Modèles IA (OpenAI, Anthropic, Google, Mistral, Meta, benchmarks LLM), Outils IA (Copilot M365, GitHub Copilot, Gemini Workspace, IA métier), Infra/Réseau (Scale Computing, VMware, Dell, HP, Lenovo, Synology, Wi-Fi), Cybersécurité (Fortinet, CVE, SASE, Zero Trust, EDR, CERT-FR), DATA (Power BI, Microsoft Fabric, Databricks, gouvernance données).

Axes exclus volontairement (hors périmètre de cette veille quotidienne, restent dans `veille-marche` sur demande) : Concurrentielle NC, Client NC, Sectorielle NC, AO NC, Réglementation.

**Historique des sources** : `CIO.fr` a été retiré (HTTP 503 persistant sur plusieurs tentatives, y compris à plusieurs jours d'intervalle) et remplacé par `Solutions Numériques` (même catégorie décideurs IT). Si `CIO.fr` redevient accessible, ne pas le rajouter sans validation explicite de l'utilisateur — la liste des 13 sources ci-dessus fait foi.

---

## Configuration

```
GITHUB_USER   = "poinde08-netizen"
GITHUB_REPO   = "veille-officeplus"
PAGES_URL     = "https://poinde08-netizen.github.io/veille-officeplus"
TARGET_FILE   = "veille-tech.html"
MARKER_START  = "<!-- NOTES-TECH -->"
MARKER_END    = "<!-- /NOTES-TECH -->"
MAX_NOTES     = 14
FUSEAU        = UTC+11 (Nouméa)
```

**Accès GitHub** : pas de token en clair dans ce fichier. Deux cas :
- **Exécution via Routine Claude Code** : le dépôt est sélectionné à la création de la Routine ; l'accès en écriture est géré par la connexion GitHub native de la Routine. Aucun token à fournir ici.
- **Test manuel ponctuel (chat claude.ai)** : exporter `GITHUB_TOKEN` comme variable d'environnement avant d'invoquer ce skill (fine-grained PAT, scope `contents:write` sur ce seul dépôt, jamais dans un fichier versionné).

---

## Déroulement d'exécution

### 1. Initialisation

Exécuter via `bash_tool` :

```python
from datetime import datetime, timezone, timedelta
utc11 = timezone(timedelta(hours=11))
now = datetime.now(utc11)
seuil = now - timedelta(hours=24)
print(f"MAINTENANT={now.strftime('%Y-%m-%d %H:%M UTC+11')}")
print(f"SEUIL_24H={seuil.strftime('%Y-%m-%d %H:%M UTC+11')}")
```

### 2. Collecte par source

Pour chacune des 13 sources : récupérer la page d'accueil (ou la rubrique pertinente), relever les articles actuellement en Une (titre, date si affichée, URL complète de l'article), puis pour chaque article à conserver (3 à 6 par source, les plus pertinents pour un DSI/dirigeant IT d'Office Plus) aller chercher la page de l'article et en produire un résumé de 2-3 phrases (sujet, faits clés, conclusion) en français, factuel, sans invention.

Classer chaque source :
- **Nouveau** : au moins un article clairement daté de moins de 24 h → lister ces articles (les autres articles de la page peuvent être ajoutés à titre de contexte si pertinent, avec leur date).
- **Rien de nouveau** : aucun article confirmé daté de moins de 24 h → bandeau `status-none` + lister les derniers articles connus de cette source (historique).
- **Dates non affichées** : le site ne montre pas de date exploitable → bandeau `status-new` avec la mention « dates non affichées — fraîcheur non garantie », lister les articles en Une tels quels.
- **Inaccessible** : après 2 tentatives (codes HTTP 403/404/503, erreur DNS...) → bandeau `status-error`, ne rien lister, ne rien inventer.

### 3. Génération de la note du jour

```
NOTE_ID = note-tech-{YYYY-MM-DD}
```

```html
<article class="note-tech" id="{NOTE_ID}">
  <div class="note-header">
    <span class="badge badge-tech">VEILLE TECH</span>
    <span class="note-date">{DATE} {HEURE UTC+11} — Fenêtre 24 h</span>
  </div>
  <div class="note-body">
    {BLOC_SOURCE_1}
    {BLOC_SOURCE_2}
    ...
    {BLOC_SOURCE_13}
  </div>
</article>
```

Chaque source utilise le gabarit suivant, avec la classe de catégorie (`cat-ia`, `cat-tech`, `cat-dsi` ou `cat-conseil` — voir tableau du Périmètre) reprise par le CSS déjà présent dans le `<head>` de `veille-tech.html` (ne pas retirer ce `<head>` lors des publications, le script de l'étape 4 ne touche que le bloc entre les marqueurs) :

```html
<section class="source-block {CATEGORIE_CLASS}">
  <h3>{NOM_SOURCE}</h3>
  <p class="source-status {status-new|status-none|status-error}">{MESSAGE_STATUT}</p>
  <ul class="article-list">
    <li><a href="{URL_ARTICLE}">{TITRE_ARTICLE}</a><span class="art-date">{DATE_SI_CONNUE}</span>
      <p>{RESUME_2_3_PHRASES}</p></li>
    ...
  </ul>
</section>
```

Pour une source inaccessible, omettre `<ul class="article-list">` et ne garder que `<h3>` + `<p class="source-status status-error">`.

### 4. Publication — accumulation avec remplacement du jour, pas duplication

Exécuter via `bash_tool` (adapter `{ARTICLE_HTML}` avec le bloc de l'étape 3) :

**Important** : la mise en forme (CSS par catégorie, légende de couleurs, styles des blocs source/liste d'articles) vit dans le `<head>` et le début du `<body>` de `veille-tech.html`, en dehors des marqueurs `NOTES-TECH` — le script ci-dessous ne touche jamais cette zone. Ne pas la régénérer sauf si `veille-tech.html` n'existe pas encore (squelette de secours ci-dessous), auquel cas reprendre le `<head>`/légende déjà déployés sur `https://poinde08-netizen.github.io/veille-officeplus/veille-tech.html` (vue source) pour ne pas perdre le style.

```python
import subprocess, os, tempfile, shutil, re

GITHUB_USER  = "poinde08-netizen"
GITHUB_REPO  = "veille-officeplus"
TARGET_FILE  = "veille-tech.html"
MARKER_START = "<!-- NOTES-TECH -->"
MARKER_END   = "<!-- /NOTES-TECH -->"
MAX_NOTES    = 14
PAGES_URL    = f"https://{GITHUB_USER}.github.io/{GITHUB_REPO}"

nouvel_article = """{ARTICLE_HTML}"""

def is_git_repo(path):
    return subprocess.run(["git", "rev-parse", "--show-toplevel"], cwd=path,
                           capture_output=True, text=True).returncode == 0

cwd = os.getcwd()
cleanup_dir = None

if is_git_repo(cwd):
    # Contexte Routine Claude Code : le dépôt est déjà cloné par la plateforme,
    # credentials git déjà en place. Ne pas recloner, ne pas reconstruire d'URL avec token.
    workdir = subprocess.run(["git", "rev-parse", "--show-toplevel"], cwd=cwd,
                              capture_output=True, text=True).stdout.strip()
else:
    # Contexte test manuel hors Routine (chat claude.ai) : GITHUB_TOKEN doit être
    # exporté en variable d'environnement avant l'appel. Jamais en dur dans ce fichier.
    token = os.environ.get("GITHUB_TOKEN", "").strip()
    if not token:
        raise RuntimeError("GITHUB_TOKEN absent. Hors contexte Routine, exporter la variable avant d'invoquer ce skill.")
    remote_url = f"https://{GITHUB_USER}:{token}@github.com/{GITHUB_USER}/{GITHUB_REPO}.git"
    workdir = tempfile.mkdtemp()
    cleanup_dir = workdir
    subprocess.run(["git", "clone", "--depth", "1", remote_url, workdir], check=True, capture_output=True)

try:
    target_path = os.path.join(workdir, TARGET_FILE)

    if os.path.exists(target_path):
        with open(target_path, "r", encoding="utf-8") as f:
            html = f.read()
    else:
        html = (
            "<!doctype html><html lang=\"fr\"><head><meta charset=\"utf-8\">"
            "<title>Veille technologique quotidienne — Office Plus</title></head>"
            "<body><h1>Veille technologique quotidienne</h1>"
            f"{MARKER_START}\n{MARKER_END}"
            "</body></html>"
        )

    if MARKER_START not in html or MARKER_END not in html:
        raise ValueError("Markers NOTES-TECH introuvables ou incomplets dans veille-tech.html")

    # Extraire les articles existants, retirer une éventuelle note du même jour
    # (ré-exécution), ajouter le nouveau en tête, tronquer à MAX_NOTES
    bloc_pattern = re.compile(re.escape(MARKER_START) + r"(.*?)" + re.escape(MARKER_END), re.DOTALL)
    match = bloc_pattern.search(html)
    contenu_existant = match.group(1)
    articles_existants = re.findall(r"<article class=\"note-tech\".*?</article>", contenu_existant, re.DOTALL)

    note_id = nouvel_article.split('id="')[1].split('"')[0]
    articles_existants = [a for a in articles_existants if f'id="{note_id}"' not in a]

    articles = [nouvel_article] + articles_existants
    articles = articles[:MAX_NOTES]

    nouveau_bloc = MARKER_START + "\n" + "\n".join(articles) + "\n" + MARKER_END
    html = bloc_pattern.sub(nouveau_bloc, html)

    with open(target_path, "w", encoding="utf-8") as f:
        f.write(html)

    subprocess.run(["git", "config", "user.email", "veille@officeplus.nc"], cwd=workdir, check=True)
    subprocess.run(["git", "config", "user.name", "Office Plus Veille Tech"], cwd=workdir, check=True)
    subprocess.run(["git", "add", TARGET_FILE], cwd=workdir, check=True)

    subprocess.run(["git", "commit", "-m", f"veille-tech: {note_id}"], cwd=workdir, check=True)

    result = subprocess.run(["git", "push"], cwd=workdir, capture_output=True, text=True)
    if result.returncode != 0:
        raise RuntimeError(result.stderr)

    print(f"SUCCES|{PAGES_URL}/{TARGET_FILE}#{note_id}")

except Exception as e:
    print(f"ERREUR|{e}")
finally:
    if cleanup_dir:
        shutil.rmtree(cleanup_dir, ignore_errors=True)
```

Lire la sortie :
- `SUCCES|...` : confirmer l'URL.
- `ERREUR|...` : afficher l'erreur brute, livrer la note en texte, ne pas interrompre l'exécution.

### 5. Retour

```
Veille tech publiée : {PAGES_URL}/{TARGET_FILE}#{NOTE_ID}
```

---

## Sources (13, par catégorie)

**Modèles IA / Outils IA :**
- https://intelligence-artificielle.com
- https://www.usine-digitale.fr
- https://www.journaldunet.com

**Infra / Cybersécurité / DATA :**
- https://www.lemondeinformatique.fr
- https://www.lemagit.fr/actualites/Virtualisation-de-serveurs
- https://www.silicon.fr
- https://www.itpro.fr

**Décideurs IT / DSI :**
- https://www.alliancy.fr
- https://www.itforbusiness.fr
- https://www.solutions-numeriques.com

**Conseil / Gestion de projet :**
- https://www.consultor.fr
- https://www.manager-go.com
- https://pmi-france.org (blog)

---

## Règles de confiance

- Recency stricte : 24h, pas 48h — mais une source sans nouvel article n'est jamais laissée vide : historique conservé + bandeau explicite.
- Ne jamais inventer le contenu d'une source inaccessible.
- Ne jamais inventer une date de publication non affichée par le site — le signaler plutôt.
- Chaque article résumé doit être accompagné du lien complet vers l'article original.
- Résumés factuels (sujet, faits clés, conclusion), en français, 2-3 phrases.

---

Créé pour Office Plus SARL, Nouméa (Nouvelle-Calédonie).
Sous-ensemble technologique de `veille-marche`, à usage automatisé exclusivement.
