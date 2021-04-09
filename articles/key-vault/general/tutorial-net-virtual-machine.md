---
title: チュートリアル - .NET で仮想マシンを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように 仮想マシン ASP.NET Core アプリケーションを構成します。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a56c08e5bf6054d24af3ade571ec625969286a77
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455646"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>チュートリアル:.NET で仮想マシンを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、コンソール アプリケーションで Azure Key Vault から情報を読み取る方法について学習します。 アプリケーションでは、Key Vault に対する認証を行うために、仮想マシンのマネージド ID を使用します。 

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * リソース グループを作成します。
> * Key Vault を作成します。
> * キー コンテナーにシークレットを追加します。
> * キー コンテナーからシークレットを取得する。
> * Azure 仮想マシンを作成します。
> * 仮想マシンに対して[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を有効にする。
> * VM ID にアクセス許可を割り当てます。

始める前に、[Key Vault の基本的な概念](basic-concepts.md)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

Windows、Mac、Linux:
  * [Git](https://git-scm.com/downloads)
  * [.Net Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
  * [Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-resources-and-assign-permissions"></a>リソースを作成してアクセス許可を割り当てる

コーディングを開始する前に、いくつかのリソースを作成し、キー コンテナーにシークレットを配置し、アクセス許可を割り当てる必要があります。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>キー コンテナーにシークレットを格納する

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
次のいずれかの方法を使用して、Windows または Linux 仮想マシンを作成します。

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>VM に ID を割り当てる
[az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign) コマンドを使用して、仮想マシン用のシステムによって割り当てられる ID を作成します。

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

システムによって割り当てられた ID が次のコードに表示されていることにご注意ください。 上記のコマンドの出力は次のようになります。 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID にアクセス許可を割り当てる
[az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) コマンドを実行して、前に作成した ID アクセス許可をキー コンテナーに割り当てます。

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>仮想マシンにサインインする

仮想マシンにサインインするには、[Azure Windows 仮想マシンへの接続とサインイン](../../virtual-machines/windows/connect-logon.md)に関するページまたは [Azure Linux 仮想マシンへの接続とサインイン](../../virtual-machines/linux/login-using-aad.md)に関するページの手順に従います。

## <a name="set-up-the-console-app"></a>コンソール アプリを設定する

コンソール アプリを作成し、`dotnet` コマンドを使用して必要なライブラリをインストールします。

### <a name="install-net-core"></a>.NET Core のインストール

.NET Core をインストールするには、[.NET のダウンロード](https://www.microsoft.com/net/download) ページに移動します。

### <a name="create-and-run-a-sample-net-app"></a>サンプルの .NET アプリを作成して実行する

コマンド プロンプトを開きます。

次のコマンドを実行して、コンソールに "Hello World" と出力できます。

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>パッケージをインストールする

コンソール ウィンドウから、.NET 用 Azure Key Vault シークレット クライアント ライブラリをインストールします。

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

このクイックスタートでは、Azure Key Vault に対する認証を行うために、次の ID パッケージをインストールする必要があります。

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>コンソール アプリを編集する

*Program.cs* ファイルを開き、次のパッケージを追加します。

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

これらの行を追加し、URI は実際のキー コンテナーの `vaultUri` に合わせて更新します。 以下のコードでは、キー コンテナーに対する認証に "[DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential)" が使用されています。この場合、アプリケーションのマネージド ID からのトークンが認証に使用されます。 また、キー コンテナーがスロットルされている場合の再試行にはエクスポネンシャル バックオフが使用されています。

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、仮想マシンとキー コンテナーを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](/rest/api/keyvault/)
