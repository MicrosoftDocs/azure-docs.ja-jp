---
title: クイックスタート -  .NET 用 Azure Key Vault クライアント ライブラリ (v4)
description: .NET クライアント ライブラリ (v4) を使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cf9c34dc2dde5c41edb7991451aebf5d90104196
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003254"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>クイック スタート:.NET 用 Azure Key Vault クライアント ライブラリ (SDK v4)

.NET 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 .NET 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- TLS または SSL 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API リファレンスのドキュメント](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.Net Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。

このクイックスタートでは、`dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)、Windows のコマンドを Windows Terminal ([PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)、[Azure Cloud Shell](https://shell.azure.com/) など) で実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="create-new-net-console-app"></a>新しい .NET コンソール アプリを作成する

コンソール ウィンドウで、`dotnet new` コマンドを使用し、`key-vault-console-app` という名前で新しい .NET コンソール アプリを作成します。

```console
dotnet new console -n key-vault-console-app
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>パッケージをインストールする

コンソール ウィンドウから、.NET 用 Azure Key Vault クライアント ライブラリをインストールします。

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

このクイックスタートでは、次のパッケージもインストールする必要があります。

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>環境変数の設定

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>オブジェクト モデル

.NET 用 Azure Key Vault クライアント ライブラリを使用すると、キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

コンソール アプリ全体は https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app から入手できます。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

キー コンテナーに対する認証とキー コンテナー クライアントの作成は、上記の「[環境変数の設定](#set-environmental-variables)」の手順にある環境変数によって異なります。 キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 下のコードではキー コンテナーの認証に [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) が使用されています。環境変数が読み取られ、アクセス トークンを取得されます。 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、[client.SetSecret メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)を使用して、キー コンテナーにシークレットを設定できます。これには、シークレットの名前が必要です (この例では、"mySecret" を使用します)。  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[client.GetSecret メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)を使用して取得できます。

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

これで、シークレットが `secret.Value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[client.DeleteSecret メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)を使用して、キー コンテナーからシークレットを削除してみましょう。

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して、シークレットがなくなったことを確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

### <a name="delete-a-key-vault"></a>Key Vault の削除
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Key Vault の消去
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>リソース グループの削除

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>サンプル コード

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

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
}
```


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 [コンソール アプリ全体は GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app) で確認してください。

Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する
