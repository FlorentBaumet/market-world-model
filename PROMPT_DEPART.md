# 🚀 Prompt de départ — projet MIRAGE (world model de marché)

> **Mode d'emploi** : ouvre une **nouvelle session Claude Code** dans le dossier `Market_World_Model`, et colle le bloc ci-dessous comme premier message. Il est auto-suffisant.

---

```
Tu m'aides à construire MIRAGE : un WORLD MODEL d'un marché financier (carnet
d'ordres / prix, action-conditionné via l'impact), évalué honnêtement ; puis,
EN BONUS, un agent basé-modèle pour tester s'il existe un edge, en distinguant
un vrai edge d'un "model exploitation".

AVANT TOUT : lis le fichier `PROJET_MARKET_WORLD_MODEL.md` dans ce dossier — c'est
la spec complète. Tout ce qui suit en découle.

⚠️ QUATRE INVARIANTS À NE JAMAIS VIOLER :
  I1. Le WORLD MODEL d'abord (Stage 1). L'agent/edge est BONUS (Stage 2). Pas d'over-scope.
  I2. ZÉRO argent réel, ZÉRO trade réel. Tout en simulation ou sur données historiques.
  I3. L'ÉVALUATION HONNÊTE est la colonne vertébrale : pas de lookahead, pas de data
      snooping, split temporel strict, out-of-sample. (C'est le cœur du projet.)
  I4. Tractable / low-compute : modèle PETIT, données basse dimension.
  Si une idée viole I1-I4, elle est hors-scope.

CONTEXTE : c'est une pièce de PORTFOLIO (candidatures stage recherche, automne 2026),
pas un projet pour gagner de l'argent. J'ai déjà de l'expérience crypto/microstructure
(un bot de funding-rate arbitrage) et une obsession pour l'évaluation objective/anti-biais
(repérer lookahead bias, overfitting, etc.).

RÈGLES DE TRAVAIL :
  - Commence SIMPLE. On ne code pas tout le pipeline d'un coup.
  - La première chose = la PHASE 0 (voir la spec) : un world model SIMPLE bat-il une
    baseline naïve (random walk / prix inchangé) en out-of-sample, évalué honnêtement ?
  - On FIGE le protocole d'évaluation AVANT d'entraîner quoi que ce soit.
  - Le piège central = le MODEL EXPLOITATION : un edge trouvé "dans le modèle" doit
    toujours être validé sur données réelles tenues à part. "Pas d'edge" est un résultat valide.
  - Intègre toujours frais + slippage dans tout test d'edge.
  - Suivi des expériences : wandb (déjà configuré chez moi).
  - Demande-moi avant toute décision majeure (voir "Décisions kickoff" de la spec).

CE QUE JE FOURNIRAI / À DÉCIDER ENSEMBLE :
  - Le marché/données : crypto historique (mon terrain) vs simulateur ABIDES vs LOBSTER.
  - L'approche d'action-conditioning (modèle d'impact vs sim).
  - La forme du world model (séquence simple d'abord, ou RSSM façon Dreamer).

PREMIÈRE TÂCHE CONCRÈTE (Phase 0) :
  1. Propose une arborescence de repo minimale (Python) + l'environnement (deps).
  2. Pose-moi les questions de "Décisions kickoff" (données, action-conditioning, forme).
  3. Conçois AVEC moi le protocole d'évaluation honnête (split temporel, walk-forward,
     baselines) — AVANT tout entraînement.
  4. Puis prototype : un mini world model qui bat (ou non) la baseline en out-of-sample,
     avec une éval propre. Conclus par un Go/No-Go documenté.

Ne code pas encore : commence par lire la spec, me poser les questions de kickoff,
et proposer le protocole d'évaluation.
```

---

### Notes pour Florent (hors prompt)
- Garde la spec `PROJET_MARKET_WORLD_MODEL.md` à la racine — le prompt s'appuie dessus.
- **Priorité = Stage 1** (le world model + l'éval). Le Stage 2 (agent/edge) ne se lance que si le Stage 1 est propre.
- **Rappel portfolio** : ce projet est la **pièce #3 (bonus)**. Le **cœur** (PR LeRobot + Claude Trading + SPOTTER) passe **avant**.
