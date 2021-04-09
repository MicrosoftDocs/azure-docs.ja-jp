---
title: クイックスタート - .NET 用 Azure Key Vault キー クライアント ライブラリ (バージョン 4)
description: .NET クライアント ライブラリ (バージョン 4) を使用して Azure キー コンテナーからキーを作成、取得、および削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cecf8330b7060a4cbc4691f64571a3c7865c575c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935259"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>クイックスタート: .NET 用 Azure Key Vault キー クライアント ライブラリ (SDK v4)

.NET 用 Azure Key Vault キー クライアント ライブラリを使ってみます。 [Azure Key Vault](../general/overview.md) は、暗号化キーのセキュリティで保護されたストアを提供するクラウド サービスです。 暗号化キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このクイックスタートでは、.NET キー クライアント ライブラリを使用して Azure キー コンテナーからキーを作成、取得、および削除する方法について説明します

Key Vault キー クライアント ライブラリのリソースは、次のとおりです。

[API リファレンスのドキュメント](/dotnet/api/azure.security.keyvault.keys) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Key Vault とキーの詳細については、以下を参照してください。
- [Key Vault の概要](../general/overview.md)
- [キーの概要](about-keys.md)。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* キー コンテナー - [Azure portal](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md)、または [Azure PowerShell](../general/quick-create-powershell.md) を使用して作成できます。

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

#### <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

自分のユーザー アカウントにキーのアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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

コマンド シェルから、.NET 用 Azure Key Vault キー クライアント ライブラリをインストールします。

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
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

キーは、.NET 用 Azure Key Vault キー クライアント ライブラリを使用して管理できます。 「[コード例](#code-examples)」セクションでは、クライアントの作成、キーの設定、キーの取得、およびキーの削除方法を示しています。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

*Program.cs* の先頭に次のディレクティブを追加します。

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、[Azure ID ライブラリ](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)の ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 キー コンテナーに対する認証について詳しくは、[開発者ガイド](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)を参照してください。

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>キーの保存

このタスクには、[CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) メソッドを使用します。 このメソッドは、パラメーターで、キー名と[キーの種類](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype)を受け取ります。

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> キー名が存在する場合は、上のコードによって、そのキーの新しいバージョンが作成されます。

### <a name="retrieve-a-key"></a>キーの取得

先ほど作成したキーを、[GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync) メソッドを使用して取得できるようになりました。

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>キーの削除

最後に、[StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) メソッドと [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync) メソッドを使用して、キー コンテナーからキーを削除して消去します。

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>サンプル コード

Key Vault を対話的に操作するために、次の手順を完了して .NET Core コンソール アプリに変更を加えます。

- *Program.cs* のコードを次のコードに置き換えます。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>テストして検証する

1.  次のコマンドを実行して、プロジェクトをビルドします

    ```dotnetcli
    dotnet build
    ```

1. 次のコマンドを実行して、アプリを実行します。

    ```dotnetcli
    dotnet run
    ```

1. メッセージが表示されたら、シークレットの値を入力します (例: mySecretPassword)。

    次のような出力が表示されます。

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、キーを格納して、そのキーを取得しました。 

Key Vault の詳細およびアプリとの統合方法の詳細については、以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [キーの概要](about-keys.md)を読む
- [App Service アプリケーションから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-create-vault-azure-web-app.md)を参照する
- [仮想マシンから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-virtual-machine.md)を参照する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
