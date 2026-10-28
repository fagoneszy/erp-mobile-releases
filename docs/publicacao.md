# Publicação de APKs — ERP Mobile

## 1. Build Android (Expo / EAS)
No projeto do app, configure `expo.android.package = "com.fagner.erpmobile"`, `expo.version` (ex.: `1.0.0`) e `expo.android.versionCode` (ex.: `1`). O pacote deve continuar **igual** nas próximas versões. Guarde a chave de assinatura do Android com segurança.

Exemplo de `eas.json`:

```json
{
  "build": {
    "release": {
      "distribution": "internal",
      "android": { "buildType": "apk" }
    }
  }
}
```

```bash
npm install -g eas-cli
eas login
eas build:configure
eas build --platform android --profile release
```

Baixe o APK resultante e renomeie, por exemplo, para `erp-mobile-v1.0.0.apk`.

## 2. Release estável no GitHub
No repositório de releases, crie uma Release **não prerelease** e **não draft**, com tag SemVer `v1.0.0` e inclua exatamente um arquivo `.apk`.

Via GitHub CLI, a partir do diretório em que o APK está salvo:

```bash
gh auth login
gh release create v1.0.0 ./erp-mobile-v1.0.0.apk --repo fagoneszy/erp-mobile-releases --title "ERP Mobile v1.0.0" --notes "Primeira versão estável"
```

O GitHub Actions `sync-version.yml` irá ler essa release e atualizar o arquivo `version.json` com a tag, o nome/URL do APK e seu hash SHA-256. Antes de publicar a próxima versão, atualize a versão e incremente o `versionCode` no app.

## 3. No aplicativo
O app pode consultar `https://raw.githubusercontent.com/fagoneszy/erp-mobile-releases/main/version.json`, com cache e tratamento de erros. Apenas ofereça a atualização se `published === true`, `appId` corresponder ao pacote instalado, a versão SemVer for maior que a instalada (o campo `versionCode` é opcional neste manifesto) e a URL de download for HTTPS e apontar para a Release esperada. Uma checagem por dia normalmente é suficiente; também ofereça **Verificar atualizações** em Configurações.

A instalação de APK requer confirmação do usuário. Para integridade, verifique o SHA-256 após baixar o arquivo, antes de oferecê-lo ao instalador do Android. No aplicativo, considere que um repositório público pode sofrer alterações e trate o manifesto como informação não confiável; não execute nenhum conteúdo retornado. Para alta garantia de autenticidade, use assinatura digital do manifesto com chave pública incorporada ao app.

**Banco de dados:** antes de atualizar o schema SQLite, implemente migrações incrementais e backup. A instalação de atualização assinada com a mesma chave normalmente preserva os dados do aplicativo.

## 4. Testes
1. Sem Release publicada: o app não indica nova versão.
2. Release válida com APK: o JSON inclui versão, URL, hash e `published=true`.
3. Release sem APK ou com múltiplos APKs: workflow falha, sem alterar o manifesto.
4. Prerelease ou draft: não atualizar o canal estável.
5. APK com assinatura divergente: Android não deve permitir atualização da instalação existente.
