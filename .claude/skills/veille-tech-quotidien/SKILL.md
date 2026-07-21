---
name: veille-tech-quotidien
description: >
  Veille technologique automatisée quotidienne, limitée à 5 axes : Modèles IA,
  Outils IA, Infra/Réseau, Cybersécurité, DATA. Conçue pour une exécution sans
  supervision (Routine Claude Code planifiée à 08h00 UTC+11), pas pour un usage
  conversationnel manuel. Distincte de `veille-marche` (10 axes, incluant les
  volets commerciaux NC) : ne pas fusionner les deux, ne pas publier sur la
  même page GitHub Pages.
---

# veille-tech-quotidien

Note quotidienne courte, publiée automatiquement sur une page GitHub Pages dédiée, distincte de la veille commerciale complète.

**Fenêtre temporelle** : 24 heures glissantes (pas 48h — cadence quotidienne, pas de recouvrement voulu). Si aucune information récente pour un axe : `Aucune information de moins de 24 h trouvée à la date du [DATE] [HEURE UTC+11].`

**Accumulation** : contrairement à `veille-marche` (qui remplace tout à chaque run), cette note s'ajoute en tête de la page à chaque exécution. Les 14 dernières notes sont conservées ; au-delà, la plus ancienne est retirée.

---

## Périmètre (5 axes, sous-ensemble de veille-marche)

| # | Axe | Focus |
|---|-----|-------|
| 1 | Modèles IA | OpenAI, Anthropic, Google, Mistral, Meta, benchmarks LLM |
| 2 | Outils IA | Copilot M365, GitHub Copilot, Gemini Workspace, IA métier |
| 3 | Infra / Réseau | Scale Computing, VMware, Dell, HP, Lenovo, Synology, Wi-Fi 6/7 |
| 4 | Cybersécurité | Fortinet (firmware, CVE, FortiOS), SASE, Zero Trust, EDR, CERT |
| 5 | DATA | Power BI, Microsoft Fabric, Databricks, gouvernance données |

Axes exclus volontairement (hors périmètre de cette veille quotidienne, restent dans `veille-marche` sur demande) : Concurrentielle NC, Client NC, Sectorielle NC, AO NC, Réglementation.

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

Toute information antérieure à `SEUIL_24H` est écartée (sauf signal faible non daté, explicitement marqué comme tel).

### 2. Collecte par axe

Pour chacun des 5 axes : rechercher, vérifier la date de publication, rejeter silencieusement tout résultat antérieur à `SEUIL_24H`, classer par pertinence décroissante, distinguer fait établi / inférence / signal faible, et pour chaque axe distinguer annonce officielle / bêta publique / roadmap non confirmée / rumeur. Signaler toute source inaccessible sans en inventer le contenu.

