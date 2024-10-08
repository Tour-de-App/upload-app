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
          access_token: ${{ secrets.ACCESS_TOKEN }}
          image_name: tda-generic
          image_path: /tmp/tda-generic.tar
```
4. Přejděta na [stránku pro vygenerování tokenu](https://odevzdavani.tourdeapp.cz/team-token) a ygenerujte si vlastní speciální token, kterým se ověříte oproti TdA registry.
5. Token [nastavte](https://docs.github.com/en/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository) jako secret, s názvem `ACCESS_TOKEN` v GitHub actions.
6. Pushněte nový commit do GitHubu, a v záložce _Actions_ si zobrazte stav vaší GitHub akce.

> [!CAUTION]
> GitHub akce v tomto příkladu je nastavena tak, aby se spustila při každém commitu do větve `main`. Pokud pracujete na svém kódu pouze na větvi `main`, může se stát že využijete všechny svoje minuty na používání GitHub actions a akce už nepůjde spustit. Proto doporučujeme vývoj provádět na jiné větvy a pak využít pull request. Více informaci o tom jak používat Git můžete najít [zde](https://tourdeapp.cz/webinare/odevzdej-a-otestuj-git-github-a-testovaci-platforma)
