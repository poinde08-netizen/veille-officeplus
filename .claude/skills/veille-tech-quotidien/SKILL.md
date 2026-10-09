---
name: veille-tech-quotidien
description: >
  Veille technologique et IA en entreprise, automatisée, publiée sur GitHub
  Pages : note quotidienne (11 axes, fenêtre de 5 jours glissants avec
  dédoublonnage) et dossier hebdomadaire de fond (business model d'ESN à IA
  souveraine, économie de l'IA, cas d'usage, ROI, souveraineté des données).
  Conçue pour une exécution sans supervision (Routine Claude Code planifiée à
  08h00 UTC+11), pas pour un usage conversationnel manuel. Distincte de
  `veille-marche` (volets commerciaux NC) : ne pas fusionner les deux, ne pas
  publier sur la même page GitHub Pages.
---

# veille-tech-quotidien

Deux publications sur la même page GitHub Pages (`veille-tech.html`), destinées à la direction et aux équipes commerciales et techniques d'Office Plus :

- **Note quotidienne** : actualités des 5 derniers jours, sans répétition de ce qui a déjà été publié.
- **Dossier hebdomadaire** (le lundi) : analyse de fond, hypothèses chiffrées, orientations de décision.

La confidentialité n'est pas un critère de ce projet : la page est publique, c'est un choix assumé.

**Périmètre géographique et linguistique** : France et Europe en priorité, actualité mondiale majeure, Nouvelle-Calédonie. Sources en français en priorité ; sources primaires anglophones (éditeurs, laboratoires, recherche) acceptées et résumées en français.

---

## Périmètre — note quotidienne (11 axes)