**Orientation métier (obligatoire)** : cette veille est lue par un dirigeant/responsable, pas par un ingénieur. Pour chaque axe :
- Faire au moins deux passes de recherche : une technique (annonce, changelog, CVE) et une orientée usage/business (« pour PME », « entreprise », « adoption », « impact tarifaire », « ROI », « cas d'usage métier ») — la seule requête technique appauvrit systématiquement le contenu.
- Ne pas se limiter à la liste brute de faits : chaque section se termine par un paragraphe `Implication métier` qui traduit ce qui précède en langage décisionnel — qu'est-ce que ça change pour l'entreprise (outil disponible dès maintenant, budget à anticiper, risque à traiter, décision à envisager) ? 2-4 phrases, factuel, sans emphase commerciale artificielle.
- Un contexte utile mais hors fenêtre 24 h (ex. hausse tarifaire entrée en vigueur le mois précédent, tendance d'adoption sectorielle) peut être cité dans `Implication métier` à condition d'être explicitement daté et non présenté comme un fait du jour — il sert à éclairer, pas à combler artificiellement l'absence d'actualité fraîche.
- Si un axe reste réellement sans rien de neuf ni de contextualisable, garder la phrase standard sans forcer du contenu creux.

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
    {SECTION_MODELES_IA}
    {SECTION_OUTILS_IA}
    {SECTION_INFRA}
    {SECTION_CYBERSECURITE}
    {SECTION_DATA}
    <div class="confiance">
      <span>Confiance globale</span>
      <span class="conf-bar"><span class="conf-fill" style="width:{CONFIANCE_PCT}%"></span></span>
      <span class="conf-value">{CONFIANCE} / 1,0</span>
    </div>
  </div>
</article>
```

Chaque section utilise une classe `axis-*` dédiée (reprise par le CSS déjà présent dans le `<head>` de `veille-tech.html` pour distinguer visuellement chaque thématique — ne pas retirer ce `<head>` lors des publications, le script de l'étape 4 ne touche que le bloc entre les marqueurs) :

| Axe | Classe |
|---|---|
| 1. Modèles IA | `axis-modeles` |
| 2. Outils IA | `axis-outils` |
| 3. Infra / Réseau | `axis-infra` |
| 4. Cybersécurité | `axis-cyber` |
| 5. DATA | `axis-data` |

```html
<section class="{AXIS_CLASS}">
  <h3>[N]. [Axe]</h3>
  <h4>Faits établis (&lt; 24 h)</h4>
  {FAITS_EN_HTML}
  <h4>Signaux faibles</h4>
  {SIGNAUX_EN_HTML}
  <h4 class="alerte">Points d'alerte</h4>
  {ALERTES_EN_HTML}
  <h4>Implication métier</h4>
  <p>{IMPLICATION_METIER}</p>
  <div class="conf-row">
    <span class="conf-label">Confiance section</span>
    <span class="conf-bar"><span class="conf-fill" style="width:{CONFIANCE_SECTION_PCT}%"></span></span>
    <span class="conf-value">{CONFIANCE_SECTION} / 1,0</span>
  </div>
</section>
```

`{CONFIANCE_SECTION_PCT}` et `{CONFIANCE_PCT}` sont la confiance (0,0–1,0) multipliée par 100, arrondie à l'entier (ex. 0,6 → `60`).

### 4. Publication — accumulation, pas remplacement

Exécuter via `bash_tool` (adapter `{ARTICLE_HTML}` avec le bloc de l'étape 3) :

**Important** : la mise en forme (CSS par thématique, légende de couleurs, barres de confiance) vit dans le `<head>` et le début du `<body>` de `veille-tech.html`, en dehors des marqueurs `NOTES-TECH` — le script ci-dessous ne touche jamais cette zone. Ne pas la régénérer sauf si `veille-tech.html` n'existe pas encore (squelette de secours ci-dessous), auquel cas reprendre le `<head>`/légende déjà déployés sur `https://poinde08-netizen.github.io/veille-officeplus/veille-tech.html` (vue source) pour ne pas perdre le style.

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

    # Extraire les articles existants, ajouter le nouveau en tête, tronquer à MAX_NOTES
    bloc_pattern = re.compile(re.escape(MARKER_START) + r"(.*?)" + re.escape(MARKER_END), re.DOTALL)
    match = bloc_pattern.search(html)
    contenu_existant = match.group(1)
    articles_existants = re.findall(r"<article class=\"note-tech\".*?</article>", contenu_existant, re.DOTALL)

    articles = [nouvel_article] + articles_existants
    articles = articles[:MAX_NOTES]

    nouveau_bloc = MARKER_START + "\n" + "\n".join(articles) + "\n" + MARKER_END
    html = bloc_pattern.sub(nouveau_bloc, html)

    with open(target_path, "w", encoding="utf-8") as f:
        f.write(html)

    subprocess.run(["git", "config", "user.email", "veille@officeplus.nc"], cwd=workdir, check=True)
    subprocess.run(["git", "config", "user.name", "Office Plus Veille Tech"], cwd=workdir, check=True)
    subprocess.run(["git", "add", TARGET_FILE], cwd=workdir, check=True)

    note_id = nouvel_article.split('id="')[1].split('"')[0]
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

## Sources prioritaires par axe

**Modèles IA :** https://openai.com/blog, https://anthropic.com/news, https://blog.google, https://mistral.ai/news, Hugging Face leaderboard, Papers with Code

**Outils IA :** https://techcommunity.microsoft.com, blogs officiels éditeurs

**Infra / Réseau :** sites constructeurs Dell, HP, Lenovo, Scale Computing, Synology, Cisco ; VMware by Broadcom blog, NetworkWorld

**Cybersécurité :** https://www.fortinet.com/blog, https://www.cert.ssi.gouv.fr, CVE Details, Bleeping Computer, Krebs on Security, CISA Alerts

**DATA :** https://learn.microsoft.com, Databricks blog, dbt Labs blog, Towards Data Science, Data Engineering Weekly

---

## Règles de confiance

- Recency stricte : 24h, pas 48h.
- Distinguer fait établi / inférence / signal faible.
- Distinguer annonce officielle / bêta publique / roadmap non confirmée / rumeur.
- Ne jamais présenter une inférence comme un fait établi.
- Classer les sources par pertinence décroissante.
- Confiance par section + confiance globale (0,0 à 1,0).

---

Créé pour Office Plus SARL, Nouméa (Nouvelle-Calédonie).
Sous-ensemble technologique de `veille-marche`, à usage automatisé exclusivement.
