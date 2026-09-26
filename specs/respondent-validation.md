# respondent-validation — Design Spec

> For agentic workers: REQUIRED NEXT SKILL: derive-roadmap — do not plan
> this spec directly and do not split it into per-subsystem plans.

A Python package, `respondent_validation` (NumPyro/JAX and Polars), that scores each
response to an incentivized, open-link online survey by the posterior probability that it
is invalid and turns that probability into a decision with a guaranteed per-group
false-rejection rate. Every check — telemetry, network and device, identity, closed-ended
psychometrics, open-ended text, cross-respondent structure — is treated as an imperfect
diagnostic test with unknown, group-specific error rates. A semi-supervised Bayesian
latent class model with conditional dependence, prevalence covariates, and group-specific
false-positive rates fuses the checks without a gold standard; anchors come from seeded
scripted bots and LLM persona agents, red-team eligibility fraud, and a randomly drawn
verification subset. A Mondrian split-conformal layer sets auto-accept / review / reject
bands per group, and classification uncertainty is carried into substantive analysis by
multiple imputation rather than truncated by deletion. The target study is a
~400-respondent pilot of adolescents aged 14–17 with a gender-minority oversample,
recruited through open social-media ads on Qualtrics, whose prior waves saw fraud rise
despite reCAPTCHA, honeypots, IP screening and screener cross-checks. Alongside the
software, the project delivers a primary-source-verified methods note that reconciles the
three research reviews this spec is built from.

Design provenance: this spec synthesizes the research prompt
[`specs/methods-spec-prompt.md`](methods-spec-prompt.md) — which stands in for the
methodology description, since the repository is greenfield and no described system exists
— with three independent, unverified, first-pass responses to it:
[`methods-spec-review-chatgpt.md`](methods-spec-review-chatgpt.md),
[`methods-spec-review-claude.md`](methods-spec-review-claude.md), and
[`methods-spec-review-gemini.md`](methods-spec-review-gemini.md) (of which only the text,
lines 1–376, was read; the remainder is embedded base64 image data from a Google Docs
export). None was adjudicated before synthesis, so every accept/reject below was made in
the 2026-09-26 triage session, not inherited. Only the Claude review verified its anchor
citations; where reviews conflict on evidence, its verified citation is taken unless a
primary source elsewhere contradicts it. Locators: **(prompt §n)** and **(prompt Output)**
for the brief; **(chatgpt §Heading)** and **(chatgpt M1–M5)** for the ChatGPT review's
unnumbered sections and its five models; **(claude KF n)**, **(claude §n)** including
§4.1–4.5, **(claude Rec)** and **(claude OP n)** for the Claude review's key findings,
details, recommendations and open problems; **(gemini §Heading)**, **(gemini M1–M5)** and
**(gemini OP n)** likewise. Markers: **(chosen)** / **(rejected)** record triage
adjudications and the six user decisions of 2026-09-26 — (1) the package and spec are named
`respondent-validation`, the repository's name, not the prompt's
`survey-response-validation`; (2) the agent makes the initial commit to
`github.com/lowmason/respondent-validation`; (3) pilot triage at α_R = 0.05 and
α_V = 0.20, tightening to 0.01 once pooled per-group calibration reaches 99, with the
incentive withheld only after a verification attempt, seeding at ≥40 bots / ≥40 agents /
≥30 red-team humans plus an instructed-carelessness arm, and a verification subset of ~150
with ≥60 gender-minority respondents; (4) gender-minority status is the sole calibration
stratum, with age band and race/ethnicity monitored; (5) custom JavaScript telemetry —
paste, focus/blur, pointer counts, environment checks — is deployable in the Qualtrics
instance; (6) the written finding is in scope. **(open — resolved by verification, not
argument)** marks a matter of fact outside this repository, each discharged by a named
Verification bullet.

## Motivation — the gaps

- **The defenses that failed were hurdles with unknown error rates.** reCAPTCHA, honeypots,
  IP screening and screener cross-checks did not stop fraud from rising across waves
  (prompt, target application). Practice everywhere is rule counts: Pratt-Chapman et al.
  dropped 1,408 of 1,977 surveys on a two-indicator rule; Pinzón et al.'s best ensembles of
  31 indicators still carried false-positive rates the authors called unacceptable
  (chatgpt §Rule-based Ensembles); the 2026 ADOPT secondary analysis states outright that
  it had no ground-truth labels (claude KF3). A rule count has no probabilistic output,
  no error rate, and no way to weight a strong signal against a weak one (gemini
  §Modeling).
- **Attention checks and paradata are obsolete against agents.** Westwood's agent passed
  99.8% of 6,000 attention-check trials while simulating reading times, mouse movement and
  keystroke-level typing with typos (claude KF2; gemini §Taxonomy; chatgpt §Closed);
  traditional checks caught 2.3% of 526 deployed agents (claude §3). Agents bias rather
  than add noise — they infer the hypothesis and confirm it (claude KF2). No study has
  measured agent prevalence on open social-media links; every prevalence figure is
  panel-based (claude KF1, OP1).
- **The other signal families are individually weak or unevaluated.** Machine-generated-text
  detectors are "easily fooled" on RAID and few operate below 1% false positives (claude
  §2e; gemini §Open; chatgpt §Open). No independent sensitivity/specificity evaluation of
  any IP-intelligence, proxy-detection or fingerprinting vendor exists for survey fraud;
  2.2% of 6.2M residential-proxy IPs appear on any blacklist; mobile browser fingerprints
  are 18.5% unique (claude §2b). Careless-responding indices and person fit detect
  trait-unrelated responding, not a coherent persona (claude §2d, KF4).
- **Fusion without a gold standard is the right frame and is unused.** All three reviews
  frame the problem as diagnostic-test accuracy without a reference standard (chatgpt
  §Latent Class; claude §4.1; gemini §Latent Class), and none found a peer-reviewed
  application of Hui–Walter or Dendukuri–Joseph models to survey-fraud checks (claude KF3;
  chatgpt §Latent Class). The closest is a best–worst-scaling study whose latent classes
  aligned with age verification (claude KF3).
