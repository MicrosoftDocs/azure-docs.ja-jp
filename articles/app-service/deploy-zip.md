---
title: ZIP または WAR ファイルによるコードのデプロイ
description: ZIP ファイル (Java 開発者の場合は WAR ファイル) を使用して Azure App Service にアプリをデプロイする方法について説明します。
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007910"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>ZIP または WAR ファイルを使用した Azure App Service へのアプリのデプロイ

この記事では、ZIP または WAR ファイルを使用して Web アプリを [Azure App Service](overview.md) にデプロイする方法について説明します。 

この ZIP ファイル デプロイでは、Kudu サービスを使用することで、継続的な統合ベース デプロイを効率化できます。 Kudu では、ZIP ファイルでのデプロイについて次の機能がサポートされています。 

- 以前のデプロイから残っているファイルの削除。
- 既定のビルド プロセスを有効にするオプション (パッケージの復元を含む)。
- デプロイのカスタマイズ (デプロイ スクリプトの実行など)。  
- デプロイ ログ。 
- ファイル サイズの上限は 2048 MB です。

詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)をご覧ください。

WAR ファイルの展開では、Java Web アプリを実行するために [WAR](https://wikipedia.org/wiki/WAR_(file_format)) ファイルを App Service に展開します。 「[WAR ファイルの展開](#deploy-war-file)」を参照してください。

> [!NOTE]
> `ZipDeploy` を使用すると、ファイルは、タイムスタンプが既にデプロイされているものと一致しない場合にのみコピーされます。 出力をキャッシュするビルド プロセスを使用して zip を生成すると、デプロイが高速になる場合があります。 詳細については、「[zip ファイルまたは URL からのデプロイ](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、[App Service アプリを作成する](./index.yml)か、別のチュートリアルで作成したアプリを使用します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
現在、上記のエンドポイントは Linux App Services では機能しません。 代わりに、FTP または [ZIP デプロイ API](faq-app-service-linux.md#continuous-integration-and-deployment) の使用を検討してください。

## <a name="deploy-zip-file-with-azure-cli"></a>Azure CLI を使って ZIP ファイルを展開する

[az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) コマンドを使用して、アップロードされた ZIP ファイルを Web アプリにデプロイします。  

次に示すのは、アップロードした ZIP ファイルをデプロイするコマンドの例です。 Azure CLI のローカル インストールを使用する場合は、`--src` にローカル ZIP ファイルのパスを指定します。

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

このコマンドは、ファイルとディレクトリを ZIP ファイルから既定の App Service アプリケーション フォルダー (`\home\site\wwwroot`) にデプロイし、アプリを再起動します。

既定で、展開エンジンでは ZIP ファイルがそのまま実行できる状態で、ビルド自動化は実行されないことが想定されています。 [Git デプロイ](deploy-local-git.md)の場合と同じビルド自動化を有効にするには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行することで、`SCM_DO_BUILD_DURING_DEPLOYMENT` アプリ設定を設定します。

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)をご覧ください。

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR ファイルの展開

WAR ファイルを App Service にデプロイするには、 POST 要求を `https://<app-name>.scm.azurewebsites.net/api/wardeploy` に送信します。 POST 要求のメッセージの本文に .war ファイルを含める必要があります。 アプリの展開資格情報は、HTTP 基本認証を使って要求で提供します。

WAR ファイルを展開するときは常に `/api/wardeploy` を使用します。 この API により、WAR ファイルが拡張され、共有ファイル ドライブに配置されます。 他の展開 API を使用すると、動作の一貫性が損なわれる可能性があります。 

HTTP BASIC 認証では、App Service 展開資格情報が必要です。 自分の展開資格情報を設定する方法については、「[ユーザー レベルの資格情報の設定とリセット](deploy-configure-credentials.md#userscope)」を参照してください。

### <a name="with-curl"></a>cURL を使用する

次の例は cURL ツールを使用して .war ファイルを展開します。 プレースホルダー `<username>`、`<war-file-path>`、`<app-name>` を置き換えます。 cURL によって要求されたら、パスワードを入力します。

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>PowerShell の場合

次の例では、[Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) を使用して .war ファイルをアップロードします。 プレースホルダー `<group-name>`、`<app-name>`、`<war-file-path>` を置き換えます。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>次のステップ

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [Kudu:Deploying from a zip file (Kudu: zip ファイルからのデプロイ)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service のデプロイ資格情報](deploy-ftp.md)
