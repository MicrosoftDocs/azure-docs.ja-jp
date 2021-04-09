---
title: テンプレートでアプリをデプロイする
description: App Service アプリをプロビジョニングしてデプロイするための Azure Resource Manager テンプレートの作成に関するガイダンスを紹介します。
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 1146b5979d81b91c6c6894aa54b2e0ca50c896c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88961620"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用した Web アプリのデプロイに関するガイダンス

この記事では、Azure App Service ソリューションをデプロイするための Azure Resource Manager テンプレートの作成に関する推奨事項を示します。 これらの推奨事項は、一般的な問題を回避するために役立ちます。

## <a name="define-dependencies"></a>依存関係を定義する

Web アプリの依存関係を定義するには、Web アプリ内のリソースがどのように連係しているかを理解する必要があります。 依存関係を間違った順序で指定すると、デプロイ エラーが発生するか、デプロイを停止させる競合状態が発生する場合があります。

> [!WARNING]
> テンプレートに MSDeploy サイト拡張機能を含める場合は、すべての構成リソースを、MSDeploy リソースに依存するように設定する必要があります。 構成を変更すると、サイトが非同期で再起動されます。 構成リソースを MSDeploy に依存させることで、サイトの再起動前に MSDeploy を確実に終了させることができます。 これらの依存関係がない場合は、MSDeploy のデプロイ プロセス中にサイトが再起動されることがあります。 テンプレートの例については、「[WordPress Template with Web Deploy Dependency (Web 配置依存関係を持つ WordPress テンプレート)](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)」を参照してください。

次の図は、さまざまな App Service リソースの依存関係の順序を示しています。

![Web アプリの依存関係](media/web-sites-rm-template-guidance/web-dependencies.png)

次の順序でリソースをデプロイします。

**レベル 1**
* App Service プラン。
* データベースやストレージ アカウントなどの他の関連リソース。

**レベル 2**
* Web アプリ - App Service プランに依存。
* サーバー ファームを対象とする Azure Application Insights インスタンス - App Service プランに依存。

**レベル 3**
* ソース管理 - Web アプリに依存。
* MSDeploy サイト拡張機能 - Web アプリに依存。
* Web アプリをターゲットとする Azure Application Insights インスタンス - Web アプリに依存。

**レベル 4**
* App Service 証明書 - ソース管理または MSDeploy に依存 (いずれかが存在する場合)。 存在しない場合は Web アプリに依存。
* 構成設定 (接続文字列、Web 構成値、アプリ設定) - ソース管理または MSDeploy に依存 (いずれかが存在する場合)。 存在しない場合は Web アプリに依存。

**レベル 5**
* ホスト名のバインディング - 証明書に依存 (存在する場合)。 存在しない場合は上位レベルのリソースに依存。
* サイト拡張機能 - 構成設定に依存 (存在する場合)。 存在しない場合は上位レベルのリソースに依存。

通常、ソリューションにはこれらのリソースとレベルの一部しか含まれていません。 ないレベルについては、下位のリソースを次に高いレベルにマップします。

次の例は、テンプレートの一部を示しています。 接続文字列の構成値は、MSDeploy 拡張機能に応じて異なります。 MSDeploy 拡張機能は、Web アプリおよびデータベースに依存します。 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

上記のコードを使用するすぐに実行できるサンプルについては、「[Template:Build a simple Umbraco Web App](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)」(テンプレート: 簡単な Umbraco Web アプリを作成する) をご覧ください。

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy エラーについての情報を見つける

Resource Manager テンプレートで MSDeploy が使用されている場合は、デプロイ エラー メッセージを理解しにくいことがあります。 デプロイの失敗後に詳細情報を取得するには、次の手順を実行します。

1. サイトの [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)に移動します。
2. D:\home\LogFiles\SiteExtensions\MSDeploy のフォルダーを参照します。
3. appManagerStatus.xml および appManagerLog.xml ファイルを探します。 最初のファイルには、状態が記録されます。 2 番目のファイルには、エラーに関する情報が記録されます。 エラーが明確でない場合は、[フォーラム](/answers/topics/azure-webapps.html)に支援を求めるときに、そのファイルを含めることができます。

## <a name="choose-a-unique-web-app-name"></a>一意の Web アプリ名の選択

Web アプリの名前は、グローバルに一意である必要があります。 一意になると思われる命名規則を使用するか、[uniqueString 関数](../azure-resource-manager/templates/template-functions-string.md#uniquestring)を使用して一意の名前の生成を支援することができます。

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Web アプリの証明書を Key Vault からデプロイする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

テンプレートに TLS/SSL バインディングの [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) リソースが含まれ、証明書が Key Vault に格納されている場合、App Service の ID が証明書にアクセスできることを確認する必要があります。

グローバル Azure では、App Service サービス プリンシパルの ID は **abfa0a7c-a6b6-4736-8310-5855508787cd** です。 App Service サービス プリンシパルに Key Vault へのアクセスを許可するには、次を使用します。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Azure Government では、App Service サービス プリンシパルの ID は **6a02c803-dafd-4136-b4c3-5a6f318b4714** です。 前の例でその ID を使います。

お使いの Key Vault で **[証明書]** と **[生成/インポート]** を選択して、証明書をアップロードします。

![証明書のインポート](media/web-sites-rm-template-guidance/import-certificate.png)

テンプレートで、`keyVaultSecretName` に対する証明書の名前を指定します。

テンプレートの例については、「[Deploy a Web App certificate from Key Vault secret and use it for creating SSL binding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)」(Key Vault シークレットから Web アプリ証明書を展開し、SSL バインディングの作成に使用する) をご覧ください。

## <a name="next-steps"></a>次のステップ

* テンプレートを使用して Web アプリをデプロイするチュートリアルについては、「[Azure でマイクロサービスを予測どおりにデプロイする](deploy-complex-application-predictably.md)」を参照してください。
* テンプレート内のリソースの種類用の JSON 構文とプロパティの詳細については、[Azure Resource Manager テンプレート リファレンス](/azure/templates/)を参照してください。