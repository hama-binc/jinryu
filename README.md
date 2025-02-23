# jinryu

## daily backup with [MixSoda-util](https://github.com/code4fukui/MixSoda-util/)

### setup

```sh
mkdir .github
mkdir .github/workflows
cat > .github/workflows/scheduled-backup.yml
```

```yml
name: Scheduled 

on:
  schedule:
    # 1:31分に実行 1(JST)-9+24=16(UTC)
    - cron: '31 16 * * *'

jobs:
  build:
    name: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: denoland/setup-deno@v1
        with:
          deno-version: v1.x
      - name: make
        env:
          SECRET_CODE: ${{ secrets.code }}
        run: |
          deno run -A https://code4fukui.github.io/MixSoda-util/backup.js $SECRET_CODE
      - name: commit and push
        run: |
          git config --global user.email "workflow@example.com"
          git config --global user.name "workflow user"
          git add .
          git commit -m 'update data' && git push ${REPO} HEAD:${{github.event.pull_request.head.ref}} || true
          git push
```

