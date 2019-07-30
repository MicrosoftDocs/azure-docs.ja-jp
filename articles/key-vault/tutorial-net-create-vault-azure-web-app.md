---
title: チュートリアル - .NET で Azure Web アプリを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションを構成します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 805893d6b5756c76ebc523187e0ed75c9fe24ed9
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874758"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>チュートリアル:.NET で Azure Web アプリを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーやデータベース接続文字列などのシークレットを保護するのに役立ちます。 アプリケーション、サービス、IT リソースへのアクセス手段が得られます。

このチュートリアルでは、Azure キー コンテナーから情報を読み取ることができる Azure Web アプリケーションを作成する方法について説明します。 このプロセスでは、Azure リソースのマネージド ID を使用します。 Azure Web アプリケーションについて詳しくは、[Azure App Service](../app-service/overview.md) に関する記事をご覧ください。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを追加します。
> * キー コンテナーからシークレットを取得する。
> * Azure Web アプリを作成する。
> * Web アプリのマネージド ID を有効にする。
> * Web アプリに対する書き込みアクセス許可を割り当てる。
> * Azure で Web アプリを実行する。

始める前に、[Key Vault の基本的な概念](key-vault-whatis.md#basic-concepts)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* Windows の場合: [.NET Core 2.1 SDK 以降](https://www.microsoft.com/net/download/windows)
* Mac の場合:[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Windows、Mac、Linux:
  * [Git](https://git-scm.com/downloads)
  * このチュートリアルでは、Azure CLI をローカルで実行する必要があります。 Azure CLI バージョン 2.0.4 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>マネージド サービス ID について

Azure Key Vault では資格情報が安全に格納されます。そのため資格情報はコードに表示されません。 ただし、キーを取得するためには Azure Key Vault に対して認証を行う必要があります。 Key Vault に対して認証を行うには、資格情報が必要となります。 これは従来からあるブートストラップ問題のジレンマです。 マネージド サービス ID (MSI) は、このプロセスを簡素化する "_ブートストラップ ID_" を提供することによって、この問題を解決します。

Azure サービス (Azure Virtual Machines、Azure App Service、Azure Functions など) に対して MSI を有効にすると、Azure によって[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が作成されます。 MSI は、サービスのインスタンスのプリンシパルを Azure Active Directory (Azure AD) に作成し、サービス プリンシパルの資格情報をそのインスタンスに挿入します。

![MSI ダイアグラム](media/MSI.png)

次に、アクセス トークンを取得するために、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出します。 そのコードは、Azure Key Vault サービスに対し、ローカルの MSI エンドポイントから取得したアクセス トークンを使用して認証を行います。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。

次にリソース グループ名を選択し、プレースホルダーを入力します。 次の例では、米国西部の場所にリソース グループを作成します。

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

このリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

ご利用のリソース グループにキー コンテナーを作成するには、次の情報を入力します。

* キー コンテナー名: 数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列
* リソース グループ名
* 場所: **[米国西部]**

Azure CLI で、次のコマンドを入力します。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

この時点で、使用している Azure アカウントのみが、この新しいコンテナーで操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

以上で、シークレットを追加できる状態になりました。 その候補としては、SQL 接続文字列など、安全性と、アプリケーションから利用できる状態の両方を維持する必要のある情報が考えられます。

**AppSecret** というキー コンテナーにシークレットを作成するには、次のコマンドを入力します。 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

このシークレットには、**MySecret** という値が格納されます。

シークレットに含まれている値をプレーンテキストとして表示するには、次のコマンドを入力します。

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 

これらの手順を完了すると、キー コンテナーのシークレットへの URI がわかります。 このチュートリアルで後で使用するために、この情報をメモしてください。 

## <a name="create-a-net-core-web-app"></a>.NET Core Web アプリを作成する

.NET Core Web アプリを作成して Azure に公開するには、「[Azure に ASP.NET Core Web アプリを作成する](../app-service/app-service-web-get-started-dotnet.md)」の手順を実行してください。 

次のビデオをご覧いただくこともできます。

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>ソリューションを開いて編集する

1. **[ページ]**  >  **[About.cshtml.cs]** ファイルの順に移動します。

1. 次の NuGet パッケージをインストールします。
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. *About.cshtml.cs* ファイルに次のコードをインポートします。

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   AboutModel クラスのコードは次のようになります。

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Web アプリの実行

1. Visual Studio 2019 のメイン メニューで、 **[デバッグ]**  >  **[開始]** の順に選択します ("デバッグあり" と "デバッグなし" のどちらでもかまいません)。 
1. ブラウザーで **[バージョン情報]** ページに移動します。  
    **AppSecret** の値が表示されます。

## <a name="enable-a-managed-identity"></a>マネージド ID の有効化

Azure Key Vault は、資格情報やその他のシークレットを安全に保管する方法を提供しますが、コードで Key Vault に対して認証を行い、それらを取得する必要があります。 [Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページは、Azure AD で自動的に管理されているマネージド ID を Azure サービスに付与することで、この問題を解決するうえで役立ちます。 この ID を使用して、コードに資格情報が表示されていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

Azure CLI でこのアプリケーションの ID を作成するには、assign-identity コマンドを実行します。

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

\<YourAppName> は、Azure 上の発行済みアプリの名前に置き換えます。  
    たとえば、その発行済みアプリの名前が **MyAwesomeapp.azurewebsites.net** である場合は、\<YourAppName> を **MyAwesomeapp** に置き換えます。

アプリケーションを Azure に発行するときの `PrincipalId` をメモします。 手順 1. で実行したコマンドの出力形式は次のとおりです。

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>この手順のコマンドは、[Azure portal](https://portal.azure.com) に移動して、Web アプリケーション プロパティの **[ID] / [システム割り当て済み]** を **[オン]** に切り替えることと同等です。

## <a name="assign-permissions-to-your-app"></a>アプリにアクセス許可を割り当てる

次のコマンドの \<YourKeyVaultName> は実際のキー コンテナーの名前に、\<PrincipalId> は **PrincipalId** の値に置き換えてください。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

このコマンドでは、App Service の ID (MSI) に、キー コンテナーに対する **get** および **list** 操作を行うためのアクセス許可を付与しています。

## <a name="publish-the-web-app-to-azure"></a>Web アプリを Azure に発行する

ライブ Web アプリからシークレット値をフェッチできることを確認するために、もう一度 Web アプリを Azure に発行します。

1. Visual Studio で、**key-vault-dotnet-core-quickstart** プロジェクトを選択します。
2. **[発行]**  >  **[開始]** の順に選択します。
3. **作成** を選択します。

アプリケーションを実行すると、シークレット値を取得できることが確認できます。

これで、シークレットを保存してキー コンテナーから取得する Web アプリを .NET で正常に作成できました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、仮想マシンとキー コンテナーを削除できます。

## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[Azure Key Vault 開発者ガイド](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
