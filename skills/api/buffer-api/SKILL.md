---
name: buffer-api
description: Build or debug Buffer API integrations using the official GraphQL API. Use when working with Buffer authentication, channel discovery, post creation, post scheduling, ideas, pagination, errors, or rate limits.
---

# Buffer API

## Purpose
Guide implementation and debugging of Buffer API integrations using the official GraphQL endpoint at `https://api.buffer.com`.

## Inputs to request
- Integration type: personal script, backend service, or multi-user app.
- Auth mode: API key (single-user, server-side only) or OAuth 2.0 authorization code with PKCE (multi-user apps).
- Target organization or channel IDs and required post fields.
- Post type: text, image, video, or draft.
- Scheduling mode: `addToQueue`, `customScheduled` (requires `dueAt`), `shareNow`, or `shareNext`.
- Needed operation: read channels, create a post, manage ideas, inspect analytics, or troubleshoot an API response.

## Data model
```
Account -> Organizations -> Channels -> Posts
                       -> Ideas (org-level, not channel-specific)
```
Always query organization ID first, then channel IDs, before creating posts. Ideas belong to an organization and are not tied to a channel until promoted to a post.

## Workflow
1. **Confirm auth model.**
   - **API key**: server-side only, scopes all orgs/channels for the key owner. No per-organization scoping. Store as `BUFFER_API_KEY`.
   - **OAuth 2.0 + PKCE**: for apps acting on behalf of multiple users. Authorization endpoint: `https://auth.buffer.com/auth`. Token exchange and refresh endpoint: `https://auth.buffer.com/token`. Request only needed scopes: `posts:read`, `posts:write`, `ideas:read`, `ideas:write`, `account:read`, `account:write`, `offline_access`. Store the resulting token as `BUFFER_ACCESS_TOKEN`. **Refresh tokens are single-use** - every successful refresh returns a new `refresh_token` and immediately invalidates the old one. Always persist the latest token before the previous one expires.

2. **Keep tokens out of client code, logs, commits, and examples.** Use env var placeholders (`$BUFFER_API_KEY`, `$BUFFER_ACCESS_TOKEN`).

3. **Send GraphQL requests** to `https://api.buffer.com` with `Authorization: Bearer <token>` and `Content-Type: application/json`. Successful GraphQL transport responses can still contain failures in the response body. Do not rely on HTTP status alone: check `data` for typed mutation results and `errors[]` for non-recoverable failures. Rate limits can return HTTP `429`.

4. **Query organizations and channels** before creating content; pass channel IDs into mutations as variables.

5. **For post creation**, use `createPost` with required fields: `text`, `channelId`, `schedulingType` (`automatic` | `notification`), and `mode` (`addToQueue` | `customScheduled` | `shareNow` | `shareNext`). For `customScheduled`, include `dueAt` in ISO 8601 UTC (e.g., `"2026-03-10T15:00:00.000Z"`). Always handle the union response with inline fragments - `... on PostActionSuccess` and `... on MutationError { message }`. Post lifecycle: `Scheduled -> Sent | Error`.

6. **For list operations**, use cursor pagination (`first` + `after`). Loop until `pageInfo.hasNextPage` is false. Use page sizes of 20-50. Treat cursors as opaque strings - do not parse them. `hasPreviousPage` is always false (forward-only). Filters use AND logic.

7. **Respect rate limits.** Track `RateLimit-Limit`, `RateLimit-Remaining`, and `RateLimit-Reset` (ISO 8601 timestamp) response headers. On HTTP 429 with `RATE_LIMIT_EXCEEDED`, read `errors[0].extensions.retryAfter` (seconds) from the response body; if absent, use exponential backoff with jitter. Plan limits:
   | Plan | 15-min | 24-hr | 30-day |
   |------|--------|-------|--------|
   | Free | 100 | 100 | 3,000 |
   | Essentials | 100 | 250 | 7,500 |
   | Team | 100 | 500 | 15,000 |
   GraphQL query limits also apply: complexity <= 175,000 pts, depth <= 25, aliases <= 30, directives <= 50, token size <= 15,000. Contact `developersupport@buffer.com` for higher limits.

8. **Check both error channels in every response:**
   - `data.<mutationName>` - typed union errors (validation, limits); catch with `... on MutationError { message }`.
   - `errors[]` - non-recoverable errors with `extensions.code`: `UNAUTHORIZED`, `FORBIDDEN`, `NOT_FOUND`, `UNEXPECTED`, `RATE_LIMIT_EXCEEDED`.

## Request patterns

### Query: get channels for an organization
```bash
curl https://api.buffer.com \
  -H "Authorization: Bearer $BUFFER_API_KEY" \
  -H "Content-Type: application/json" \
  --data '{
    "query": "query GetChannels($orgId: String!) { channels(input: { organizationId: $orgId }) { id name service } }",
    "variables": { "orgId": "YOUR_ORG_ID" }
  }'
```

### Mutation: create a post (with full error handling)
```graphql
mutation CreatePost($input: CreatePostInput!) {
  createPost(input: $input) {
    ... on PostActionSuccess {
      post {
        id
        text
        status
        dueAt
      }
    }
    ... on MutationError {
      message
    }
  }
}
```
Variables:
```json
{
  "input": {
    "text": "Your post content here",
    "channelId": "$CHANNEL_ID",
    "schedulingType": "automatic",
    "mode": "addToQueue"
  }
}
```

## Output
- A GraphQL query or mutation with typed variables and inline fragment error handling.
- A runnable `curl`, Node, or Python example using environment variables for secrets.
- Notes on required scopes, token storage, channel IDs, scheduling mode, pagination, and retry behavior.

## Troubleshooting checklist
- **`UNAUTHORIZED` / 401**: Token missing, malformed, or expired. Verify the `Authorization: Bearer <token>` header is present and the token is valid.
- **`FORBIDDEN`**: Token valid but insufficient scope. Check that requested scopes were granted and match the operation (e.g., `posts:write` for `createPost`).
- **Mutation returns HTTP 200 but no `post` field**: The `MutationError` branch fired. Ensure `... on MutationError { message }` is in your query and log `data.<mutationName>.message`.
- **HTTP 200 but unexpected behavior**: Inspect the top-level `errors[]` array and any typed mutation error branch.
- **HTTP 429 / `RATE_LIMIT_EXCEEDED`**: Read `errors[0].extensions.retryAfter` from the response body. Check your plan tier and which window (15-min, 24-hr, 30-day) is exhausted via the `RateLimit-*` headers.
- **Schema or field not found**: Check the official reference for the current type, selected fields, and query depth/complexity limits.

## Quality bar
- Never expose real Buffer tokens, channel IDs, or customer content.
- Use variables for mutation inputs and user-generated text - never string-interpolate.
- Inspect both HTTP status and GraphQL response body; HTTP 200 can still include `errors[]` or typed mutation errors.
- Always include `... on MutationError { message }` in every mutation.
- Never reuse a refresh token after a successful refresh - save the new one immediately.
- Mention relevant official docs sections when giving schema-sensitive guidance.
