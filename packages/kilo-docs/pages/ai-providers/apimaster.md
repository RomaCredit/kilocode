---
title: "Using APIMaster with Kilo Code | OpenAI-Compatible AI Gateway"
description: "Configure APIMaster in Kilo Code through its OpenAI-compatible API to access Claude, GPT, DeepSeek, MiniMax and Gemini models behind a single key and endpoint."
sidebar_label: APIMaster
---

# Using APIMaster With Kilo Code

APIMaster exposes an OpenAI-compatible API at `https://apimaster.ai/v1`.
You can use it from Kilo Code through the **Custom provider** flow.

**Website:** [https://apimaster.ai/](https://apimaster.ai/)

APIMaster routes a single API key to multiple upstream model families —
Claude, GPT, DeepSeek, MiniMax, Kimi, GLM and Gemini — so one provider entry in
Kilo Code covers models that would otherwise need separate provider
configurations.

## Getting an API Key

1. Sign in to the [APIMaster console](https://apimaster.ai/console/dashboard).
2. Create an API key.
3. Copy the key and the model ids you plan to use from the
   [model list](https://apimaster.ai/docs/en/models).

## Configuration in Kilo Code

{% tabs %}
{% tab label="VSCode" %}

Open **Settings** (gear icon) and go to the **Providers** tab. Under
**Custom provider**, click **+ Connect** and fill in:

| Field | Value |
| --- | --- |
| Provider ID | `apimaster` |
| Display name | `APIMaster` |
| Base URL | `https://apimaster.ai/v1` |
| API key | your APIMaster key |

Leave **Headers** empty.

On the next screen, select the model ids you want (for example
`claude-sonnet-4-6`, `gpt-5.5`, `deepseek-v4-pro`), click
**Add N model(s)**, then **Submit**.

APIMaster then appears under **Connected providers** with a `CUSTOM` tag, and
its models are selectable from the model dropdown in a chat session.

{% /tab %}
{% tab label="CLI" %}

Set the API key as an environment variable:

```bash
export APIMASTER_API_KEY="your-api-key"
```

Then configure APIMaster as an OpenAI-compatible provider in your `kilo.json`:

```jsonc
{
  "provider": {
    "apimaster": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "APIMaster",
      "env": ["APIMASTER_API_KEY"],
      "options": {
        "baseURL": "https://apimaster.ai/v1"
      },
      "models": {
        "claude-sonnet-4-6": {
          "name": "Claude Sonnet 4.6"
        },
        "gpt-5.5": {
          "name": "GPT-5.5"
        },
        "deepseek-v4-pro": {
          "name": "DeepSeek V4 Pro"
        }
      }
    }
  }
}
```

{% /tab %}
{% /tabs %}

## Base URL

The base URL must end in `/v1`:

```
https://apimaster.ai/v1
```

Kilo Code appends `/chat/completions` itself. Without the `/v1` suffix the
request resolves to `https://apimaster.ai/chat/completions` and returns
`404 Not Found`.

## Available Models

Model ids are passed through to APIMaster verbatim — there is no aliasing, so
the id in Kilo Code must match the id APIMaster serves. Fetch the current list
with:

```bash
curl https://apimaster.ai/v1/models \
  -H "Authorization: Bearer $APIMASTER_API_KEY"
```

The catalog spans Anthropic, OpenAI, DeepSeek, MiniMax, Moonshot, Zhipu and
Google model families. See the
[model list](https://apimaster.ai/docs/en/models) for current ids and pricing.

## Troubleshooting

| Symptom | Cause |
| --- | --- |
| `401 Unauthorized` | Key contains trailing whitespace, or was copied from a different console |
| `404 Not Found` on every model | Base URL is missing the `/v1` suffix |
| Empty model list | Key lacks permission, or the endpoint is unreachable from your network |
| Model not found | The model id is not in the account's catalog — check `GET /v1/models` |

## See Also

- [OpenAI Compatible](/docs/ai-providers/openai-compatible) — the generic flow this
  page specializes
- [APIMaster Kilo Code guide](https://apimaster.ai/docs/en/agents/kilo)
