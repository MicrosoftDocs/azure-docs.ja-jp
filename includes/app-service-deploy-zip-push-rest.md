---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 4f3236c0a167a2b6f7586c6cb5fea8e30f55a86c
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954083"
---
## <a name="rest"></a>REST API を使って ZIP ファイルを展開する 

[展開サービス REST API](https://github.com/projectkudu/kudu/wiki/REST-API) を使って、.zip ファイルを Azure 内のアプリに展開できます。 展開するには、POST 要求を https://<アプリ名>.scm.azurewebsites.net/api/zipdeploy に送信します。 POST 要求のメッセージの本文に .zip ファイルを含める必要があります。 アプリの展開資格情報は、HTTP 基本認証を使って要求で提供します。 詳しくは、[.zip のプッシュ展開](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)に関するページをご覧ください。 

HTTP BASIC 認証では、App Service 展開資格情報が必要です。 自分の展開資格情報を設定する方法については、「[ユーザー レベルの資格情報の設定とリセット](../articles/app-service/deploy-configure-credentials.md#userscope)」を参照してください。

### <a name="with-curl"></a>cURL を使用する

次の例は cURL ツールを使用して .zip ファイルを展開します。 プレースホルダー `<username>`、`<password>`、`<zip_file_path>`、`<app_name>` を置き換えます。 cURL によって要求されたら、パスワードを入力します。

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

この要求により、アップロードされた .zip ファイルからのプッシュ展開がトリガーされます。 次の cURL の例で示すように、`https://<app_name>.scm.azurewebsites.net/api/deployments` エンドポイントを使うことにより、現在および過去の展開を確認できます。 やはり、`<app_name>` はアプリの名前に置き換え、`<deployment_user>` は展開資格情報のユーザー名に置き換えます。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>PowerShell の場合

次の例では、[Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) を使用して .zip ファイルをアップロードします。 プレースホルダー `<group-name>`、`<app-name>`、`<zip-file-path>` を置き換えます。

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

この要求により、アップロードされた .zip ファイルからのプッシュ展開がトリガーされます。 

現在と過去の展開を確認するには、次のコマンドを実行します。 ここでも、`<deployment-user>`、`<deployment-password>`、および `<app-name>` の各プレースホルダーを置き換えます。

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
