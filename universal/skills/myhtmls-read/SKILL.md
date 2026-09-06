---
name: myhtmls-read
description: Use when the user provides a myhtmls.dev URL to read.
metadata:
  harness: [all]
  platform: [darwin, linux]
  scope: fleet
  requires: "curl"
---

# myhtmls Read

Fetch the uploaded HTML with the shell. Do not use web search or a browser.

1. Remove a trailing slash, then append `/raw` unless the URL already ends in
   `/raw`.
2. Drafts are private (owner-only), so send the API key from the CLI config:

   ```sh
   KEY=$(node -e "console.log(require(require('os').homedir()+'/.myhtmls/credentials.json').apiKey)")
   curl --fail --silent --show-error --location --max-time 30 \
     -H "Authorization: Bearer $KEY" --output /tmp/myhtmls.html '<raw-url>'