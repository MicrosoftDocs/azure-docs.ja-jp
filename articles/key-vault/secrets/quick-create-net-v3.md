---
title: クイックスタート - .NET 用 Azure Key Vault クライアント ライブラリ (SDK v3)
description: .NET クライアント ライブラリ (v3) を使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078872"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>クイック スタート:.NET 用 Azure Key Vault クライアント ライブラリ (SDK v3)

.NET 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

> [!NOTE]
> このクイックスタートでは、3.0.4 バージョンの Microsoft.Azure.KeyVault クライアント ライブラリを使用します。 最新バージョンの Key Vault クライアント ライブラリを使用するには、[.NET 用 Azure Key Vault クライアント ライブラリ (SDK v4)](quick-create-net.md) に関するページを参照してください。 

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 .NET 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- TLS または SSL 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API リファレンスのドキュメント](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> 各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。


## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.Net Core 3.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。

このクイックスタートでは、`dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)、Windows のコマンドを Windows Terminal ([PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)、[Azure Cloud Shell](https://shell.azure.com/) など) で実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="create-new-net-console-app"></a>新しい .NET コンソール アプリを作成する

コンソール ウィンドウで、`dotnet new` コマンドを使用し、`akv-dotnet` という名前で新しい .NET コンソール アプリを作成します。


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

このクイックスタートでは、次のパッケージもインストールする必要があります。

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

## <a name="object-model"></a>オブジェクト モデル

.NET 用 Azure Key Vault クライアント ライブラリを使用すると、キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルでは、シークレットの設定やシークレットの取得に関する方法を紹介しています。

コンソール アプリ全体は https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet から入手できます。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>キー コンテナーに対する認証

この .NET クイックスタートでは、環境変数を使用して、コードに記述すべきでない資格情報を格納します。 

アプリを作成して実行する前に、`setx` コマンドを使用して、`akvClientId`、`akvClientSecret`、`akvTenantId`、`akvSubscriptionId` の各環境変数を、先ほど書き留めた値に設定します。

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

これらの環境変数をコード内の文字列に代入した後、それらを [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)に渡してアプリケーションを認証します。

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、[SetSecretAsync メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)を使用して、キー コンテナーにシークレットを設定できます。これには、キー コンテナーの URL (`https://<your-unique-keyvault-name>.vault.azure.net/secrets/` 形式) が必要です。 また、シークレットの名前も必要です。ここでは "mySecret" という名前を使用します。 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[GetSecretAsync メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)を使用して取得できます。

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

これで、シークレットが `keyvaultSecret.Value;` として保存されました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 [コンソール アプリ全体は GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet) で確認してください。

Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [.NET を使用した Azure Key Vault に対するサービス間認証](../general/service-to-service-authentication.md)を実装する
- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する
