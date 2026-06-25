# 🌐 MIRAGE — World model de marché (+ test d'edge, en bonus)

> **Pitch** : construire un **world model d'un marché** (carnet d'ordres / prix, action-conditionné via l'impact) et **l'évaluer honnêtement** ; puis, *en bonus*, y greffer un **agent basé-modèle** pour voir s'il existe un edge — en distinguant rigoureusement un **vrai edge** d'un **model exploitation** (un mirage). D'où le nom.
>
> **Codename** : MIRAGE *(le cœur du projet = ne pas se laisser berner par un edge illusoire — libre à toi de renommer).*
> **Auteur** : Florent · **Type** : pièce de portfolio #3 (world-models + ton angle éval-sans-biais) · **Statut** : spec, à exécuter dans une session dédiée.

---

## 0. Pourquoi ce projet (contexte)
- **But réel = PORTFOLIO** (candidatures TFE recherche, automne 2026). Pas faire du fric, pas un projet parfait.
- **Pourquoi celui-ci** : il **unifie** ton thème (world models), ta vraie passion (la finance — Claude Trading, Funding-Arb…), et **ta question signature** (évaluer objectivement, sans biais).
- **Avantage clé** : un world model de **marché** est **basse dimension** (≠ vidéo) → **constructible from scratch en solo**, sur 1 GPU voire CPU. La finance contourne le mur du « world model trop dur ».

---

## 1. ⚠️ Les invariants (ne jamais violer)
| # | Invariant | Pourquoi |
|---|---|---|
| **I1** | **Le WORLD MODEL d'abord (Stage 1) ; l'agent/edge est BONUS (Stage 2).** | C'est une pièce de portfolio — le world model + son éval honnête se suffit. Pas d'over-scope. |
| **I2** | **ZÉRO argent réel, ZÉRO trade réel.** Tout en **simulation** ou sur **données historiques**. | Zéro capital, zéro risque, **reproductible** (parfait pour un repo public). |
| **I3** | **L'évaluation honnête est la COLONNE VERTÉBRALE** : pas de lookahead, pas de data snooping, split temporel strict, out-of-sample. | C'est *ta Q1* — et c'est ce qui fait la valeur du projet. |
| **I4** | **Tractable / low-compute** : modèle **petit**, données basse dimension. | Solo, quelques mois, pas un monstre. |

> Si une feature viole I1-I4, elle est hors-scope.

---

## 2. Ce que fait le projet (les deux stages)

**Stage 1 — LE LIVRABLE : le world model + son éval honnête.**
Un modèle qui, étant donné l'**état du marché** (carnet/prix) + une **action** (ton ordre/position), **prédit l'état futur** — *y compris l'impact de ton ordre*. Puis tu **évalues honnêtement** sa qualité prédictive (out-of-sample, anti-lookahead). **Pièce complète et crédible en soi.**

