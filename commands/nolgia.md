---
description: Generate images, video, or audio on NOLGIA, or check your jobs, credits and library, through the NOLGIA MCP server.
---

# /nolgia

The user's request is whatever they typed after `/nolgia`. Fulfil it with the
tools of the `nolgia` MCP server (every tool name starts with `nolgia_`).

## 1. Make sure the server is connected

If no `nolgia_*` tools are available, or a call answers `401`, the server is
not connected. Tell the user, then stop:

1. Create a personal access token at https://nolgia.ai/settings/api-tokens.
2. Export it as `NOLGIA_TOKEN` in the environment Cursor starts from (for
   example in `~/.zshrc`, then fully restart Cursor).
3. Enable the `nolgia` server under Cursor Settings > MCP.

If the user typed nothing after `/nolgia`, say in three lines what NOLGIA can
make (images, video with sound, voiceovers, music and sound effects) and ask
what they want.

## 2. Route the request to one tool

| The user wants | Tool |
|---|---|
| An image from words | `nolgia_text_to_image` |
| An image from a picture they have | `nolgia_image_to_image` (store a local picture first with `nolgia_upload_asset`) |
| A video from words | `nolgia_text_to_video` |
| A video that starts from an image | `nolgia_image_to_video` |
| A voiceover, music bed or sound effect | `nolgia_text_to_audio` (the model decides which) |
| A named preset or a proven starting point | `nolgia_list_presets`, `nolgia_get_preset`, `nolgia_run_preset` |
| Something they made earlier | `nolgia_list_assets`, then `nolgia_get_asset` |
| Job status | `nolgia_list_jobs` |
| Their credit balance | `nolgia_get_account` |
| A recurring person or mascot | `nolgia_list_characters`, `nolgia_get_character` |

## 3. Pick the model from the catalog, never from memory

Call `nolgia_list_models` once. Choose a model whose capabilities fit the
request (modality, aspect ratio, duration, reference inputs) and send only
the parameters that model publishes; anything else is refused with a `400`
that names the capability. For a saved character, pass its reference URLs
from `nolgia_get_character` as the image inputs.

## 4. Say what it costs before spending

The catalog carries the credit cost of each model. State the estimate before
you generate. Ask for confirmation before anything over about 2,000 credits,
or before a batch of more than four generations.

## 5. Video is asynchronous

The video tools return a queued job. Poll `nolgia_list_jobs` every 20 to 30
seconds until that job reads `succeeded` or `failed`, then open the result
with `nolgia_list_assets` / `nolgia_get_asset`. Never submit the same request
again while its job is queued or running: a second submit is a second charge.

## 6. Deliver

Give the asset id and its signed URL. Signed URLs expire after about 15
minutes; do not edit them (that breaks the signature). Offer the next useful
step: a variation, a different aspect ratio, or animating a still.

## 7. When something fails

- A `400` names what the model does not accept: adjust the parameters to the
  catalog and retry once.
- A content-policy refusal from the provider: rephrase the flagged part of
  the prompt once; do not retry verbatim in a loop.
- `402`: the token's API credit pool is empty; the user tops up at nolgia.ai.
- A failed job: report the job's error message as written, and suggest one
  concrete change.
