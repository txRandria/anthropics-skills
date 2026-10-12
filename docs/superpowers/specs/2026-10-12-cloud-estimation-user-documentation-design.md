# Documentation utilisateur de la suite Cloud Estimation

**Date :** 2026-10-12  
**Statut :** conception validée, prête pour revue  
**Portée :** documentation publique de la suite de onze skills Cloud Estimation

## 1. Objectif

La documentation doit permettre à une personne qui découvre le dépôt de répondre sans lire les onze `SKILL.md` aux questions suivantes :

1. Pourquoi utiliser cette suite plutôt qu'un tableur libre ou une estimation non structurée ?
2. Quel skill choisir pour une demande donnée ?
3. Quelles informations fournir à l'agent ?
4. Comment obtenir et contrôler le livrable XLSX homogène ?
5. Quelles limites empêchent de présenter une hypothèse comme un prix ou une décision fournisseur vérifiée ?

Le parcours principal cible Codex et Claude Code. Les instructions restent génériques lorsque le mécanisme est commun aux deux outils.

## 2. Public visé

- architectes cloud, Kubernetes et infrastructure ;
- équipes DevOps, CI/CD, sécurité et FinOps ;
- responsables techniques préparant un budget ou un arbitrage ;
- consultants qui doivent livrer un chiffrage traçable et anonymisable ;
- utilisateurs non spécialistes qui ont besoin d'un prompt guidé et d'un XLSX explicable.

La documentation n'exige pas une expertise préalable des onze domaines. Elle explique les concepts indispensables : JH, JH/mois, Build, Run, hypothèse, source, scénario et blocage.

## 3. Livrables documentaires

### 3.1 Guide français

Créer `docs/guide-cloud-estimation.md`. Il constitue la référence principale demandée par le mainteneur et contient le parcours complet, les tableaux de décision et les exemples de prompts.

### 3.2 Guide anglais

Créer `docs/cloud-estimation-guide.md`. Il reprend le même contrat d'information et les mêmes exemples, adaptés en anglais. Les deux guides doivent rester structurellement alignés, sans traduction automatique non relue.

### 3.3 Points d'entrée

Mettre à jour :

- `README.md` avec une section française présentant la suite et pointant vers le guide français ;
- `README.en.md` avec la section équivalente et le guide anglais ;
- `skill-updates/2026-10-07/cloud-estimation-suite/README.md` avec un démarrage rapide et des liens vers les deux guides.

Les six skills de sécurité déjà présents dans le dépôt restent décrits séparément. La documentation ne doit pas les renommer ni laisser entendre qu'ils appartiennent aux onze skills de chiffrage.

## 4. Structure des guides

Les deux guides utilisent les mêmes sections, dans cet ordre :

1. **Pourquoi utiliser la suite** — homogénéité, traçabilité, séparation des hypothèses et faits, rapprochement Build/Run/cloud et prévention du double comptage.
2. **Ce que la suite produit** — classeur XLSX éditable, JH et JH/mois, coûts en EUR, scénarios, décisions, risques, sources et diagramme.
3. **Choisir le bon skill** — tableau des onze skills avec déclencheur, raison d'utilisation, entrées minimales et résultat attendu.
4. **Installation** — installation depuis les bundles `.skill`, vérification des hashes et emplacement pour Codex ou Claude Code.
5. **Préparer le brief** — périmètre, environnements, charge, disponibilité, sécurité, pipeline, coûts, contraintes et sources.
6. **Parcours standard** — choisir ou orchestrer, fournir les données, produire, recalculer, valider et faire approuver le XLSX.
7. **Prompts copiables** — un prompt minimal et un prompt complet pour l'orchestrateur, puis un prompt ciblé pour chacun des dix skills spécialisés.
8. **Trois exemples de bout en bout** — chiffrage complet, optimisation FinOps, comparaison architecture/provider.
9. **Lire et contrôler le XLSX** — rôle des douze feuilles, cellules d'entrée, formules, décisions bloquantes et contrôles avant diffusion.
10. **Limites et sécurité** — données manquantes, prix datés, absence d'autorisation de modifier le cloud, anonymisation et validation humaine.
11. **Dépannage** — skill non détecté, dépendance manquante, recalcul natif indisponible, prix inconnu et résultat bloqué.

## 5. Contrat de sélection des skills

