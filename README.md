## Hi there 👋

<!--
**flyby-blog-publisher/Flyby-blog-publisher** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
name: Daily WordPress Post

on:
  schedule:
    # 04:00 UTC = 09:30 IST. Change the cron below to shift the posting time.
    - cron: "0 4 * * *"
  workflow_dispatch: {}   # lets you also trigger a run manually from the Actions tab

permissions:
  contents: write   # needed so the workflow can commit the "post published" move

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repo
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Publish next queued post
        env:
          WP_SITE_URL: ${{ secrets.WP_SITE_URL }}
          WP_USERNAME: ${{ secrets.WP_USERNAME }}
          WP_APP_PASSWORD: ${{ secrets.WP_APP_PASSWORD }}
        run: python scripts/publish.py

      - name: Commit queue state
        run: |
          git config user.name "flyby-blog-bot"
          git config user.email "actions@users.noreply.github.com"
          git add posts_queue/
          git diff --quiet --cached || git commit -m "Mark post as published [skip ci]"
          git push
