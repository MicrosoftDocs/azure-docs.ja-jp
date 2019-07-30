---
title: チュートリアル - Linux 仮想マシンと Python アプリケーションを使用してシークレットを Azure Key Vault に格納する | Microsoft Docs
description: このチュートリアルでは、Azure Key Vault からシークレットを読み取るように Python アプリケーションを構成する方法について学習します。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: f002a4d67ee782b11cd8f39784eb48b136328c89
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876702"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>チュートリアル:Linux VM と Python アプリを使用してシークレットを Azure Key Vault に格納する

Azure Key Vault は、API キーや、アプリケーション、サービス、IT リソースへのアクセスに必要なデータベース接続文字列などのシークレットを保護するのに役立ちます。

このチュートリアルでは、Azure リソースのマネージド ID を使用して Azure Key Vault から情報を読み取るように Azure Web アプリケーションを設定します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault を作成します
> * シークレットをキー コンテナーに格納する
> * Linux 仮想マシンの作成
> * 仮想マシンに対して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にする
> * キー コンテナーからデータを読み取るために必要な権限をコンソール アプリケーションに付与する
> * キー コンテナーからシークレットを取得する

先に進む前に、[Key Vault の基本的な概念](key-vault-whatis.md#basic-concepts)を理解しておいてください。

## <a name="prerequisites"></a>前提条件

* [Git](https://git-scm.com/downloads)。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
* [Azure CLI バージョン 2.0.4 以降](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)または Azure Cloud Shell。

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>マネージド サービス ID について

Azure Key Vault には、資格情報をコードに含めることなく安全に格納することができます。 それらを取得するためには、Azure Key Vault に対して認証を行う必要があります。 しかし、Key Vault に対して認証するには資格情報が必要になります。 これは、古典的なブートストラップ問題です。 マネージド サービス ID (MSI) は、Azure と Azure Active Directory (Azure AD) を通じてブートストラップ ID を提供します。これにより、物事が簡単になります。

Azure サービス (Virtual Machines、App Service、Functions など) に対して MSI を有効にすると、そのサービスのインスタンスに使用されるサービス プリンシパルが Azure AD に作成されます。 サービスのインスタンスには、サービス プリンシパルの資格情報が挿入されます。

![MSI](media/MSI.png)

次に、ユーザーのコードが、Azure リソース上で利用可能なローカル メタデータ サービスを呼び出してアクセス トークンを取得します。 そのコードは、Azure Key Vault サービスに対し、ローカルの MSI エンドポイントから取得したアクセス トークンを使用して認証を行います。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次のコードで `az group create` コマンドを使用して、米国西部の場所にリソース グループを作成します。 `YourResourceGroupName` を任意の名前に置き換えます。

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

このリソース グループは、このチュートリアルの全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、前の手順で作成したリソース グループにキー コンテナーを作成します。 次の情報を指定します。

* キー コンテナー名:名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、およびハイフン (-) のみです。
* リソース グループ名。
* 場所:**米国西部**。

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列や、安全性を保ちつつアプリケーションから利用できる状態にする必要があるその他の情報を格納したい場合があります。

次のコマンドを入力して、*AppSecret* というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linux 仮想マシンの作成

`az vm create` コマンドを使用して、VM を作成します。

次の例では、**myVM** という名前の VM を作成し、**azureuser** という名前のユーザー アカウントを追加します。 `--generate-ssh-keys` パラメーターによって SSH キーが自動的に生成され、キーは既定のキーの場所 ( **~/.ssh**) に配置されます。 代わりに特定のキーのセットを作成するには、`--ssh-key-value` オプションを使用します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM とサポートするリソースを作成するには数分かかります。 次の出力例は、成功した VM 作成操作を示します。

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

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

コマンドの出力結果は次のとおりです。

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

`systemAssignedIdentity` を書き留めておきます。 これは、次の手順で使用します。

## <a name="give-the-vm-identity-permission-to-key-vault"></a>VM ID に Key Vault へのアクセス許可を付与する

これで、作成した ID に Key Vault へのアクセス許可を付与することができます。 次のコマンドを実行します。

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM にログインする

ターミナルを使用して、仮想マシンにログインします。

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>VM に Python ライブラリをインストールする

HTTP GET 呼び出しを行うために、[requests](https://pypi.org/project/requests/2.7.0/) Python ライブラリをダウンロードしてインストールします。

## <a name="create-edit-and-run-the-sample-python-app"></a>サンプル Python アプリを作成、編集、および実行する

**Sample.py** という Python ファイルを作成します。

Sample.py を開き、次のコードが含まれるように編集します。

```python
# importing the requests library
  import requests
  
# Step 1: Fetch an access token from an MSI-enabled Azure resource      
  # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
  MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
  r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
  # This request gets an access token from Azure Active Directory by using the local MSI endpoint
  data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
  KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
  kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

上のコードは、次の 2 段階のプロセスを実行します。

   1. VM 上のローカル MSI エンドポイントからトークンをフェッチします。 次にそのエンドポイントが Azure Active Directory からトークンをフェッチします
   1. トークンをキー コンテナーに渡し、シークレットをフェッチします。

次のコマンドを実行します。 シークレット値が表示されるはずです。

```console
python Sample.py
```

このチュートリアルでは、Azure Key Vault と、Linux 仮想マシン上で実行されている Python アプリを使用する方法について説明しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループと仮想マシン、関連するすべてのリソースは、不要になったら削除してください。 そのためには、VM のリソース グループを選択し、 **[削除]** を選択します。

`az keyvault delete` コマンドを使用して、キー コンテナーを削除します。

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