- **Python has no tooling.** There is no maintained equivalent of R's `careless`, `PerFit`
  (l_z*), `randomLCA` or `carelessonset`, and no mixture-IRT package with response-time
  components (claude §7; gemini §Specifications; prompt §5).
- **Equity is asserted, not measured, and the instruments cut the wrong way.** Whether
  VPN/IP rules disproportionately exclude legitimate gender-minority or privacy-sensitive
  respondents is unmeasured; 76% of Kennedy et al.'s VPS users failed no quality check,
  and recruitment costs rise in states with more anti-LGBTQIA+ legislation — a plausible
  driver of VPN use among the very teens being oversampled (claude §8; gemini §Network).
  Mischief indices and cis-as-gender-minority fraud inflate disparities in the same
  direction, and Cimpian-style reweighting down-weights multiply-marginalized youth
  (claude §1).
- **Eligibility fraud by attentive humans has no content-based signal.** Adults posing as
  minors and cisgender respondents posing as gender-minority produce coherent answers;
  only identity verification catches them (claude OP4; gemini §Taxonomy).
- **The evidence base is thin, vendor-heavy, and the three reviews disagree on facts.**
  They cite different DOIs and titles for the same Affonso 2026 paper, attribute a paper
  to the wrong authors, and quote vendor detection rates as evidence (gemini §Key
  Literature; chatgpt §References; claude Caveats). Resolving those on the record is the
  written finding's job (Req 20).

## Core principle

**Every check is an imperfect diagnostic test, and no decision rests on one.** Each
indicator enters a latent class measurement model that learns its sensitivity and its
group-specific false-positive rate from the checks' joint behaviour plus anchors; the
model's posterior is the suspicion score. Decisions are made by a distribution-free layer
calibrated on *verified*-valid respondents drawn at random, so that the false-rejection
rate is guaranteed per group without any modeling assumption. Classification uncertainty
is carried into analysis, never truncated. And detection is downstream of design:
identity verification before payment and single-use links do the heavy lifting against the
threat no signal detects; the package scores what verification leaves and supplies the
anchors interface that makes verification useful to the model.

## Requirements

### Input contract and instrumentation

**Req 1 — Respondent-level input contract.** One documented, validated, long-format input
contract (Polars-native), keyed by an opaque respondent id `rid`, with these tables:
(a) `responses` — `(rid, item_id, block_id, position, value, reverse_keyed, n_categories)`;
(b) `timing` — `(rid, screen_id, first_click_s, last_click_s, submit_s, click_count)`,
the page-level timers Qualtrics exposes (claude §2a); item-level response times are
optional and absent by default; (c) `telemetry` — derived counts and flags only:
`paste_count`, `focus_loss_count`, `pointer_events_present`, the automation flags
(`webdriver`, CDP artifact, plugin or `userAgentData` inconsistency), `script_loaded`
(claude §2a; prompt §2a); raw keystroke streams and mouse trajectories are refused at
validation (chosen — keystroke dynamics are quasi-biometric and the respondents are minors,
claude §2a, gemini §Paradata; collecting and later discarding rejected); (d)
`network_device` — `(rid, ip_hashed, ip24_hashed, asn, proxy_class ∈ {residential, mobile,
datacenter, vpn, tor, unknown}, vendor_risk_score ∈ [0,1] or null, ip_country,
tz_offset_min, device_hash, user_agent_family)`; (e) `identity` — `(rid,
email_local_hashed, email_domain, payout_handle_hashed, disposable_domain)`, every
personal identifier one-way hashed at ingestion with a per-study salt (chosen; plaintext
storage rejected); (f) `open_ends` — `(rid, item_id, text)`; (g) `platform` — `(rid,
recaptcha_score ∈ [0,1] or null, recaptcha_status ∈ {ok, error, script_load_failed,
missing}, duplicate_flag)` (claude KF5; gemini §Paradata); (h) `submission` — `(rid,
started_at, submitted_at, ad_set, campaign, stratum, self_reported_state, group)`, where
`group` is the equity stratum of Req 14; (i) `labels` — `(rid, label ∈ {verified_valid,
verified_invalid, seeded_bot, seeded_agent, seeded_redteam, instructed_careless},
verification_pathway, selected_at_random)` (Req 21). Missing telemetry and missing
attestation scores are explicit categories, never imputed as benign (gemini §Paradata;
claude §2a). **(open — resolved by verification, not argument):** the exact Qualtrics export
fields — `Q_RecaptchaScore` and its status field, `Q_DuplicateRespondent`, the page-timer
columns, and the embedded-data fields the Req 2 telemetry writes — are confirmed against
the live instance before the adapter is written; the contract above is the target, not a
claim about the export.

**Req 2 — Qualtrics adapter and telemetry contract.** One adapter from a Qualtrics export
plus embedded-data fields to the Req 1 tables; adapters for other platforms are out of
scope. The package ships the survey-side JavaScript telemetry contract as documentation
and a reference snippet (a non-Python asset): what the script writes into embedded-data
fields — paste count, focus-loss count, pointer-event presence, and the automation flags —
as counts and booleans only (chosen — user decision 2026-09-26 that custom JavaScript is
deployable; leaving the contract undocumented rejected, since the Req 5 indicators are only
as good as their collection). Environment checks catch naive Selenium/Playwright and lose
to stealth plugins and real-browser agents — Gordon et al.'s "perfect discrimination" is a
pilot result in a preprint (claude §2a) — so they enter as indicators, never as a block.
Attestation scores (reCAPTCHA v3) attest a real browser, not an eligible human; Westwood's
agent was designed around bypass tools (claude §2a). They are logged and never thresholded
at ingestion (gemini §Pipeline step 1 agrees: log the reputation score, do not block).

### Indicator families

