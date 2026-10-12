# Cloud Estimation User Documentation Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish bilingual, task-oriented documentation that explains why, when and how to use each of the eleven Cloud Estimation skills and how to validate their homogeneous XLSX/JH deliverable.

**Architecture:** Keep the complete French and English guides in the repository-level `docs/` directory, with the suite README as the common entry point shared by both pull requests. Add concise repository-root links only in `txRandria/skills`, because the Anthropic repository has a different root README and no `README.en.md`. Protect the documentation contract with automated structure/link tests and three independent reader scenarios.

**Tech Stack:** Markdown, Python 3.11+, pytest, existing anonymisation scanner, Git/GitHub pull requests.

## Global Constraints

- Document exactly the eleven names in `tooling/domain-registry.json`; do not rename or merge skills.
- The primary deliverable remains an editable `.xlsx`; effort is expressed in JH and recurring human effort in JH/month.
- Every usage example requires sources, explicit assumptions and preservation of unknown values.
- Never invent provider prices, regional eligibility, service levels, topology or approval status.
- Use only neutral synthetic organisations, reserved domains and non-private paths in examples.
- Do not change the behavior of the eleven skills, the workbook contract, the bundles or the six existing security skills.
- Keep common guide content byte-identical across the Anthropic and `txRandria/skills` pull requests.
- Keep both pull requests open; publication does not authorize merging either pull request.
- All new text files use LF line endings.

---

### Task 1: Establish the documentation contract and RED baseline

**Files:**
- Create: `skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py`
- Read: `skill-updates/2026-10-07/cloud-estimation-suite/tooling/domain-registry.json`
- Record locally: `.superpowers/sdd/2026-10-12-cloud-estimation-documentation/baseline.md`

**Interfaces:**
- Consumes: the canonical skill registry and the existing suite README.
- Produces: pytest assertions used by Tasks 2 and 3; three baseline reader reports used by Task 5.

- [ ] **Step 1: Create three fresh-context baseline scenarios**

Run each scenario without supplying either proposed guide. Record the selected skill, required inputs and expected deliverable in the local baseline report.

```text
Scenario A — multidomain:
You have cloned txRandria/skills. A client needs infrastructure, CI/CD,
hardening, provider comparison and a consolidated estimate. From the public
documentation only, identify the first skill to use, the minimum inputs and
the required deliverable.

Scenario B — FinOps only:
You have billing exports and utilisation metrics for an existing platform.
From the public documentation only, identify the skill, explain why it fits,
and state how unknown prices and savings must appear in the deliverable.

Scenario C — architecture and provider:
The decision is Kubernetes versus managed runtime versus VPS, followed by a
provider comparison. From the public documentation only, identify the initial
routing skill, the specialist skills involved and the outputs to compare.
```

Expected RED evidence: the root documentation of `txRandria/skills` describes only six security skills and does not expose a Cloud Estimation installation path, reusable prompt or XLSX/JH workflow. If every fresh reader independently finds all three answers, stop and reduce the proposed documentation to only the missing information demonstrated by the reports.

- [ ] **Step 2: Write the failing documentation tests**

Create `test_user_documentation.py` with this contract:

```python
import json
import re
from pathlib import Path

SUITE = Path(__file__).resolve().parents[1]
REPOSITORY = SUITE.parents[2]
GUIDES = {
    "fr": REPOSITORY / "docs/guide-cloud-estimation.md",
    "en": REPOSITORY / "docs/cloud-estimation-guide.md",
}
SECTIONS = {
    "fr": (
        "Pourquoi utiliser la suite",
        "Ce que la suite produit",
        "Choisir le bon skill",
        "Installation",
        "Préparer le brief",
        "Parcours standard",
        "Prompts copiables",
        "Exemples de bout en bout",
        "Lire et contrôler le XLSX",
        "Limites et sécurité",
        "Dépannage",
    ),
    "en": (
        "Why use the suite",
        "What the suite produces",
        "Choose the right skill",
        "Installation",
        "Prepare the brief",
        "Standard workflow",
        "Copy-ready prompts",
        "End-to-end examples",
        "Read and validate the XLSX",
        "Limits and safety",
        "Troubleshooting",
    ),
}
REGISTRY = json.loads(
    (SUITE / "tooling/domain-registry.json").read_text(encoding="utf-8")
)
SKILLS = tuple(item["name"] for item in REGISTRY["skills"])
LOCAL_LINK = re.compile(r"\[[^]]+\]\(([^)]+)\)")


def text(path: Path) -> str:
    return path.read_text(encoding="utf-8")


def test_bilingual_guides_have_the_complete_navigation_contract():
    for language, path in GUIDES.items():
        content = text(path)
        for heading in SECTIONS[language]:
            assert f"## {heading}" in content
        for name in SKILLS:
            assert content.count(f"`{name}`") >= 2


def test_every_guide_preserves_the_delivery_and_evidence_contract():
    required = (".xlsx", "JH", "JH/month", "source")
    for path in GUIDES.values():
        content = text(path)
        for token in required:
            assert token.lower() in content.lower()
        assert "cloud-estimation-orchestrator" in content
        assert "à chiffrer" in content.lower() or "to be priced" in content.lower()


def test_all_local_guide_links_resolve():
    for path in (*GUIDES.values(), SUITE / "README.md"):
        for target in LOCAL_LINK.findall(text(path)):
            clean = target.split("#", 1)[0]
            if not clean or "://" in clean or clean.startswith("mailto:"):
                continue
            assert (path.parent / clean).resolve().exists(), (path, target)


def test_suite_readme_links_both_user_guides():
    content = text(SUITE / "README.md")
    assert "../../../docs/guide-cloud-estimation.md" in content
    assert "../../../docs/cloud-estimation-guide.md" in content
```

