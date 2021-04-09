---
title: クイックスタート - .NET 用 Azure Key Vault シークレット クライアント ライブラリ (バージョン 4)
description: .NET クライアント ライブラリ (バージョン 4) を使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a82c2cdf7084b31eb6ba861e48ecffb81e6d1363
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102453701"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>クイックスタート: .NET 用 Azure Key Vault シークレット クライアント ライブラリ (SDK v4)

.NET 用 Azure Key Vault シークレットクライアント ライブラリを使ってみます。 [Azure Key Vault](../general/overview.md) は、シークレットのセキュリティで保護されたストアを提供するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このクイックスタートでは、.NET クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します

Key Vault クライアント ライブラリのリソースは、次のとおりです。

[API リファレンスのドキュメント](/dotnet/api/azure.security.keyvault.secrets) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Key Vault とシークレットの詳細については、以下を参照してください。
- [Key Vault の概要](../general/overview.md)
- [シークレットの概要](about-secrets.md)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* キー コンテナー - [Azure portal](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md)、または [Azure PowerShell](../general/quick-create-powershell.md) を使用して作成できます

このクイックスタートでは、`dotnet` および Azure CLI を使用します

## <a name="setup"></a>セットアップ

このクイックスタートでは、Azure CLI と Azure Identity ライブラリを使用して、Azure サービスに対するユーザーの認証を行います。 開発者は、Visual Studio または Visual Studio Code を使用してその呼び出しを認証することもできます。詳細については、[Azure Identity クライアント ライブラリを使用してクライアントを認証する](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)方法に関するページを参照してください。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. `login` コマンドを実行します。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

    それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

2. ブラウザーでアカウントの資格情報を使用してサインインします。

### <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

自分のユーザー アカウントにシークレットのアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

### <a name="create-new-net-console-app"></a>新しい .NET コンソール アプリを作成する

1. コマンド シェルで、次のコマンドを実行して、`key-vault-console-app` という名前のプロジェクトを作成します。

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. 新しく作成した *key-vault-console-app* ディレクトリに移動し、次のコマンドを実行してプロジェクトをビルドします。

    ```dotnetcli
    dotnet build
    ```

    ビルドの出力に警告やエラーが含まれないようにする必要があります。
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>パッケージのインストール

コマンド シェルから、.NET 用 Azure Key Vault シークレット クライアント ライブラリをインストールします。

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

このクイックスタートでは、Azure Identity 用 Azure SDK クライアント ライブラリもインストールする必要があります。

```dotnetcli
dotnet add package Azure.Identity
```
#### <a name="set-environment-variables"></a>環境変数の設定

このアプリケーションでは、`KEY_VAULT_NAME` という環境変数にキー コンテナーの名前を使用します。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS または Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>オブジェクト モデル

シークレットは、.NET 用 Azure Key Vault シークレット クライアント ライブラリを使用して管理できます。 クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を「[コード例](#code-examples)」セクションで紹介しています。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

*Program.cs* の先頭に次のディレクティブを追加します。

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、[Azure ID ライブラリ](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)の ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 キー コンテナーに対する認証について詳しくは、[開発者ガイド](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)を参照してください。

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>シークレットを保存する

コンソール アプリの認証が済んだら、キー コンテナーにシークレットを追加します。 このタスクには、[SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) メソッドを使用します。 このメソッドの第 1 パラメーターには、シークレットの名前 (このサンプルでは "mySecret") を指定します。

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> シークレット名が存在する場合は、上のコードによって、そのシークレットの新しいバージョンが作成されます。


### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) メソッドを使用して取得できます。

```csharp
var secret = await client.GetSecretAsync(secretName);
```

これで、シークレットが `secret.Value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) メソッドと [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient) メソッドを使用して、キー コンテナーからシークレットを削除してみましょう。

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>サンプル コード

Key Vault を対話的に操作するために、次の手順を完了して .NET Core コンソール アプリに変更を加えます。

1. *Program.cs* のコードを次のコードに置き換えます。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>テストして検証する

1. 次のコマンドを実行して、アプリを実行します。

    ```dotnetcli
    dotnet run
    ```

1. メッセージが表示されたら、シークレットの値を入力します (例: mySecretPassword)。

次のような出力が表示されます。

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>次のステップ

Key Vault の詳細およびアプリとの統合方法の詳細については、以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [App Service アプリケーションから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-create-vault-azure-web-app.md)を参照する
- [仮想マシンから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-virtual-machine.md)を参照する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