**Req 3 — Closed-ended careless and person-fit indices.** Over `responses` blocks:
long-string (maximum run of identical values), intra-individual response variability,
even–odd consistency, psychometric synonym and antonym correlations, Mahalanobis $`D^2`$
with a robust-covariance option, and IRT person fit — $`l_z`$ and the Snijders-corrected
$`l_z^*`$ for dichotomous items and its polytomous extension for graded items — against
item parameters estimated on the sample or supplied (claude §2d; gemini §Closed, M5;
chatgpt §Closed; prompt §2d). Each index maps to a binary indicator through a configured
threshold or sample-quantile rule; thresholds are configuration, never constants (chosen),
because their false-positive rates are estimated by Req 10 rather than assumed. These
indices detect trait-unrelated responding only; none detects a coherent persona agent,
which maintained persona-consistent psychometric profiles in Westwood's study (claude
§2d). That is why they are indicators, not the score. The R packages `careless` and
`PerFit` are the reference implementations (prompt §5; claude §7); parity fixtures are
committed under Req 19.

**Req 4 — Pre-registered content checks and cognitive traps.** A configuration registry of
pre-registered items of five types, each producing a failure indicator: (i) logically
inconsistent item pairs; (ii) age-consistency items — school grade, driving, employment —
for adult-as-minor eligibility fraud (claude §2d); (iii) low-base-rate "mischievous" items
whose joint endorsement is implausible (Robinson-Cimpian 2014; Cimpian et al.; prompt
§1, §2d); (iv) cognitive traps hard for vision-language architectures — Affonso (2026)
reports 97.1% of 526 seeded agents detected at 4.1% human false positives, and two traps
reaching over 93% detection under 3% false positives for about a minute of survey time
(claude §3, Rec 1); (v) instructed-response attention checks, retained only as indicators
since agents pass 99.8% of them (claude KF2; gemini §Taxonomy). Hard rules: (a) counts of
low-base-rate endorsements never become a hurdle or a reweighting on their own — mischief
screening is contested (Phillips 2020) and Cimpian-style reweighting gave a mean weight of
1.01 to youth selecting one racial identity versus 0.40 to those selecting four or more
(Delgado-Ron 2024; claude §1) — so each enters Req 10 with a group-specific false-positive
rate; (b) traps are refreshed every wave because model improvement is non-monotonic and
each new model changes which traps work (claude §6; Req 18). Item authoring is out of
scope; the registry schema and the failure logic are in.

**Req 5 — Telemetry and environment indicators.** From Req 1(c) and (g): paste count ≥ 1
on open-ends; focus-loss count above a configured rate; pointer events absent; any
automation flag; `recaptcha_status ≠ ok` and `recaptcha_score` below a configured cut as
*separate* indicators, since scripts fail to load under ad blockers and fast exits and
treating missing as benign manufactures false negatives (gemini §Paradata); page speed as
the ratio of screen time to the per-screen sample median, with the ≤30–50%-of-median rule
Pinzón et al. tested as one configurable indicator (chatgpt §Paradata). Paste and
focus-loss are the most discriminating signals against LLM-assisted humans — 41 of 46
LLM-written summaries in Veselovsky et al. involved pasting (claude §2a) — and weak against
agents that type keystroke-by-keystroke (claude KF2). No indicator in this family is a
hurdle.

**Req 6 — Network, device, and identity indicators.** `proxy_class ∈ {datacenter, vpn,
tor}`; vendor risk score above a configured cut (Pinzón et al. rank the MaxMind minFraud
score among their best indicators; claude §2b); IP country outside the target country;
time-zone offset inconsistent with the self-reported state; submission hour in a configured
window (Pozzar et al.: 82.5% of screeners arrived between midnight and 4 a.m.; claude §2b);
fingerprint-collision degree (respondents sharing `device_hash`); email-pattern score
(digits, random strings — Pinzón's email-address score was among the best indicators;
claude §2c) and disposable domain (all three reviews). Every one is a soft indicator with
a group-specific false-positive rate (chosen). (rejected) auto-rejecting VPN, datacenter or
non-target-country IPs, including the proposal to block foreign IPs live during fielding
(chatgpt §Pilot Pipeline step 3): no independent sensitivity/specificity evaluation of any
IP-intelligence or fingerprinting vendor exists (claude §2b); 2.2% of 6.2M
residential-proxy IPs appear on any blacklist (Mi et al. 2019); 95% of Pinzón's verified
fraud had US IPs; 76% of Kennedy et al.'s VPS users failed no quality check; and mobile
fingerprints are 18.5% unique (Gómez-Boix et al. 2018), so adolescents on the same phone
model collide legitimately (claude §2b, §8). Whether these rules disproportionately flag
legitimate gender-minority teens is unmeasured; the pilot measures it (Req 17).

**Req 7 — Open-ended text indicators.** (a) Exact and near-duplicate degree across
respondents by shingled MinHash with locality-sensitive-hash buckets (chosen; SimHash an
acceptable alternative), also feeding Req 8; (b) cross-respondent stylistic homogeneity —
embedding cosine to the sample centroid and to nearest neighbours, plus length and lexicon
shift — targeting LLM homogenization of open-ends (claude §2e: Zhang, Xu & Alvero 2025);
(c) an AI-text detector score (Binoculars or Fast-DetectGPT, both open source) as one
**weak** indicator, never a hurdle: on RAID's 6M+ generations across 11 models, detectors
were easily fooled by adversarial attacks, sampling changes and unseen models, and few
operate below 1% false positives (Dugan et al. 2024; claude §2e; gemini §Open; chatgpt
§Open); persona-grounded agents push detectors toward chance (claude KF2). **(open —
resolved by verification, not argument):** the detector's false-positive rate on short,
informal adolescent writing is unknown; it is measured on verified-valid open-ends before
the indicator is enabled in Req 10.

