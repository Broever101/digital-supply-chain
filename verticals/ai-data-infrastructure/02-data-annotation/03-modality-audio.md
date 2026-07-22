# 3. Audio & Speech Annotation

## What Goes In

Audio waveforms: spoken language, music, environmental sounds, animal calls, industrial noise, and acoustic sensor data.

## What Comes Out

Transcribed text, speaker labels, emotion tags, timestamped events, and wake word markers that enable voice assistants, transcription services, and audio understanding models.

## Annotation Types

### Transcription (ASR: Automatic Speech Recognition)
Converting spoken audio into written text.
- **Example**: A 30-minute podcast → verbatim text transcript with timestamps.
- **Variants**: Clean transcription vs. verbatim (including "um," "ah," false starts).

### Speaker Diarization
Identifying who spoke when in multi-speaker audio.
- **Example**: "Speaker A: 0:00-0:45; Speaker B: 0:46-1:30; Speaker A: 1:31-2:00."

### Emotion / Sentiment in Voice
Labeling the emotional content of speech beyond the words themselves.
- **Example**: "The customer said 'I am fine' but the tone is angry and frustrated."
- **Categories**: Anger, happiness, sadness, fear, surprise, neutrality, sarcasm.

### Wake Word / Command Annotation
Marking specific trigger phrases and voice commands.
- **Example**: "Hey Siri," "OK Google," "Alexa, set a timer for 5 minutes."

### Audio Event Detection
Identifying non-speech sounds in audio streams.
- **Example**: In a security recording: gunshot, glass breaking, footsteps, car engine.

## Key Players

| Name | URL | Role |
|------|-----|------|
| Appen | https://appen.com | Speech collection and transcription at scale |
| TELUS International | https://www.telusinternational.com | Multilingual speech datasets |
| Scale AI | https://scale.com | Audio transcription and speaker labeling |
| Deepgram | https://deepgram.com | Speech recognition API with built-in transcription |
| Rev.com | https://www.rev.com | Human transcription service (also used for annotation) |
| Mozilla Common Voice | https://commonvoice.mozilla.org | Open-source multilingual speech dataset |
| Whisper (OpenAI) | https://github.com/openai/whisper | Open-source speech recognition model (reduces transcription annotation needs) |

## Open vs. Proprietary

**Mixed and shifting**. Mozilla Common Voice and LibriSpeech are foundational open datasets. However, most commercial speech data (especially for specific accents, domains, and languages) is proprietary. OpenAI's Whisper model has disrupted the space by making high-quality transcription nearly free, reducing the need for human transcription annotation.

## The Stalwart

**Mozilla Common Voice**. A crowdsourced project by Mozilla to build an open, multilingual speech dataset. Volunteers record and validate audio clips in their native languages. Over 28,000 hours of speech in 130+ languages, completely free, built by a non-profit with no profit motive. Enables voice technology in low-resource languages that commercial platforms ignore.

## TBD

- Whisper and the commoditization of transcription: what happens to transcription annotation companies?
- Low-resource languages: most speech datasets are English-centric
- Real-time vs. batch transcription annotation workflows
- Noise robustness: annotating speech in noisy environments (cafes, cars, factories)
- Music and audio understanding: beyond speech into general audio event recognition

---

*Back to [overview](00-overview.md)*
