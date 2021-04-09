---
title: チュートリアル - Python で仮想マシンを使用して Azure Key Vault を使用する | Microsoft Docs
description: このチュートリアルでは、キー コンテナーからシークレットを読み取るように仮想マシン Python アプリケーションを構成します。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2fc77d0cdfb6bd8a62555951c0b6dc7e9b732f93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203540"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>チュートリアル:Python で仮想マシンを使用して Azure Key Vault を使用する

Azure Key Vault は、API キーやデータベース接続文字列などのキー、シークレット、証明書を保護するのに役立ちます。

このチュートリアルでは、Azure リソース用マネージド ID を使用して Azure Key Vault から情報を読み取るよう Python アプリケーションを設定します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Key Vault を作成します
> * Key Vault にシークレットを格納する
> * Azure Linux 仮想マシンを作成する
> * 仮想マシンに対して[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を有効にする
> * コンソール アプリケーションが Key Vault のデータを読み取るために必要なアクセス許可を付与する
> * Key Vault からシークレットを取得する

始める前に、[Key Vault の基本的な概念](basic-concepts.md)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

Windows、Mac、Linux:
  * [Git](https://git-scm.com/downloads)
  * このチュートリアルでは、Azure CLI をローカルで実行する必要があります。 Azure CLI バージョン 2.0.4 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](/cli/azure/install-azure-cli)」を参照してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>キー コンテナーにシークレットを格納する

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

次のいずれかの方法を使用して、**myVM** という名前の VM を作成します。

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) | [Azure ポータル](../../virtual-machines/windows/quick-create-portal.md) |

Azure CLI を使用して Linux VM を作成するには、[az vm create](/cli/azure/vm) コマンドを使用します。  次の例では、*azureuser* という名前のユーザー アカウントを追加します。 SSH キーを自動的に生成するために `--generate-ssh-keys` パラメーターが使用され、キーは既定のキーの場所 ( *~/.ssh*) に配置されます。 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

出力の `publicIpAddress` の値を記録しておきます。

## <a name="assign-an-identity-to-the-vm"></a>VM に ID を割り当てる

Azure CLI の [az vm identity assign](/cli/azure/vm/identity#az-vm-identity-assign) コマンドを使用して、仮想マシンに対するシステム割り当ての ID を作成します。

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

システムによって割り当てられた ID が次のコードに表示されていることにご注意ください。 上記のコマンドの出力は次のようになります。 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>VM ID にアクセス許可を割り当てる

ここで、次のコマンドを実行して、以前に作成した ID アクセス許可をキー コンテナーに割り当てることができます。

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>VM にログインする

仮想マシンにサインインするには、[Linux が実行されている Azure 仮想マシンへの接続とサインイン](../../virtual-machines/linux/login-using-aad.md)に関するページまたは [Windows が実行されている Azure 仮想マシンへの接続とサインイン](../../virtual-machines/windows/connect-logon.md)に関するページの手順のようにします。


Linux VM にログインするには、「[仮想マシンの作成](#create-a-virtual-machine)」ステップで得られた "<publicIpAddress>" を指定して ssh コマンドを使用します。

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>VM に Python ライブラリをインストールする

仮想マシンで、Python スクリプトで使用する 2 つの Python ライブラリ `azure-keyvault-secrets` と `azure.identity` をインストールします。  

たとえば、Linux VM では、`pip3` を使用してこれらをインストールできます。

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>サンプルの Python スクリプトを作成して編集する

仮想マシンで、**sample.py** という Python ファイルを作成します。 ファイルを編集して次のコードを追加します。"<your-unique-keyvault-name>" はお使いのキー コンテナーの名前に置き換えます。

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>サンプルの Python アプリを実行する

最後に、**sample.py** を実行します。 すべてがうまくいった場合、シークレットの値が返されます。

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、仮想マシンとキー コンテナーを削除します。  それらが属しているリソース グループを削除するだけで、簡単にこれを行うことができます。

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[Azure Key Vault REST API](/rest/api/keyvault/)