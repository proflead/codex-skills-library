---
name: buffer-api
description: Manage Buffer content via the GraphQL API. Use when creating, scheduling, editing, or deleting posts, saving ideas, reading scheduled queues, or pulling post analytics. Not for general API debugging.
---

# Buffer API — Content Operations

## Purpose
Create, schedule, edit, and analyze social media content through Buffer's GraphQL API at `https://api.buffer.com`.

## Inputs to request
- What to do: create, schedule, draft, edit, delete, list, or analyze a post — or save/list ideas.
- Channel ID(s) to target (or ask the user to run the "get channels" query first).
- Post content: text, and optionally image/video URLs or thread structure.
- Scheduling intent: add to queue, schedule at a specific time, publish now, or save as draft.
- For analytics: which post ID(s) and which metrics matter (impressions, reactions, comments, etc.).

## Operations map

| Goal | API call |
|------|----------|
| Create / schedule a post | `createPost` mutation |
| Save a post as draft | `createPost` with `saveToDraft: true` |
| Edit an existing post | `editPost` mutation |
| Delete a post | `deletePost` mutation |
| List scheduled / sent posts | `posts` query with `filter: { status: [scheduled] }` |
| Get a single post | `post` query by ID |
| Read post metrics | `post { metrics }` or `aggregatedPostMetrics` query |
| Save a content idea | `createIdea` mutation |
| Find channel IDs | `channels` query by organization ID |
| Find organization ID | `account { organizations { id } }` query |

## Auth setup (one-time)
All requests need `Authorization: Bearer $BUFFER_API_KEY` and `Content-Type: application/json`.

For personal scripts and automations: use an API key from `https://publish.buffer.com/settings/api`.
For multi-user apps: use OAuth 2.0 with PKCE — authorize at `https://auth.buffer.com/auth`, exchange at `https://auth.buffer.com/token`. Refresh tokens are single-use; always save the new one immediately after refresh.

## Workflow

1. **Get your organization ID** (first time only):
   ```graphql
   query { account { organizations { id name } } }
   ```

2. **Get channel IDs** for your target platforms:
   ```graphql
   query GetChannels($orgId: String!) {
     channels(input: { organizationId: $orgId }) {
       id name service
     }
   }
   ```

3. **Create or schedule the post** using the relevant example below.

4. **Check the response** — the mutation returns a union type. `PostActionSuccess` means it worked; `MutationError` carries the reason it failed. GraphQL always responds with HTTP 200, so always inspect the response body.

5. **Edit or delete** if needed using the post `id` returned in step 3.

6. **Pull analytics** after the post publishes (metrics are refreshed daily; allow up to 24 hours after publish).

## Examples

### Create a text post (add to queue)
```graphql
mutation CreatePost($input: CreatePostInput!) {
  createPost(input: $input) {
    ... on PostActionSuccess {
      post { id text status dueAt }
    }
    ... on MutationError { message }
  }
}
```
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

### Schedule at a specific time
```json
{
  "input": {
    "text": "Your post content here",
    "channelId": "$CHANNEL_ID",
    "schedulingType": "automatic",
    "mode": "customScheduled",
    "dueAt": "2026-07-01T14:00:00.000Z"
  }
}
```

### Save as draft
```json
{
  "input": {
    "text": "Draft content here",
    "channelId": "$CHANNEL_ID",
    "schedulingType": "automatic",
    "mode": "addToQueue",
    "saveToDraft": true
  }
}
```

### Post with image
```json
{
  "input": {
    "text": "Your caption here",
    "channelId": "$CHANNEL_ID",
    "schedulingType": "automatic",
    "mode": "addToQueue",
    "assets": [{ "image": { "url": "https://your-public-image-url.jpg" } }]
  }
}
```
Image URL must be publicly accessible. Each asset entry specifies exactly one type: `image`, `video`, `document`, or `link`.

### Edit an existing post
```graphql
mutation EditPost($input: EditPostInput!) {
  editPost(input: $input) {
    ... on PostActionSuccess {
      post { id text status dueAt }
    }
    ... on MutationError { message }
  }
}
```
```json
{ "input": { "id": "$POST_ID", "text": "Updated content here" } }
```

### Delete a post
```graphql
mutation DeletePost {
  deletePost(input: { id: "$POST_ID" }) {
    ... on PostActionSuccess { post { id } }
    ... on MutationError { message }
  }
}
```

### List scheduled posts
```graphql
query GetScheduledPosts($orgId: String!) {
  posts(input: {
    organizationId: $orgId,
    filter: { status: [scheduled] },
    sort: [{ field: dueAt, direction: asc }]
  }) {
    edges {
      node { id text dueAt channelId }
    }
    pageInfo { hasNextPage endCursor }
  }
}
```
For more pages, add `after: "$endCursor"` to `input`. Page size: 20–50 items.

### Get post analytics
```graphql
query GetPostMetrics {
  post(input: { id: "$POST_ID" }) {
    id text metricsUpdatedAt
    metrics { type name value unit }
  }
}
```
Available metric types (varies by network): `reactions`, `reposts`, `comments`, `shares`, `impressions`, `reach`, `views`, `saves`, `follows`, `likes`. Metrics appear up to ~24 hours after publish.

### Save an idea
```graphql
mutation CreateIdea($input: CreateIdeaInput!) {
  createIdea(input: $input) {
    ... on MutationError { message }
  }
}
```
```json
{
  "input": {
    "organizationId": "$ORG_ID",
    "content": { "title": "Optional title", "text": "Idea content here" }
  }
}
```
Ideas are org-level (not tied to a channel). Promote to a post by using the idea's text in `createPost`.

## Rate limits
Buffer enforces three time windows. On HTTP 429, read `retryAfter` (seconds) from the response body.

| Plan | 15-min | 24-hr | 30-day |
|------|--------|-------|--------|
| Free | 100 | 100 | 3,000 |
| Essentials | 100 | 250 | 7,500 |
| Team | 100 | 500 | 15,000 |

## Troubleshooting
- **No `post` field in mutation response** → `MutationError` fired; log `data.<mutationName>.message`.
- **`UNAUTHORIZED`** → check `Authorization: Bearer $BUFFER_API_KEY` header is present and token is valid.
- **`FORBIDDEN`** → token lacks the right scope (e.g., `posts:write` needed for create/edit/delete).
- **Metrics missing** → post published less than 24 hours ago; check `metricsUpdatedAt`.
- **Image not attaching** → URL must be publicly accessible; see the Hosting Media guide.

## Quality bar
- Always use GraphQL variables — never interpolate user content into query strings.
- Include `... on MutationError { message }` in every mutation.
- Never expose real tokens or channel IDs in examples.
- Metrics API is preview-only and available for personal API keys only (not OAuth apps).