| # | Axe | Focus | Classe CSS |
|---|-----|-------|-----------|
| 1 | Modèles IA | OpenAI, Anthropic, Google, Mistral, Meta, modèles open weights, benchmarks LLM | `axis-modeles` |
| 2 | Outils IA | Copilot M365, GitHub Copilot, Gemini Workspace, IA métier | `axis-outils` |
| 3 | Infra / Réseau | Scale Computing, VMware, Dell, HP, Lenovo, Synology, Wi-Fi 6/7 | `axis-infra` |
| 4 | Cybersécurité | Fortinet (firmware, CVE, FortiOS), SASE, Zero Trust, EDR, CERT-FR | `axis-cyber` |
| 5 | DATA | Power BI, Microsoft Fabric, Databricks, Snowflake, gouvernance de la donnée par l'IA (catalogage automatique, qualité, lignage, lakehouse souverain) | `axis-data` |
| 6 | IA en entreprise | Cas d'usage déployés, retours d'expérience, cabinets d'expertise et de conseil, études d'adoption | `axis-ia-ent` |
| 7 | Infrastructure IA souveraine | GPU et accélérateurs (NVIDIA, AMD, Intel), inférence sur site, LLM open weights déployables localement, serveurs IA, clouds souverains (OVHcloud, Scaleway, 3DS Outscale, S3NS, Bleu) | `axis-souverain` |
| 8 | Agents et protocoles | Agents IA, MCP, RAG, fine-tuning, évaluation et observabilité des LLM, sécurité des agents (injection de prompt, fuite de données) | `axis-agents` |
| 9 | Marché des ESN et intégrateurs | Offres « IA souveraine » et annonces de Capgemini, Sopra Steria, Eviden, Orange Business, cabinets de conseil, Numeum | `axis-esn` |
| 10 | Réglementation et conformité IA | AI Act (calendrier d'application), RGPD appliqué à l'IA, CNIL, SecNumCloud, NIS2, DORA, cadre local Nouvelle-Calédonie | `axis-regl` |
| 11 | Appels d'offres et financements | Programmes français et européens de soutien à l'IA, commande publique IA, AO en Nouvelle-Calédonie | `axis-ao` |

Axes exclus (restent dans `veille-marche` sur demande) : concurrence, clients et secteurs de Nouvelle-Calédonie hors IA.

---

## Périmètre — dossier hebdomadaire

Publié le lundi (fuseau UTC+11), en plus de la note quotidienne. Contenu :

1. **Business model d'une ESN à infrastructure IA souveraine** : sources de revenus (abonnement, infogérance IA, projets, licences), structure de coûts (GPU, énergie, hébergement, ingénierie), dimensionnement, risques, comparaison avec les offres concurrentes relevées dans la semaine.
2. **Économie de l'IA** : coût total de possession sur site contre cloud, prix des tokens, tarification des GPU, mouvements de financement du secteur.
3. **Cas d'usage structurés par fonction métier** (finance, RH, juridique, support, commerce, production, DSI) avec le gain annoncé et la source.
4. **Cabinets d'expertise, retour sur investissement et adoption** : synthèses publiques de Gartner, McKinsey, BCG, IDC, Forrester, Bpifrance, baromètres PME.
5. **Souveraineté des données** : tendances, doctrine (cloud de confiance, SecNumCloud, extraterritorialité), décisions de marché.
6. **Gouvernance de la donnée par l'IA** : tendances de fond.
7. **Veille technologique IA** : ce qui change durablement (architectures, matériel, protocoles), à distinguer du bruit de la semaine.
8. **Synthèse décisionnelle** : points à trancher pour la direction ; arguments utilisables par les équipes commerciales et techniques.

Règle de rigueur du dossier : tout chiffre est soit sourcé (URL), soit marqué « hypothèse » avec la méthode de calcul. Un chiffre sans source n'est jamais présenté comme un fait. Les études payantes (Gartner, Forrester, IDC) ne sont citées que par leurs synthèses et communiqués publics.

---

## Configuration

```
GITHUB_USER      = "poinde08-netizen"
GITHUB_REPO      = "veille-officeplus"
PAGES_URL        = "https://poinde08-netizen.github.io/veille-officeplus"
TARGET_FILE      = "veille-tech.html"
MARKER_START     = "<!-- NOTES-TECH -->"          # notes quotidiennes
MARKER_END       = "<!-- /NOTES-TECH -->"
HEBDO_START      = "<!-- DOSSIERS-HEBDO -->"      # dossiers hebdomadaires
HEBDO_END        = "<!-- /DOSSIERS-HEBDO -->"
MAX_NOTES        = 14
MAX_DOSSIERS     = 12
FENETRE_JOURS    = 5
JOUR_DOSSIER     = lundi (weekday() == 0 en UTC+11)
FUSEAU           = UTC+11 (Nouméa)
POLICE           = Raleway
```

**Accès GitHub** : pas de token en clair dans ce fichier.
- **Exécution via Routine Claude Code** : le dépôt est sélectionné à la création de la Routine ; l'accès en écriture est géré par la connexion GitHub native de la Routine.
- **Test manuel (chat claude.ai)** : exporter `GITHUB_TOKEN` (fine-grained PAT, scope `contents:write` sur ce seul dépôt) avant d'invoquer ce skill, jamais dans un fichier versionné.

---

## Déroulement d'exécution

### 1. Initialisation

```python
from datetime import datetime, timezone, timedelta
utc11 = timezone(timedelta(hours=11))
now = datetime.now(utc11)
seuil = now - timedelta(days=5)
est_lundi = now.weekday() == 0
print(f"MAINTENANT={now.strftime('%Y-%m-%d %H:%M UTC+11')}")
print(f"SEUIL_5J={seuil.strftime('%Y-%m-%d %H:%M UTC+11')}")
print(f"DOSSIER_HEBDO={'oui' if est_lundi else 'non'}")
```

Toute information antérieure à `SEUIL_5J` est écartée (sauf signal faible non daté, explicitement marqué comme tel).

### 2. Lecture de l'existant et dédoublonnage

Avant toute rédaction, lire `veille-tech.html` et extraire des notes quotidiennes déjà publiées les 5 derniers jours :

- la date de chaque note ;
- les URL de sources déjà citées (clé de comparaison principale) ;
- les sujets déjà traités (comparaison sémantique en second recours).

Règles :
- Une information dont l'URL ou le sujet figure déjà dans une note des 4 jours précédents n'est **pas** une nouveauté : elle n'est pas rédigée à nouveau.
- Exception : évolution significative (correctif, démenti, nouvelle donnée, passage de rumeur à annonce officielle). L'information est alors republiée comme nouveauté avec la mention « Mise à jour » et la référence à la note initiale.
- Un même événement couvert par plusieurs sources compte pour une seule information ; citer au plus deux sources.

### 3. Collecte par axe

Pour chacun des 11 axes : rechercher dans la fenêtre de 5 jours, vérifier la date de publication, rejeter silencieusement tout résultat antérieur à `SEUIL_5J`, classer par pertinence décroissante. Pour chaque information, distinguer :

- **fait établi / inférence / signal faible** ;
- **annonce officielle / bêta publique / roadmap non confirmée / rumeur**.

Signaler toute source inaccessible sans en inventer le contenu. Pour la Nouvelle-Calédonie, l'absence d'information est signalée comme telle, jamais comblée.

Si un axe n'a aucune information dans la fenêtre : `Aucune information de moins de 5 jours trouvée à la date du [DATE] [HEURE UTC+11].`

### 4. Génération de la note quotidienne

```
NOTE_ID = note-tech-{YYYY-MM-DD}
```

```html
<article class="note-tech" id="{NOTE_ID}">
  <div class="note-header">
    <span class="badge badge-tech">VEILLE TECH</span>
    <span class="note-date">{DATE} {HEURE UTC+11} — Fenêtre 5 jours glissants</span>
  </div>
  <div class="note-body">
    <div class="synthese-5j">
      <h3>Synthèse des 5 jours</h3>
      <ul>{5 à 8 points les plus importants, une ligne chacun}</ul>
    </div>
    {SECTION_1} ... {SECTION_11}
    <div class="confiance">
      <span>Confiance globale</span>
      <span class="conf-bar"><span class="conf-fill" style="width:{CONFIANCE_PCT}%"></span></span>
      <span class="conf-value">{CONFIANCE} / 1,0</span>
    </div>
  </div>
</article>
```

Chaque section :

```html
<section class="{AXIS_CLASS}">
  <h3>[N]. [Axe]</h3>
  <h4>Nouveautés</h4>
  <ul>
    <li><strong>{titre court}</strong> — {résumé factuel, 1 à 3 phrases}. <a href="{URL}">{source}</a> <span class="art-date">{date}</span></li>
  </ul>
  <h4>Signaux faibles</h4>
  {SIGNAUX_EN_HTML}
  <h4 class="alerte">Points d'alerte</h4>
  {ALERTES_EN_HTML}
  <h4>Rappel des jours précédents</h4>
  <ul class="rappel">
    <li>{titre court} — publié le {date de la note d'origine} (voir <a href="#{NOTE_ID_ORIGINE}">note du {date}</a>)</li>
  </ul>
  <div class="conf-row">
    <span class="conf-label">Confiance section</span>
    <span class="conf-bar"><span class="conf-fill" style="width:{CONFIANCE_SECTION_PCT}%"></span></span>
    <span class="conf-value">{CONFIANCE_SECTION} / 1,0</span>
  </div>
</section>
```

- Le bloc « Rappel des jours précédents » liste une ligne par élément (5 au plus par axe), sans résumé détaillé. Il est omis si l'axe n'a rien à rappeler.
- Une « mise à jour » est signalée dans `<li>` par `<span class="maj">Mise à jour</span>`.
- Les blocs « Signaux faibles » et « Points d'alerte » sont omis s'ils sont vides.
- `{CONFIANCE_SECTION_PCT}` et `{CONFIANCE_PCT}` sont la confiance (0,0–1,0) multipliée par 100, arrondie à l'entier.
- Cible de volume : 25 Ko maximum par note, pour garder la page chargeable avec 14 notes.

### 5. Génération du dossier hebdomadaire (lundi uniquement)

```
DOSSIER_ID = dossier-hebdo-{YYYY-MM-DD}
```

```html
<article class="note-hebdo" id="{DOSSIER_ID}">
  <div class="note-header">
    <span class="badge badge-hebdo">DOSSIER HEBDOMADAIRE</span>
    <span class="note-date">Semaine du {DATE} — {HEURE UTC+11}</span>
  </div>
  <div class="note-body">
    <section class="axis-esn"><h3>1. Business model d'une ESN à IA souveraine</h3>...</section>
    <section class="axis-souverain"><h3>2. Économie de l'IA</h3>...</section>
    <section class="axis-ia-ent"><h3>3. Cas d'usage par fonction métier</h3>...</section>
    <section class="axis-modeles"><h3>4. Cabinets d'expertise, ROI et adoption</h3>...</section>
    <section class="axis-regl"><h3>5. Souveraineté des données</h3>...</section>
    <section class="axis-data"><h3>6. Gouvernance de la donnée par l'IA</h3>...</section>
    <section class="axis-agents"><h3>7. Veille technologique IA</h3>...</section>
    <section class="axis-ao"><h3>8. Synthèse décisionnelle</h3>
      <h4>Pour la direction</h4>...
      <h4>Pour les équipes commerciales et techniques</h4>...
    </section>
    <div class="confiance">...</div>
  </div>
</article>
```

Les hypothèses chiffrées sont présentées dans un tableau HTML (`<table class="hypotheses">`) avec les colonnes : hypothèse, valeur, méthode ou source, niveau de confiance. Le dossier s'appuie sur les notes quotidiennes de la semaine écoulée (elles sont déjà dans `veille-tech.html`) et sur des recherches complémentaires.

### 6. Publication — accumulation, pas remplacement

**Important** : la mise en forme (CSS par thématique, police Raleway, légende, barres de confiance) vit dans le `<head>` et le début du `<body>` de `veille-tech.html`, en dehors des marqueurs. Le script ne touche jamais cette zone. Ne la régénérer que si le fichier n'existe pas.

```python
import subprocess, os, tempfile, shutil, re

GITHUB_USER  = "poinde08-netizen"
GITHUB_REPO  = "veille-officeplus"
TARGET_FILE  = "veille-tech.html"
PAGES_URL    = f"https://{GITHUB_USER}.github.io/{GITHUB_REPO}"

BLOCS = {
    "note":    ("<!-- NOTES-TECH -->",     "<!-- /NOTES-TECH -->",     r"<article class=\"note-tech\".*?</article>",  14),
    "hebdo":   ("<!-- DOSSIERS-HEBDO -->", "<!-- /DOSSIERS-HEBDO -->", r"<article class=\"note-hebdo\".*?</article>", 12),
}

nouvel_article = """{ARTICLE_HTML}"""          # note quotidienne
dossier_article = """{DOSSIER_HTML}"""         # vide si ce n'est pas lundi

def is_git_repo(path):
    return subprocess.run(["git", "rev-parse", "--show-toplevel"], cwd=path,
                           capture_output=True, text=True).returncode == 0

def inserer(html, cle, article):
    start, end, motif, maxi = BLOCS[cle]
    if start not in html or end not in html:
        raise ValueError(f"Marqueurs {start} introuvables dans {TARGET_FILE}")
    pattern = re.compile(re.escape(start) + r"(.*?)" + re.escape(end), re.DOTALL)
    existants = re.findall(motif, pattern.search(html).group(1), re.DOTALL)
    articles = ([article] + existants)[:maxi]
    bloc = start + "\n" + "\n".join(articles) + "\n" + end
    return pattern.sub(lambda m: bloc, html)

cwd = os.getcwd()
cleanup_dir = None

if is_git_repo(cwd):
    workdir = subprocess.run(["git", "rev-parse", "--show-toplevel"], cwd=cwd,
                              capture_output=True, text=True).stdout.strip()
else:
    token = os.environ.get("GITHUB_TOKEN", "").strip()
    if not token:
        raise RuntimeError("GITHUB_TOKEN absent. Hors contexte Routine, exporter la variable avant d'invoquer ce skill.")
    remote_url = f"https://{GITHUB_USER}:{token}@github.com/{GITHUB_USER}/{GITHUB_REPO}.git"
    workdir = tempfile.mkdtemp()
    cleanup_dir = workdir
    subprocess.run(["git", "clone", "--depth", "1", remote_url, workdir], check=True, capture_output=True)

try:
    target_path = os.path.join(workdir, TARGET_FILE)
    with open(target_path, "r", encoding="utf-8") as f:
        html = f.read()

    html = inserer(html, "note", nouvel_article)
    if dossier_article.strip():
        html = inserer(html, "hebdo", dossier_article)

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

Le workflow GitHub `auto-merge-veille-tech.yml` ne fusionne automatiquement que si `veille-tech.html` est le seul fichier modifié : la Routine ne doit donc modifier aucun autre fichier.

### 7. Retour

```
Veille tech publiée : {PAGES_URL}/{TARGET_FILE}#{NOTE_ID}
Dossier hebdomadaire : {PAGES_URL}/{TARGET_FILE}#{DOSSIER_ID}   (lundi uniquement)
```

---

## Sources prioritaires par axe

Privilégier les sources primaires (éditeur, régulateur, dépôt de code) puis la presse spécialisée. Une source inaccessible est signalée, jamais inventée.

**1. Modèles IA** : blogs et pages d'actualité d'OpenAI, Anthropic, Google DeepMind, Mistral AI, Meta AI, Hugging Face ; arXiv (cs.AI, cs.CL) ; classements publics (LMArena, Artificial Analysis) ; https://www.usine-digitale.fr, https://intelligence-artificielle.com.

**2. Outils IA** : blog Microsoft 365, https://github.blog/changelog, blog Google Workspace ; https://www.lemondeinformatique.fr, https://www.journaldunet.com.

**3. Infra / Réseau** : communiqués Scale Computing, Dell, HP, Lenovo, Synology, Broadcom/VMware ; https://www.lemagit.fr/actualites/Virtualisation-de-serveurs, https://www.silicon.fr, https://www.itpro.fr.

**4. Cybersécurité** : CERT-FR (https://www.cert.ssi.gouv.fr), ANSSI (https://cyber.gouv.fr), FortiGuard PSIRT (https://www.fortiguard.com/psirt), CISA Known Exploited Vulnerabilities (https://www.cisa.gov) ; https://www.silicon.fr, https://www.itpro.fr.

**5. DATA** : blogs Microsoft Fabric / Power BI, Databricks, Snowflake ; https://www.lemondeinformatique.fr, https://www.silicon.fr.

**6. IA en entreprise** : synthèses publiques de Gartner, McKinsey, BCG, IDC, Forrester, Bpifrance ; https://www.lemondeinformatique.fr, https://www.journaldunet.com, https://www.itforbusiness.fr.

**7. Infrastructure IA souveraine** : blog NVIDIA ; pages presse d'OVHcloud, Scaleway, 3DS Outscale, S3NS, Bleu ; fabricants de serveurs (Dell, HP, Lenovo, Supermicro) ; https://www.lemagit.fr.

**8. Agents et protocoles** : https://modelcontextprotocol.io et son dépôt GitHub ; documentation des principaux frameworks d'agents ; OWASP Top 10 for LLM Applications.

**9. Marché des ESN et intégrateurs** : communiqués Capgemini, Sopra Steria, Eviden, Orange Business ; Numeum (https://numeum.fr) ; https://www.lemondeinformatique.fr, https://www.silicon.fr.

**10. Réglementation et conformité IA** : AI Office de la Commission européenne, EUR-Lex (https://eur-lex.europa.eu), CNIL (https://www.cnil.fr), ANSSI, gouvernement de la Nouvelle-Calédonie (https://gouv.nc).

**11. Appels d'offres et financements** : BOAMP (https://www.boamp.fr), TED (https://ted.europa.eu), Bpifrance, France 2030 ; sources locales : gouv.nc, province-sud.nc, province-nord.nc, cci.nc, opt.nc.

---

## Règles de confiance

- Fenêtre : 5 jours glissants, avec dédoublonnage (voir étape 2).
- Distinguer fait établi / inférence / signal faible.
- Distinguer annonce officielle / bêta publique / roadmap non confirmée / rumeur.
- Ne jamais présenter une inférence ou une communication d'éditeur non vérifiée comme un fait établi.
- Chaque information porte l'URL de sa source.
- Classer les sources par pertinence décroissante.
- Confiance par section et confiance globale (0,0 à 1,0).
- Un chiffre sans source est une hypothèse, et il est marqué comme telle.

---

Créé pour Office Plus SARL, Nouméa (Nouvelle-Calédonie).
Sous-ensemble technologique de `veille-marche`, à usage automatisé exclusivement.
