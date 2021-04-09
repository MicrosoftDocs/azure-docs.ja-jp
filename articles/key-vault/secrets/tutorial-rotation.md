---
title: Azure Key Vault に格納された 1 セットの認証資格情報を使用したリソースのローテーションのチュートリアル
description: このチュートリアルでは、1 セットの認証資格情報を使用するリソースを対象に、シークレットのローテーションを自動化する方法について学習します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: 526c3d2d85a3f2877f82b3b764f395c51f7c05c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99805232"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>1 セットの認証資格情報を使用したリソースを対象にシークレットのローテーションを自動化する

Azure サービスに対する認証を行う最善の方法は[マネージド ID](../general/authentication.md) を使用することですが、この方法を選択できないシナリオもあります。 このような場合は、アクセス キーまたはシークレットが使用されます。 アクセス キーやシークレットは定期的にローテーションする必要があります。

このチュートリアルでは、1 セットの認証資格情報を使用するデータベースとサービスを対象に、シークレットの定期的なローテーションを自動化する方法について説明します。 具体的には、このチュートリアルでは Azure Event Grid の通知によってトリガーされる関数を使用して、Azure Key Vault に格納されている SQL Server のパスワードをローテーションします。


:::image type="content" source="../media/rotate-1.png" alt-text="ローテーション ソリューションの図":::

1. シークレットの有効期限が切れる 30 日前に、Key Vault から "有効期限が近づいている" ことを示すイベントが Event Grid に発行されます。
1. Event Grid がイベント サブスクリプションを確認し、HTTP POST を使用して、このイベントをサブスクライブしている関数アプリ エンドポイントを呼び出します。
1. 関数アプリがシークレットの情報を受け取り、新たにランダムなパスワードを生成してから、新しいパスワードを使用して Key Vault にシークレットの新しいバージョンを作成します。
1. 関数アプリが新しいパスワードを使用して SQL Server を更新します。

> [!NOTE]
> ステップ 3 とステップ 4 の間にタイム ラグが生じることがあります。 その間、Key Vault 内のシークレットは、SQL Server に対して認証を行うことができません。 いずれかのステップで障害が発生した場合、Event Grid は 2 時間にわたって再試行します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* Azure Key Vault
* SQL Server

まだ Key Vault と SQL Server をお持ちでない場合は、以下のデプロイ リンクを使用してください。

