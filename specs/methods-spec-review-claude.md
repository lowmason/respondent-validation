# Detecting and Scoring Invalid Responses in Open-Link Online Surveys: State of the Art (2023–2026) and a Reference Design for survey-response-validation

For an incentivized, open social-media-recruited survey of 14–17-year-olds, no single signal is reliable. The best-supported design fuses many imperfect checks in a Bayesian semi-supervised latent class model with group-specific error rates. Its anchors are seeded agents and a randomly sampled verification subset, and a group-conditional (Mondrian) conformal layer sets the triage thresholds. The largest gains, though, come from design controls (identity re-verification before payment, single-use links), not from post-hoc detection.

## TL;DR

- **Threat mix depends on channel.** On panels, most bad data is human fraud, and deployed LLM agents are rare outside MTurk. Jaffe et al. 2026 tie most panel data-quality problems to fraudulent non-US humans. Gordon et al. 2026 (preprint) found primary agent-detection rates of 11.2–16% on MTurk and below 2% on all other platforms (most below 1%; CloudResearch Connect 0%, Prolific 0.2%). Open social-media links are far worse: 36–95% of responses are fraudulent in the studies that verified fraud. No study has yet measured LLM-agent prevalence on open links, so for this use case, prioritize human eligibility fraud, farms/duplicates and scripted bots, and treat autonomous agents as a fast-growing, unmeasured tail.
- **Detection evidence is weak and mostly unvalidated.** Westwood's agent passed 99.8% of 6,000 attention-check trials. Persona-grounded agents push machine-generated-text detectors to chance. The best supervised careless-responding classifier (Schroeders et al. 2022) reached at most 19% precision against instructed labels. Only a few studies report sensitivity/specificity against real ground truth: Pinzón et al. 2024 (verified closed lists), Affonso 2026 (97.1% agent detection vs. 4.1% human false positives, using seeded agents) and vendor-internal tests (Prolific, not independently replicated). No independent accuracy evaluation of IP-intelligence vendors for survey fraud exists.
- **Recommendation:** build the package around (1) a semi-supervised Bayesian LCM with random-effect conditional dependence, prevalence covariates and group-specific false-positive parameters; (2) an RT-based latent-response mixture/IRT model with onset for careless responding; (3) an identifier-graph plus burst detector for farms, duplicates and eligibility-fraud rings; (4) positive-unlabeled (PU) correction when training any supervised fusion model; and (5) Mondrian split-conformal three-band triage that guarantees per-group false-positive rates. Validate on a 400-respondent pilot using seeded bots and agents, red-team eligibility fraud and a randomly sampled, verification-weighted ground-truth subset.

## Key Findings

1. **Relative prevalence by channel (measured, not assumed).**
   - *Online panels/crowdsourcing:*
     - Jaffe et al. (2026, *Perspect Psychol Sci* 21(2)) report four studies over 5 years on MTurk and Lucid. They attribute most data-quality problems to fraudulent users outside the US, not bots.\[1\] The authors are CloudResearch-affiliated, which is a commercial interest to weigh.
     - Kennedy et al. (2020) found about 20% of 24,930 MTurk respondents across 38 studies were VPS or non-US users in 2018. In a September 2018 study, 26.9% were fraudulent, and removing attention-check failures only cut this to 17.3–20.5%.\[2\]
     - Gordon et al. (2026, PsyArXiv) recruited 5,200 respondents across 10 platforms; the primary flag caught 2.37% (123), concentrated on MTurk (11.2% filtered, 16% unfiltered), and "all other platforms had primary detection rates below 2%, with most below 1%" (CloudResearch Connect 0%, Prolific 0.2% filtered). The detected cases resembled traditional bots more than LLM agents.
     - Prolific reports (vendor claim) that a January 2026 internal audit found "0.8% of responses across the platform were flagged for AI-generated content," and that its bot checks flagged "only 0.04%" of more than 1 million submissions.
   - *Open social-media links:*
     - Pozzar et al. (2020): 94.5% of 271 completed surveys were fraudulent, 16.2% showed bot automation, and 82.5% of screeners arrived between midnight and 4 a.m.\[3\]
     - Griffin et al. (2022): an LGBTQ+ COVID-19 survey removed 773 of 1,251 wave-1 responses (61.8%).\[4\]
     - Pinzón et al. (2024): 36–39% of open-distribution responses were verified fraudulent, versus 0% from verified closed lists.\[5\]
     - MacKinnon et al. (2025): an LGBTQ2S+ study kept 957 of 1,377 responses (69.5%).\[6\]
   - *LLM-assisted humans:*
     - Veselovsky et al. (2023, preprint): 33–46% of 46 MTurk summaries were LLM-generated.\[7\]
     - Westwood (2025) cites 34.3% of a 2024 Prolific sample self-reporting AI use on open-ended items.\[8\]
     - The APS Observer reports a preprint led by Chicago Booth PhD student Grace Zhang (Zhang et al. 2026) in which verified humans (Mindworks baseline) "failed AI-detection tests around 2% of the time," versus 6% on Prolific, 10% on CloudResearch and 40% on MTurk.
   - *Bottom line:* the strongest evidence that fraud is "human, not bot" comes from panels. Open adolescent links combine all threat types and add eligibility fraud, which is incentive-driven and targeted at oversampled strata.

