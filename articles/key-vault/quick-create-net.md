---
title: クイックスタート -  .NET 用 Azure Key Vault クライアント ライブラリ
description: Azure SDK クライアント ライブラリのクイックスタートを作成するための形式とコンテンツの基準を示します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: d24323996e222caf6456372cbc65681d2055c3db
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996638"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>クイック スタート:.NET 用 Azure Key Vault クライアント ライブラリ

.NET 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 .NET 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- SSL または TLS 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API リファレンスのドキュメント](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.Net Core 2.1 SDK 以降](https://dotnet.microsoft.com/download/dotnet-core/2.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/overview)。

このクイックスタートでは、`dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)、Windows のコマンドを Windows ターミナル ([PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)、[Azure Cloud Shell](https://shell.azure.com/) など) で実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="create-new-net-console-app"></a>新しい .NET コンソール アプリを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。

コンソール ウィンドウで、`dotnet new` コマンドを使用し、`akv-dotnet` という名前で新しいコンソール アプリを作成します。


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

このクイックスタートでは、あらかじめ作成しておいた Azure キー コンテナーを使用します。 キー コンテナーは、[Azure CLI のクイックスタート](quick-create-cli.md)、[Azure PowerShell のクイックスタート](quick-create-powershell.md)、または [Azure portal のクイックスタート](quick-create-portal.md)の手順に従って作成できます。 または、以下の Azure CLI コマンドを実行するだけでもかまいません。

> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例では、*myKV* という名前のキー コンテナーを作成しますが、ご自身で別の名前を付けて、その名前をこのクイックスタート全体で使用する必要があります。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

クラウドベースの .NET アプリケーションを認証するための最も簡単な方法は、マネージド ID を使用することです。詳細については、「[.NET を使用した Azure Key Vault に対するサービス間認証](service-to-service-authentication.md)」を参照してください。 ただし、わかりやすくするために、このクイックスタートでは .NET コンソール アプリケーションを作成します。 Azure でデスクトップ アプリケーションを認証するには、サービス プリンシパルを使用する必要があります。
Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパルを作成します。

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

この操作では、一連のキーと値のペアが返されます。 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

clientId および clientSecret を書き留めておきます。これらは、下記の手順「[キー コンテナーに対する認証](#authenticate-to-your-key-vault)」で使用します。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドに clientId を渡すことでご利用のサービス プリンシパルにアクセス許可を付与するアクセス ポリシーをご利用のキー コンテナー用に作成します。 キーとシークレットの両方に対する get、list、set の各アクセス許可をサービス プリンシパルに付与します。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

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

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

`setx` を呼び出すたびに、"成功: 指定した値は保存されました。" という応答が返されます。

これらの環境変数をコード内の文字列に代入した後、それらを [KeyVaultClient クラス](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)に渡してアプリケーションを認証します。

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、[SetSecretAsync メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)を使用して、キー コンテナーにシークレットを設定できます。これには、キー コンテナーの URL (`https://<your-unique-keyvault-name>.vault.azure.net/secrets/` 形式) が必要です。 また、シークレットの名前も必要です。ここでは "mySecret" という名前を使用します。  再利用のために、これらの文字列を変数に代入してもかまいません。

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[GetSecretAsync メソッド](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)を使用して取得できます。

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

これで、シークレットが `keyvaultSecret.Value;` として保存されました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 [コンソール アプリ全体は GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet) で確認してください。

Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [.NET を使用した Azure Key Vault に対するサービス間認証](service-to-service-authentication.md)を実装する
- [Azure Key Vault の概要](key-vault-overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照する
- [キー、シークレット、証明書](about-keys-secrets-and-certificates.md)について学習する
- [Azure Key Vault のベスト プラクティス](key-vault-best-practices.md)を確認する
