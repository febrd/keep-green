# Keep Green

**Keep Green** is an automated GitHub Action designed to keep the repository active by periodically committing changes to the `master` branch.

## 📌 Purpose
- **Prevent repository inactivity**: Some services or algorithms may archive or restrict features for repositories that remain inactive for extended periods.
- **Experiment with GitHub Actions**: Keep Green serves as an example of automation using GitHub Actions.
- **Practice CI/CD automation**: This repository can be used as a case study for automation using GitHub Actions.

## 🚀 How It Works
1. **Trigger the workflow**: This workflow runs on every push to `master` and executes every minute via a cron job (`* * * * *`).
2. **Checkout repository**: Fetches the entire repository without credentials to avoid security conflicts.
3. **Update log file**: The `KEEP_GREEN_LOG` file is updated with the latest timestamp.
4. **Automatically commit changes**: A commit is made with a randomly selected message from a predefined list.
5. **Push changes to the repository**: The newly created commit is pushed to the repository using GitHub Actions.

## 🛠 GitHub Action Configuration
Here are the main steps of the `keep-green.yml` configuration:

```yaml
name: Keep Green

on:
  schedule:
    - cron: "*/5 * * * *" # Menjalankan workflow setiap 2 menit
  push:
    branches:
      - master

jobs:
  auto_commit:
    runs-on: ubuntu-latest

    steps:
      - name: Debug Reference
        run: echo "::debug::Ref = ${{ github.ref }}"

      - name: Checkout Repository
        uses: actions/checkout@v3
        with:
          persist-credentials: false
          fetch-depth: 0 

      - name: Modify Last Update
        run: |
          d=$(date '+%Y-%m-%dT%H:%M:%SZ')
          echo "$d" > KEEP_GREEN_LOG

      - name: Check for Changes
        id: changes
        run: |
          set +e 
          git diff --exit-code > /dev/null
          if [ $? -ne 0 ]; then
            echo "changes_detected=true" >> $GITHUB_ENV
          else
            echo "changes_detected=false" >> $GITHUB_ENV
          fi
          set -e

      - name: Commit Changes
        if: env.changes_detected == 'true'
        run: |
          git config --local user.email "your@email.domain"
          git config --local user.name "yourgithubusername"
          git add -A

          arr=("febrd(v1): 😂 Keep Green"
               "febrd(v1): 😱 Keep Green"
               "febrd(v1): 👿 Keep Green"
               "febrd(v1): 🙏 Keep Green"
               "febrd(v1): 🙈 Keep Green"
               "febrd(v1): 🐐 Keep Green"
               "febrd(v1): 🤖 Keep Green"
               "febrd(v1): 🟩 Keep Green"
               "febrd(v1): 👻 Keep Green")

          rand=$((RANDOM % ${#arr[@]}))
          git commit -m "${arr[$rand]}"

      - name: Pull Latest Changes
        if: env.changes_detected == 'true'
        run: |
          git pull origin master --rebase

      - name: Push Changes
        if: env.changes_detected == 'true'
        uses: ad-m/github-push-action@v0.6.0
        with:
          directory: "."
          github_token: ${{ secrets.GITHUB_TOKEN }}

```

## 📌 Notes
- **Important**: This workflow will push changes to the `master` branch periodically. Ensure there are no conflicts with other repository tasks.
- **Security**: `github_token` is used for authentication and pushing changes.
- **Customization**: You can modify the commit message list or adjust the cron schedule as needed.

---

💡 **Keep Green** is a simple example of how GitHub Actions can be used for repository automation. 🚀

