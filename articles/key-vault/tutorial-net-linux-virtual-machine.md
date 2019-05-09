---
title: チュートリアル - .NET で Azure Linux 仮想マシンを使用して Azure Key Vault を使用する方法 - Azure Key Vault | Microsoft Docs
description: 'チュートリアル: キー コンテナーからシークレットを読み取るように ASP.Net Core アプリケーションを構成する'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 354ffb14eb95e7f09256f337ffea069cff14b226
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306563"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-net"></a>チュートリアル:.NET で Azure Linux 仮想マシンを使用して Azure Key Vault を使用する方法

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るようにコンソール アプリケーションを設定するために必要な手順を学習します。 ここでは、次の操作を行う方法について学習します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを格納する。
> * キー コンテナーからシークレットを取得する。
> * Azure 仮想マシンを作成する。
> * 仮想マシンに対して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする。
> * キー コンテナーからデータを読み取るために必要な権限をコンソール アプリケーションに付与する。
> * キー コンテナーからシークレットを取得する

先に進む前に、[基本概念](key-vault-whatis.md#basic-concepts)をご確認ください。

## <a name="prerequisites"></a>前提条件
* すべてのプラットフォーム:
  * Git ([ダウンロード](https://git-scm.com/downloads))。
  * Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) バージョン 2.0.4 以降。 これは、Windows、Mac、Linux に対応しています。

このチュートリアルでは、マネージド サービス ID を使用します

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>マネージド サービス ID とは何か、およびそのしくみ

本題に入る前に、MSI について理解しましょう。 Azure Key Vault を使用すると、資格情報を安全に保存することができます。したがって、コードには資格情報を含めませんが、資格情報を取得するために Azure Key Vault に対して認証する必要があります。 そして、Key Vault に対して認証するには資格情報が必要になります。 これは、古典的なブートストラップ問題です。 MSI は、Azure と Azure AD を利用して "ブートストラップ ID" を提供します。これにより、物事がはるかに簡単になります。

そのしくみについて説明しましょう。 Virtual Machines、App Service、Functions などの Azure サービスに対して MSI を有効にすると、Azure によってサービス インスタンスの[サービス プリンシパル](key-vault-whatis.md#basic-concepts)が Azure Active Directory に作成され、サービス プリンシパルの資格情報がサービスのインスタンスに挿入されます。 

![MSI](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。
コードは、ローカルの MSI_ENDPOINT から取得したアクセス トークンを使用して、Azure Key Vault サービスに対して認証します。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力します。
次の例では、米国西部の場所にリソース グループを作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

作成したリソース グループは、この記事の全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 次の情報を指定します。

* キー コンテナー名:名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、および - のみです。
* リソース グループ名。
* 場所:**米国西部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。

次のコマンドを入力して、**AppSecret** というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm) コマンドで VM を作成します。

次の例では、*myVM* という名前の VM を作成し、*azureuser* という名前のユーザー アカウントを追加します。 SSH キーを自動的に生成するために `--generate-ssh-keys` パラメーターが使用され、キーは既定のキーの場所 (*~/.ssh*) に配置されます。 代わりに特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例では、成功した VM 作成操作を示します。

```
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

作成した VM からの出力に含まれる実際の `publicIpAddress` を記録しておいてください。 このアドレスは、次の手順で VM にアクセスするために使います。

## <a name="assign-identity-to-virtual-machine"></a>仮想マシンに ID を割り当てる

この手順では、次のコマンドを実行して、仮想マシンに対するシステム割り当て ID を作成しています

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

次に示す systemAssignedIdentity に注目してください。 上記のコマンドの出力は次のようになります 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Key Vault に VM ID のアクセス許可を付与する

ここで、次のコマンドを実行して、上記の手順で作成した ID のアクセス許可を Key Vault に付与することができます

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>仮想マシンへのサインイン

ここで、ターミナルを使用して仮想マシンにサインインします

```
ssh azureuser@<PublicIpAddress>
```

## <a name="install-dot-net-core-on-linux"></a>Linux に .NET Core をインストールする

### <a name="register-the-microsoft-product-key-as-trusted-run-the-following-two-commands"></a>Microsoft プロダクト キーを信頼済みとして登録します。 次の 2 つのコマンドを実行します

```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```

### <a name="set-up-desired-version-host-package-feed-based-on-operating-system"></a>オペレーティング システムに基づいて、目的のバージョンのホスト パッケージのフィードを設定します

```
 
# Ubuntu 16.04 / Linux Mint 18
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
 
# Ubuntu 14.04 / Linux Mint 17
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
```

### <a name="install-net-core"></a>.NET Core のインストール

.NET のバージョンを確認します

```
sudo apt-get install dotnet-sdk-2.1.4
dotnet --version
```

## <a name="create-and-run-sample-dot-net-app"></a>サンプルの .NET アプリを作成して実行する

次のコマンドを実行すると、"Hello World" がコンソールに出力されます

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>コンソール アプリを編集する

Program.cs ファイルを開き、次のパッケージを追加します
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

クラス ファイルを変更して、次のコードを含めます。 これは 2 段階のプロセスです。

1. VM 上のローカル MSI エンドポイントからトークンをフェッチし、次にその VM が Azure Active Directory からトークンをフェッチします
2. トークンを Key Vault に渡し、シークレットを取得します 

```
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

上記のコードは、Azure Linux 仮想マシンで Azure Key Vault を操作する方法を示しています。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
