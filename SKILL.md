---
name: portal-google-photos
description: Select good photos from a user-approved Google Photos batch and replace the Immortal slideshow on a Meta Portal Mini over USB ADB. Use for refreshing the Portal photo frame with Google Photos.
---

# Refresh the Portal Mini slideshow

## Get the selected photos

- Find the local checkout of the community Google Photos MCP server, or follow the upstream setup below. The built server uses its local ignored `.env` and token database. Reuse that setup. Keep credentials and tokens out of tool output and the skill.
- The local MCP endpoint is `http://localhost:3000/mcp`. If it is down, run `npm.cmd start` from the server folder. The local build binds to localhost. If Windows blocks the token database ACL inside the sandbox, request escalation for startup and keep the ACL check intact.
- Call `auth_status`. If sign-in is needed, have the user open `http://localhost:3000/auth` and approve access. For an OAuth Testing 403, add the sign-in account in the Cloud project's Google Auth Platform → Audience → Test users, then retry. For a fresh setup, follow the [upstream documentation](https://github.com/savethepolarbears/google-photos-mcp#setup) and use the Web application redirect `http://localhost:3000/auth/callback`.
- Call `create_picker_session` and retain its session ID in ignored local working storage. Give the returned picker URL to the user. The user approves the batch in Google Photos and clicks **Done**. You can curate that batch after approval.
- Call `poll_picker_session` with the Picker session ID. Once `mediaItemsSet` is true, paginate using `nextPageToken`. Download images with `download_picker_media` into a fresh ignored review folder. Use each item's `baseUrl` and `mimeType`, with `savePath`, `downloadOriginal=false`, and up to 1920 px per side for this display. Check the saved files decode. Skip videos for a photos-only slideshow.
- If the server is not registered in the active client, connect from the server folder with its installed SDK's `Client` and `StreamableHTTPClientTransport` against the same endpoint. In Windows PowerShell pipe a literal here-string to `node --input-type=module`. End each HTTP MCP client session with `transport.terminateSession()` before `client.close()`. If initialization returns HTTP 500 with “Already connected to a transport”, close the stale HTTP MCP session or restart the local server. Keep the Google Picker session ID for polling across turns.

## Curate and replace

1. Generate numbered contact sheets and review all shared images. Favor clear faces, good framing, and a mix of scenes. Skip near-duplicates, blurry shots, and accidental captures. Prepare the chosen images as EXIF-corrected JPEGs in an isolated folder, preserving the aspect ratio. Choose the number based on the batch and the user's preference.
2. Verify the Portal is the USB device before copying. Use `adb -d` from Android SDK Platform-Tools, passing the full executable path if needed. Confirm the model reports Portal and the configured screensaver `settings get secure screensaver_components` points to `com.immortal.launcher/.PhotoDreamService`. If USB ADB finds no device, have the user check **Portal Settings → Debug → ADB Enabled**, reconnect the data cable behind the speaker cover, and accept the debugging prompt.
3. On this Portal the local slideshow folder is `/sdcard/Pictures/Exports-Selected`. Confirm it is the intended folder. For a replacement, pull the existing folder to a fresh ignored local backup, push the new set to a staging folder on the device, and compare counts and SHA-256 hashes. Swap the staged folder into the same slideshow path. Keep the old folder available for rollback until the swap verifies, then remove only that old device folder. If anything fails before verification, preserve or restore the previous set.
4. In Immortal **Settings → Screensaver**, set **Fit** and keep **Crop vertical photos (~20%)** off for full pictures at their original aspect ratio. Preserve the background choice unless requested. **Blur photo background** uses a blurred copy in the extra space. Turning it off uses black.
5. Restart the slideshow so Immortal reloads the folder. Tap to exit the current slideshow and relaunch it from the home photo-frame button or, on this Portal, use ADB to start `com.android.systemui/.Somnambulator` after leaving the current dream. Use an ADB screenshot to verify a new photo is actually displayed. Report the installed count, verification, and backup location. Keep private photos and screenshots in ignored storage.