**Req 8 — Cross-respondent structure: identifier graph and submission bursts.** A
bipartite graph $`G=(R\cup I,E)`$ between respondents $`R`$ and identifiers $`I`$ — IP/24,
ASN, device hash, normalized email local-part, payout handle, MinHash-LSH text buckets —
projected onto respondents with edge weight $`w_{ii'}=\sum_{\iota\ \text{shared}}
1/\mathrm{freq}(\iota)`$, so that carrier-grade NAT and school Wi-Fi collisions are
down-weighted. Per-respondent outputs: connected-component size, Leiden community size,
and maximum shared-identifier degree (claude §2f, §4.3). (rejected for v1) a stochastic
block model or other Bayesian graph model (chatgpt M4): at n ≈ 400 with sparse edges,
components and communities suffice, and the SBM's identifiability rests on fraudsters
being far more interconnected than the population — exactly what residential-proxy farms
defeat. Submission bursts: counts per window modeled as Poisson with a baseline tied to ad
delivery (impressions × click-through) when available and a rolling median otherwise;
standardized excess above a configured z flags the window and its respondents (claude
§4.3). Shared identifiers do not imply one person (claude §4.3), so every graph feature is
a Req 10 indicator with its own group-specific false-positive rate (chosen); (rejected)
deterministic removal of shared-IP or shared-device respondents (gemini §Pipeline step 2).
Byte-identical response vectors from the same payout handle are deduplicated as data
hygiene before scoring; that is not classification.

**Req 9 — Careless-responding component: response-time latent response mixture.** After
Ulitzsch et al. (2022), simplified (claude §4.2; gemini M2; chatgpt M3; prompt §3). For
person $`i`$ and item $`j`$ with graded response $`r_{ij}\in\{0,\dots,M_j-1\}`$ and
log response time $`t_{ij}`$: under attentive responding, a graded response model
$`P(r_{ij}\ge m\mid\theta_i)=\mathrm{expit}(a_j\theta_i-b_{jm})`$ with
$`t_{ij}\sim N(\beta_j-\tau_i,\sigma_a^2)`$ for person speed $`\tau_i`$ and item
time-intensity $`\beta_j`$; under careless responding, $`r_{ij}`$ uniform over categories
and $`t_{ij}\sim N(\beta_j-\tau_i-\delta,\sigma_c^2)`$ with $`\delta>0`$. The careless
probability is $`\mathrm{logit}\,\pi_{ij}=g_i+\kappa\,\mathrm{pos}_j`$, so $`\kappa>0`$
encodes onset — the smooth analogue of a changepoint. Estimands: item-level
$`P(\text{careless}_{ij})`$, the person-level careless share
$`\rho_i=J^{-1}\sum_j P(\text{careless}_{ij}\mid\text{data})`$, the onset slope
$`\kappa`$, and trait estimates purified of careless responding. Identifiability: careless
responses are unrelated to $`\theta`$ (reverse-keyed items in every block are required),
$`\delta>0`$ orders the response-time components and breaks label switching (gemini M2
agrees), and at least two constructs with several items each are needed to separate
$`\theta`$ from careless patterns (claude §4.2). Timing: **screen-level** components on
the page timers of Req 1(b), following Ulitzsch, Shin & Lüdtke (2024) (chosen); item-level
response times through per-question JavaScript (rejected for v1 — adds script complexity to
every question for a component the LCM consumes as one indicator). Output into Req 10:
$`1\{\rho_i>\rho^*\}`$ with $`\rho^*`$ in configuration (default 0.5) and the continuous
$`\rho_i`$ reported. Extensions recorded: a third mixture component inflating endorsement
of the Req 4 low-base-rate items — a mixture version of Cimpian's screener logic yielding
a probability rather than a rank (claude §4.2); and a discrete changepoint in the CODERS
line (Welz & Alfons; R `carelessonset`), which carries nonparametric false-positive
guarantees (claude KF4; gemini §Response-Time; chatgpt M3) — (rejected for v1) in favour of
$`\kappa`$; recorded as the roadmap alternative. Blind spot, stated: an attentive
fraudster or LLM agent is "attentive" under this model, which is why its output is one
indicator and not the fraud score (claude §4.2).

### Fusion

**Req 10 — The fusion model: semi-supervised Bayesian latent class model with
conditional dependence, prevalence covariates and group-specific false-positive rates.**
(claude §4.1 as the primary; chatgpt M1–M2 and gemini M1 the same family — see Req 11 for
what is rejected.) For respondent $`i=1,\dots,n`$ with binary indicators
$`y_{ik}\in\{0,1\}`$, $`k=1,\dots,K`$ (Reqs 3–9), equity group $`g_i`$ (Req 14),
prevalence covariates $`x_i`$ (ad set, hour block, oversample stratum, campaign), and
anchor label $`\ell_i`$ (Req 1(i)):

```math
C_i\in\{0,1\},\qquad \mathrm{logit}\,P(C_i=1\mid x_i)=\alpha_0+x_i^\top\beta,
```
```math
u_i\sim N(0,1),\qquad
\mathrm{logit}\,P(y_{ik}=1\mid C_i=c,\,u_i,\,g_i)=a_{g_i k}+c\,\gamma_k+s_k\,\mathbb{1}[\ell_i\in\text{seeded}]+\lambda_{ck}\,u_i,
```

with $`\gamma_k\ge 0`$ (monotonicity: invalid respondents trip every check at least as
often), $`\lambda_{ck}\ge 0`$ class-specific loadings on one shared random effect — the
Qu–Tan–Kutner / Dendukuri–Joseph conditional-dependence device — and
$`a_{gk}=a_k+\Delta_{gk}`$, $`\Delta_{gk}\sim N(0,\sigma_a^2)`$, partially pooled across
groups (chosen; fully separate per-group parameters rejected because the gender-minority
stratum is small; a single shared $`a_k`$ rejected because it defeats the equity
estimand). Anchors enter through their emission likelihood only, never the prevalence
term: $`\ell_i=\text{verified\_valid}`$ contributes the $`C_i=0`$ emission,
$`\ell_i=\text{verified\_invalid}`$ the $`C_i=1`$ emission, and seeded cases the
$`C_i=1`$ emission with a per-check seeded offset $`s_k\sim N(0,\tau_s^2)`$,
$`\tau_s\sim\text{HalfNormal}`$ (chosen) — seeded agents are not a random sample of real
fraud, so they inform $`\gamma_k`$ only through a shrinkage prior; (rejected) pooling
seeded with organic invalid cases, and (rejected) excluding seeded cases from the fit,
which discards the only per-threat sensitivity information (claude §4.1).
`instructed_careless` cases inform Req 9 only and are excluded from the LCM anchors
(chosen): they are eligible humans responding carelessly by instruction, neither class.
Checks from one family (e.g. three timing rules) share evidence: they are collapsed to
one indicator by default (chosen), or given an additional family-level random effect when
the Req 11 posterior predictive check on within-family concordance fails (claude §4.1).
Estimands: the per-respondent posterior $`p_i=P(C_i=1\mid y_i,x_i)`$ — the suspicion
score — with its posterior uncertainty; per-check sensitivity
$`\mathrm{Se}_k(g)=E_u[\mathrm{expit}(a_{gk}+\gamma_k+\lambda_{1k}u)]`$ and specificity
$`\mathrm{Sp}_k(g)=1-E_u[\mathrm{expit}(a_{gk}+\lambda_{0k}u)]`$ per group; and
prevalence $`\pi(x)`$ as a function of ad set, hour and stratum (claude §4.1). Inference:
$`C_i`$ is marginalized analytically and the continuous parameters are sampled by NUTS in
NumPyro with convergence diagnostics reported (R-hat, ESS, divergences); posterior draws
of $`p_i`$ are retained for Req 15.