**Stage 2 — BONUS : l'agent + le test d'edge.**
Tu entraînes/planifies un **agent basé-modèle** *dans* le world model (paradigme Dreamer), tu regardes s'il trouve un edge, **et tu valides sur données réelles tenues à part** pour distinguer un **vrai edge** d'un **model exploitation** (l'agent exploite les *erreurs* de ton modèle, pas un vrai signal).

> **La valeur n'est pas « j'ai trouvé un edge »** (rare). C'est **« j'ai construit un world model de marché ET montré rigoureusement si l'edge est réel ou un mirage »**. C'est le skill rare du quant.

---

## 3. Architecture

```
Données (sim ou historique) ──► [World Model] ──► prédiction de l'état futur (action-conditionnée)
                                       │
                                       ├──► [Évaluation honnête]  ◄── LA colonne vertébrale (Stage 1)
                                       │
                                       └──► [Agent basé-modèle] ──► edge ? ──► [Validation hors-échantillon] (Stage 2 bonus)
                                                                                  └─ vrai edge vs model exploitation
```

### 3.1 Données / environnement (décision kickoff)
- **Option A — Données historiques crypto** *(recommandé : tu connais via Funding-Arb)* : carnet d'ordres / OHLCV crypto (via ccxt / un exchange). Réaliste, ton terrain. L'**impact de tes ordres** = overlay via un **modèle d'impact** (tu « manges » les niveaux du carnet + impact temporaire).
- **Option B — Simulateur ABIDES** : marché simulé où tes ordres ont un **impact natif** (action-conditioning propre, sans modèle d'impact à bricoler). Plus « propre » côté RL, moins « réel ».
- **Option C — Données LOB académiques (LOBSTER)** : carnet réel haute résolution (actions).

### 3.2 Le world model
- **État** : features du carnet / prix (+ ta position/portefeuille).
- **Action** : ordre (acheter/vendre/taille) ou position cible.
- **Sortie** : état futur (évolution prix/carnet) **incluant l'impact de l'action**.
- **Forme** : petit modèle de séquence (MLP / RNN / petit Transformer), ou **RSSM façon Dreamer** si tu vises directement le model-based RL. Commence **simple**.

### 3.3 L'évaluation (la colonne vertébrale — détaillée §6)
Métriques de prédiction **out-of-sample**, split temporel strict, baselines, et (Stage 2) détection du model exploitation.

---

## 4. Roadmap (phases + definition of done)

### Phase 0 — MVP / hypothèse la plus risquée
- **But** : un world model **simple** prédit-il l'évolution du marché **mieux qu'une baseline naïve** (ex. « le prix reste identique » / random walk), **évalué honnêtement** (out-of-sample) ?
- **DoD** : un notebook qui entraîne un mini-modèle + une éval out-of-sample propre + comparaison baseline. **Go/No-Go documenté.**

### Phase 1 — Le world model + éval honnête *(LE livrable)*
- World model action-conditionné + protocole d'éval béton (anti-lookahead, walk-forward).
- **DoD** : repo propre, le modèle prédit, l'éval est honnête et reproductible, write-up des résultats (y compris « le marché est dur, voilà ce qui est prédictible et ce qui ne l'est pas »).

### Phase 2 — BONUS : agent basé-modèle + test d'edge
- Agent qui plan/apprend dans le world model → cherche un edge → **validation hors-échantillon** → verdict **vrai edge vs model exploitation**.
- **DoD** : l'agent tourne, ET tu démontres rigoureusement si l'edge survit au réel. (Le « non, c'était du model exploitation » est un **résultat valide et publiable**.)

### Phase 3 — Packaging portfolio
- Repo + README + write-up + (option) note de blog/arXiv.
- **DoD** : un chercheur clique, comprend en 2 min, et voit « code propre + vraie question + éval honnête ».

---

## 5. ⚠️ Risques & pièges (le cœur intellectuel)
| Piège | Parade |
|---|---|
| **Model exploitation** (l'agent trouve un edge dans le MODÈLE, pas dans le réel) | **Toujours valider sur données réelles tenues à part.** C'est LE point. |
| **Lookahead bias / data snooping** (tu utilises du futur sans le savoir) | Split temporel strict, walk-forward, features causales uniquement, pré-enregistrer la métrique. |
| **Overfitting du backtest** | Out-of-sample, peu de réglages, honnêteté sur le nombre d'essais. |
| **Marché bruité → world model faible** | C'est *attendu* : reporte honnêtement ce qui est prédictible vs pas. Un modèle faible bien évalué > un modèle « génial » mal évalué. |

---

## 6. Discipline d'évaluation (à ne pas négocier)
- **Split temporel** : train sur le passé, test sur le futur (jamais l'inverse).
- **Walk-forward** : ré-entraîne/évalue en fenêtres glissantes.
- **Baselines obligatoires** : random walk, « prix inchangé », un modèle linéaire trivial. Ton modèle doit les battre *out-of-sample* pour valoir quelque chose.
- **Anti-lookahead** : aucune feature ne doit contenir d'information future (vérifie les fuites comme dans ton stage physio).
- **Stage 2** : compare l'edge **dans le world model** vs **sur données réelles** → l'écart = la mesure du model exploitation.
- **Coûts** : intègre frais + slippage dans tout test d'edge (un edge brut ≠ un edge net).

---

## 7. Hors-scope (non-goals)
- ❌ Trader du **vrai argent** / exécuter de vrais ordres.
- ❌ Course HFT / latence.
- ❌ Promettre / forcer un edge (« si pas d'edge » = un résultat, pas un échec).
- ❌ Sur-scoper le Stage 2 avant que le Stage 1 soit propre.

---

## 8. Comment ça « se vend » (portfolio)
- Démontre : **world model construit from scratch + model-based RL (bonus) + évaluation rigoureuse anti-biais + domaine finance maîtrisé.**
- **Le fil rouge** : ta Q1 (objectivité/anti-biais) appliquée à un domaine où c'est *le* problème n°1 (le quant). Différenciateur fort.
- **Pitch (même si non-aligné NAVER/valeo)** : *« planification basée-modèle quand mes actions affectent le monde, + un protocole d'évaluation qui distingue un vrai signal d'une auto-illusion »* → tu montres que tu transposes le concept world-model **et** que tu as la rigueur d'éval.

---

## 9. Décisions à prendre au kickoff
- [ ] **Marché/données** : crypto historique (recommandé, ton XP) vs ABIDES (impact natif) vs LOBSTER.
- [ ] **Action-conditioning** : modèle d'impact sur données historiques, ou sim.
- [ ] **Forme du world model** : séquence simple (MLP/RNN) d'abord, ou RSSM Dreamer direct.
- [ ] **Métrique cible** Phase 0 + baselines.
- [ ] Figer le **protocole d'éval** AVANT d'entraîner quoi que ce soit.
