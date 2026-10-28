# ERP Mobile — Atualizações

Repositório público dedicado à distribuição de versões do **ERP Mobile** para Android. O código-fonte e os dados pessoais do estoque não precisam ser publicados aqui.

## Estado atual
O canal está preparado, mas **ainda não há APK nem versão publicada**. Enquanto `version.json` tiver `"published": false`, o aplicativo deve considerar que não existem atualizações disponíveis.

## Endpoints
- **Manifesto:** https://raw.githubusercontent.com/fagoneszy/erp-mobile-releases/main/version.json
- **Última release (API):** https://api.github.com/repos/fagoneszy/erp-mobile-releases/releases/latest
- **Releases:** https://github.com/fagoneszy/erp-mobile-releases/releases

## Funcionamento
1. O aplicativo consulta `version.json` ao iniciar, com um intervalo mínimo entre verificações (por exemplo, 12–24 horas).
2. Se `published` for `false`, nenhum aviso é exibido.
3. Se existir uma versão superior à instalada, mostra versão, notas e botão **Baixar atualização**.
4. O botão abre `downloadUrl` para baixar o APK; a instalação é confirmada pelo Android.
5. Os dados SQLite **não devem ser apagados** pela atualização. Faça backups e migrações de schema quando necessário.

A versão instalada deve ser lida pelo próprio app (ex.: `expo-application`). Compare versões usando uma biblioteca SemVer; nunca compare as strings diretamente.

## Publicação
Veja [docs/publicacao.md](docs/publicacao.md) para o processo completo de geração, assinatura e lançamento de um novo APK.

Se a automação de sincronização estiver habilitada, publicar uma GitHub Release estável com APK anexo atualiza `version.json` automaticamente.

## Segurança
- Publique somente APKs assinados com a **mesma chave** do aplicativo já instalado.
- Aumente `android.versionCode` em cada build e ajuste `expo.version`.
- Não publique segredos, chaves de assinatura, dados do estoque nem credenciais no repositório.
- Valide o domínio do arquivo de atualização e obtenha o link apenas do repositório esperado.
- **Não há instalação silenciosa:** o Android solicita a aprovação do usuário.

Uso pessoal, distribuição Android por APK (fora da Play Store).