Chaque ligne du tableau de sélection doit contenir exactement :

- le nom du skill ;
- « utiliser quand » ;
- « pourquoi » ;
- les entrées minimales ;
- le résultat distinctif.

Le guide doit rendre les règles suivantes explicites :

- utiliser `cloud-estimation-orchestrator` lorsqu'au moins deux domaines se chevauchent ou lorsqu'un livrable consolidé est attendu ;
- appeler directement un skill spécialisé lorsque le périmètre est unique et déjà délimité ;
- ne jamais additionner plusieurs scénarios alternatifs dans un même budget ;
- attribuer chaque tâche à un propriétaire canonique afin d'éviter le double comptage ;
- conserver « à chiffrer » ou un blocage explicite lorsqu'une donnée financière manque.

## 6. Contrat des exemples de prompts

Chaque prompt copiable doit préciser :

- le contexte et le périmètre ;
- les données disponibles et celles qui sont inconnues ;
- le ou les scénarios à comparer ;
- l'unité JH et, si connu, le TJM ;
- le livrable obligatoire `.xlsx` ;
- l'exigence de citer les sources et de signaler les hypothèses ;
- l'interdiction d'inventer un prix, une éligibilité régionale ou une topologie ;
- la demande de décisions, risques et critères d'acceptation vérifiables.

Les exemples utilisent uniquement des noms neutres, des domaines réservés et des valeurs synthétiques. Aucun chemin local, client, compte cloud, tarif réel non daté ou source privée ne doit apparaître.

## 7. Installation et exécution

La documentation distingue :

- l'installation d'un bundle autonome dans le répertoire des skills ;
- l'utilisation du dépôt d'auteur pour régénérer et tester les bundles ;
- l'utilisation normale par prompt, qui ne nécessite pas d'exécuter manuellement les scripts internes.

Les commandes d'installation ne doivent pas écraser silencieusement une installation existante. Les guides demandent de comparer les hashes et d'examiner la destination avant remplacement.

## 8. Contrat du livrable XLSX

Les guides expliquent les douze feuilles sans recopier toute la spécification technique. Ils indiquent notamment :

- `04_Taches_JH` comme registre canonique des charges Build et Run ;
- `05_Couts_cloud` pour les coûts fournisseur distincts des coûts humains ;
- `06_Scenarios` pour comparer des alternatives de même périmètre ;
- `09_Arbitrages` pour les décisions et blocages ;
- `10_Sources` pour la provenance et la date des faits ;
- `11_Synthese` pour le résultat consolidé sans masquer les inconnues.

Le guide rappelle que le classeur est un support de décision éditable, pas un devis fournisseur ni une approbation de production.

## 9. Validation documentaire

La modification est acceptée si :

1. les deux guides contiennent les onze skills et les onze noms correspondent au registre ;
2. un lecteur peut choisir le bon skill pour les trois parcours sans consulter un `SKILL.md` ;
3. chaque exemple exige un XLSX, des JH, des sources et la conservation des inconnues ;
4. tous les liens Markdown locaux pointent vers un fichier existant ;
5. le scan d'anonymisation de l'arbre public reste propre ;
6. les 386 tests de la suite restent verts, hors skip Windows déjà documenté ;
7. les fichiers sont en LF et aucun contenu privé n'est introduit ;
8. les versions française et anglaise couvrent les mêmes sections et les mêmes onze skills.

La validation d'usage comprend trois scénarios de lecture indépendants : demande multidomaine, FinOps seul et choix d'architecture/provider. Pour chacun, le lecteur doit identifier le skill initial, les entrées nécessaires et le livrable attendu.

## 10. Publication

La documentation est développée sur la branche source de la suite, puis propagée sans divergence vers :

- `txRandria/skills:feat/cloud-estimation-suite`, PR interne vers `main` ;
- la branche de la PR `anthropics/skills#2006`.

Les deux PR restent ouvertes. Les classeurs sources privés, scripts locaux de travail et journaux de harness ne sont jamais ajoutés aux commits publics.

## 11. Hors périmètre

- modification du comportement des onze skills ;
- modification du format contractuel du XLSX ;
- ajout de prix réels ou connexion automatique aux calculateurs fournisseurs ;
- publication de logos non déjà autorisés par la politique d'icônes ;
- refonte des six skills de sécurité existants ;
- fusion automatique de l'une des deux PR.
