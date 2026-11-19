# Google Photos for Meta Portal Mini

A Codex skill that helps you refresh the [Immortal](https://github.com/starbrightlab/immortal) photo slideshow on a Meta Portal Mini. You choose a batch in Google Photos, then Codex picks a varied set of sharp photos, backs up the previous slideshow, and copies the replacements over USB.

## What you need

- A Meta Portal Mini with Immortal installed and its screensaver set to use a local photo folder.
- A USB data cable and [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools) for ADB.
- A local checkout of the [community Google Photos MCP server](https://github.com/savethepolarbears/google-photos-mcp#setup), configured with Google OAuth.
- Python with Pillow for reviewing and preparing the photos.

## Install the skill

Clone this repository into the `portal-google-photos` folder under your personal Codex skills directory. On Windows with the default Codex home, run this in PowerShell.

```powershell
git clone https://github.com/karthik-sc-ca/portal-google-photos-skill.git "$env:USERPROFILE/.codex/skills/portal-google-photos"
```

If you use a custom Codex home, use its `skills/portal-google-photos` folder. Open a new Codex session and use this prompt.

> Use $portal-google-photos to replace the slideshow on my Portal Mini with photos I choose from Google Photos.

## What happens

1. Codex opens a Google Photos Picker session. Select the photos you want it to review and click **Done**.
2. Codex downloads the approved batch and skips blurry photos, near-duplicates, and videos.
3. Codex backs up the existing slideshow, verifies the replacement files over USB, and restarts Immortal's photo frame.
4. Immortal's **Fit** setting keeps the full photo visible at its original aspect ratio. The unused space follows your background setting.

Google grants the picker access to the photos you select. Keep OAuth credentials, photos, and backups in local working storage. See [SKILL.md](SKILL.md) for the full workflow.