- [ ] **Step 3: Run the RED tests**

Run:

```powershell
rtk python -m pytest skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py -v
```

Expected: FAIL because both guide files and their suite README links are absent.

- [ ] **Step 4: Commit the RED contract**

```powershell
rtk git add skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py
rtk git commit -m "test: define cloud estimation documentation contract"
```

---

### Task 2: Write the French guide and common suite entry point

**Files:**
- Create: `docs/guide-cloud-estimation.md`
- Modify: `skill-updates/2026-10-07/cloud-estimation-suite/README.md`
- Test: `skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py`

**Interfaces:**
- Consumes: section names and skill registry assertions from Task 1.
- Produces: the canonical French explanations and prompt semantics mirrored by Task 3.

- [ ] **Step 1: Add the common guide navigation to the suite README**

Immediately after the introductory paragraph, add:

```markdown
## User guides

- [Guide d'utilisation en français](../../../docs/guide-cloud-estimation.md)
- [English user guide](../../../docs/cloud-estimation-guide.md)

The guides explain why and when to use each skill, which inputs to prepare,
copy-ready prompts, and how to review the XLSX/JH deliverable. Start with
`cloud-estimation-orchestrator` when two or more domains overlap.
```

- [ ] **Step 2: Write the French guide with the eleven-section contract**

The guide must contain:

1. a short value proposition contrasting a traceable decision workbook with an unstructured estimate;
2. the XLSX/JH/JH-month/EUR output contract and the twelve sheet names;
3. one five-column table: `Skill`, `Utiliser quand`, `Pourquoi`, `Entrées minimales`, `Résultat distinctif`;
4. installation paths for Codex (`$CODEX_HOME/skills` or the configured skills directory) and Claude Code (`~/.claude/skills`), with review-before-replace wording;
5. a brief checklist covering perimeter, environments, workload, availability, security, pipeline, costs, constraints and dated sources;
6. the six-step workflow: route, collect, model, generate, recalculate/validate, approve;
7. one complete orchestrator prompt and ten specialist prompts, each naming its skill in backticks;
8. three end-to-end examples matching the baseline scenarios;
9. a concise explanation of the twelve sheets and the color convention;
10. explicit limitations and non-authorization language;
11. troubleshooting for discovery, dependencies, native recalculation and financial unknowns.

Use this full orchestrator prompt as the reference shape:

```text
Utilise `cloud-estimation-orchestrator` pour produire un chiffrage consolidé.
Périmètre : [applications, environnements et services inclus].
Données disponibles : [inventaire, charge, SLO/RTO/RPO, pipeline, sécurité].
Données inconnues : [prix, éligibilité régionale, capacité mesurée].
Compare : [scénarios de même périmètre].
Exprime le Build en JH, le Run humain en JH/month et sépare les coûts cloud.
Le livrable obligatoire est un `.xlsx` éditable avec sources datées,
hypothèses, risques, arbitrages, critères d'acceptation et diagramme.
N'invente aucun prix, service disponible, flux ou topologie : conserve les
inconnues comme « à chiffrer » ou comme décisions bloquantes.
```

- [ ] **Step 3: Run the French-focused assertions**

Run:

```powershell
rtk python -m pytest skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py::test_every_guide_preserves_the_delivery_and_evidence_contract skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py::test_suite_readme_links_both_user_guides -v
```

Expected: the suite README assertion passes; the bilingual guide assertion still fails only because Task 3 has not created the English guide.

- [ ] **Step 4: Commit the French guide**

```powershell
rtk git add docs/guide-cloud-estimation.md skill-updates/2026-10-07/cloud-estimation-suite/README.md
rtk git commit -m "docs: add French cloud estimation user guide"
```

---

### Task 3: Write the English guide and prove bilingual parity

**Files:**
- Create: `docs/cloud-estimation-guide.md`
- Modify: `skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py`
- Test: `skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py`

**Interfaces:**
- Consumes: the French guide's eleven sections, selection matrix and prompt semantics.
- Produces: an English guide with the same skill coverage and a green common documentation contract.

- [ ] **Step 1: Write the English guide**

Mirror every French section and row without weakening these requirements:

- `.xlsx` is mandatory and editable;
- Build uses JH, recurring human Run uses JH/month, provider costs stay separate;
- missing financial inputs remain `to be priced` and block only dependent totals;
- the orchestrator owns cross-domain reconciliation;
- prompts forbid invented prices, eligibility, topology and approval claims;
- source dates and acceptance criteria remain mandatory.

Use this reference orchestrator prompt:

```text
Use `cloud-estimation-orchestrator` to produce one consolidated estimate.
Scope: [included applications, environments and services].
Available evidence: [inventory, workload, SLO/RTO/RPO, pipeline, security].
Unknown inputs: [prices, regional eligibility, measured capacity].
Compare: [same-scope scenarios].
Express Build in JH, human Run in JH/month, and keep cloud costs separate.
The required deliverable is an editable `.xlsx` with dated sources,
assumptions, risks, decisions, acceptance criteria and an architecture diagram.
Do not invent prices, service availability, flows or topology: preserve unknowns
as `to be priced` or as blocking decisions.
```

- [ ] **Step 2: Add a parity assertion**

Append:

```python
def test_guides_have_matching_skill_and_prompt_coverage():
    french = text(GUIDES["fr"])
    english = text(GUIDES["en"])
    for name in SKILLS:
        assert french.count(f"`{name}`") == english.count(f"`{name}`")
    assert french.count("```text") == english.count("```text") == 14
```

The fourteen prompt blocks are: one complete orchestrator prompt, ten specialist prompts and three end-to-end scenario prompts.

- [ ] **Step 3: Run the complete documentation contract**

Run:

```powershell
rtk python -m pytest skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py -v
```

Expected: all documentation tests PASS.

- [ ] **Step 4: Commit the English guide and parity gate**

```powershell
rtk git add docs/cloud-estimation-guide.md skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py
rtk git commit -m "docs: add English cloud estimation user guide"
```

---

### Task 4: Add repository-specific discovery in `txRandria/skills`

**Files:**
- Modify only on `publish/cloud-estimation-suite`: `README.md`
- Modify only on `publish/cloud-estimation-suite`: `README.en.md`
- Import from source branch: `docs/guide-cloud-estimation.md`
- Import from source branch: `docs/cloud-estimation-guide.md`
- Import from source branch: `skill-updates/2026-10-07/cloud-estimation-suite/README.md`
- Import from source branch: `skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py`

**Interfaces:**
- Consumes: the green common documentation commits from Tasks 1–3.
- Produces: visible French and English entry points in the repository that the user identified as the publication target.

- [ ] **Step 1: Transfer the common documentation without changing its bytes**

On `publish/cloud-estimation-suite`, restore the five common files from `feat/cloud-estimation-suite`, then compare them with `git diff --no-index` or blob hashes before committing.

- [ ] **Step 2: Add the French root README section**

Insert after the existing six-skill introduction and before `## Pourquoi`:

```markdown
## Suite complémentaire : chiffrage et architecture cloud

La branche propose aussi onze skills pour produire des chiffrages homogènes
en XLSX : infrastructure, CI/CD, sécurité serveur, FinOps, capacité, migration,
Kubernetes, choix d'architecture, comparaison de providers et diagrammes cloud.

Consultez le [guide complet d'utilisation](docs/guide-cloud-estimation.md) pour
choisir le bon skill, préparer le brief et contrôler les JH, coûts, hypothèses
et sources. Pour un besoin multidomaine, commencez par
`cloud-estimation-orchestrator`.
```

- [ ] **Step 3: Add the equivalent English root README section**

