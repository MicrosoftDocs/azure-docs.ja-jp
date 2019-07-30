---
title: チュートリアル - .NET で Windows 仮想マシンを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションを構成します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 3bb4647b39a276e2dd54260c17eca1d700d5ba16
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874981"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>チュートリアル:.NET で Windows 仮想マシンを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、コンソール アプリケーションで Azure Key Vault から情報を読み取る方法について学習します。 そのためには、Azure リソースのマネージド ID を使用します。 

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * リソース グループを作成します。
> * Key Vault を作成します。
> * キー コンテナーにシークレットを追加します。
> * キー コンテナーからシークレットを取得する。
> * Azure 仮想マシンを作成します。
> * 仮想マシンに対して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする。
> * VM ID にアクセス許可を割り当てます。

始める前に、[Key Vault の基本的な概念](key-vault-whatis.md#basic-concepts)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

Windows、Mac、Linux:
  * [Git](https://git-scm.com/downloads)
  * このチュートリアルでは、Azure CLI をローカルで実行する必要があります。 Azure CLI バージョン 2.0.4 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://review.docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="about-managed-service-identity"></a>マネージド サービス ID について

Azure Key Vault では資格情報が安全に格納されます。そのため資格情報はコードに表示されません。 ただし、キーを取得するためには Azure Key Vault に対して認証を行う必要があります。 Key Vault に対して認証を行うには、資格情報が必要となります。 これは従来からあるブートストラップ問題のジレンマです。 マネージド サービス ID (MSI) は、このプロセスを簡素化する "_ブートストラップ ID_" を提供することによって、この問題を解決します。

Azure サービス (Azure Virtual Machines、Azure App Service、Azure Functions など) に対して MSI を有効にすると、Azure によって[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が作成されます。 MSI によってこれが Azure Active Directory (Azure AD) 内でサービスのインスタンスに対して行われ、サービス プリンシパルの資格情報がそのインスタンスに挿入されます。 

![MSI](media/MSI.png)

次に、アクセス トークンを取得するために、Azure リソース上で利用可能なローカル メタデータ サービスがユーザーのコードによって呼び出されます。 Azure Key Vault サービスを認証するために、コードではローカルの MSI エンドポイントから取得したアクセス トークンを使用します。 

## <a name="create-resources-and-assign-permissions"></a>リソースを作成してアクセス許可を割り当てる

コーディングを開始する前に、いくつかのリソースを作成し、キー コンテナーにシークレットを配置し、アクセス許可を割り当てる必要があります。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 [az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 

この例では、米国西部にリソース グループが作成されます。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

このチュートリアル全体で、新規に作成したリソース グループを使用します。

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>キー コンテナーを作成してシークレットを設定する

[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) コマンドで以下の情報を指定することで、リソース グループ内にキー コンテナーを作成します。

* キー コンテナー名: 数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列
* リソース グループ名
* 場所: **[米国西部]**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
この時点で、使用している Azure アカウントのみが、この新しいキー コンテナーに対して操作を実行することを許可されます。

ここで、[az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) コマンドを使用して、シークレットをキー コンテナーに追加します。


**AppSecret** というキー コンテナーにシークレットを作成するには、次のコマンドを入力します。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

このシークレットには、**MySecret** という値が格納されます。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成
次のいずれかの方法を使用して、仮想マシンを作成します。

* [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure ポータル](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

### <a name="assign-an-identity-to-the-vm"></a>VM に ID を割り当てる
[az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) コマンドを使用して、仮想マシン用のシステムによって割り当てられる ID を作成します。

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

システムによって割り当てられた ID が次のコードに表示されていることにご注意ください。 上記のコマンドの出力は次のようになります。 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>VM ID にアクセス許可を割り当てる
[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドを実行して、前に作成した ID アクセス許可をキー コンテナーに割り当てます。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>仮想マシンにサインインする

仮想マシンにサインインするには、「[Windows が実行されている Azure 仮想マシンに接続してサインインする](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)」の手順に従ってください。

## <a name="set-up-the-console-app"></a>コンソール アプリを設定する

コンソール アプリを作成し、`dotnet` コマンドを使用して必要なライブラリをインストールします。

### <a name="install-net-core"></a>.NET Core のインストール

.NET Core をインストールするには、[.NET のダウンロード](https://www.microsoft.com/net/download) ページに移動します。

### <a name="create-and-run-a-sample-net-app"></a>サンプルの .NET アプリを作成して実行する

コマンド プロンプトを開きます。

次のコマンドを実行して、コンソールに "Hello World" と出力できます。

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>パッケージのインストール

 コンソール ウィンドウで、このクイックスタートで必要な .NET パッケージをインストールします。

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>コンソール アプリを編集する

*Program.cs* ファイルを開き、次のパッケージを追加します。

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

次の 2 段階プロセスで、コードを含めるクラス ファイルを編集します。

1. VM 上のローカル MSI エンドポイントからトークンをフェッチします。 それにより、Azure AD からもトークンがフェッチされます。
1. トークンをキー コンテナーに渡し、シークレットをフェッチします。 

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

前出のコードは、Windows 仮想マシンで Azure Key Vault を操作する方法を示しています。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、仮想マシンとキー コンテナーを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
