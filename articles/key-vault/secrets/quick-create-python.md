---
title: クイックスタート - Python 用の Azure Key Vault クライアント ライブラリ - シークレットの管理
description: Python クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、削除する方法について学習します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: f1f044eb3af35019eaf010e118bc4a5814269e9e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378554"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>クイック スタート:Python 用 Azure Key Vault シークレット クライアント ライブラリ

Python 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Python 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- TLS または SSL 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Python 2.7、3.5.3 以降
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) を実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>パッケージをインストールする

コンソール ウィンドウから、Python 用 Azure Key Vault シークレット ライブラリをインストールします。

```console
pip install azure-keyvault-secrets
```

このクイックスタートでは、azure.identity パッケージもインストールする必要があります。

```console
pip install azure.identity
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

Python 用 Azure Key Vault クライアント ライブラリを使用すると、キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

この記事に示されているものと同様の操作およびその他の Key Vault の機能を示すサンプル アプリは、[GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets/samples) で入手できます。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

```python
import os
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

キー コンテナーに対する認証とキー コンテナー クライアントの作成は、上記の「[環境変数の設定](#set-environmental-variables)」の手順にある環境変数によって異なります。 キー コンテナーの名前は、"https://<your-key-vault-name>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。

```python
credential = DefaultAzureCredential()

client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、client.[set_secret メソッド](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-)を使用して、対象のキー コンテナーにシークレットを設定できます。これには、シークレットの名前が必要です (この例では、"mySecret" を使用します)。  

```python
client.set_secret(secretName, secretValue)
```

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[get_secret メソッド](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-)を使用して取得できます。

```python
retrieved_secret = client.get_secret(secretName)
 ```

これで、シークレットが `retrieved_secret.value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[begin_delete_secret メソッド](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-)を使用して、対象のキー コンテナーからシークレットを削除してみましょう。

```python
client.begin_delete_secret(secretName)
```

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して、シークレットがなくなったことを確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>サンプル コード

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = "mySecret"

print("Input the value of your secret > ")
secretValue = raw_input()

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}` ...")

client.set_secret(secretName, secretValue)

print(" done.")

print("Forgetting your secret.")
secretValue = ""
print(f"Your secret is {secretValue}.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

client.begin_delete_secret(secretName)

print(" done.")
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)
