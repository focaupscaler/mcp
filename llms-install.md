# Connect to Foca Upscaler MCP

Foca Upscaler is a live hosted MCP service using Streamable HTTP at `https://focaupscaler.com/api/mcp`. Send `Authorization: Bearer YOUR_API_KEY` on MCP requests. Foca hosts the server; no local image-processing server is needed.

The user can create an API key at [API key settings](https://focaupscaler.com/settings/api-keys). The key is shown only once. Store it in private client settings or an environment variable; never put it in a prompt, URL, or public repository. [README.md](README.md) includes complete configurations for Cursor, VS Code, Claude Code, Codex, Windsurf, and Claude Desktop. Claude Desktop uses the pinned `mcp-remote@0.15.1` bridge.

## Account access and prices

First call `get_upscale_info` and inspect top-level `balance`, `can_create_tasks`, and the selected tier's `available` value. Creating tasks requires an active paid subscription or a previously successful credit-pack purchase. Promotional credits alone are insufficient. MCP and the website share one account credit balance; plan permissions and sufficient credits are also required.

| `resolution_tier` | Output | Credits | Required access |
| --- | --- | --- | --- |
| `2` | 2K (default) | 15 | Paid MCP access |
| `4` | 4K | 30 | Starter, Pro, or Elite |
| `8` | 8K | 60 | Pro or Elite |
| `16` | 16K | 120 | Elite |

## Create and retrieve a task

`upscale_image` accepts a public HTTPS `image_url` on port 443, a 1–128 character `idempotency_key`, and an optional `resolution_tier` of `2`, `4`, `8`, or `16`. Supported inputs are JPEG, PNG, and WebP up to 20 MiB (20,971,520 bytes) and 64 million pixels (64 MP). The URL must need no additional request credentials. Private network and local addresses are rejected. Use current limits from `get_upscale_info`.

Confirm the image, tier, and credit cost with the user before submitting paid work. Both task tools return top-level `credits_remaining`. Preserve the exact key, URL, and tier when retrying the same request; a replay returns the existing task without another task charge. Keys are scoped to the account. A different image or tier needs a new key. Idempotency records remain for at least 30 days and are then removed by daily cleanup; reusing an old key after removal can create and charge a new task.

Query the returned `task_id` using `get_upscale_task` every 5 seconds until `succeeded` or `failed`, backing off when rate limited. States are `pending`, `processing`, `succeeded`, and `failed`. Stop at a terminal state.

On success, download from `download_url` before `expires_at`. Retrieve a fresh link for the same task while the result is retained if needed. Results remain available for 24 hours from task creation, after which lookup returns `RESULT_EXPIRED` or `TASK_NOT_FOUND`. Save a copy within that period.

Documentation: [Foca MCP guide](https://focaupscaler.com/docs/mcp). Support: [support@focaupscaler.com](mailto:support@focaupscaler.com).

This repository provides public service documentation and metadata under the [MIT license](LICENSE). It does not distribute or license the hosted backend.
