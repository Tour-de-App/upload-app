![Frame 1](https://github.com/user-attachments/assets/4b8145b6-db05-415b-9d1c-511b88dfff83)

GitHub akce pro nahrávání Docker obrazů do TdA registry.

## ❓ Jak použít

1. Vytvořte si vlastní `Dockerfile`, nebo použijte jednu z našich [šablon](https://github.com/sablony)
2. Ve složce `.github/workflows` vytvořte nový soubor `upload.yml`
3. Do GitHub akce vložte tento kód, který sestaví váš Docker obraz, a poté ho nahraje do TdA registry
```yml
name: Build and push to TdA

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Check Out Repo
        uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Build
        uses: docker/build-push-action@v6.8.0
        with:
          context: ./
          cache-from: type=gha
          cache-to: type=gha,mode=max`
          tags: tda-generic:latest
          outputs: type=docker,dest=/tmp/tda-generic.tar

      - name: Upload to TdA
        uses: Tour-de-App/upload-app@tda-25
        with:
          access_token: ${{ secrets.TEAM_TOKEN }}
          image_name: tda-generic
          image_path: /tmp/tda-generic.tar
```
4. Vygenerujte si vlastní `TEAM_TOKEN`, a [nastavte](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository) si ho jako secret v GitHub actions.
