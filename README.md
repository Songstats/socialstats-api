# Socialstats API

Official Postman collection for the **Socialstats Enterprise API**.

API Documentation: https://developers.stats.company/socialstats<br>
API Key Access: Please contact api@socialstats.com

---

## Files

- `socialstats_api_postman_collection.json` - Postman Collection v2.1 export for the Socialstats Enterprise API

---

## Usage

Import the collection into Postman from this URL:

    https://raw.githubusercontent.com/Songstats/socialstats-api/main/socialstats_api_postman_collection.json

Or import it from a local checkout:

1. Open Postman.
2. Select **Import**.
3. Choose `socialstats_api_postman_collection.json`.
4. Set the `api_key` collection variable to your Socialstats Enterprise API key.

All authenticated requests include your API key in the `apikey` header.

Creator-dependent requests use `socialstats_creator_id` by default. They also include disabled
`instagram_creator_id`, `facebook_creator_id`, `youtube_creator_id`, and `tiktok_creator_id`
query parameters. Enable one source-specific parameter and disable `socialstats_creator_id` to
request a creator by the platform's internal ID or exact username.

Creator information responses include both `external_id` and `username` for every source profile
in the `links` array. `username` is `null` when the source has no stored username or handle.

## Creator Backstage Authorization

Socialstats authorization grants first-party analytics access for one creator and one source at a
time. YouTube, Instagram, Facebook, and TikTok are supported.

### Before You Start

- The API Key needs Socialstats access and permission for the creator, source, and authorized
  endpoints.
- The matching source profile must already be linked to the creator in Socialstats.
- Instagram must be a professional profile connected to a Facebook Page that is also linked to the
  creator. The creator must grant access to that Page in the Meta flow.
- All configured provider scopes are mandatory. Partial consent returns `missing_scope`.

### Postman Flow

1. Set `api_key`, select one creator variable, and set `authorization_source_id`.
2. Send **Authorization → Create OAuth Authorization URL**.
3. Copy `state_token` into the collection's `state_token` variable and open the returned
   `authorization_url` in the creator's browser. The link expires after 30 minutes.
4. The creator selects the exact linked profile and grants every requested permission. Socialstats
   handles the provider callback, verifies the profile identity, stores the token, and schedules the
   authorized data sync.
5. Send **Authorization → Get OAuth Attempt Status** until `status` changes from `pending`. This
   request intentionally uses no API Key. If `return_url` was enabled on the create request,
   Socialstats also redirects there with the final status fields.
6. Send **Authorization → List OAuth Authorizations** with the API Key. Access is usable when
   `status` is `active` and `is_authorized` is `true`.
7. Use the requests under **Creators (Authorized)** and **Posts (Authorized)**.
8. To remove this API Key's grant, set `authorization_id` and send
   **Authorization → Revoke OAuth Authorization**.

Do not manually call **Platform OAuth Callback** or exchange provider codes in your application.
The callback request is included in the collection only to document the provider-facing contract.

### Requested Scopes

| Provider flow | Required scopes |
| --- | --- |
| YouTube | `https://www.googleapis.com/auth/youtube.readonly`, `https://www.googleapis.com/auth/yt-analytics.readonly` |
| Meta for Facebook and Instagram | `email`, `instagram_basic`, `instagram_manage_comments`, `pages_manage_metadata`, `pages_read_user_content`, `instagram_manage_insights`, `pages_read_engagement`, `read_insights`, `pages_show_list`, `business_management` |
| TikTok | `user.info.basic`, `user.info.stats`, `user.info.profile`, `video.list` |

Common terminal statuses are `active`, `expired_attempt`, `missing_scope`, `wrong_profile`,
`wrong_source`, `missing_profile`, `provider_error`, `token_expired`, and `revoked`. Instagram and
Facebook can additionally return `missing_facebook`, `missing_facebook_entity`,
`instagram_unauthorized`, or `facebook_unauthorized`. The full meaning of each status is documented
in the canonical API documentation.

---

## SDKs

- Node.js: https://www.npmjs.com/package/socialstats-node-sdk
- Ruby: https://rubygems.org/gems/socialstats-ruby-sdk
- Python: https://pypi.org/project/socialstats-sdk/

---

## API Documentation

Full endpoint documentation is available at:

https://developers.stats.company/socialstats

---

## Authentication

We recommend storing your key securely in a Postman environment variable or collection variable.

---

## Versioning

This collection is maintained alongside the Socialstats Enterprise API documentation.

---

## License

MIT
