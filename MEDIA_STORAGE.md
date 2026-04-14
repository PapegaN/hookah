# Media Storage

## Goal

Prepare the project for tobacco cards, forum posts and comment attachments without tying files to the local filesystem.

## Recommended approach

- Use S3-compatible object storage as the single binary storage layer.
- Use `MinIO` locally in Docker and a managed S3-compatible service in production.
- Keep only metadata, links and processing state in PostgreSQL.

## Why this is the right fit

- Images will outgrow app containers quickly.
- Web and API can scale independently from uploaded media.
- Presigned upload/download URLs work well for mobile clients.
- The same storage model fits tobacco catalog images, forum galleries and comment attachments.

## Target domains

### `media`

- `media.assets`
  - logical asset id
  - owner user id
  - storage bucket
  - storage object key
  - mime type
  - byte size
  - sha256 checksum
  - width / height
  - status (`uploaded`, `processed`, `failed`, `deleted`)
  - created / updated timestamps

- `media.asset_variants`
  - original asset id
  - variant kind (`thumb`, `card`, `full`)
  - bucket + object key
  - width / height
  - format

### `forum`

- `forum.tobacco_posts`
  - tobacco id
  - author user id
  - title
  - body
  - cover asset id

- `forum.post_assets`
  - post id
  - asset id
  - sort order

- `forum.comment_assets`
  - comment id
  - asset id
  - sort order

## Upload flow

1. Frontend asks API to create a draft asset record.
2. API validates mime type / size and returns a presigned upload URL.
3. Frontend uploads the file directly to object storage.
4. API confirms the upload and enqueues image processing.
5. Worker creates variants and updates metadata in PostgreSQL.

## Validation rules to plan for

- Max size per image.
- Allowed mime types: `image/jpeg`, `image/png`, `image/webp`.
- Strip EXIF on processed variants.
- Generate at least `thumb`, `card`, `full`.
- Store checksum to deduplicate repeated uploads.

## Environment shape

Expected variables:

- `OBJECT_STORAGE_ENDPOINT`
- `OBJECT_STORAGE_PUBLIC_URL`
- `OBJECT_STORAGE_REGION`
- `OBJECT_STORAGE_ACCESS_KEY`
- `OBJECT_STORAGE_SECRET_KEY`
- `OBJECT_STORAGE_BUCKET_HOOKAH_MEDIA`

## Near-term implementation plan

1. Add `MinIO` to local compose.
2. Add `media` tables and API module for draft assets.
3. Implement presigned upload endpoints in `hookah-api`.
4. Add image picker / preview components in `hookah-web`.
5. Reuse the same asset pipeline for forum posts, tobacco cards and comment attachments.