**Req 11 — Identifiability, priors, label switching, and diagnostics.** Under conditional
independence one population needs $`K\ge 3`$ checks ($`2^K-1\ge 2K+1`$); Hui–Walter's
two-population route requires sensitivity and specificity invariant across populations,
which Req 10's group-specific $`a_{gk}`$ deliberately relaxes, so ad sets and strata do
not substitute for checks (claude §4.1; chatgpt M1; gemini §Latent Class). Target 8–12
heterogeneous indicators drawn from at least four of the Req 3–9 families, plus anchors;
without anchors, informative priors on at least some sensitivities and specificities are
mandatory — diffuse priors produce near-non-identifiability diagnostics in Stan LCMs
(Cerullo et al.; claude §4.1). Label switching is broken by $`\gamma_k\ge 0`$ (chosen);
(rejected) the prevalence constraint $`\pi<0.5`$ and the "sensitivity exceeds specificity"
order constraint of gemini M1: invalid responses are frequently the *majority* on open
links — Pozzar 94.5%, Griffin 61.8%, Pinzón 36–39% (claude KF1) — so $`\pi<0.5`$ would
truncate the true posterior. Monotonicity is checked per indicator after fitting: a check
whose posterior $`\gamma_k`$ concentrates at zero is dropped or re-signed. Fixed-effects
pairwise covariance terms (gemini M1; Dendukuri & Joseph 2001) are the recorded
alternative for $`K\le 4`$ and rejected as the primary: they grow as $`K^2`$, the
cell-count form yields no per-respondent posterior, and $`K=2`$ is unidentified without
them. Diagnostics shipped: posterior predictive checks on every pairwise indicator
concordance; prior sensitivity on $`a_k`$ and $`\gamma_k`$; a three-class comparison
(valid / careless / invalid) reported beside the two-class default (chosen default
two-class — Req 9 already separates carelessness; three-class as default rejected until the
comparison favours it) (claude §4.1); and parameter recovery on simulated data before any
fit to real data (Req 17).

**Req 12 — Cohort-level agent diagnostics.** Agents bias rather than add noise: they infer
the researcher's hypothesis and confirm it, and produce distributions lacking human
idiosyncratic variance (Westwood 2025; claude KF2, §2f; gemini §Cross-Respondent). This is
a diagnostic defined on groups, not individuals, so it never enters Req 10 (chosen). For
Req 8 clusters and for the high-posterior band, test whether substantive means deviate
toward hypothesis-consistent or persona-stereotyped values relative to the low-posterior
band, and report between-band variance ratios. Output is a report section.

**Req 13 — Positive-unlabeled discriminative sensitivity analysis.** A gradient-boosted
classifier on all indicators with positive-unlabeled correction: Elkan–Noto under SCAR as
the baseline, $`P(C=1\mid x)=P(s=1\mid x)/c`$ with $`c`$ the out-of-fold mean score on
labeled positives and out-of-fold isotonic or Platt calibration *before* the division
(claude §4.4). SCAR is almost certainly violated — the fraud you caught is the easy fraud —
so the labeling propensity $`e(x)`$ is modeled as a function of detection pathway (Bekker
& Davis) as the SAR variant (claude §4.4; gemini M3), and seeded cases are never pooled
with organically caught fraud when estimating $`c`$. Role: a sensitivity analysis and
discriminative complement to Req 10, never the primary score (chosen); below roughly 50
organic labeled positives the LCM is preferred outright. Uncorrected supervised boosting is
rejected as a primary: the best published careless-responding classifier reached at most
19% precision against instructed labels (Schroeders et al. 2022, per Alfons & Welz 2024;
claude §3, KF2). (rejected for v1) non-negative PU risk under SAR and contrastive PU
(gemini M3, §Supervised): representation learning on ~400 rows is unfounded; both are
recorded alternatives.

### Decision and downstream

**Req 14 — Mondrian split-conformal three-band triage with per-group false-rejection
control.** (claude §4.5; gemini M4 and chatgpt M5 the same family.) Take any monotone
suspicion score $`s_i`$ — the Req 10 posterior mean $`p_i`$ by default; the Req 13 score,
or isolation-forest/LOF anomaly scores, accepted as alternative non-conformity scores (the
latter's only sanctioned role, since as detectors they flag any minority subgroup; chatgpt
§Unsupervised; claude §3). For each group $`g`$, the calibration set is the verified-valid
respondents of that group drawn *at random* (Req 21), and

```math
p^{\mathrm{conf}}_i=\frac{1+\#\{j\in\mathrm{cal}_{g_i}: s_j\ge s_i\}}{n_{g_i}+1};\qquad
\text{reject if } p^{\mathrm{conf}}_i\le\alpha_R,\quad
\text{review if } \alpha_R<p^{\mathrm{conf}}_i\le\alpha_V,\quad
\text{accept otherwise.}
```

