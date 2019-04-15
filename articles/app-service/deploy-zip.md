---
title: ZIP または WAR ファイルによるコードのデプロイ- Azure App Service | Microsoft Docs
description: ZIP ファイル (Java 開発者の場合は WAR ファイル) を使用して Azure App Service にアプリをデプロイする方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.custom: seodec18
ms.openlocfilehash: ef313ea631a963aa7893bf15e826e591c9d9cfc3
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619800"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>ZIP または WAR ファイルを使用した Azure App Service へのアプリのデプロイ

この記事では、ZIP または WAR ファイルを使用して Web アプリを [Azure App Service](overview.md) にデプロイする方法について説明します。 

この ZIP ファイル デプロイでは、Kudu サービスを使用することで、継続的な統合ベース デプロイを効率化できます。 Kudu では、ZIP ファイルでのデプロイについて次の機能がサポートされています。 

- 以前のデプロイから残っているファイルの削除。
- 既定のビルド プロセスを有効にするオプション (パッケージの復元を含む)。
- [デプロイのカスタマイズ](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) (デプロイ スクリプトの実行など)。  
- デプロイ ログ。 
- ファイル サイズの上限は 512 MB です。

詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)をご覧ください。

WAR ファイルの展開では、Java Web アプリを実行するために [WAR](https://wikipedia.org/wiki/WAR_(file_format)) ファイルを App Service に展開します。 「[WAR ファイルの展開](#deploy-war-file)」を参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のことが条件となります。

* [App Service アプリを作成する](/azure/app-service/)か、別のチュートリアルで作成したアプリを使用します。

## <a name="create-a-project-zip-file"></a>プロジェクトの ZIP ファイルを作成する

>[!NOTE]
> ファイルを ZIP ファイルでダウンロードした場合は、まずファイルを抽出します。 たとえば、GitHub から ZIP ファイルをダウンロードした場合、そのファイルをそのままデプロイすることはできません。 GitHub では、入れ子になったディレクトリが追加されますが、これらは App Service と連携しません。 
>

ローカル ターミナル ウィンドウで、アプリ プロジェクトのルート ディレクトリに移動します。 

このディレクトリには、Web アプリへの入力ファイルを含める必要があります (_index.html_、 _index.php_、_app.js_ など)。 パッケージ管理ファイルを含めることもできます (_project.json_、_composer.json_、_package.json_、_bower.json_、_requirements.txt_ など)。

プロジェクト内のすべての ZIP アーカイブを作成します。 次のコマンドには、お使いの端末の既定のツールを使用します。

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Azure CLI を使って ZIP ファイルを展開する

Azure CLI バージョンが 2.0.21 以降であることを確認します。 使用しているバージョンを確認するには、ターミナル ウィンドウで `az --version` コマンドを実行します。

[az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) コマンドを使用して、アップロードされた ZIP ファイルを Web アプリにデプロイします。  

次に示すのは、アップロードした ZIP ファイルをデプロイするコマンドの例です。 Azure CLI のローカル インストールを使用する場合は、`--src` にローカル ZIP ファイルのパスを指定します。   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

このコマンドは、ファイルとディレクトリを ZIP ファイルから既定の App Service アプリケーション フォルダー (`\home\site\wwwroot`) にデプロイし、アプリを再起動します。 任意の追加のカスタム ビルド プロセスが構成されている場合、そのプロセスも実行されます。 詳しくは、[Kudu ドキュメント](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)をご覧ください。

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR ファイルの展開

WAR ファイルを App Services に展開するには、POST 要求を https://<アプリ名>.scm.azurewebsites.net/api/wardeploy に送信します。 POST 要求のメッセージの本文に .war ファイルを含める必要があります。 アプリの展開資格情報は、HTTP 基本認証を使って要求で提供します。 

HTTP BASIC 認証では、App Service 展開資格情報が必要です。 自分の展開資格情報を設定する方法については、「[ユーザー レベルの資格情報の設定とリセット](deploy-configure-credentials.md#userscope)」を参照してください。

### <a name="with-curl"></a>cURL を使用する

次の例は cURL ツールを使用して .war ファイルを展開します。 プレースホルダー `<username>`、`<war_file_path>`、`<app_name>` を置き換えます。 cURL によって要求されたら、パスワードを入力します。

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>PowerShell の場合

次の例では、[Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)を使って、.war ファイルを含む要求を送信します。 プレースホルダー `<deployment_user>`、`<deployment_password>`、`<zip_file_path>`、`<app_name>` を置き換えます。

```powershell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>次の手順

高度なデプロイ シナリオの詳細については、[Git を使用した Azure へのデプロイ](deploy-local-git.md)に関するページをご覧ください。 Azure への Git ベース デプロイでは、バージョン管理、パッケージの復元、MSBuild などが可能です。

## <a name="more-resources"></a>その他のリソース

* [Kudu:Deploying from a zip file (Kudu: zip ファイルからのデプロイ)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service のデプロイ資格情報](deploy-ftp.md)
