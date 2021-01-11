---
title: クイックスタート - Node.js 用 Azure Key Vault クライアント ライブラリ (v4)
description: Node.js クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: 99424e5a7b39d3f60f3337ff10bf7d9265e2ab4c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422880"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>クイック スタート:Node.js 用 Azure Key Vault クライアント ライブラリ (v4)

Node.js 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Node.js 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- TLS または SSL 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API リファレンスのドキュメント](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 使用するオペレーティング システム用の最新の [Node.js](https://nodejs.org)。
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) を実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>パッケージをインストールする

コンソール ウィンドウから、Node.js 用 Azure Key Vault シークレット ライブラリをインストールします。

```console
npm install @azure/keyvault-secrets
```

このクイックスタートでは、azure.identity パッケージもインストールする必要があります。

```console
npm install @azure/identity
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

Node.js 用 Azure Key Vault クライアント ライブラリを使用すると、キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。

コンソール アプリ全体は https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app から入手できます。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

キー コンテナーに対する認証とキー コンテナー クライアントの作成には、上記の「[環境変数の設定](#set-environmental-variables)」の手順にある環境変数と、[SecretClient コンストラクター](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-)が必要です。 

キー コンテナーの名前は、`https://<your-key-vault-name>.vault.azure.net` という形式で、キー コンテナーの URI に展開されます。 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、[client.setSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-)を使用して、キー コンテナーにシークレットを設定できます。これには、シークレットの名前が必要です (この例では、"mySecret" を使用します)。  

```javascript
await client.setSecret(secretName, secretValue);
```

シークレットが設定されたことは、[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) コマンドを使用して確認できます。

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[client.getSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-)を使用して取得できます。

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

これで、シークレットが `retrievedSecret.value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[client.beginDeleteSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-)を使用して、キー コンテナーからシークレットを削除してみましょう。

```javascript
await client.beginDeleteSecret(secretName)
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

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する