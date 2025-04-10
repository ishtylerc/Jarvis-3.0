# Voice Tracing

Just like the way agents are traced, voice pipelines are also automatically traced.

You can read the [tracing doc](./tracing.md) above for basic tracing information, but you can additionally configure tracing of a pipeline via `VoicePipelineConfig`.

Key tracing related fields are:

*   `tracing_disabled`: controls whether tracing is disabled. By default, tracing is enabled.
*   `trace_include_sensitive_data`: controls whether traces include potentially sensitive data, like audio transcripts. This is specifically for the voice pipeline, and not for anything that goes on inside your Workflow.
*   `trace_include_sensitive_audio_data`: controls whether traces include audio data.
*   `workflow_name`: The name of the trace workflow.
*   `group_id`: The `group_id` of the trace, which lets you link multiple traces.
*   `trace_metadata`: Additional metadata to include with the trace. 