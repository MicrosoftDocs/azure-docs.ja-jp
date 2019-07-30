---
title: チュートリアル - Linux 仮想マシンと ASP.NET コンソール アプリケーションを使用してシークレットを Azure Key Vault に格納する | Microsoft Docs
description: このチュートリアルでは、Azure Key Vault からシークレットを読み取るよう ASP.NET Core アプリケーションを構成する方法について説明します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8c142b4c1663a7ccd462836e9aac4ee80accaae4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874829"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>チュートリアル:Linux VM と .NET アプリを使用してシークレットを Azure Key Vault に格納する

Azure Key Vault は、お使いのアプリケーション、サービス、IT リソースへのアクセスに必要なシークレット (API キーやデータベース接続文字列など) を保護するのに役立ちます。

このチュートリアルでは、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るよう .NET コンソール アプリケーションを設定します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault を作成します
> * Key Vault にシークレットを格納する
> * Azure Linux 仮想マシンを作成する
> * 仮想マシンに対して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする
> * コンソール アプリケーションが Key Vault のデータを読み取るために必要なアクセス許可を付与する
> * Key Vault からシークレットを取得する

先に進む前に、[Key Vault の基本概念](key-vault-whatis.md#basic-concepts)について確認してください。

## <a name="prerequisites"></a>前提条件

* [Git](https://git-scm.com/downloads)。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* [Azure CLI 2.0 以降](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)または Azure Cloud Shell。

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>マネージド サービス ID について

Azure Key Vault を使用すると、資格情報を安全に保存することができます。したがって、コードには資格情報を含めませんが、資格情報を取得するために Azure Key Vault に対して認証する必要があります。 しかし、Key Vault に対して認証するには資格情報が必要になります。 これは、古典的なブートストラップ問題です。 マネージド サービス ID (MSI) は、Azure と Azure Active Directory (Azure AD) を使用してブートストラップ ID を提供できます。これにより、物事をさらに簡単に始められるようになります。

Azure サービス (Virtual Machines、App Service、Functions など) に対して MSI を有効にすると、そのサービスのインスタンス用のサービス プリンシパルが Azure Active Directory に作成されます。 サービスのインスタンスには、サービス プリンシパルの資格情報が挿入されます。

![MSI](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。
コードは、ローカルの MSI_ENDPOINT から取得したアクセス トークンを使用して、Azure Key Vault サービスに対して認証します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

`az group create` コマンドを使用してリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

米国西部の場所にリソース グループを作成します。 リソース グループの名前を選択し、次の例にある `YourResourceGroupName` を置き換えます。

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

このリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、リソース グループにキー コンテナーを作成します。 次の情報を指定します。

* キー コンテナー名: 数字、文字、ハイフン (0-9、a-z、A-Z、\-) のみを含んだ 3 から 24 文字の文字列。
* リソース グループ名
* 場所: **[米国西部]**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

この時点では、自分の Azure アカウントのみに、この新しいコンテナーで任意の操作を実行する権限があります。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

ここで、シークレットを追加します。 実際のシナリオでは、安全に保持しながらもアプリケーションで利用できるようにしなければならない SQL 接続文字列やその他の情報を保存している場合があります。

このチュートリアルでは、次のコマンドを入力してこのキー コンテナーにシークレットを作成します。 このシークレットには **AppSecret** という名前を付け、その値は **MySecret** にします。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linux 仮想マシンの作成

`az vm create` コマンドを使用して VM を作成します。

次の例では、**myVM** という名前の VM を作成し、**azureuser** という名前のユーザー アカウントを追加します。 `--generate-ssh-keys` パラメーターは、SSH キーを自動的に生成して既定のキーの場所 ( **~/.ssh**) に配置するために使用されます。 代わりに特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例では、VM 作成操作が成功したことを示します。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

作成した VM からの出力に含まれる実際の `publicIpAddress` を書き留めておいてください。 このアドレスは、後の手順で VM にアクセスするために使用します。

## <a name="assign-an-identity-to-the-vm"></a>VM に ID を割り当てる

次のコマンドを実行して、仮想マシンに対するシステム割り当て ID を作成します。

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

このコマンドの出力は次のようになります。

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

`systemAssignedIdentity` を書き留めておきます。 これは、次の手順で使用します。

## <a name="give-the-vm-identity-permission-to-key-vault"></a>VM ID に Key Vault へのアクセス許可を付与する

これで、作成した ID に Key Vault へのアクセス許可を付与することができます。 次のコマンドを実行します。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM にログインする

ターミナルを使用して、仮想マシンにログインします。

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Linux に .NET Core をインストールする

Linux VM では、以下を実行します。

次のコマンドを実行して、Microsoft プロダクト キーを信頼済みとして登録します。

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

オペレーティング システムに基づいて、目的のバージョンのホスト パッケージのフィードを設定します。

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

.NET をインストールしてバージョンを確認します。

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>サンプルの .NET アプリを作成して実行する

次のコマンドを実行します。 "Hello World" がコンソールに出力されることがわかります。

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>コンソール アプリを編集してシークレットをフェッチする

Program.cs ファイルを開き、次のパッケージを追加します。

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

アプリがキー コンテナー内のシークレットにアクセスできるようにクラス ファイルを変更する 2 段階のプロセスを次に示します。

1. VM 上のローカル MSI エンドポイントからトークンをフェッチし、次にその VM が Azure Active Directory からトークンをフェッチします。
1. そのトークンを Key Vault に渡し、シークレットをフェッチします。

   クラス ファイルを編集して次のコードを含めます。

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

これで、Azure Linux 仮想マシンで実行されている .NET アプリケーションで Azure Key Vault に対する操作を実行する方法を習得できました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループと仮想マシン、関連するすべてのリソースは、不要になったら削除してください。 そのためには、VM のリソース グループを選択し、 **[削除]** を選択します。

`az keyvault delete` コマンドを使用して、キー コンテナーを削除します。

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