2. **What current checks can and cannot detect.**
   - *Attention checks are obsolete against agents.* Westwood's o4-mini agent passed 99.8% of 6,000 attention-check trials. It simulated reading times, mouse movement and keystroke-level typing with typos. It answered "17" (claiming to be human) in 100% of reverse-shibboleth trials, and 7 of 9 other LLMs did the same.\[8\] Affonso found traditional attention checks caught only 2.3% of 526 deployed agents.\[9\]
   - *Agents introduce bias, not just noise.* Their error is systematic (they infer the researcher's hypothesis and confirm it) rather than random.\[8\]\[10\] So the distributional fingerprint to look for is "too coherent / hypothesis-consistent," not "noisy."
   - *AI-text detectors are fragile.* On RAID (6M+ generations, 11 models, 11 attacks), 12 detectors were "easily fooled" by adversarial attacks, sampling changes and unseen models, and few could operate at false-positive rates below 1%.\[11\]\[12\] Wang, Mamaev & Leckie (arXiv 2609.17317, Sept 2026) show persona-grounded agents push detectors toward chance. A training-free aggregator of behavioral traces improved mean AUROC by +0.14 over the best text detector.\[13\]
   - *IP geolocation alone is a poor fraud signal.* In Pinzón et al., 95% of fraudulent responses had US IPs; state-level recall was better (50% of fraud outside California).\[14\] Kennedy et al. relied on IP Hub's own, never independently checked, error rates.\[2\] Only 2.20% of 6.2M residential-proxy IPs appeared on any blacklist (Mi et al. 2019).\[15\]

3. **Fusion models are underused.**
   - I found no peer-reviewed application of Hui–Walter or Dendukuri–Joseph conditional-dependence LCMs to survey-fraud checks.
   - The closest is a best–worst-scaling health-preferences study (PubMed 40316881). There, LCA classes aligned with age-verified respondent categories: 76% likely fraudulent, 14% unsure, 10% likely real.\[16\]
   - Practice remains rule counts with unknown error rates. The ADOPT study (2026) explicitly states it had no ground-truth labels.\[17\]

4. **Careless-responding models are mature but threat-specific.**
   - Ulitzsch et al.'s RT-based latent-response mixture (Psychometrika 2022) and the explanatory mixture IRT model (BJMSP 2022) identify C/IER at item-by-person level with no threshold tuning.\[18\]\[19\]
   - Welz & Alfons' CODERS (arXiv 2303.07167; R package `carelessonset`) finds the onset changepoint, with nonparametric false-positive-rate guarantees.\[20\]\[21\]
   - These models detect trait-unrelated responding. They will not detect a coherent LLM agent or an attentive adult posing as a 15-year-old.

5. **Platform features.**
   - *Qualtrics:* RelevantID was deprecated on **June 30, 2025**. The user's "retired July 2025" is effectively right, but the cut-over date was June 30 after an initially announced June 15. Its replacement, `Q_DuplicateRespondent`, is a post-submission true/false flag (no score) that cannot be used in branch logic, and the fraud score was discontinued.\[22\]\[23\]
   - *Prolific:* authenticity checks work only for Prolific-sourced participants and are vendor-validated. Bot checks report "100% sensitivity and 100% specificity" in internal testing against five agents, and LLM checks report 98.7% precision with a 0.6% false-positive rate.\[24\]\[25\]
   - *CloudResearch Sentry:* a pre-survey vetting layer that the vendor says works with "any sample source or survey platform."\[26\] In principle it could sit in front of an open link, but no independent evaluation exists.
   - *Consequence:* for open social-media links, neither Prolific's checks nor any panel-level history applies. You must instrument your own.

## Details

### 1. Threat taxonomy with detection-relevant properties

| Threat | Distinguishing signature | Primary signals | Evidence on prevalence (channel) |
|---|---|---|---|
| Scripted bots / form-fillers | Implausible timing, no pointer events, `navigator.webdriver`/CDP artifacts, datacenter IP, bursts | Telemetry, environment checks, burst detection | Pozzar: 16.2% bot automation (open social link); Gordon: MTurk agent flags look bot-like |
| Human farms (VPN/VPS) | Shared devices/IP ranges, templated open-ends, time-zone mismatch, fast but attentive | IP/ASN, fingerprint graph, near-duplicate text, time-zone consistency | Jaffe (panels): dominant source; Kennedy: ~20% VPS/non-US on MTurk 2018 |
| Eligibility fraud (adults as minors; cis as gender-minority) | Coherent answers; inconsistency with age-typical knowledge/behavior; clustering in oversampled cells; screener–survey mismatch | Cross-wave consistency, identity re-verification, graph clustering, group-specific prevalence covariates | No channel-specific estimate found; BWS study: age verification reclassified many respondents |
| Duplicates / ballot stuffing | Shared identifiers, near-identical content, sequential submissions | Identifier graph, MinHash, `Q_DuplicateRespondent` | Pinzón: "consecutive submissions" among best indicators |
| Mischievous responders | Endorsement of multiple low-base-rate items; jointly implausible extremes | Low-base-rate screeners, boosted-model mischief index, reweighting | Cimpian et al. 2018: YRBS (n=148,960) disparities shrink substantially after removal |
| Careless/insufficient effort | Fast RTs, invariance, inconsistency, trait-unrelated responses, onset mid-survey | Long-string, IRV, person-fit, RT mixtures, CODERS | Ubiquitous; not channel-specific |
| Autonomous LLM agents | Coherent, hypothesis-consistent, systematically biased; fail vision-architecture traps; automation environment | Environment checks, cognitive traps, behavioral traces, distributional tests | Gordon: <2% outside MTurk, most <1% (panels); unmeasured on open links |
| LLM-assisted humans | Paste events, focus loss, homogenized style, length/lexicon shift | Paste/blur telemetry, cross-respondent homogeneity | Veselovsky: 33–46% (MTurk, n=46); Westwood cites 34.3% (Prolific self-report) |

The mischievous-responder literature matters doubly here. Mischief and cis-as-gender-minority fraud both inflate minority–majority disparities in the same direction. The method is contested, however: Phillips et al. (2020) argue screening can mislabel genuine SGM youth. Delgado-Ron et al. (2024, *Child Development*) applied Cimpian, Timmer & Kim's (2023) reweighting to 9,674 SGM youth aged 15–29. Weights fell as the number of selected racial identities increased (mean weight 1.01 for one versus 0.40 for four or more).\[27\] That is a warning that "unusual pattern" indices can penalize multiply-marginalized respondents.

### 2. Signal families

**(a) Paradata/telemetry.**
- *Page timing:* Qualtrics exposes page-level timers (first click, last click, page submit, click count), not item-level RTs. Use Ulitzsch, Shin & Lüdtke's (2024, *Behav Res Methods*) screen-time weighting rather than item-RT models unless you add custom JavaScript.
- *Keystroke, mouse, paste and focus/blur:* these are the most discriminating signals against LLM-assisted humans. Veselovsky et al. validated their classifier with copy/paste logs, and 41/46 summaries involved pasting.\[7\] Prolific's LLM check (launched May 2025) "examines over 15 different behaviors, such as copy-pasting and tab-switching," with vendor-reported 98.7% precision and a 0.6% false-positive rate. They are weak against agents that type keystroke-by-keystroke (Westwood).\[8\]
- *Automation/headless signals:* `navigator.webdriver`, CDP artifacts, missing plugins and inconsistent `userAgentData` catch naive Selenium/Playwright but are defeated by stealth plugins and real-browser agents; no peer-reviewed survey-context evaluation exists. Gordon et al.'s "automated environment check" (details not public) reportedly achieved perfect discrimination in pilot testing, a preprint result with seeded ground truth.\[9\]
- *Device attestation:* reCAPTCHA v3/Enterprise scores, Cloudflare Turnstile and Private Access Tokens attest "a real browser/device," not "a real, eligible human." Westwood's agent was designed to accommodate reCAPTCHA-bypass tools.\[8\]
- *Costs for minors:* telemetry and fingerprinting are personal data, keystroke dynamics are quasi-biometric, and CAPTCHAs impose accessibility costs. Minimize collection to derived features (e.g., paste count, not keystroke streams).

**(b) Network/device.**
- *No independent accuracy studies exist.* I found no independent, non-vendor evaluation reporting sensitivity/specificity for MaxMind minFraud, IPQualityScore, Scamalytics, IPHub, ipinfo or FingerprintJS in survey-fraud detection.
- *Indirect evidence:* residential proxies evade blacklists (only 2.20% of 6.2M were listed; Mi et al., IEEE S&P 2019). Geolocation databases are reliable at country level but not city level (Poese et al. 2011). Browser fingerprints are far less unique than assumed: 33.6% unique across 2.07M fingerprints, and 18.5% on mobile (Gómez-Boix et al., WWW 2018).\[15\]\[28\]\[29\]\[30\] Adolescents are mostly on mobile, so fingerprint collisions among legitimate teens on the same phone model and OS are expected.
- *Survey-specific evidence:* Pinzón et al. rank MaxMind's minFraud Risk Score among their best indicators, measured with "predictive power" (a precision variant) and recall against verified lists.\[31\]
- *Time-zone/IP/self-report consistency* is cheap and useful. Pozzar's 82.5% midnight–4 a.m. screeners is the canonical signature.\[3\]

**(c) Identity.**
- Pinzón et al.'s email-address score (structure, digits, randomness) was among the best indicators,\[31\] and the BWS study found "suspicious email" the most frequently failed red flag.\[16\]
- Lambert & Luisi (2026, *JMIR Form Res*) recruited SGM adolescents aged 13–17 via Facebook/Instagram in 8 Southern states. Staff verified identity within 3–5 days, then sent single-use links, and required re-verification before paying the $20 incentive. 34,455 clicks became 384 enrolled, at US$101.76 per enrolled adolescent.\[32\] This is the closest published analogue to the target design and shows verification is operationally feasible at the needed scale.

**(d) Closed-ended content.**
- Long-string, IRV, even–odd consistency, psychometric synonyms/antonyms, Mahalanobis D and lz/lz* person-fit all target trait-unrelated responding. Ward & Meade (2023, *Annu Rev Psychol* 74) is the current synthesis.\[33\]
- Logically inconsistent item pairs and low-base-rate items catch mischief and some eligibility fraud (e.g., age-inconsistent school grade, driving, employment).
- None detects a coherent persona agent. Westwood's agent maintained memory and persona-consistent psychometric profiles.\[8\]

**(e) Open-ended content.**
- *Duplicates:* exact and near-duplicate detection (MinHash/SimHash on shingles; embedding cosine) targets farms and duplicates.
- *Homogenization:* stylometric homogeneity across respondents targets LLM assistance. Zhang, Xu & Alvero (2025, *Sociol Methods Res*) document homogenization of AI-assisted open-ends.
- *Detectors:* RAID (Dugan et al., ACL 2024, pp. 12463–12492) found Binoculars best at low false-positive rates, but "few detectors can operate at FPR<1%."\[12\] Lebrun et al. (2024) found detectors performed poorly on ChatGPT output passed through Undetectable.AI.\[34\]
- *Operational rule:* use detector scores only as one weak LCM indicator, never as a hurdle. With adolescents writing short, informal text, detector false-positive rates are unknown.

**(f) Cross-respondent structure.**
- Respondent–identifier bipartite graphs (IP/24, ASN, device hash, normalized email local-part, payout handle, text-shingle buckets), connected components/Leiden communities and submission-time bursts target farms, duplicates and eligibility-fraud rings. These are the hardest signals for independent fraudsters to fake, because the signal lies in cross-respondent correlation.
- The distributional anomaly specific to agents is systematic bias. Test whether suspected clusters' substantive responses deviate toward hypothesis-consistent or persona-stereotyped means, not toward uniform noise.

### 3. Comparison of methods

| Method | Signal inputs | Supervision | Reported performance (ground truth) | Robust to LLM agents / human farms | Implementation | Maturity |
|---|---|---|---|---|---|---|
| Rule-count / multiple hurdle | Any binary flags | None | Pinzón: per-indicator recall/"predictive power" vs verified closed lists; others report removal rates only | Low / Medium | Ad hoc | Peer-reviewed (descriptive) |
| IP/VPN intelligence | IP, ASN, proxy scores | Vendor model | Kennedy: IP Hub error rates not independently verified; Pinzón: geolocation poor nationally | Low (residential proxies) / Medium | rIP (R), vendor APIs | Peer-reviewed use; vendor accuracy claims only |
| Attention checks (ACQs) | Instructed items | None | Westwood: 99.8% agent pass; Affonso: 2.3% agent detection | Very low / Low | Any platform | Peer-reviewed |
| Cognitive traps | Vision-architecture-hard items | None | Affonso: 97.1% of 526 agents detected, 4.1% of 1,007 humans flagged (seeded agents; Prolific humans) | High (currently) / None | Public trap repository | Peer-reviewed (JCR 2026) |
| Environment/automation checks | Browser environment | None | Gordon: perfect in pilot (preprint); Prolific: 100%/100% (vendor, five agents) | High vs naive, unknown vs adaptive / None | Custom JS; Prolific only | Preprint / vendor |
| Paste/focus telemetry | JS events | None | Prolific LLM check: 98.7% precision, 0.6% FPR (vendor); Veselovsky: paste validated classifier | Low vs typing agents; high vs pasting humans / Low | Custom JS | Vendor / preprint |
| AI-text detectors | Open-ended text | Pretrained | RAID: fragile under attacks; Wang et al. 2026: near chance vs persona agents | Very low / None | Binoculars, Fast-DetectGPT (open source) | Peer-reviewed benchmarks |
| Behavioral-trace aggregator | Response-level cues | Few-shot | Wang et al.: +0.14 mean AUROC over best detector (ASURRE benchmark) | Medium / Low | Project code (arXiv) | Preprint (Sept 2026) |
| Supervised GBM | Indicators + paradata | Instructed labels | Schroeders: ≤19% of flagged were instructed-careless (per Alfons & Welz 2024) | Unknown / Unknown | R/Python generic | Peer-reviewed |
| Unsupervised (IF/LOF/autoencoder) | Response matrix | None | Simulation + empirical (Alfons & Welz) | Low / Low | scikit-learn, PyOD | Peer-reviewed (careless only) |
| RT latent-response mixture / mixture IRT | Responses + RTs | None (model-based) | Simulation recovery + empirical (Ulitzsch 2022, 2024) | Low / Low (targets C/IER) | Mplus/Stan code; no Python package | Peer-reviewed |
| CODERS onset | Responses (+RT) | None | Simulation; FPR guarantee (Welz & Alfons) | Low / Low | R `carelessonset` (TensorFlow) | Preprint |
| Bayesian LCM (imperfect tests) | Binary/ordinal flags + covariates | None or anchors | No survey-fraud validation; BWS LCA aligned with age verification | Medium / Medium–High | poLCA, randomLCA (R); Stan/NumPyro custom | DTA-mature; untested for fraud |
| PU learning | Features + known-fraud labels | Positive-only | No survey application found | Depends on features | scikit-learn wrappers, pulearn | Mature in ML; untested here |
| Identifier graph + bursts | Identifiers, timestamps, text hashes | None | Pinzón: consecutive submissions among best; Pozzar: temporal clustering | Low–Medium / High | networkx/igraph, datasketch | Peer-reviewed signals; model untested |
| Mischief index + reweighting | Low-base-rate screeners | None | YRBS sensitivity analyses (disparity change, not Se/Sp) | None / Low | Cimpian R code | Peer-reviewed, contested |
| Identity re-verification | Contact, callback, payout re-verify | Human | Lambert & Luisi: operational feasibility; BWS: age verification improved categorization | High / High | Staff protocol | Peer-reviewed (descriptive) |
| Conformal triage | Any score + verified-valid calibration | Calibration labels | No survey application found | Inherits score | ~30 lines numpy/polars; MAPIE | Mature theory; untested here |

### 4. Five most promising approaches: specification, estimands, identifiability, Python sketches

#### 4.1 Semi-supervised Bayesian latent class model with conditional dependence, prevalence covariates and group-specific error rates

*Specification.* For respondent i with latent class $C_i\in\{0,1\}$ (1 = invalid) and K binary checks $y_{ik}$: $\text{logit}\,P(C_i=1)=\alpha_0+x_i^\top\beta$ and $\text{logit}\,P(y_{ik}=1\mid C_i=c,u_i,g_i)=a_{g_i k}+c\,\gamma_k+\lambda_{ck}u_i$, with $u_i\sim N(0,1)$ and $\gamma_k\ge 0$. $a_{gk}$ are group-specific false-positive logits (e.g., gender-minority vs not). The shared random effect $u_i$ with class-specific loadings is the Qu–Tan–Kutner/Dendukuri–Joseph-style conditional-dependence device. Anchors (seeded agents/bots, verified duplicates, verified-valid cases) enter through their emission likelihood only, so they inform $a,\gamma,\lambda$ without biasing prevalence.

*Estimands.* Per-respondent posterior $P(C_i=1\mid y_i)$; group-specific specificity $1-\text{expit}(a_{gk})$ and sensitivity per check; prevalence as a function of ad set, hour and stratum.

*Identifiability.*
- *Number of tests:* under conditional independence, one population needs K≥3 tests ($2^K-1\ge 2K+1$).
- *Hui–Walter with two tests:* needs ≥2 populations with different prevalence and equal Se/Sp across populations. Ad sets or strata can play this role only if error rates are invariant across them, and that is exactly the assumption group-specific $a_{gk}$ relaxes, costing degrees of freedom.
- *Conditional dependence:* each random-effect loading adds parameters. With ~8–12 heterogeneous checks and anchors, the model is identified; without anchors, informative priors on at least some Se/Sp are needed. Cerullo et al. report that diffuse priors in Stan LCMs produce diagnostic failures from near-non-identifiability.\[35\]
- *Label switching:* broken by the monotonicity constraint $\gamma_k\ge 0$ (invalid respondents trip every check at least as often). Verify it per check; drop or re-sign checks that violate it.
- *Anchor representativeness* is the key untestable assumption: seeded agents are not a random sample of real fraud. Include a separate "seeded" emission component, or restrict anchors to checks where representativeness is plausible.
- *Local dependence:* checks from the same family (e.g., three timing rules) must share a random effect or be collapsed.

```python
import jax
import jax.numpy as jnp
import numpyro
import numpyro.distributions as dist
from numpyro.infer import MCMC, NUTS


def lcm_model(y, x, anchor, group, n_groups):
    '''Semi-supervised two-class LCM; anchor: -1 unlabeled, 0 valid, 1 invalid.'''
    n, k = y.shape
    p = x.shape[1]
    alpha0 = numpyro.sample('alpha0', dist.Normal(-0.5, 1.5))
    beta = numpyro.sample('beta', dist.Normal(0.0, 1.0).expand([p]).to_event(1))
    a0 = numpyro.sample('a0', dist.Normal(-2.5, 1.0).expand([n_groups, k]).to_event(2))
    gap = numpyro.sample('gap', dist.HalfNormal(2.5).expand([k]).to_event(1))
    lam = numpyro.sample('lam', dist.HalfNormal(0.75).expand([2, k]).to_event(2))
    with numpyro.plate('resp', n):
        u = numpyro.sample('u', dist.Normal(0.0, 1.0))
    eta0 = a0[group] + lam[0] * u[:, None]
    eta1 = a0[group] + gap + lam[1] * u[:, None]
    em0 = dist.Bernoulli(logits=eta0).log_prob(y).sum(-1)
    em1 = dist.Bernoulli(logits=eta1).log_prob(y).sum(-1)
    logit_pi = alpha0 + x @ beta
    j0 = em0 + jax.nn.log_sigmoid(-logit_pi)
    j1 = em1 + jax.nn.log_sigmoid(logit_pi)
    ll_unl = jnp.logaddexp(j0, j1)
    ll = jnp.where(anchor == 1, em1, jnp.where(anchor == 0, em0, ll_unl))
    numpyro.factor('loglik', ll.sum())
    numpyro.deterministic('p_invalid', jnp.exp(j1 - ll_unl))


def fit_lcm(y, x, anchor, group, n_groups, seed=0):
    kernel = NUTS(lcm_model, target_accept_prob=0.9)
    mcmc = MCMC(kernel, num_warmup=1000, num_samples=1000, num_chains=4)
    mcmc.run(jax.random.PRNGKey(seed), y, x, anchor, group, n_groups)
    return mcmc
```

Diagnostics to ship: posterior predictive checks on pairwise check concordance; prior sensitivity on `a0` and `gap`; comparison with a 3-class model (valid / careless / fraudulent); and a hierarchical `gap[group]` with partial pooling, since the gender-minority stratum will be small.

#### 4.2 RT-based latent-response mixture / mixture IRT with onset (careless and mischievous responding)

*Specification* (after Ulitzsch et al. 2022; simplified): each person–item response is attentive (graded response model in θ, with lognormal RT at person speed τ) or careless (uniform over categories, RT shifted faster by δ>0). The careless probability is $\text{logit}\,\pi_{ij}=g_i+\kappa\,\text{pos}_j$, so κ>0 encodes onset (a smooth analogue of CODERS' changepoint).

*Estimands.* Item-level $P(\text{careless}_{ij})$; person-level careless proportion; onset slope κ; trait estimates purified of C/IER.

*Identifiability.* Two separating assumptions: careless responses are unrelated to θ (reverse-keyed items help), and careless RTs differ in location; with page-level timing only, collapse to screen-level components (Ulitzsch, Shin & Lüdtke 2024). It needs multiple items per construct and ≥2 constructs to separate θ from careless patterns; δ>0 identifies the careless component. *Blind spot:* an attentive fraudster or LLM agent is "attentive" under this model, so its output enters the fusion LCM as one indicator, not as the fraud score.

```python
def rt_mixture_model(resp, log_rt, pos, n_cat):
    '''Item-level attentive/careless mixture with RT (after Ulitzsch et al., 2022).'''
    n, j = resp.shape
    with numpyro.plate('items', j):
        a = numpyro.sample('a', dist.LogNormal(0.0, 0.5))
        b = numpyro.sample('b', dist.Normal(0.0, 1.0))
        c0 = numpyro.sample('c0', dist.Normal(-1.5, 1.0))
        d = numpyro.sample('d', dist.HalfNormal(1.0).expand([n_cat - 2]).to_event(1))
        beta_rt = numpyro.sample('beta_rt', dist.Normal(1.0, 1.0))
        sig_a = numpyro.sample('sig_a', dist.HalfNormal(0.5))
    cut = jnp.concatenate([c0[:, None], c0[:, None] + jnp.cumsum(d, -1)], -1)
    with numpyro.plate('persons', n):
        theta = numpyro.sample('theta', dist.Normal(0.0, 1.0))
        tau = numpyro.sample('tau', dist.Normal(0.0, 0.5))
        g = numpyro.sample('g', dist.Normal(-2.0, 1.5))
    kappa = numpyro.sample('kappa', dist.Normal(0.0, 1.0))
    delta = numpyro.sample('delta', dist.HalfNormal(1.0))
    sig_c = numpyro.sample('sig_c', dist.HalfNormal(0.75))
    pred = a * theta[:, None] - b
    lp_att = (dist.OrderedLogistic(pred, cut).log_prob(resp)
              + dist.Normal(beta_rt - tau[:, None], sig_a).log_prob(log_rt))
    lp_car = (-jnp.log(n_cat)
              + dist.Normal(beta_rt - tau[:, None] - delta, sig_c).log_prob(log_rt))
    logit_pc = g[:, None] + kappa * pos
    lp = jnp.logaddexp(jax.nn.log_sigmoid(-logit_pc) + lp_att,
                       jax.nn.log_sigmoid(logit_pc) + lp_car)
    numpyro.factor('loglik', lp.sum())
    numpyro.deterministic('p_careless', jnp.exp(jax.nn.log_sigmoid(logit_pc) + lp_car - lp))
```

For mischievous responding, add a third component that inflates endorsement of pre-registered low-base-rate items. This is a mixture version of Cimpian's screener logic, which yields a probability rather than a boosted-model rank.

#### 4.3 Identifier graph + submission-burst detection (farms, duplicates, eligibility-fraud rings)

*Specification.* Build a bipartite graph $G=(R\cup I,E)$ between respondents R and identifiers I (IP/24, ASN, device hash, normalized email local-part, payout handle, MinHash LSH buckets of open-ends via `datasketch`). Project onto R with edge weights equal to the sum of inverse identifier frequencies (downweighting common carrier-NAT IPs), and report component or Leiden community size. Separately, model submission counts per window as Poisson with a baseline tied to ad delivery (impressions × click-through), and flag windows where standardized excess exceeds a threshold.

*Estimands.* Community membership and size; per-respondent max-shared-identifier degree; burst indicator and onset time.

*Assumptions.* Shared identifiers do not imply one person: carrier-grade NAT, school Wi-Fi and popular phone models generate legitimate collisions, especially for adolescents (fingerprint uniqueness is only 18.5% on mobile).\[29\] Treat graph features as LCM indicators with their own group-specific false-positive rates, not as hurdles.

```python
import polars as pl
import networkx as nx


def identifier_long(df):
    '''df: rid (str), ip, device_hash, email, payout_handle.'''
    keyed = df.with_columns(
        pl.col('ip').str.replace(r'\.\d+$', '').alias('ip24'),
        pl.col('email').str.to_lowercase().str.extract(r'^([^@+]+)', 1)
        .str.replace_all(r'\.', '').alias('email_key'),
    )
    long = keyed.unpivot(
        index='rid', on=['ip24', 'device_hash', 'email_key', 'payout_handle'],
        variable_name='kind', value_name='key',
    ).filter(pl.col('key').is_not_null())
    shared = (long.group_by(['kind', 'key'])
              .agg(pl.col('rid').n_unique().alias('n'))
              .filter(pl.col('n').ge(2)))
    return long.join(shared.select(['kind', 'key']), on=['kind', 'key'], how='semi')


def respondent_clusters(long):
    g = nx.Graph()
    for rid, kind, key in long.select(['rid', 'kind', 'key']).iter_rows():
        g.add_edge(('r', rid), (kind, key))
    rows = []
    for cid, comp in enumerate(nx.connected_components(g)):
        rids = [node[1] for node in comp if node[0] == 'r']
        rows.extend((rid, cid, len(rids)) for rid in rids)
    return pl.DataFrame(rows, schema=['rid', 'cluster_id', 'cluster_size'], orient='row')


def burst_flags(df, every='10m', z=4.0):
    counts = (df.sort('submitted_at')
              .group_by_dynamic('submitted_at', every=every)
              .agg(pl.len().alias('n')))
    return (counts
            .with_columns(pl.col('n').rolling_median(window_size=36).alias('base'))
            .with_columns(pl.col('n').sub(pl.col('base'))
                          .truediv(pl.col('base').add(1).sqrt()).alias('score'))
            .with_columns(pl.col('score').ge(z).alias('burst')))
```

Online monitoring: run `burst_flags` and a CUSUM on hourly LCM posterior-mean prevalence during fielding, and pause the link automatically when either triggers; Pozzar's 576 screeners in 7 hours would have tripped both immediately.\[3\] A Bayesian online changepoint model (Adams & MacKay) is the principled upgrade.

#### 4.4 Positive-unlabeled correction for supervised fusion

*Specification.* "Labeled" means known invalid (s=1): verified duplicates, confirmed adults, seeded agents; everything else is unlabeled. Under SCAR (labeled positives are a random sample of positives, with propensity c), $P(C=1\mid x)=P(s=1\mid x)/c$, with c estimated as the mean classifier score on held-out labeled positives (Elkan & Noto).

*Estimands:* the calibrated $P(\text{invalid}\mid x)$ and the class prior.

*Identifiability.* SCAR is almost certainly violated, because the fraud you caught is the easy fraud. Under SAR, the labeling propensity e(x) must be modeled, e.g., as a function of detection pathway (Bekker & Davis). Do not pool seeded agents with organic caught fraud when estimating c. Use PU as the discriminative complement to the LCM, and prefer the LCM with fewer than about 50 organic labeled positives.

```python
import numpy as np
from sklearn.ensemble import HistGradientBoostingClassifier
from sklearn.model_selection import StratifiedKFold


def elkan_noto(X, s, n_splits=5, seed=0):
    '''s = 1 for labeled invalid, 0 unlabeled. Returns SCAR-corrected P(invalid | x) and c.'''
    g = np.zeros(len(s))
    folds = StratifiedKFold(n_splits=n_splits, shuffle=True, random_state=seed)
    for tr, te in folds.split(X, s):
        clf = HistGradientBoostingClassifier(max_depth=3, learning_rate=0.05)
        clf.fit(X[tr], s[tr])
        g[te] = clf.predict_proba(X[te])[:, 1]
    c = g[s == 1].mean()
    return np.clip(g / c, 0.0, 1.0), c
```

Calibrate g (isotonic or Platt, out of fold) before dividing by c; uncalibrated boosted probabilities make ĉ meaningless.

#### 4.5 Mondrian split-conformal three-band triage with group-conditional false-positive control

*Specification.* Take any suspicion score s (e.g., the LCM posterior). Draw a calibration set of **verified-valid** respondents for each group g, sampled at random from those who pass verification. Compute $p_i=(1+\#\{j\in\text{cal}_g: s_j\ge s_i\})/(n_g+1)$. Reject if $p_i\le\alpha_R$, review if $\alpha_R<p_i\le\alpha_V$, otherwise accept.

*Guarantee:* if calibration and test valid respondents in group g are exchangeable, $P(\text{reject}\mid\text{valid},g)\le\alpha_R$ for each g. This is exactly the per-group false-positive constraint the equity requirement calls for, with no model assumptions. Conformal BH over the p-values instead controls FDR among rejections (Bates et al.).

*Requirements.* Resolution is $p\ge 1/(n_g+1)$, so $\alpha_R=0.01$ needs ≥99 verified-valid calibration respondents per group and α=0.05 needs ≥19. Exchangeability breaks if verification is offered selectively, so randomize who is verified. Recalibrate every wave. $\alpha_R$ is set by the cost of wrongly denying a legitimate teen's incentive and data; the review band is sized to reviewer capacity.

```python
def conformal_p(cal_scores, test_scores):
    cal = np.sort(np.asarray(cal_scores))
    n_ge = cal.size - np.searchsorted(cal, test_scores, side='left')
    return (1.0 + n_ge) / (cal.size + 1.0)


def mondrian_triage(df, alpha_reject=0.01, alpha_review=0.10):
    '''df: rid, group, score (higher = more suspicious), is_cal (verified-valid calibration).'''
    parts = []
    for grp in df.get_column('group').unique().to_list():
        sub = df.filter(pl.col('group').eq(grp))
        cal = sub.filter(pl.col('is_cal')).get_column('score').to_numpy()
        test = sub.filter(pl.col('is_cal').not_())
        p = conformal_p(cal, test.get_column('score').to_numpy())
        parts.append(test.with_columns(pl.Series('p_conf', p)))
    return pl.concat(parts).with_columns(
        pl.when(pl.col('p_conf').le(alpha_reject)).then(pl.lit('reject'))
        .when(pl.col('p_conf').le(alpha_review)).then(pl.lit('review'))
        .otherwise(pl.lit('accept')).alias('band')
    )
```

### 5. Carrying classification uncertainty into substantive analysis

- *Multiple imputation of class membership:* for each of M posterior draws, sample $C_i^{(m)}\sim\text{Bernoulli}(p_i^{(m)})$, fit the substantive model on $C=0$ and combine with Rubin's rules. This propagates both parameter and classification uncertainty, and is the preferred default.
- *Probability weighting:* weight by $1-p_i$, the continuous analogue of Cimpian, Timmer & Kim's (2023) reweighting. It is simpler, but understates variance unless bootstrapped over the LCM fit.
- *Bounds:* report trimming bounds that assume invalid prevalence up to $\bar q$ (the posterior 97.5% quantile), removing the most outcome-favorable or -unfavorable cases. This matters for disparity estimates because invalid responses are directional: mischief and cis-as-gender-minority fraud inflate SGM disparities.
- *Joint modeling:* ideally embed the substantive model in the LCM. The risk is outcome-dependent classification, so pre-register which variables are excluded from the classifier.

### 6. Evaluation: designs, metrics, datasets

- *Seeded ground truth:* scripted bots (Selenium/Playwright, with and without stealth); LLM persona agents across several frameworks, following Westwood's persona-plus-memory design and Affonso's 526-agent deployment; red-team research-staff confederates (adults posing as 14–17-year-olds; cisgender posing as gender-minority), with IRB approval. Seeded cases estimate sensitivity per threat but not organic prevalence.
- *Verification subset:* randomly sample organic respondents, stratified by provisional score and group, for identity/age re-verification (Lambert & Luisi-style). Estimate Se/Sp with inverse-probability weights to correct verification bias (Begg–Greenes).
- *Blinded two-coder adjudication:* coders see a rubric excluding the signals under evaluation, to avoid incorporation bias; report κ. Adjudication labels are an imperfect reference and should enter the LCM as another test, not as truth.
- *Metrics:* per-threat sensitivity with Wilson CIs; specificity/FPR by group; AUROC and PR-AUC (essential at 30–60% prevalence swings across waves); calibration against the verified subset; LCM posterior predictive checks; realized conformal FPR per group.
- *Adversarial adaptation:* Affonso tested the six retained traps against 34 vision-language models (2,040 API trials, February 2026) and found model improvement is non-monotonic, so each new model changes which traps work. Re-seed agents every wave and track per-signal sensitivity over time as a drift indicator.
- *Public datasets/simulators:* ASURRE (Wang et al. 2026; AI-assisted survey responses paired with genuine human responses, three surveys);\[36\] RAID; Affonso's public cognitive-trap repository with OSF data; Gordon et al.'s OSF data;\[9\] Kennedy et al.'s rIP tooling. No public labeled dataset of open-link survey fraud with verified ground truth exists, and none for adolescents.

### 7. Software and tooling

- *R:* `careless` (long-string, IRV, even–odd, psychometric synonyms, Mahalanobis); `carelessonset` (CODERS; GitHub; requires Python TensorFlow/Keras via reticulate);\[37\] `PerFit` (lz, lz*); `mirt` (IRT; mixture IRT); `poLCA` (conditional-independence LCA with covariates); `randomLCA` (random-effects LCA); `rIP` (IP Hub VPS checks).
- *Python gaps:* no maintained equivalent of `careless`, `PerFit` lz*, `randomLCA` or `carelessonset`, and no Python mixture-IRT package with RT components. PU tooling (`pulearn`) and conformal tooling (`MAPIE`, `crepes` for Mondrian) exist. Bayesian LCMs for imperfect tests are routinely written in Stan in the diagnostic-test-accuracy literature (Cerullo et al., arXiv 2103.06858 and 2509.18489). This is the package's clearest contribution opportunity: one Python/JAX toolkit with careless indices, person-fit, the RT mixture, the semi-supervised LCM and conformal triage.
- *Platform features and open links:*

| Feature | Works on open social-media link? | Evidence type |
|---|---|---|
| Qualtrics bot detection (reCAPTCHA v3 score) | Yes | Vendor; Westwood designed around bypass |
| Qualtrics `Q_DuplicateRespondent` (post-RelevantID, from June 30, 2025) | Yes | Vendor; boolean, no published accuracy |
| Qualtrics RelevantID fraud score | No (deprecated) | — |
| Prolific bot and LLM authenticity checks | No (Prolific participants only) | Vendor internal testing |
| CloudResearch Sentry | Possibly (vendor says any sample source; requires routing through Sentry) | Vendor claims; no independent evaluation |
| Cognitive traps (Affonso repository) | Yes | Peer-reviewed |
| Custom JS telemetry and environment checks | Yes | Preprints / vendor analogues |

### 8. Equity

- *Group-specific parameters:* the LCM's $a_{gk}$ gives each check its own false-positive rate per group, and the Mondrian conformal layer enforces $P(\text{reject}\mid\text{valid},g)\le\alpha_R$ per group. That is an equal-opportunity-style constraint on the valid class (Hardt, Price & Srebro), achieved by group-specific thresholds rather than constrained training.
- *VPN/IP evidence:* no empirical study measures whether VPN/IP rules disproportionately exclude legitimate LGBTQ+ or privacy-sensitive respondents. Kennedy et al. acknowledge some US respondents use VPS "out of privacy concerns," and 76% of VPS users in their Study 1 failed none of the quality checks.\[2\] Mayer et al. (2025, *J Genet Couns*) argue conceptually that screening burdens marginalized communities with privacy concerns and that duplicate-IP rules exclude shared-network users.\[38\] Lambert & Luisi document higher recruitment costs in states with more anti-LGBTQIA+ legislation,\[32\] a plausible driver of VPN use among the very teens being oversampled.
- *Decision:* treat VPN/datacenter status as a soft LCM indicator with a group-specific false-positive rate, never as an auto-reject hurdle, until the pilot measures its false-positive rate among verified gender-minority teens.

### 9. Adjacent fields that transfer

Diagnostic-test accuracy without a gold standard (Hui–Walter, Dendukuri–Joseph, latent-class multivariate probit; Cerullo et al. 2025 simulation) is the direct template for the fusion LCM.\[39\] Click/ad fraud and sybil detection contribute bipartite user–resource graphs, lockstep-burst detection and SybilRank-style trust propagation seeded from verified-valid nodes. Social-media bot detection documents rapid adversarial drift. Alfons & Wilms' robust matrix completion for rating-scale data with fake profiles (arXiv 2412.20802)\[40\] bridges recommender-system fake-profile detection to Likert matrices.

## Recommendations

### Reference pipeline (package architecture)

1. **Design layer (before detection).** Screener collects contact info; verification comes before a **single-use** survey link. Re-verify identity before paying the incentive, and delay payment (Lambert & Luisi protocol). Pre-register low-base-rate and logically linked items (age-consistent school/driving/employment items) and 2–3 cognitive traps from Affonso's repository, refreshed per wave; Affonso reports "as few as two traps achieve over 93% detection with under 3% false positives, adding approximately 1 minute of survey time."
2. **Instrumentation.** Page timers; custom JS for paste count, focus-loss count, pointer-event presence and automation-environment flags (derived counts only); reCAPTCHA score, `Q_DuplicateRespondent`, IP/ASN/proxy lookup and time-zone offset vs self-reported state.
3. **Feature layer.** Careless indices (long-string, IRV, even–odd, psychometric antonyms, Mahalanobis, lz*); RT-mixture posterior (§4.2); identifier-graph cluster size and burst indicator (§4.3); open-end near-duplicate degree and cross-respondent embedding homogeneity; AI-text detector score (weak); trap failures.
4. **Fusion.** Semi-supervised Bayesian LCM (§4.1), 2- vs 3-class, group-specific false-positive parameters, prevalence covariates for ad set/hour/stratum. PU-corrected GBM (§4.4) as a sensitivity analysis.
5. **Decision.** Mondrian conformal three-band triage (§4.5): auto-accept; human review (blinded rubric); reject, which withholds the incentive only after a verification attempt.
6. **Analysis.** Multiple imputation over class draws (default), plus weighting and bounds as sensitivity analyses (§5).
7. **Monitoring.** Live burst and CUSUM on posterior prevalence, with auto-pause, during fielding.

### Evaluation protocol for a ~400-respondent pilot

- **Organic sample:** ~400 respondents via open ads, with the gender-minority oversample as fielded.
- **Seeded cases** (labeled and excluded from substantive analysis): ≥40 scripted bots across two stealth levels; ≥40 LLM persona agents across ≥3 frameworks and ≥2 model families; ≥30 red-team humans (half age-misrepresentation, half gender-identity misrepresentation).
- **Verification subset:** randomly select ~150 organic respondents, stratified by provisional score tertile and group, oversampling the gender-minority stratum to ≥60, for identity/age re-verification.
- **Blinded two-coder adjudication** of all organic respondents on a rubric that excludes the signals under test.
- **Precision you can expect** (Wilson 95% CIs): sensitivity 36/40 = 0.90 gives a CI of 0.77–0.96; a group FPR of 3/60 = 0.05 gives a CI of 0.017–0.137. So the pilot can rank signals and detect gross group disparities in false positives, but cannot certify group FPR below about 5%. Pool calibration sets across waves before setting $\alpha_R=0.01$, which needs ≥99 verified-valid per group.
- **Pre-registered outputs:** per-threat sensitivity per signal and for the fused score; group-specific FPR per signal, especially VPN/datacenter and graph features; verification-weighted AUROC/PR-AUC and calibration of $P(\text{invalid})$; LCM posterior predictive checks; realized conformal FPR per group; change in key disparity estimates under MI vs listwise removal vs bounds.

### Open problems where the literature is thin

1. Prevalence of autonomous LLM agents on **open social-media links** (all measurements are panel-based).
2. Any independent sensitivity/specificity evaluation of IP-intelligence, proxy-detection or fingerprinting vendors for survey fraud.
3. Empirical group-specific false-positive rates of VPN/IP/fingerprint rules among legitimate LGBTQ+ and adolescent respondents.
4. Detection of **eligibility fraud by attentive humans** (adults as minors; cis as gender-minority). No validated content-based signal exists, only verification.
5. LCMs for survey-fraud checks: no peer-reviewed application with conditional dependence, anchors or group-specific parameters. Anchor representativeness is untested.
6. Conformal or other distribution-free error control for survey-quality flags: no applications found.
7. Longevity of cognitive traps and environment checks against adaptive agents (Affonso's non-monotonicity finding suggests a perpetual refresh cycle).
8. AI-text detector false-positive rates on short, informal adolescent writing, and public labeled open-link fraud benchmarks (none exists for minors).

## Caveats

- **Anchor-reference verification** (status as of September 2026):

| Anchor as given | Verified citation | Status / discrepancy |
|---|---|---|
| Westwood 2025 PNAS | Westwood SJ. PNAS 122(47):e2518075122. doi:10.1073/pnas.2518075122\[8\] | Published Nov 2025; accurate |
| Jaffe et al. 2026 Perspect Psychol Sci | Jaffe SN, Moss AJ, Hartman R, Rosenzweig C, Gautam R, Robinson J, Litman L. "The Bots Ruining Social Science Are Not Bots at All." 21(2). doi:10.1177/17456916251404872 | Online Jan 6, 2026; accurate; panels only; CloudResearch-affiliated authors |
| Pinzón et al. 2024 Front Res Metr Anal | 9:1432774. doi:10.3389/frma.2024.1432774\[41\] | Published Dec 2, 2024; accurate |
| Comachio et al. 2025 BMJ EBM | 30(3):173–182. doi:10.1136/bmjebm-2024-113170 | Online Dec 22, 2024; issue 2025; accurate |
| Guy et al. 2024 Psychol Methods | Guy AA, Murphy MJ, Zelaya DG, Kahler CW, Sun S. doi:10.1037/met0000696\[42\] | Advance online Sept 9, 2024; final volume/pages not verified |
| Ward & Meade 2023 Annu Rev Psychol | 74:577–596. doi:10.1146/annurev-psych-040422-045007\[33\] | Accurate; title says "careless responding" |
| Ulitzsch et al. 2022 Psychometrika | 87(2):593–619. doi:10.1007/s11336-021-09817-7 | Accurate; affiliation erratum 87:798 |
| Schroeders et al. 2022 EPM | 82(1):29–56. doi:10.1177/00131644211004708 | Accurate (online April 2021) |
| Veselovsky et al. 2023 | arXiv:2306.07899\[43\] | Preprint only; related peer-reviewed piece: Commun ACM 68(3):42–47 (2025), doi:10.1145/3685527 |
| Lebrun et al. 2024 | Front Robot AI 10:1277635. doi:10.3389/frobt.2023.1277635 | Published Feb 2, 2024 in the 2023 volume |
| Affonso 2026 J Consum Res | "Brief Commentary: A Framework for Detecting AI Agents in Online Research." 53(3):619–633. doi:10.1093/jcr/ucag006 | Online Mar 25, 2026; October 2026 issue; accurate |
| Gordon et al. 2026 preprint | Gordon A, Rothschild D, Affonso FM, Sulik J, Hauser DJ, Pepin K, Jones S. "AI Agent Prevalence and Data Quality Across Multiple Online Sample Providers." PsyArXiv (osf.io/preprints/psyarxiv/pvdjr) | Preprint; Affonso describes it as Prolific's evaluation, a potential conflict of interest |
| Kennedy et al. 2020 PSRM | 8(4):614–629. doi:10.1017/psrm.2020.6\[44\] | Accurate; "rwhois" component not verified |
| Dennis, Goodson & Pearson 2020 | Behavioral Research in Accounting 32(1):119–134. doi:10.2308/bria-18-044\[45\] | Journal is BRIA (not Accounting Horizons); exact fraud proportions unverified |
| Griffin et al. 2022 | Quality & Quantity 56(4):2841–2852. doi:10.1007/s11135-021-01252-1\[46\] | Accurate |
| Pozzar et al. 2020 | J Med Internet Res 22(10):e23021. doi:10.2196/23021\[47\] | Accurate |
| Robinson-Cimpian 2014 | Educational Researcher 43(4):171–185 | Accurate |

- **Not fully verified in this review:** Chen, Urminsky et al. 2026 (PsyArXiv; seen only secondhand); Zhang et al. 2026 human-vs-platform AI-detection failure rates (reported via APS Observer); the venue of Cimpian, Timmer & Kim 2023; authorship of the BWS LCA study (PubMed 40316881); venues for Binoculars (arXiv 2401.12070) and Fast-DetectGPT (arXiv 2310.05130). Classic methods references (Hui & Walter 1980; Dendukuri & Joseph 2001; Elkan & Noto 2008; Bekker & Davis 2020; Hardt, Price & Srebro 2016; Bates et al. 2023; Begg & Greenes 1983) are cited from the standard literature; DOIs not re-checked.
- **Vendor claims:** Prolific's figures and CloudResearch's Sentry claims are internal and not independently replicated. Several "independent" evaluations (Jaffe et al.; Gordon et al.) involve authors affiliated with platforms that sell data-quality products.
- **The code sketches are architectural references,** not tested production code. Check NumPyro shape broadcasting and priors against simulated data from your own instrument before use.

## Sources

1. [The Bots Ruining Social Science Are Not Bots at All - Shalom N. Jaffe, Aaron J. Moss, Rachel Hartman, Cheskie Rosenzweig, Richa Gautam, Jonathan Robinson, Leib Litman, 2026](https://journals.sagepub.com/doi/abs/10.1177/17456916251404872)
2. <https://www.nicholasjgwinter.com/assets/papers/Kennedy_etal_PSRM_2020.pdf>
3. <https://ncbi.nlm.nih.gov/pmc/articles/PMC7578815>
4. ["Ensuring Survey Research Data Integrity in the Era of Internet Bots" by Marybec Griffin, Richard J. Martino et al.](https://academicworks.cuny.edu/bb_pubs/1210/)
5. [https://public-pages-files-2025.frontiersin. ...](https://public-pages-files-2025.frontiersin.org/journals/research-metrics-and-analytics/articles/10.3389/frma.2024.1432774/xml/nlm)
6. [Journal of Medical Internet Research - Introducing Novel Methods to Identify Fraudulent Responses (Sampling With Sisyphus): Web-Based LGBTQ2S+ Mixed-Methods Study](https://doi.org/10.2196/63252)
7. <https://arxiv.org/html/2306.07899v1>
8. [The potential existential threat of large language models to online survey research - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC12663962/)
9. [Research | Felipe M. Affonso](https://felipemaffonso.com/research/)
10. [(PDF) The potential existential threat of large language models to online survey research](https://www.researchgate.net/publication/397806283_The_potential_existential_threat_of_large_language_models_to_online_survey_research)
11. [RAID: A Shared Benchmark for Robust Evaluation of Machine-Generated Text Detectors - ACL Anthology](https://aclanthology.org/2024.acl-long.674/)
12. [RAID: A Shared Benchmark for Robust Evaluation of Machine-Generated Text Detectors](https://arxiv.org/html/2405.07940v1)
13. [\[2609.17317v1\] Towards Detecting AI-Assisted Responses in Online Surveys](https://arxiv.org/abs/2609.17317v1)
14. [(PDF) AI-powered fraud and the erosion of online survey integrity: an analysis of 31 fraud detection strategies](https://www.researchgate.net/publication/386344774_AI-powered_fraud_and_the_erosion_of_online_survey_integrity_an_analysis_of_31_fraud_detection_strategies)
15. [Resident Evil: Understanding Residential IP Proxy as a Dark Service](https://conferences.computer.org/sp/pdfs/sp/2019/ResidentEvilUnderstandingResidentialIPProxyasa.pdf)
16. [Identifying and Managing Fraudulent Respondents in Online Stated Preferences Surveys: A Case Example from Best-Worst Scaling in Health Preferences Research - PubMed](https://pubmed.ncbi.nlm.nih.gov/40316881/)
17. [Practical Fraud Detection and Prevention in Incentivized Online Surveys: Secondary Analysis of the ADOPT Study - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC13426555/)
18. [A Response-Time-Based Latent Response Mixture Model for Identifying and Modeling Careless and Insufficient Effort Responding in Survey Data | Psychometrika](https://link.springer.com/article/10.1007/s11336-021-09817-7)
19. [An explanatory mixture IRT model for careless and insufficient effort responding in self‐report measures - Ulitzsch - 2022 - British Journal of Mathematical and Statistical Psychology - Wiley Online Library](https://bpspsychub.onlinelibrary.wiley.com/doi/full/10.1111/bmsp.12272)
20. [Identifying the Onset of Careless Responding](https://arxiv.org/pdf/2303.07167)
21. [\[2303.07167v3\] When Respondents Don't Care Anymore: Identifying the Onset of Careless Responding](https://arxiv.org/abs/2303.07167v3)
22. [News and Announcements](https://kb.wisc.edu/news/real-estate-news/it.wisc.edu/%22https:/kb.wisc.edu/news.php?id=13804)
23. [Relevant ID Replacement](https://uis.jhu.edu/2025/06/18/relevant-id-replacement)
24. [Authenticity checks detect AI agents best | Prolific](https://www.prolific.com/resources/authenticity-checks-how-we-tested-the-most-accurate-method-for-identifying-agentic-ai)
25. [New authenticity checks detect AI misuse in research | Prolific](https://www.prolific.com/resources/introducing-authenticity-checks-beta-ensure-genuine-human-responses-in-the-age-of-ai)
26. [How to Stop Survey Fraud From Burning Your Brand](https://www.cloudresearch.com/fire/)
27. [Mitigating invalid data bias in the estimation of sexual orientation disparities in a survey of youth in US and Canada | Child Development | Oxford Academic](https://academic.oup.com/chidev/article/95/5/e373/8255434)
28. [IP geolocation databases: unreliable?: ACM SIGCOMM Computer Communication Review: Vol 41, No 2](https://dl.acm.org/doi/10.1145/1971162.1971171)
29. [Browser Fingerprinting: A survey](https://arxiv.org/pdf/1905.01051)
30. [Hiding in the Crowd: an Analysis of the Effectiveness of Browser Fingerprinting at Large Scale](https://dl.acm.org/doi/fullHtml/10.1145/3178876.3186097)
31. [TYPE Original Research PUBLISHED 02 December 2024 DOI 10.3389/frma.2024.1432774](https://public-pages-files-2025.frontiersin.org/journals/research-metrics-and-analytics/articles/10.3389/frma.2024.1432774/pdf)
32. <https://pmc.ncbi.nlm.nih.gov/articles/PMC13528605/>
33. <https://api.crossref.org/works/10.1146/annurev-psych-040422-045007>
34. [Frontiers | Detecting the corruption of online questionnaires by artificial intelligence](https://www.frontiersin.org/journals/robotics-and-ai/articles/10.3389/frobt.2023.1277635/full)
35. [Meta-analysis of dichotomous and ordinal tests without a gold standard](https://arxiv.org/pdf/2103.06858)
36. [Towards Detecting AI-Assisted Responses in Online Surveys · Pith Review](https://pith.science/paper/2609.17317)
37. [GitHub - mwelz/carelessonset: Implementation of the working paper "I Don't Care Anymore: Identifying the Onset of Careless Responding" by Max Welz and Andreas Alfons.](https://github.com/mwelz/carelessonset)
38. mcp\_\_Scholar\_Gateway\_\_semanticSearch
39. [Latent class multivariate probit and latent trait models for evaluating test accuracy without a gold standard: A simulation study](https://arxiv.org/pdf/2509.18489)
40. [Andreas Alfons](https://personal.eur.nl/alfons/)
41. [www.frontiersin.org](https://www.frontiersin.org/journals/research-metrics-and-analytics/articles/10.3389/frma.2024.1432774/pdf)
42. [Data Integrity in an Online World: Demonstration of Multimodal Bot Screening Tools and Considerations for Preserving Data Integrity in Two Online Social and Behavioral Research Studies With Marginalized Populations - PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC11890204/)
43. [Artificial Artificial Artificial Intelligence: Crowd Workers Widely Use Large Language Models for Text Production Tasks](https://arxiv.org/abs/2306.07899)
44. [The shape of and solutions to the MTurk quality crisis | Political Science Research and Methods | Cambridge Core](https://www.cambridge.org/core/journals/political-science-research-and-methods/article/shape-of-and-solutions-to-the-mturk-quality-crisis/521AEEB9A9753D5C6038440BD123826C)
45. <https://api.crossref.org/works?query.bibliographic=Dennis+Goodson+Pearson+online+worker+fraud+Mechanical+Turk+accounting&rows=3&select=DOI,title,author,container-title,volume,issue,page,published>
46. [Ensuring survey research data integrity in the era of internet bots - Rutgers, The State University of New Jersey](https://www.researchwithrutgers.com/en/publications/ensuring-survey-research-data-integrity-in-the-era-of-internet-bo/)
47. [Journal of Medical Internet Research - Threats of Bots and Other Bad Actors to Data Quality Following Research Participant Recruitment Through Social Media: Cross-Sectional Questionnaire](https://www.jmir.org/2020/10/e23021/)