Under exchangeability of calibration and test valid respondents within a group,
$`P(\text{reject}\mid\text{valid},g)\le\alpha_R`$ for every $`g`$ — the per-group
false-positive constraint the equity requirement calls for, with no model assumption; it
is an equal-opportunity-style constraint on the valid class achieved by group-specific
thresholds rather than constrained training (claude §8; gemini §Metrics). Resolution is
$`p^{\mathrm{conf}}\ge 1/(n_g+1)`$, so the package refuses an $`\alpha_R`$ below the
resolution with an error naming the required $`n_g`$. Defaults (user decision 2026-09-26):
$`\alpha_R=0.05`$ and $`\alpha_V=0.20`$ for the pilot, tightening to $`\alpha_R=0.01`$
once pooled per-group calibration reaches $`n_g\ge 99`$ across waves (claude Rec eval);
recalibrate every wave. Groups: gender-minority status from the screener is the sole
calibration stratum (chosen — user decision; a ~60-respondent stratum affords resolution
≈ 0.016 and nothing finer); age band and race/ethnicity are monitored — realized
false-rejection rate reported per group (Req 17) — but not separately calibrated
(rejected for the pilot: resolution). Consequence of a rejection: the incentive is withheld
only after a verification attempt (claude Rec 5; user decision), recorded on the Req 21
`verification_attempted` field. (rejected) a "presumed nominal" calibration set (gemini
M4): at 40–70% prevalence a presumed-clean set is contaminated and the guarantee is
vacuous — calibration is on *verified*-valid only. (rejected) score-quantile bands such as
auto-rejecting the top 5% (chatgpt §Pilot Pipeline step 7). (rejected for v1) prediction-powered
online conformal detection (gemini §Unsupervised and Conformal). Conformal
Benjamini–Hochberg over the p-values, controlling the false discovery rate among
rejections, is the recorded alternative (Bates et al.; claude §4.5).

**Req 15 — Carrying classification uncertainty into substantive analysis.** Default:
multiple imputation of class membership — for each of $`M`$ posterior draws sample
$`C_i^{(m)}\sim\text{Bernoulli}(p_i^{(m)})`$, fit the substantive model on
$`\{i: C_i^{(m)}=0\}`$, combine with Rubin's rules — which propagates both parameter and
classification uncertainty (chosen; claude §5; prompt §3). Sensitivity analyses shipped
beside it: probability weighting by $`1-p_i`$ with a bootstrap over the LCM fit (it
understates variance otherwise; the continuous analogue of Cimpian, Timmer & Kim's
reweighting), and trimming bounds that assume invalid prevalence up to the posterior 97.5%
quantile and remove the most outcome-favorable or -unfavorable cases — which matters
because invalid responses are directional and inflate gender-minority disparities (claude
§5). (rejected for v1) the Bolck–Croon–Hagenaars three-step approach (gemini §Classification
Uncertainty): recorded as the alternative for structural-equation users; MI is more general
and already required. The variables excluded from the classifier are pre-registered so
that classification never depends on the outcome (claude §5). The package emits the
$`M`$ completed datasets, the weights, and the bounds; the substantive analysis itself is
the study's.

**Req 16 — Online monitoring during fielding.** Hourly: the Req 8 burst detector and a
CUSUM on the posterior-mean prevalence of new arrivals under the current Req 10 fit;
alarms are emitted with the triggering statistic and window. The package never pauses the
survey — that is study operations (claude §4.3; chatgpt §Sequential; prompt §3). Bayesian
online changepoint detection (Adams & MacKay) is the recorded upgrade. Pozzar's 576
screeners in 7 hours is the fixture both detectors must trip immediately (claude §4.3).

### Evaluation

