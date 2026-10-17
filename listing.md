# Foca Upscaler product information

## Name

Foca Upscaler

## Short description

Upscale images from HTTPS URLs with account credits and retrieve async results.

## Alternative description

AI image upscaling for MCP clients: check credits, create paid tasks, and download completed results.

## About the service

Foca Upscaler connects an MCP-compatible AI client to a hosted image upscaling service. An agent can inspect the user's account credits and available output tiers, submit one image from an HTTPS URL, and retrieve the completed result without running a local image-processing server.

The server exposes three tools. `get_upscale_info` reports top-level `balance` and `can_create_tasks`, tier availability, and input limits. `upscale_image` creates an asynchronous task using Foca's `physics` engine. `get_upscale_task` retrieves a task owned by the authenticated account and supplies a temporary download link after successful processing. Both task tools report top-level `credits_remaining`.

Output tiers are 2K, 4K, 8K, and 16K, costing 15, 30, 60, and 120 credits per image. The default is 2K for 15 credits. Task creation requires an active paid subscription or a previously successful credit-pack purchase; promotional credits alone do not grant eligibility. Each account's plan determines which tiers are available, and the live information tool should be checked before submitting paid work.

Inputs must be publicly retrievable HTTPS images on port 443, without additional request credentials, in JPEG, PNG, or WebP format, up to 20 MiB (20,971,520 bytes) and 64 million pixels (64 MP). Every submission includes an idempotency key. Retrying the same key, URL, and tier returns the original task without another task charge while its idempotency record is retained. Records are retained for at least 30 days, then removed by daily cleanup; reusing an old key after removal can create and charge a new task. Task states are `pending`, `processing`, `succeeded`, and `failed`. Query `get_upscale_task` every 5 seconds until `succeeded` or `failed`, backing off when rate limited. Successful responses include `download_url` and `expires_at`. Results are retained for 24 hours from task creation; download and save a copy within that period.

The live endpoint is `https://focaupscaler.com/api/mcp`. The remote connection uses Streamable HTTP and a Bearer API key created in [Foca API key settings](https://focaupscaler.com/settings/api-keys). Keys remain private and can be revoked. Typical uses include preparing website images, enlarging product imagery, and integrating an explicit paid upscaling step into an assistant workflow. This package provides connection instructions and public metadata; it does not distribute the hosted backend.

## Category

Image & Video Processing; AI & Machine Learning.

## Tags

`image-upscaling`, `image-processing`, `ai`, `photography`, `streamable-http`, `remote-mcp`, `paid-api`

## Use cases

- Enlarge an authorized product image for a website or marketplace listing.
- Add a paid image upscaling step to an assistant workflow after checking account credits.
- Retrieve the result of an existing task using its task ID.
- Inspect account eligibility and tier availability before estimating an image workflow's cost.

## FAQ

**Is the service free?** Task creation is paid. It consumes account credits and requires an active paid subscription or a previously successful credit-pack purchase. Free promotional credits alone do not establish eligibility.

**Which engine is used?** MCP submissions use the `physics` engine. The three tools do not expose every editing tool on the Foca website.

**How much does an image cost?** 2K, 4K, 8K, and 16K cost 15, 30, 60, and 120 credits. The default is 2K. MCP and the website share the same credit balance. 2K requires paid MCP access; 4K requires Starter, Pro, or Elite; 8K requires Pro or Elite; 16K requires Elite. Check current tool output before submitting.

**Can I upload a local file directly?** The MCP tool accepts an HTTPS image URL. A local file must first be placed at a URL accessible to the service. Do not send private network addresses or URLs requiring custom request credentials.

**What happens when I retry a submission?** Retry the same image URL and tier with the same idempotency key. A different image or tier needs a new key. Keys contain 1 to 128 characters and are scoped to the account. Idempotency records are retained for at least 30 days, then removed by daily cleanup. Reusing an old key after removal can create and charge a new task.

**Are download links permanent?** No. Check `expires_at` and download promptly. Re-query the task for a fresh link while the result remains retained. Results are retained for 24 hours from task creation. After that, lookup returns `RESULT_EXPIRED` or `TASK_NOT_FOUND`.

**Which clients can I connect?** [README.md](README.md) provides configurations for Cursor, VS Code, Claude Code, Codex, Windsurf, and Claude Desktop. Claude Desktop connects through `mcp-remote@0.15.1`; the other examples use remote HTTP directly.

**Is the backend included in this repository?** This repository contains public connection documentation, metadata, and assets under the [MIT license](LICENSE). The hosted backend is not distributed or licensed here.

## Contact

Support: [support@focaupscaler.com](mailto:support@focaupscaler.com).

Website: [Foca Upscaler](https://focaupscaler.com). Documentation: [MCP guide](https://focaupscaler.com/docs/mcp).