[![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. **[リソース グループ]** で、 **[新規作成]** を選択します。 グループに名前を付けます。このチュートリアルでは、**akvrotation** を使用します。
1. **[SQL の管理者ログイン]** で、SQL 管理者のログイン名を入力します。 
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[作成]**

:::image type="content" source="../media/rotate-2.png" alt-text="リソース グループの作成":::

これで、キー コンテナーと SQL Server インスタンスが完成しました。 このセットアップは、Azure CLI から次のコマンドを実行して検証できます。

```azurecli
az resource list -o table -g akvrotation
```

次のような出力結果が得られます。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>SQL Server のパスワード ローテーション関数を作成してデプロイする
> [!IMPORTANT]
> 次のテンプレートでは、Key Vault、SQL サーバー、Azure 関数が同じリソース グループに含まれている必要があります。

次に、システムマネージド ID を使用して関数アプリとその他の必須コンポーネントを作成し、SQL Server のパスワード ローテーション関数をデプロイします。

関数アプリには次のコンポーネントが必要です。
- Azure App Service プラン
- イベント トリガーと HTTP トリガーによる SQL パスワード ローテーション関数を備えた関数アプリ 
- 関数アプリのトリガーの管理に必要なストレージ アカウント
- Key Vault 内のシークレットにアクセスする関数アプリ ID のアクセス ポリシー
- **SecretNearExpiry** イベントの EventGrid イベント サブスクリプション

1. Azure テンプレートのデプロイのリンクを選択します。 

   [![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. **[リソース グループ]** 一覧から **[akvrotation]** を選択します。
1. **[SQL Server 名]** に、SQL Server の名前とローテーションするパスワードを入力します。
1. **[Key Vault 名]** に、Key Vault の名前を入力します。
1. **[関数アプリ名]** に関数アプリの名前を入力します。
1. **[シークレット名]** に、パスワードが格納されるシークレットの名前を入力します。
1. **[Repo Url]\(リポジトリの URL\)** に、関数コードがある GitHub の場所 ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** ) を入力します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **[作成]** を選択します。

:::image type="content" source="../media/rotate-3.png" alt-text="[確認および作成] を選択します。":::
  

上記の手順を完了すると、ストレージ アカウント、サーバー ファーム、関数アプリを使用できるようになります。 このセットアップは、Azure CLI から次のコマンドを実行して検証できます。

```azurecli
az resource list -o table -g akvrotation
```

次のような出力結果が得られます。

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

関数アプリを作成し、マネージド ID を使用して Key Vault にアクセスする方法については、「[Azure portal から関数アプリを作成する](../../azure-functions/functions-create-function-app-portal.md)」、[App Service と Azure Functions のマネージド ID を使用する方法](../../app-service/overview-managed-identity.md)、および [Azure portal を使用した Key Vault アクセス ポリシーの割り当て](../general/assign-access-policy-portal.md)に関するページを参照してください。

### <a name="rotation-function"></a>ローテーション関数
前の手順でデプロイした関数は、イベントを使用して、Key Vault と SQL データベースを更新することにより、シークレットのローテーションをトリガーします。 

#### <a name="function-trigger-event"></a>関数のトリガー イベント

この関数は、イベント データを読み取り、ローテーション ロジックを実行します。

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>シークレットのローテーション ロジック
このローテーション メソッドでは、シークレットからデータベース情報を読み取り、新しいバージョンのシークレットを作成し、新しいシークレットを使用してデータベースを更新します。

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
[GitHub](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp) に完全なサンプル コードがあります。

## <a name="add-the-secret-to-key-vault"></a>Key Vault にシークレットを追加する
*シークレットの管理* 権限をユーザーに付与するようにアクセス ポリシーを設定します。

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

SQL Server のリソース ID、SQL Server のログイン名、シークレットの有効期間 (日数) を含んだタグを使用して、新しいシークレットを作成します。 シークレットの名前、SQL データベースの初期パスワード (この例では "Simple123")、有効期限 (翌日に設定) を指定します。

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

有効期限が短いシークレットを作成すると、15 分以内に `SecretNearExpiry` イベントが発行されます。これにより、関数がトリガーされてシークレットがローテーションされます。

## <a name="test-and-verify"></a>テストして検証する

シークレットのローテーションが完了したことを確認するために、 **[Key Vault]**  >  **[シークレット]** に移動します。

:::image type="content" source="../media/rotate-8.png" alt-text="[Key Vault] > [シークレット] にアクセスする方法を示すスクリーンショット。":::

**sqlPassword** シークレットを開き、元のバージョンとローテーション後のバージョンを確認します。

:::image type="content" source="../media/rotate-9.png" alt-text="[シークレット] に移動する":::

### <a name="create-a-web-app"></a>Web アプリを作成する

SQL の資格情報を確認するために、Web アプリを作成します。 この Web アプリは、Key Vault からシークレットを取得し、SQL データベースの情報と資格情報をシークレットから抽出して、SQL Server への接続をテストします。

Web アプリには次のコンポーネントが必要です。
- システム マネージド ID が付与されている Web アプリ
- Web アプリのマネージド ID を使用して Key Vault 内のシークレットにアクセスするためのアクセス ポリシー

1. Azure テンプレートのデプロイのリンクを選択します。 

   [![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. **akvrotation** リソース グループを選択します。
1. **[SQL Server 名]** に、SQL Server の名前とローテーションするパスワードを入力します。
1. **[Key Vault 名]** に、Key Vault の名前を入力します。
1. **[シークレット名]** に、パスワードが格納されるシークレットの名前を入力します。
1. **[Repo Url]\(リポジトリの URL\)** に、Web アプリのコードがある GitHub の場所 ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** ) を入力します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. **［作成］** を選択します


### <a name="open-the-web-app"></a>Web アプリを開く

デプロイされたアプリケーションの URL に移動します。
 
'https://akvrotation-app.azurewebsites.net/'

アプリケーションをブラウザーで開いている場合、**生成されたシークレット値** が表示され、**データベース接続済み** の値が *true* と表示されます。

## <a name="learn-more"></a>詳細情報

- チュートリアル:[2 セットの資格情報を使用したリソースのローテーション](tutorial-rotation-dual.md)
- 概要:[Azure Event Grid での Key Vault の監視](../general/event-grid-overview.md)
- 方法:[キー コンテナーのシークレットが変更されたときにメールを受信する](../general/event-grid-logicapps.md)
- [Azure Key Vault 用の Azure Event Grid イベント スキーマ](../../event-grid/event-schema-key-vault.md)
