# BCI Agents and Human-Machine Partnership

A BCI agent is more than a decoder connected to a chatbot or robot. It is an interactive system that interprets neural evidence, reasons over task context, chooses bounded actions, communicates its uncertainty, and learns with a user over time. This framing shifts the goal from maximizing isolated decoding accuracy to building a reliable partnership between person, neural interface, and intelligent system.

BCI agents can use passive estimates such as workload or affect, active control signals such as motor imagery or attention, and explicit user input. The challenge is to combine these channels without confusing a noisy state estimate with the user's intention or consent.

## Agent Components

A useful BCI-agent architecture separates roles that are often conflated:

| Component | Responsibility | Example output |
| --- | --- | --- |
| Neural perception | Estimate bounded neural variables and uncertainty | `high workload, confidence 0.62` |
| Context and memory | Track task state, calibration history, preferences, and prior actions | `three errors after difficulty increase` |
| Planner | Propose safe actions that pursue a user-approved goal | `offer a pause or reduce information density` |
| Action layer | Execute or request confirmation for an interface or device change | `highlight pause option` |
| Feedback layer | Explain state, confidence, and available control | `I may be detecting high workload; would you like to slow down?` |
| Adaptation manager | Update user and model state under explicit rules | `queue high-confidence calibration example` |
| Safety monitor | Enforce constraints, override rules, and audit logging | `block autonomous mobility command` |

![BCI agent architecture. The diagram should show neural perception, context and memory, planning, action, feedback, adaptation, and safety monitoring as separate components, with explicit intent, state uncertainty, consent, confirmation, and audit logs flowing between them.](figures/bci-agent-architecture.png)

**Figure 12.5: BCI agent architecture.** A reliable BCI agent separates neural perception from context reasoning, planning, action, adaptation, feedback, and safety enforcement so that no single model silently becomes the system's authority.

The separation is important. A language model may help with explanation and planning, but it should not silently become the neural decoder, safety monitor, or authority for an irreversible action.

## Intent, State, and Consent Are Different Signals

A central design rule is to separate three concepts:

- **Intent:** What action the user wants to take now.
- **State:** A probabilistic estimate of workload, fatigue, affect, attention, or other condition.
- **Consent and preference:** What assistance the user permits, prefers, or can revoke.

EEG can sometimes provide evidence about state and, in an active BCI, a limited intentional command. It is not a substitute for explicit consent. An agent should ask or wait for confirmation when an action has meaningful consequences, especially when state estimates are uncertain or when the system is adapting a shared environment.

**Example scenario:** A communication BCI detects a possible selection command with moderate confidence while also estimating fatigue. The agent slows the interface and offers a confirmation step. It does not infer that the user wants to end the conversation solely because fatigue appears elevated.

## Shared Autonomy Rather Than Full Automation

Neural commands are typically low bandwidth and uncertain. Shared autonomy lets an agent combine them with task rules and environmental context while preserving user control. A navigation agent may use a decoded directional preference while avoiding obstacles; a tutoring agent may offer choices shaped by workload without choosing the learning goal itself.

Shared autonomy should expose its role. Users need to know whether an action was directly commanded, suggested by the system, or executed autonomously under a safety policy. Logging this distinction makes later error analysis possible and supports meaningful user trust.

![Shared-autonomy control loop for a BCI agent. The diagram should show the user, neural decoder, context model, bounded planner, safety monitor, interface or device, and feedback loop, with separate paths for direct commands, system suggestions, confirmation, override, and autonomous safety actions.](figures/shared-autonomy-control-loop.png)

**Figure 12.6: Shared-autonomy control loop.** BCI assistance should combine uncertain neural commands with context and safety constraints while making direct control, suggestions, confirmation, and override paths visible to the user.

## Co-Adaptation and Continual Learning

A BCI agent operates in a nonstationary loop: the user learns strategies, the interface changes behavior, and the neural distribution changes with both. Agent memory should distinguish stable preferences from temporary state estimates and calibration examples from evaluation data.

Adaptation can be scheduled rather than continuous. For example, the system may update a decoder only after confirmed outcomes, periodically review drift with the user, or revert to a stable model after a sudden quality failure. This is safer than treating every agent interaction as an unquestioned training label.

## Evaluate Partnership Quality

Evaluation should include technical, behavioral, and experiential outcomes:

- decoding accuracy, calibration, latency, and abstention behavior;
- task completion, error recovery, and effective throughput;
- frequency and quality of confirmation requests;
- user learning curves and calibration burden;
- perceived agency, trust, workload, and controllability;
- fairness across users with different control strategies or sensor quality;
- and safety incidents, blocked actions, and recovery behavior.

Compare the full agent with decoder-only, context-only, fixed-assistance, and user-controlled baselines. This reveals whether agent reasoning genuinely helps rather than simply adding automation around a neural model.

## Future Direction

The strongest BCI agents will be modest about what neural data can establish and ambitious about collaboration. They will use EEG to make interfaces more responsive, not to replace the user's voice. Progress will depend on protocols that measure long-term co-adaptation, transparent shared control, and user-defined success rather than single-session offline accuracy.

## References

- Millan, J. del R., Rupp, R., Muller-Putz, G. R., et al. (2010). Combining brain-computer interfaces and assistive technologies: State-of-the-art and challenges. Frontiers in Neuroscience, 4, 161.
- Lotte, F., Larrue, F., and Muehl, C. (2013). Flaws in current human training protocols for spontaneous brain-computer interfaces: Lessons learned from instructional design. Frontiers in Human Neuroscience, 7, 568.
- Shneiderman, B. (2020). Human-centered artificial intelligence: Reliable, safe and trustworthy. International Journal of Human-Computer Interaction, 36(6), 495-504.
- Yuste, R., Goering, S., Arcas, B. A. y., et al. (2017). Four ethical priorities for neurotechnologies and AI. Nature, 551, 159-163.
