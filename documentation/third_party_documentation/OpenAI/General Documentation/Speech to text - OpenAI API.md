---
title: "Speech to text - OpenAI API"
source: "https://platform.openai.com/docs/guides/speech-to-text"
author:
published:
created: 2025-03-20
description: "Learn how to turn audio into text with the OpenAI API."
tags:
  - "clippings"
---
Learn how to turn audio into text.

The Audio API provides two speech to text endpoints:

- `transcriptions`
- `translations`

Historically, both endpoints have been backed by our open source [Whisper model](https://openai.com/blog/whisper/) (`whisper-1`). The `transcriptions` endpoint now also supports higher quality model snapshots, with limited parameter support:

- `gpt-4o-mini-transcribe`
- `gpt-4o-transcribe`

All endpoints can be used to:

- Transcribe audio into whatever language the audio is in.
- Translate and transcribe the audio into English.

File uploads are currently limited to 25 MB, and the following input file types are supported: `mp3`, `mp4`, `mpeg`, `mpga`, `m4a`, `wav`, and `webm`.

## Quickstart

### Transcriptions

The transcriptions API takes as input the audio file you want to transcribe and the desired output file format for the transcription of the audio. All models support the same set of input formats. On output, `whisper-1` supports a range of formats (`json`, `text`, `srt`, `verbose_json`, `vtt`); the newer `gpt-4o-mini-transcribe` and `gpt-4o-transcribe` snapshots currently only support `json` or plain `text` responses.

Transcribe audio

```highlighter
1
2
3
4
5
6
7
8
9
10

from openai import OpenAI
client = OpenAI()

audio_file= open("/path/to/file/audio.mp3", "rb")
transcription = client.audio.transcriptions.create(
  model="whisper-1", 
  file=audio_file
)

print(transcription.text)
```

By default, the response type will be json with the raw text included.

{ "text": "Imagine the wildest idea that you've ever had, and you're curious about how it might scale to something that's a 100, a 1,000 times bigger. .... }

The Audio API also allows you to set additional parameters in a request. For example, if you want to set the `response_format` as `text`, your request would look like the following:

Additional options

```highlighter
1
2
3
4
5
6
7
8
9
10
11

from openai import OpenAI
client = OpenAI()

audio_file = open("/path/to/file/speech.mp3", "rb")
transcription = client.audio.transcriptions.create(
  model="whisper-1", 
  file=audio_file, 
  response_format="text"
)

print(transcription.text)
```

The [API Reference](https://platform.openai.com/docs/api-reference/audio) includes the full list of available parameters.

The newer `gpt-4o-mini-transcribe` and `gpt-4o-transcribe` models currently have a limited parameter surface: they only support `json` or `text` response formats. Other parameters, such as `timestamp_granularities`, require `verbose_json` output and are therefore only available when using `whisper-1`.

### Translations

The translations API takes as input the audio file in any of the supported languages and transcribes, if necessary, the audio into English. This differs from our /Transcriptions endpoint since the output is not in the original input language and is instead translated to English text.

Translate audio

```highlighter
1
2
3
4
5
6
7
8
9
10

from openai import OpenAI
client = OpenAI()

audio_file = open("/path/to/file/german.mp3", "rb")
transcription = client.audio.translations.create(
  model="whisper-1", 
  file=audio_file,
)

print(transcription.text)
```

In this case, the inputted audio was german and the outputted text looks like:

Hello, my name is Wolfgang and I come from Germany. Where are you heading today?

We only support translation into English at this time.

## Supported languages

We currently [support the following languages](https://github.com/openai/whisper#available-models-and-languages) through both the `transcriptions` and `translations` endpoint:

Afrikaans, Arabic, Armenian, Azerbaijani, Belarusian, Bosnian, Bulgarian, Catalan, Chinese, Croatian, Czech, Danish, Dutch, English, Estonian, Finnish, French, Galician, German, Greek, Hebrew, Hindi, Hungarian, Icelandic, Indonesian, Italian, Japanese, Kannada, Kazakh, Korean, Latvian, Lithuanian, Macedonian, Malay, Marathi, Maori, Nepali, Norwegian, Persian, Polish, Portuguese, Romanian, Russian, Serbian, Slovak, Slovenian, Spanish, Swahili, Swedish, Tagalog, Tamil, Thai, Turkish, Ukrainian, Urdu, Vietnamese, and Welsh.

While the underlying model was trained on 98 languages, we only list the languages that exceeded <50% [word error rate](https://en.wikipedia.org/wiki/Word_error_rate) (WER) which is an industry standard benchmark for speech to text model accuracy. The model will return results for languages not listed above but the quality will be low.

We support some ISO 639-1 and 639-3 language codes for GPT-4o based models. For language codes we don’t have, try prompting for specific languages (i.e., “Output in English”).

By default, the Transcriptions API will output a transcript of the provided audio in text. The [`timestamp_granularities[]` parameter](https://platform.openai.com/docs/api-reference/audio/createTranscription#audio-createtranscription-timestamp_granularities) enables a more structured and timestamped json output format, with timestamps at the segment, word level, or both. This enables word-level precision for transcripts and video edits, which allows for the removal of specific frames tied to individual words.

Timestamp options

```highlighter
1
2
3
4
5
6
7
8
9
10
11
12

from openai import OpenAI
client = OpenAI()

audio_file = open("/path/to/file/speech.mp3", "rb")
transcription = client.audio.transcriptions.create(
  file=audio_file,
  model="whisper-1",
  response_format="verbose_json",
  timestamp_granularities=["word"]
)

print(transcription.words)
```

## Longer inputs

By default, the Transcriptions API only supports files that are less than 25 MB. If you have an audio file that is longer than that, you will need to break it up into chunks of 25 MB's or less or used a compressed audio format. To get the best performance, we suggest that you avoid breaking the audio up mid-sentence as this may cause some context to be lost.

One way to handle this is to use the [PyDub open source Python package](https://github.com/jiaaro/pydub) to split the audio:

```highlighter
1
2
3
4
5
6
7
8
9
10

from pydub import AudioSegment

song = AudioSegment.from_mp3("good_morning.mp3")

# PyDub handles time in milliseconds
ten_minutes = 10 * 60 * 1000

first_10_minutes = song[:ten_minutes]

first_10_minutes.export("good_morning_10.mp3", format="mp3")
```

*OpenAI makes no guarantees about the usability or security of 3rd party software like PyDub.*

## Prompting

You can use a [prompt](https://platform.openai.com/docs/api-reference/audio/createTranscription#audio/createTranscription-prompt) to improve the quality of the transcripts generated by the Transcriptions API. The model tries to match the style of the prompt, so it's more likely to use capitalization and punctuation if the prompt does too. However, the current prompting system is more limited than our other language models and provides limited control over the generated audio. The `prompt` parameter is not currently supported by the `gpt-4o-mini-transcribe` and `gpt-4o-transcribe` snapshots.

Here are some examples of how prompting can help in different scenarios:

1. Prompts can help correct specific words or acronyms that the model misrecognizes in the audio. For example, the following prompt improves the transcription of the words DALL·E and GPT-3, which were previously written as "GDP 3" and "DALI": "The transcript is about OpenAI which makes technology like DALL·E, GPT-3, and ChatGPT with the hope of one day building an AGI system that benefits all of humanity."
2. To preserve the context of a file that was split into segments, prompt the model with the transcript of the preceding segment. The model uses relevant information from the previous audio, improving transcription accuracy. The model only considers the final 224 tokens of the prompt and ignores anything earlier. For multilingual inputs, Whisper uses a custom tokenizer. For English-only inputs, it uses the standard GPT-2 tokenizer. Find both tokenizers in the open source [Whisper Python package](https://github.com/openai/whisper/blob/main/whisper/tokenizer.py#L361).
3. Sometimes the model skips punctuation in the transcript. To prevent this, use a simple prompt that includes punctuation: "Hello, welcome to my lecture."
4. The model may also leave out common filler words in the audio. If you want to keep the filler words in your transcript, use a prompt that contains them: "Umm, let me think like, hmm... Okay, here's what I'm, like, thinking."
5. Some languages can be written in different ways, such as simplified or traditional Chinese. The model might not always use the writing style that you want for your transcript by default. You can improve this by using a prompt in your preferred writing style.

## Streaming transcriptions

There are two ways you can stream your transcription depending on your use case and whether you are trying to transcribe an already completed audio recording or handle an ongoing stream of audio and use OpenAI for turn detection.

### Streaming the transcription of a completed audio recording

If you have an already completed audio recording, either because it's an audio file or you are using your own turn detection (like push-to-talk), you can use our Transcription API with `stream=True` to receive a stream of [transcript events](https://platform.openai.com/docs/api-reference/audio/transcript-text-delta-event) as soon as the model is done transcribing that part of the audio.

Stream transcriptions

```highlighter
1
2
3
4
5
6
7
8
9
10
11
12
13

from openai import OpenAI
client = OpenAI()

audio_file = open("/path/to/file/speech.mp3", "rb")
stream = client.audio.transcriptions.create(
  model="gpt-4o-mini-transcribe", 
  file=audio_file, 
  response_format="text",
  stream=True
)

for event in stream:
  print(event)
```

You will receive a stream of `transcript.text.delta` events as soon as the model is done transcribing that part of the audio, followed by a `transcript.text.done` event when the transcription is complete that includes the full transcript.

Additionally, you can use the `include[]` parameter to include `logprobs` in the response to get the log probabilities of the tokens in the transcription. These can be helpful to determine how confident the model is in the transcription of that particular part of the transcript.

Streamed transcription is not supported in `whisper-1`.

### Streaming the transcription of an ongoing audio recording

In the Realtime API, you can stream the transcription of an ongoing audio recording. To start a streaming session with the Realtime API, create a WebSocket connection with the following URL:

```highlighter
wss://api.openai.com/v1/realtime?intent=transcription
```

Below is an example payload for setting up a transcription session:

```highlighter
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22

{
  "type": "transcription_session.update",
  "input_audio_format": "pcm16",
  "input_audio_transcription": {
    "model": "gpt-4o-transcribe-latest",
    "prompt": "",
    "language": ""
  },
  "turn_detection": {
    "type": "server_vad",
    "threshold": 0.5,
    "prefix_padding_ms": 300,
    "silence_duration_ms": 500,
    "create_response": true
  },
  "input_audio_noise_reduction": {
    "type": "near_field"
  },
  "include": [
    "item.input_audio_transcription.logprobs"
  ]
}
```

To stream audio data to the API, append audio buffers:

```highlighter
1
2
3
4

{
  "type": "input_audio_buffer.append",
  "audio": "Base64EncodedAudioData"
}
```

The API responds with transcription events indicating speech start, stop, and completed transcriptions. The primary resource used by the streaming ASR API is the `TranscriptionSession`:

```highlighter
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21

{
  "object": "realtime.transcription_session",
  "id": "string",
  "input_audio_format": "pcm16",
  "input_audio_transcription": [{
    "model": "whisper-1" | "gpt-4o-transcribe-latest" | "gpt-4o-mini-transcribe-latest",
    "prompt": "string",
    "language": "string"
  }],
  "turn_detection": {
    "type": "server_vad",
    "threshold": "float",
    "prefix_padding_ms": "integer",
    "silence_duration_ms": "integer",
    "create_response": "boolean"
  } | null,
  "input_audio_noise_reduction": {
    "type": "near_field" | "far_field"
  },
  "include": ["string"]
}
```

Authenticate directly through the WebSocket connection using your API key or an ephemeral token obtained from:

```highlighter
POST /v1/realtime/transcription_sessions
```

This endpoint returns an ephemeral token (`client_secret`) to securely authenticate WebSocket connections.

## Improving reliability

One of the most common challenges faced when using Whisper is the model often does not recognize uncommon words or acronyms. Here are some different techniques to improve the reliability of Whisper in these cases:

Using the prompt parameter

Post-processing with GPT-4