**Req 17 — Ground-truth design, metrics, and validation before real data.** (claude §6 and
Rec eval as the superset; gemini §Evaluation; chatgpt §Pilot Pipeline; prompt §4.) Ground truth is
manufactured, not assumed: (a) seeded cases, labeled in Req 1(i) and excluded from every
substantive output — ≥40 scripted bots across two stealth levels (with and without stealth
plugins), ≥40 LLM persona agents across ≥3 frameworks and ≥2 model families following
Westwood's persona-plus-memory design, ≥30 red-team research-staff confederates (half
age misrepresentation, half gender-identity misrepresentation) under IRB approval, and an
instructed-carelessness arm in which consenting pilot testers answer the second half at
random (gemini §Evaluation) — sizes per user decision 2026-09-26; seeded cases estimate
per-threat sensitivity, never organic prevalence; (b) a verification subset of ~150
organic respondents, randomly selected and stratified by provisional score tertile and
group, oversampling the gender-minority stratum to ≥60, for identity and age
re-verification, with sensitivity and specificity estimated under inverse-probability
weights for verification bias (Begg & Greenes); (c) blinded two-coder adjudication of all
organic respondents on a rubric that excludes the signals under evaluation, with κ
reported, entering Req 10 as another imperfect test rather than as truth. Metrics:
per-threat sensitivity with Wilson intervals; specificity and false-rejection rate per
group (Req 14's monitored groups included); AUROC and PR-AUC, essential at prevalence that
swings 30–60% across waves; calibration of $`p_i`$ against the verified subset; Req 11's
posterior predictive checks; realized conformal false-rejection rate per group; and the
change in key disparity estimates under multiple imputation versus listwise deletion versus
bounds (Req 15). Removal rates are never reported as success (gemini §Metrics). Expected
precision, stated in advance: 36/40 seeded detections give a sensitivity interval of
0.77–0.96; 3/60 group false rejections give 0.017–0.137 — the pilot can rank signals and
detect gross group disparities but cannot certify a group false-rejection rate below about
5%, hence the α defaults of Req 14 (claude Rec eval). Before any real fit, Reqs 9, 10 and
14 are validated on simulated data with known parameters — recovery of $`\pi`$,
$`\mathrm{Se}_k`$, $`\mathrm{Sp}_k`$ and the group gaps $`\Delta_{gk}`$ at n = 400 and
K = 10; recovery of $`\kappa`$ and $`\delta`$; realized conformal false-rejection at or
below $`\alpha_R`$. Public resources — ASURRE, RAID, Affonso's trap repository, Gordon et
al.'s OSF data — calibrate the text-detector and trap indicators only; no public labeled
dataset of open-link fraud with verified ground truth exists, and none for adolescents
(claude §6, OP8).

**Req 18 — Adversarial drift protocol.** Each wave: re-seed agents on current model
families, refresh the Req 4 traps, re-measure the Req 7 detector false-positive rate, and
recalibrate Req 14; track per-signal sensitivity over waves as the drift indicator, since
which traps and environment checks work changes non-monotonically with model releases
(Affonso's 34-model, 2,040-trial test; claude §6, OP7; gemini OP4). Output is a drift
section of the evaluation report comparing waves.

### Package and written finding

**Req 19 — Package identity, stack, parity, determinism.** Package `respondent_validation`
in repository `respondent-validation` (chosen — user decision 2026-09-26; the prompt's
`survey-response-validation` rejected as the name). Python ≥ 3.14 per `pyproject.toml`;
NumPyro/JAX for the Bayesian models, ArviZ for diagnostics, Polars for every table (the
house stack; prompt Output). **(open — resolved by verification, not argument):** NumPyro,
JAX, ArviZ and Polars install and import under Python 3.14 via `uv add`; if JAX lacks a
3.14 wheel, the `requires-python` floor is lowered rather than the stack changed (chosen
fallback). R parity: `careless` and `PerFit` outputs on committed synthetic response
matrices are generated once and committed as golden files with the R session information;
the Req 3 indices must match within a stated tolerance (prompt §5; claude §7).
Determinism: every stochastic routine takes an explicit PRNG key and seeded runs
reproduce bit-identically. Graph, hashing and conformal library choices (networkx or
igraph, datasketch, MAPIE or crepes versus a short numpy implementation) are left to the
plans.

**Req 20 — Verified methods note and bibliography.** A GitHub-renderable document at
`docs/respondent-validation-review.md` delivering the brief's outputs (chosen — user
decision 2026-09-26; software-only scope rejected): the method comparison table — signal
inputs, supervision needed, reported performance and its ground truth, robustness to LLM
agents and human farms, implementation availability, maturity labeled peer-reviewed /
preprint / vendor claim (prompt Output; claude §3; chatgpt §Method Comparison; gemini
§Comparison); the prevalence-by-channel evidence table (claude KF1); the provenance of the
five model specifications above; the open-problems list seeded from claude OP1–8, gemini
OP1–4 and chatgpt §Open Problems; and a full reference list with DOIs or URLs in which
every citation is verified against the journal or DOI record or explicitly marked
*unverified*, each claim carrying one of the evidence labels *documented / supported /
asserted / contested / not found*. Conflicts among the three reviews are resolved on the
record, at minimum: the Affonso 2026 citation (gemini's DOI `10.1093/jcr/ucaf051` and
title "Survey Sabotage" versus claude's verified `10.1093/jcr/ucag006`, 53(3):619–633,
"Brief Commentary: A Framework for Detecting AI Agents in Online Research"); the
attribution of "Careless Responding: Why Many Findings Are Spurious or Spuriously
Inflated" to Guy et al. 2024 (gemini §Key Literature) against Guy et al. 2024 *Psychol
Methods* being the multimodal bot-screening paper (claude Caveats); the RelevantID
retirement date (June 30 versus "July" 2025); the Westwood figures; MacKinnon et al. 2025
retention; the secondhand Zhang et al. 2026 platform failure rates; the ChatGPT "559 of
560 replies were fraudulent" reading of Pinzón et al.; and every vendor detection rate
(CloudResearch Sentry, Verisoul, Prolific), which is labeled vendor and never cited as
performance evidence. Where the note's verified sensitivities and specificities exist for
an indicator, they are the source of Req 11's informative priors.

**Req 21 — Design-layer interface.** The package does not run the study; it specifies the
interface the study must satisfy for the model and the guarantee to hold: (a) the
`labels` table of Req 1(i) with `verification_pathway` and `selected_at_random`, because
Req 14's guarantee requires verified-valid calibration respondents drawn at random,
stratified by provisional score and group (claude Rec eval); (b) a
`verification_attempted` field on every rejected respondent, so that incentive withholding
follows a verification attempt (Req 14); (c) the pre-registration registry of Req 4; (d)
the telemetry contract of Req 2. The protocol itself — staff identity verification within
days of the screener, single-use survey links, re-verification before the incentive,
delayed payment (Lambert & Luisi 2026: 34,455 clicks became 384 enrolled adolescents at
US$101.76 each; claude §2c, Rec 1; chatgpt §Pilot Pipeline steps 1 and 6) — is study operations
and out of scope, recorded here because eligibility fraud by attentive humans has no
validated content-based signal (claude OP4): the package scores what verification leaves.

## Verification — observable outcomes

- [ ] Discharge of (open) in Req 1: a written inventory of the live Qualtrics export fields
      — `Q_RecaptchaScore` and its status field, `Q_DuplicateRespondent`, page-timer
      columns, and the embedded-data telemetry fields written by the Req 2 snippet — is
      committed, and the adapter's fixture is a real (anonymized) export row set.
- [ ] Discharge of (open) in Req 7: the AI-text detector's false-positive rate on
      verified-valid adolescent open-ends is measured with a Wilson interval and recorded
      before the indicator is enabled in the Req 10 configuration.
- [ ] Discharge of (open) in Req 19: NumPyro, JAX, ArviZ and Polars install and import under
      the `requires-python` floor via `uv add`, recorded in `pyproject.toml` and `uv.lock`,
      and a Req 10 fit on simulated data completes with clean diagnostics.
- [ ] Req 1 validation refuses a `telemetry` table carrying raw keystroke or trajectory
      columns, refuses unhashed identifiers, and accepts a fixture containing every label
      type and every `recaptcha_status` category.
- [ ] Req 3 indices match the committed R `careless` / `PerFit` golden files within the
      stated tolerance on every fixture matrix, including the polytomous $`l_z^*`$.
- [ ] Req 2's reference snippet, run in a Qualtrics survey preview, populates the four
      embedded-data fields the adapter reads with counts and booleans only.
- [ ] Req 4's registry rejects a low-base-rate item configured as a hurdle or a weight and
      produces one indicator per registered item on a fixture containing all five types.
- [ ] Reqs 5 and 6 produce one indicator per configured rule on a fixture in which every
      telemetry and attestation missingness category is present, with each missing
      category yielding its own indicator rather than a benign default.
- [ ] Req 12's report section, on a fixture with one seeded hypothesis-consistent cluster,
      reports that cluster's mean shift and variance ratio relative to the low-posterior
      band and leaves every $`p_i`$ unchanged.
- [ ] Req 8 down-weights a seeded carrier-NAT collision (many respondents on one IP/24,
      nothing else shared) below the community threshold while a seeded farm (shared payout
      handle, device hash and text bucket) forms one community; the burst detector trips on
      the Pozzar-shaped fixture and not on the baseline fixture (Req 16).
- [ ] Req 9 recovers $`\kappa`$, $`\delta`$ and the person-level careless shares on
      simulated data with known onset, with $`\delta>0`$ enforced and no label switching
      across chains.
- [ ] Req 10 recovers $`\pi`$, every $`\mathrm{Se}_k`$ and $`\mathrm{Sp}_k`$, and the group
      gaps $`\Delta_{gk}`$ on simulated data at n = 400, K = 10, two groups, with anchors
      present and again with anchors absent under informative priors; posterior intervals
      cover the truth at the nominal rate across seeds.
- [ ] Req 11's diagnostics are produced by one command: pairwise-concordance posterior
      predictive checks, prior-sensitivity tables for $`a_k`$ and $`\gamma_k`$, the
      two- versus three-class comparison, and the per-indicator monotonicity report; a
      fixture with a reversed indicator is flagged for re-signing.
- [ ] Req 13 produces $`\hat c`$ and the corrected probabilities out of fold, with seeded
      and organic positives handled separately, and its output is labeled a sensitivity
      analysis wherever it appears in the report.
- [ ] Req 14 refuses $`\alpha_R<1/(n_g+1)`$ with an error naming the required $`n_g`$; on
      simulated data the realized false-rejection rate among held-out verified-valid
      respondents is at or below $`\alpha_R`$ in every group; on the pilot the realized
      rate per group is reported with a Wilson interval.
- [ ] Req 15 emits $`M`$ completed datasets, Rubin-combined estimates for a fixture
      substantive model, bootstrapped weighted estimates, and trimming bounds, and the
      pre-registered exclusion list is enforced at classifier construction.
- [ ] Req 17's evaluation report is produced by one command from the pilot tables: per-threat
      sensitivity with Wilson intervals, false-rejection rate per calibration and monitored
      group, AUROC and PR-AUC, calibration against the verified subset, realized conformal
      rates, and the disparity-estimate comparison; seeded cases are absent from every
      substantive output.
- [ ] Req 18's drift section compares at least two seeding rounds per signal.
- [ ] Req 20's document exists with every named conflict resolved on the record, every
      citation verified or marked *unverified*, and every vendor figure labeled vendor.
- [ ] Req 21's schema documentation lists every field the study must supply, and a fixture
      lacking `selected_at_random` on a verified-valid respondent fails validation.
- [ ] Every seeded run of Reqs 9, 10, 13 and 14 reproduces bit-identically (Req 19).

## Out of scope

- Running the study: recruitment, IRB submission, the verification protocol, item and trap
  authoring, pausing the link. Req 21 specifies the interface; the operations are the
  study's (claude Rec 1; chatgpt §Pilot Pipeline).
- Panel-vendor authenticity checks — Prolific's bot and LLM checks, CloudResearch Sentry —
  which do not apply to an open social-media link or are unevaluated (claude KF5).
- Raw keystroke dynamics and mouse-trajectory biometrics (Req 1), and any device
  fingerprinting beyond a hashed device identifier.
- Adapters for platforms other than Qualtrics (Req 2).
- Bayesian graph models (stochastic block models), contrastive and non-negative PU
  learning, prediction-powered online conformal detection, and Bolck–Croon–Hagenaars
  three-step analysis — all recorded alternatives (Reqs 8, 13, 14, 15).
- Ordinal or continuous indicators in the fusion model; v1 indicators are binary (Req 10).
- Item-level response times through per-question JavaScript, and the discrete CODERS
  changepoint (Req 9).
- The substantive analysis itself; the package emits imputed datasets, weights and bounds
  (Req 15).
- Developing or training new machine-generated-text detectors; Req 7 consumes existing
  open-source detectors as one weak indicator.
- Certifying a per-group false-rejection rate below about 5% in the pilot (Req 17).

## Rollout note

Three (open) items open this rollout, in order of what they block. The Python 3.14 stack
check (Req 19) blocks the first model fit and is a one-command test. The Qualtrics export
inventory and the telemetry contract (Reqs 1–2) block ingestion and everything downstream
of it. The detector false-positive calibration (Req 7) needs verified-valid text and
therefore waits for the pilot's verification subset; the indicator ships disabled until it
is discharged.

Build order follows dependency and information order, and it **diverges from the reviews'
value ranking** — all three rank the fusion model, cognitive traps and verification
highest, which is the right way to rank recommendations and the wrong way to sequence
work. The input contract and indicator families (Reqs 1–8) are prerequisites for
everything; the fusion model (Reqs 10–11) must be validated on simulated data before it
sees a real row, and that simulation study is independent of the adapter and can run in
parallel with Reqs 1–2; the careless component (Req 9) follows because the LCM consumes it
as one indicator; conformal triage (Req 14) and uncertainty propagation (Req 15) follow
the LCM; monitoring (Req 16) and the evaluation harness (Reqs 17–18) close the loop. The
written finding (Req 20) is an investigation stage whose exit artifact is a document; its
verified sensitivities and specificities are the informative priors Req 11 needs when
anchors are scarce, so it belongs early, not last. The prompt and the three reviews retire
alongside this spec when its last stage completes; they are its inputs. Stage stamps and
the roadmap reference are added to this note by derive-roadmap.