Insert after the six-skill introduction and before `## Skills`:

```markdown
## Companion suite: cloud estimation and architecture

This branch also provides eleven skills for homogeneous XLSX estimates across
infrastructure, CI/CD, server security, FinOps, capacity, migration, Kubernetes,
architecture selection, provider comparison and cloud diagrams.

Read the [complete user guide](docs/cloud-estimation-guide.md) to choose a skill,
prepare the brief and review effort, costs, assumptions and sources. Start with
`cloud-estimation-orchestrator` for a multi-domain request.
```

- [ ] **Step 4: Verify target-repository links and commit**

Run:

```powershell
rtk powershell -NoProfile -Command "@('docs/guide-cloud-estimation.md','docs/cloud-estimation-guide.md') | ForEach-Object { if (-not (Test-Path -LiteralPath $_)) { throw \"Missing $_\" } }"
rtk rg -n "guide-cloud-estimation|cloud-estimation-guide" README.md README.en.md skill-updates/2026-10-07/cloud-estimation-suite/README.md
rtk git diff --check
```

Expected: both files exist, each README contains its intended link and `git diff --check` is silent.

Commit:

```powershell
rtk git add README.md README.en.md docs/guide-cloud-estimation.md docs/cloud-estimation-guide.md skill-updates/2026-10-07/cloud-estimation-suite/README.md skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py
rtk git commit -m "docs: explain cloud estimation skill usage"
```

---

### Task 5: Run the reader harness, full gate and update both pull requests

**Files:**
- Verify: `docs/guide-cloud-estimation.md`
- Verify: `docs/cloud-estimation-guide.md`
- Verify: `skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py`
- Record locally: `.superpowers/sdd/2026-10-12-cloud-estimation-documentation/final-reader-harness.md`

**Interfaces:**
- Consumes: all documentation and tests from Tasks 1–4.
- Produces: independent usage evidence, two updated remote branches and two still-open pull requests.

- [ ] **Step 1: Re-run the three reader scenarios with the guides**

Use fresh contexts and provide only the relevant root README plus the selected guide. Score each response against this exact rubric:

```text
1 point — selects the correct initial skill.
1 point — explains why that skill fits.
1 point — lists the minimum inputs without inventing missing values.
1 point — requires an editable XLSX with JH and separate cloud costs.
1 point — preserves unknown prices/eligibility as blocked or to be priced.
```

Expected: 5/5 for all three scenarios. Compare with Task 1 and record the observed improvement. A response that merely repeats skill names without choosing a route does not pass.

- [ ] **Step 2: Run the targeted documentation and privacy gates**

Run from the source worktree:

```powershell
rtk python -m pytest skill-updates/2026-10-07/cloud-estimation-suite/tests/test_user_documentation.py -v
rtk python skill-updates/2026-10-07/cloud-estimation-suite/tooling/anonymisation.py docs
rtk git diff --check
```

Expected: documentation tests PASS, anonymisation reports zero findings for the public docs and `git diff --check` is silent. If scanning the repository-wide `docs` directory includes unrelated upstream content, copy only the two guide files into a newly created temporary directory and scan that directory instead; record the exact temporary input count and delete no source files.

- [ ] **Step 3: Run the full suite from a fresh worktree**

Create a detached verification worktree from the exact source commit after Tasks 1–3, then run:

```powershell
rtk python -m pytest skill-updates/2026-10-07/cloud-estimation-suite/tests -q
```

Expected: all tests PASS with only the previously documented Windows symlink skip. Report the new total rather than reusing the earlier count of 386.

- [ ] **Step 4: Update the Anthropic pull-request branch**

Push the common documentation commits from `feat/cloud-estimation-suite` to the existing fork branch used by `anthropics/skills#2006`. Do not add the `txRandria/skills` root README changes to this branch. Verify via the GitHub API that pull request 2006 remains `open` and its head SHA equals the pushed commit.

- [ ] **Step 5: Update the `txRandria/skills` pull-request branch**

Push `publish/cloud-estimation-suite` to `txRandria/skills:feat/cloud-estimation-suite`. Verify via the GitHub API that pull request 2 remains `open`, targets `main`, lists the two guide files and has the same common guide blob hashes as the Anthropic branch.

- [ ] **Step 6: Final publication report**

Report:

- links to both open pull requests;
- source and target head SHAs;
- documentation-test and full-suite counts;
- reader-harness scores for all three scenarios;
- confirmation that private source spreadsheets remain untracked and absent from both diffs;
- confirmation that neither pull request was merged or closed.
