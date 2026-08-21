# Neurotechnology Governance, Privacy, and Human Agency

EEG-based systems can infer sensitive information about attention, fatigue, affect, health, and behavior. When they are embedded in workplaces, education, assistive devices, or consumer interfaces, their impact depends on who controls sensing, what inferences are permitted, how adaptation occurs, and whether users can understand and refuse system actions. Governance is therefore a design requirement, not an administrative step after deployment.

## Consent for Sensing Is Not Consent for Intervention

A person may consent to an EEG recording for one purpose without consenting to emotion inference, model personalization, data sharing, or an automated intervention. Systems should distinguish consent for collection, storage, inference, adaptation, disclosure, and action.

| Decision | Example question |
| --- | --- |
| Collection | May the system record EEG during this task? |
| Inference | May it estimate workload or affect from the recording? |
| Adaptation | May it use this session to update a personal model? |
| Sharing | May derived embeddings or reports leave the device? |
| Intervention | May it change interface difficulty or trigger an alert? |
| Retention | How long may raw and derived data be stored? |

Consent must be understandable and revocable. For systems used by people with communication or motor limitations, refusal and override mechanisms need to be accessible through more than one interaction channel.

![Consent and permission lifecycle for neurotechnology. The diagram should separate consent for EEG collection, storage, inference, personalization, sharing, intervention, and retention, with explicit user review, revocation, deletion, and safe fallback paths.](figures/neurotechnology-consent-lifecycle.png)

**Figure 11.11: Consent and permission lifecycle.** Consent is purpose-specific and revocable: permission to record EEG does not automatically authorize inference, adaptation, disclosure, intervention, or indefinite retention.

## Privacy, Security, and Data Minimization

Neural recordings, embeddings, and calibration histories can be identifying or reveal sensitive context. Collect only data needed for a defined purpose, prefer local processing when practical, restrict access to raw and derived records, and document retention and deletion policies.

Security should address the full pipeline: acquisition device, wireless transmission, storage, model updates, logs, and interfaces that display inferences. A privacy-preserving design also avoids unnecessary data repurposing. An EEG signal collected to improve assistive control should not become an unannounced productivity or emotion-surveillance measure.

![Privacy and accountability boundary map. The diagram should trace raw EEG, derived embeddings, calibration histories, model updates, logs, and displayed inferences across device, network, storage, and interface boundaries, marking access controls, minimization, retention, deletion, and audit points.](figures/neurotechnology-privacy-boundaries.png)

**Figure 11.12: Privacy and accountability boundaries.** Protection must cover raw signals, derived representations, model updates, logs, and displayed inferences across the complete acquisition and deployment pipeline.

## Human Agency and Meaningful Control

Affective estimates are uncertain and can be wrong for reasons that are invisible to a user. Systems should make sensing active, show when adaptation or automation is engaged, explain the role of neural evidence in important actions, and provide an accessible way to correct, pause, or override the system.

Agency also requires clear boundaries between an observed signal, an inferred state, and a decision. A system may say that it detected uncertain evidence consistent with high workload; it should not represent that estimate as a definitive account of the user's feelings or intent. High-stakes decisions should not be made solely from affective EEG inference.

## Fairness and Contextual Harm

Error rates and calibration burdens can differ across people because of hair type, skin-electrode contact, disability, medication, culture, communication style, task familiarity, or access to high-quality hardware. Evaluate performance, abstention, and intervention outcomes across relevant groups without treating demographic categories as fixed biological explanations.

Potential harms are contextual. A fatigue alert may be supportive in a user-controlled rehabilitation tool but coercive in employment monitoring. A system that optimizes engagement may be manipulative if it continuously changes content to sustain attention without transparent user goals. Governance should begin with a clear account of who benefits, who bears risk, and who can contest an inference.

## Accountability in Adaptive Systems

Adaptive models require records of model version, calibration source, update rule, confidence, system action, user correction, and safety override. These records support debugging, auditing, and meaningful recourse when a system behaves unexpectedly.

Before deployment, define which actions require confirmation, which conditions cause abstention, when the system must fall back to a safe mode, and who is responsible for monitoring failures. Human oversight is not a vague promise; it is a concrete set of controls and responsibilities.

## Practical Questions for Future Systems

A responsible neurotechnology project should answer:

- What neural data and derived inferences are necessary for the user-defined goal?
- Which inferences and interventions are explicitly permitted?
- How can a user see, correct, stop, or delete personalization?
- What happens when signal quality, confidence, or context is inadequate?
- Who can access raw data, embeddings, model updates, and interaction logs?
- How are harms, performance disparities, and user complaints investigated?

Trustworthy systems make these answers operational in the interface, data pipeline, and evaluation plan.

## References

- Yuste, R., Goering, S., Arcas, B. A. y., et al. (2017). Four ethical priorities for neurotechnologies and AI. Nature, 551, 159-163.
- Ienca, M., and Andorno, R. (2017). Towards new human rights in the age of neuroscience and neurotechnology. Life Sciences, Society and Policy, 13, 5.
- Nuffield Council on Bioethics. (2013). Novel neurotechnologies: Intervening in the brain.
- OECD. (2019). Recommendation of the Council on Responsible Innovation in Neurotechnology.
