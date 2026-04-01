# LinkedIn Post Publisher (Kotlin / Gradle)

A command-line tool to publish **text** and **text + image** posts to LinkedIn via the official UGC Posts API.

---

## Prerequisites

| Tool | Version |
|------|---------|
| JDK  | 17+     |
| Gradle | 8.x (wrapper included) |

---

## Step 1 — Create a LinkedIn Developer App

1. Go to [https://www.linkedin.com/developers/apps](https://www.linkedin.com/developers/apps) and click **Create App**.
2. Fill in the required fields (App Name, LinkedIn Page, Logo).
3. After creation, open the **Auth** tab and note your **Client ID** and **Client Secret**.
4. Under **OAuth 2.0 settings → Authorized redirect URLs** add:
   ```
   http://localhost:8080/callback
   ```
5. Go to the **Products** tab and request access to **Share on LinkedIn** (usually instant approval).
   - This grants the `w_member_social` scope needed to post.

---

## Step 2 — Configure the app

Copy the example env file and fill in your credentials:

```bash
cp .env.example .env
```

Edit `.env`:

```dotenv
LINKEDIN_CLIENT_ID=86xyz...
LINKEDIN_CLIENT_SECRET=WfA1...
LINKEDIN_REDIRECT_URI=http://localhost:8080/callback
```

> ⚠️ Never commit `.env` or `.linkedin_token` to version control.
> Both files are already listed in `.gitignore`.

---

## Step 3 — Build

```bash
./gradlew shadowJar
```

This produces a fat JAR at:
```
build/libs/linkedin-poster-all-1.0.0.jar
```

---

## Step 4 — Run

### Interactive mode (guided prompts)

```bash
java -jar build/libs/linkedin-poster-all-1.0.0.jar
```

The first time you run it, a browser window will open for LinkedIn OAuth authorization.
After approving, the token is cached in `.linkedin_token` (valid ~60 days).

### CLI mode (scriptable)

**Text post:**
```bash
java -jar build/libs/linkedin-poster-all-1.0.0.jar \
  --text "Hello LinkedIn! Posting via Kotlin 🚀"
```

**Text + image post:**
```bash
java -jar build/libs/linkedin-poster-all-1.0.0.jar \
  --text "Check out this screenshot!" \
  --image /path/to/photo.jpg \
  --alt "A screenshot of my terminal"
```

**Post to connections only:**
```bash
java -jar build/libs/linkedin-poster-all-1.0.0.jar \
  --text "Private update for connections" \
  --connections
```

**Clear cached token (force re-login):**
```bash
java -jar build/libs/linkedin-poster-all-1.0.0.jar --logout
```

---

## Project structure

```
linkedin-poster/
├── build.gradle.kts
├── settings.gradle.kts
├── .env.example
├── gradle/wrapper/
└── src/main/kotlin/com/linkedinposter/
    ├── Main.kt                   # Entry point (interactive + CLI modes)
    ├── auth/
    │   └── LinkedInAuth.kt       # OAuth 2.0 flow + token caching
    ├── api/
    │   └── LinkedInClient.kt     # LinkedIn UGC Posts API + image upload
    └── model/
        └── Models.kt             # Data classes
```

---

## OAuth scopes used

| Scope | Purpose |
|-------|---------|
| `openid` | OpenID Connect (get user sub/ID) |
| `profile` | Read display name |
| `w_member_social` | Create posts on your behalf |

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `LINKEDIN_CLIENT_ID is not set` | Create `.env` from `.env.example` |
| `Authorization timed out` | Complete login within 2 minutes |
| `Post failed (403)` | Ensure "Share on LinkedIn" product is added to your app |
| `Image upload failed` | Supported formats: JPEG, PNG, GIF, WEBP |
| Browser doesn't open | Copy the URL from terminal and paste it manually |
# linkedin-poster
