---
title: チュートリアル - Python で Windows 仮想マシンを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように ASP.NET Core アプリケーションを構成します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: cdc540f2f6fa834a97c4c405276414f29672e5c7
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876666"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-python"></a>チュートリアル:Python で Windows 仮想マシンを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、コンソール アプリケーションで Azure Key Vault から情報を読み取る方法について学習します。 そのためには、Azure リソースのマネージド ID を使用します。 

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * キー コンテナーにシークレットを追加します。
> * キー コンテナーからシークレットを取得する。
> * Azure 仮想マシンを作成します。
> * マネージド ID を有効化します。
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

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 

リソース グループ名を選択し、プレース ホルダーを入力します。 次の例では、米国西部の場所にリソース グループを作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

新規に作成したリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

前の手順で作成したリソース グループにキー コンテナーを作成するには、次の情報を指定します。

* キー コンテナー名: 数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列
* リソース グループ名
* 場所: **[米国西部]**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
この時点で、使用している Azure アカウントのみが、この新しいキー コンテナーに対して操作を実行することを許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 シークレットとしては、SQL 接続文字列など、安全性と、アプリケーションから利用できる状態の両方を維持するために必要な情報が考えられます。

**AppSecret** というキー コンテナーにシークレットを作成するには、次のコマンドを入力します。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

このシークレットには、**MySecret** という値が格納されます。

## <a name="create-a-virtual-machine"></a>仮想マシンの作成
次のいずれかの方法を使用して、仮想マシンを作成できます。

* [Azure CLI](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli)
* [PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)
* [Azure ポータル](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

## <a name="assign-an-identity-to-the-vm"></a>VM に ID を割り当てる
この手順では、Azure CLI で次のコマンドを実行して、仮想マシンに対するシステム割り当て ID を作成します。

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

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID にアクセス許可を割り当てる
ここで、次のコマンドを実行して、以前に作成した ID アクセス許可をキー コンテナーに割り当てることができます。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>仮想マシンへのログオン

仮想マシンにログオンするには、[Windows が実行されている Azure 仮想マシンへの接続とサインオン](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)に関する記事の手順に従ってください。

## <a name="create-and-run-a-sample-python-app"></a>サンプルの Python アプリを作成して実行する

次のセクションに、*Sample.py* という名前のファイルの例があります。 ここでは、HTTP GET 呼び出しを行うために[要求](http://docs.python-requests.org/en/master/)ライブラリが使用されます。

## <a name="edit-samplepy"></a>Sample.py を編集する

*Sample.py* の作成後、ファイルを開いてこのセクションのコードをコピーします。 

このコードは、2 段階のプロセスを表します。
1. VM 上のローカル MSI エンドポイントからトークンをフェッチします。  
  それにより、Azure AD からもトークンがフェッチされます。
1. トークンをキー コンテナーに渡し、シークレットをフェッチします。 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

次のコードを実行して、シークレット値を表示できます。 

```
python Sample.py
```

前出のコードは、Windows 仮想マシンで Azure Key Vault を操作する方法を示しています。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、仮想マシンとキー コンテナーを削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
