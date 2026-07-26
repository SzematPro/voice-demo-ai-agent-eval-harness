# Voice extension demo · Cite-or-refuse healthtech AI agent (trilingual EN / ES / PT-BR)

**Demonstration only · synthetic data · not a medical device · not medical advice**

Trilingual ElevenLabs text-to-speech golden samples from a **measurement-first, cite-or-refuse conversational health agent** for **medication adherence**. This public repository is the voice showcase for the **AI Agent Eval Harness (healthtech)** — a medication-adherence health agent whose every clinical assertion must cite a verified knowledge-base card or refuse, paired with a **CI-gated evaluation harness** that proves the property holds. It ships three MP3 renderings of one compliant agent response, one per locale the harness evaluates — Spanish (`es-419`), English (`en-US`), and Brazilian Portuguese (`pt-BR`) — produced with the ElevenLabs `eleven_multilingual_v2` model. The demo carries the cite-or-refuse contract from text into the voice modality. Built and evaluated on 100% synthetic data by [Waldemar Szemat](https://szemat.pro), AI Solutions Architect and Fractional Head of AI.

## Part of the AI Agent Eval Harness project

- **Documentation (source of truth):** https://agent-docs.szemat.pro
- **Interactive demo:** https://agent.szemat.pro
- **Voice architecture decision (ADR-0013):** https://agent-docs.szemat.pro/en/adr/adr-0013-voice-extension/
- **Governance & readiness index:** https://agent-docs.szemat.pro/en/governance/
- **Portfolio hub:** https://szemat.pro
- **GitHub:** https://github.com/SzematPro · **LinkedIn:** https://www.linkedin.com/in/szematpro · **Microsoft Learn:** https://learn.microsoft.com/en-us/users/wszemat/

## The conversation

The agent is asked, in Spanish:

> Empecé hace dos meses con una pastilla para la presión arterial y algunos días simplemente la olvido. ¿Algún consejo para mantener la rutina?

(English gloss: *I started a blood-pressure pill two months ago and some days I just forget it. Any advice for keeping the routine?*)

The narrated response honours the compliance properties the harness enforces on every output before it can ship:

- **Cite-or-refuse.** The clinical content is anchored to a verified knowledge-base card (`card-hypertension-adherence-barriers`, MedlinePlus, us-gov domain); with no citable source, the agent refuses.
- **Safe envelope.** No dose change, no medication stop, and no drug-class switch is ever advised; side effects and clinical judgement are routed back to the prescribing clinician.
- **Motivational-interviewing stance.** An open, autonomy-honouring question rather than a directive.

The English and Brazilian Portuguese renditions are translations of the same response with the same compliance shape. This is the path toward the modality-agnostic auditability the harness is built for.

## Trilingual voice rendering (ElevenLabs TTS)

Three MP3s, one compliant response rendered per locale. These are the original **click-to-play golden run** — the first audio produced through the live demo's on-demand TTS path (see [how the live demo works](#how-the-live-demo-works)).

- [English (en-US) — Matilda: compliant medication-adherence response, ElevenLabs eleven_multilingual_v2](./golden-es419-hypertension-anchor-01_en_matilda.mp3)
- [Spanish (es-419) — Sarah: compliant medication-adherence response](./golden-es419-hypertension-anchor-01_es_sarah.mp3)
- [Brazilian Portuguese (pt-BR) — Bella: compliant medication-adherence response](./golden-es419-hypertension-anchor-01_pt-br_bella.mp3)

| File | Locale | Voice | Characters | Size |
|---|---|---|---|---|
| `golden-es419-hypertension-anchor-01_es_sarah.mp3` | es-419 | Sarah | 959 | ~0.95 MB |
| `golden-es419-hypertension-anchor-01_en_matilda.mp3` | en-US | Matilda | 884 | ~0.85 MB |
| `golden-es419-hypertension-anchor-01_pt-br_bella.mp3` | pt-BR | Bella | 958 | ~0.97 MB |

Model: `eleven_multilingual_v2`. Output format: `mp3_44100_128`.

## Voice selection (ElevenLabs premade voices)

`eleven_multilingual_v2` renders any premade ElevenLabs voice across the languages the model supports, so the per-locale choice is a timbre decision, not a primary-language one. Three premade voices were chosen — all female, all professional — one per locale, on the criterion of which timbre best supports medication-adherence counseling in that language. The agent persona stays consistent across modalities while tonal nuance adapts to the language.

| Locale | Voice | ElevenLabs voice ID | Why this voice |
|---|---|---|---|
| es-419 | **Sarah** | `EXAVITQu4vr4xnSDxMaL` | Mature, reassuring, confident; carries adherence counseling without paternalism, matching the motivational-interviewing stance. |
| en-US | **Matilda** | `XrExE9yKIg1WjnnlVkGX` | Knowledgeable, professional (`informative_educational`); native primary-language match and the cleanest English healthcare fit. |
| pt-BR | **Bella** | `hpp4J3VqNfWAUOO0d1Us` | Bright and warm; carries Brazilian Portuguese melodic prosody more naturally than a flatter delivery. |

The golden run was produced within the ElevenLabs free tier (~2,800 of the 10,000 monthly characters). In the deployed demo, voice is a paid, gated capability (below); this free-tier render is the one-time golden reference.

## How the live demo works

The three MP3s are static golden samples, but the [interactive demo](https://agent.szemat.pro) generates voice live. Per [ADR-0013](https://agent-docs.szemat.pro/en/adr/adr-0013-voice-extension/) and [ADR-0017](https://agent-docs.szemat.pro/en/adr/adr-0017-voice-off-by-default-safety-policy/), the voice layer is an additive, channel-agnostic extension:

- **Off by default.** Text-only chat is the baseline. A session that never unlocks voice makes zero ElevenLabs API calls.
- **Entitlement- and consent-gated.** Voice unlocks only on keys provisioned with the voice entitlement, and only after explicit, server-recorded consent. The server returns `403 consent_required` until consent is granted and `403 voice_not_entitled` for keys without the entitlement; neither is a client-side flag.
- **Click-to-play TTS.** On-demand `eleven_multilingual_v2` synthesis is triggered when the user clicks play on a completed assistant turn — audio is generated only when it is actually consumed. Locale voices: Sarah (es-419), Matilda (en-US), Bella (pt-BR).
- **Push-to-talk STT.** Microphone audio streams over a WebSocket to ElevenLabs realtime speech-to-text; the final transcript enters the normal `/chat` pipeline (retrieval, guardrails, generation).
- **Audio never retained.** Audio is processed in transit and never persisted; only the transcribed text is logged, subject to the same guardrail redaction as typed input.
- **Contract-stable.** The `/chat` JSON schema is frozen; TTS metadata rides a dedicated `voice_audio` SSE event, so consumers that ignore voice are unaffected.

Full-duplex conversational voice is architecturally compatible and deferred to a future milestone; click-to-play TTS plus push-to-talk STT already prove the pipeline is channel-independent.

## Why voice auditability matters for healthtech AI

The harness's core property is that every model output reaching a user either cites a verified source or refuses. In regulated industries that property has to hold across modalities, not just on the page. A text answer is auditable by reading it; a voice answer must be auditable by listening to it. This demo is the first step toward a voice channel where the cite-or-refuse contract is preserved end to end and the audio itself can be evaluated for faithfulness and prosody-level hallucination by an extension of the existing eval harness.

The broader system is documented across 36 ADRs and a governance package mapping to HIPAA readiness, the EU AI Act, the NIST AI RMF, ISO/IEC 42001, SOC 2, MITRE ATLAS, and Chile's Ley 19.628/21.719. See the [governance and readiness index](https://agent-docs.szemat.pro/en/governance/).

## About this repository

This is a public showcase artifact: a short write-up plus three synthetic audio renders. **It contains no source code.** The implementation and evaluation harness live in separate, private repositories; that source is licensed Apache-2.0 and available on request. Do not treat this repository as the harness source. The full technical and governance documentation is public at [agent-docs.szemat.pro](https://agent-docs.szemat.pro).

## Credits and licensing

- **Audio:** generated with [ElevenLabs](https://elevenlabs.io) `eleven_multilingual_v2` on the ElevenLabs free tier. The voices are licensed ElevenLabs premade voices, not clones of any real person.
- **This repository** — the README text and the three audio samples — is licensed **CC-BY-4.0**; see [`LICENSE`](./LICENSE). The agent and evaluation-harness source code is separately licensed Apache-2.0 and is private (available on request).

> [!CAUTION]
> **Reference implementation, not a medical device.** Built and evaluated on 100% synthetic, representative data by design. It is a capability and readiness reference, not a compliance certification or legal advice, is **not clinically validated**, is **not medical advice**, and handles **no production PHI**. The conversation, the patient persona, and the audio are synthetic — no real patient is involved. In the live demo, voice is off by default and consent-gated, and user audio is never retained; the files here are offline-rendered agent-output samples. Medical questions belong with a qualified clinician.

## About the author

**Waldemar Szemat — AI Solutions Architect and Fractional Head of AI.** Takes GenAI systems from pilot to production and keeps them running at scale.

- Portfolio hub: https://szemat.pro
- Documentation: https://agent-docs.szemat.pro
- Interactive demo: https://agent.szemat.pro
- GitHub: https://github.com/SzematPro
- LinkedIn: https://www.linkedin.com/in/szematpro
- Microsoft Learn: https://learn.microsoft.com/en-us/users/wszemat/
- Contact: waldemar@szemat.pro
