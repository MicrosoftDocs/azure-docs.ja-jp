---
title: クイックスタート - JavaScript 用 Azure Key Vault シークレット クライアント ライブラリ (バージョン 4)
description: JavaScript クライアント ライブラリを使用して Azure キー コンテナーからシークレットを作成、取得、および削除する方法について説明します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 769a63819925caa50ff364869b8d9a14c3258a2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214543"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-javascript-version-4"></a>クイックスタート: JavaScript 用 Azure Key Vault シークレット クライアント ライブラリ (バージョン 4)

JavaScript 用 Azure Key Vault シークレットクライアント ライブラリを使ってみます。 [Azure Key Vault](../general/overview.md) は、シークレットのセキュリティで保護されたストアを提供するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このクイックスタートでは、JavaScript クライアント ライブラリを使用して Azure キー コンテナーにシークレットを作成し、それを取得および削除する方法について説明します

Key Vault クライアント ライブラリのリソースは、次のとおりです。

[API リファレンスのドキュメント](/javascript/api/overview/azure/key-vault-index) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

Key Vault とシークレットの詳細については、以下を参照してください。
- [Key Vault の概要](../general/overview.md)
- [シークレットの概要](about-secrets.md)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 使用するオペレーティング システム用の最新の [Node.js](https://nodejs.org)。
- [Azure CLI](/cli/azure/install-azure-cli)
- キー コンテナー - [Azure portal](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md)、または [Azure PowerShell](../general/quick-create-powershell.md) を使用して作成できます

このクイックスタートでは、[Azure CLI](/cli/azure/install-azure-cli) を実行していることを前提としています。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

1. `login` コマンドを実行します。

    ```azurecli-interactive
    az login
    ```

    CLI で既定のブラウザーを開くことができる場合、開いたブラウザに Azure サインイン ページが読み込まれます。

    それ以外の場合は、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) でブラウザー ページを開き、ターミナルに表示されている認証コードを入力します。

2. ブラウザーでアカウントの資格情報を使用してサインインします。

## <a name="create-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

次に、クラウドにデプロイできる Node.js アプリケーションを作成します。 

1. コマンド シェルで、`key-vault-node-app` という名前のフォルダーを作成します。

```terminal
mkdir key-vault-node-app
```

1. 新しく作成した *key-vault-node-app* ディレクトリに移動し、"init" コマンドを実行してノード プロジェクトを初期化します。

```terminal
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault パッケージをインストールする

コンソール ウィンドウから、Node.js 用 Azure Key Vault [シークレット ライブラリ](https://www.npmjs.com/package/@azure/keyvault-secrets)をインストールします。

```terminal
npm install @azure/keyvault-secrets
```

キー コンテナーに対する認証を行うために、[azure.identity](https://www.npmjs.com/package/@azure/identity) パッケージをインストールします

```terminal
npm install @azure/identity
```

## <a name="set-environment-variables"></a>環境変数の設定

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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>キー コンテナーへのアクセス許可を付与する

自分のユーザー アカウントにシークレットのアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

## <a name="code-examples"></a>コード例

以下のコード サンプルでは、クライアントの作成、シークレットの設定、シークレットの取得、シークレットの削除を行う方法を示します。 

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

1. 新しいテキスト ファイルを作成し、"index.js" として保存します

1. Azure および Node.js モジュールを読み込むための require 呼び出しを追加します

1. 基本的な例外処理を含め、プログラムの構造を作成します

```javascript
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
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、[Azure ID ライブラリ](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)の ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 キー コンテナーに対する認証の詳細については、[開発者ガイド](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)を参照してください。

次のコードを "main()" 関数に追加します

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>シークレットを保存する

アプリケーションが認証されたら、[setSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient#setSecret_string__string__SetSecretOptions_)を使用して、キー コンテナーにシークレットを設定できます。これには、シークレットの名前が必要です (この例では、"mySecret" を使用します)。  

```javascript
await client.setSecret(secretName, secretValue);
```

### <a name="retrieve-a-secret"></a>シークレットを取得する

先ほど設定した値は、[getSecret メソッド](/javascript/api/@azure/keyvault-secrets/secretclient#getSecret_string__GetSecretOptions_)を使用して取得できます。

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

これで、シークレットが `retrievedSecret.value` として保存されました。

### <a name="delete-a-secret"></a>シークレットを削除します

最後に、[beginDeleteSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#beginDeleteSecret_string__BeginDeleteSecretOptions_) メソッドと [purgeDeletedSecret](https://docs.microsoft.com/javascript/api/@azure/keyvault-secrets/secretclient?#purgeDeletedSecret_string__PurgeDeletedSecretOptions_) メソッドを使用して、キー コンテナーからシークレットを削除および消去してみましょう。

```javascript
const deletePoller = await client.beginDeleteSecret(secretName);
await deletePoller.pollUntilDone();
await client.purgeDeletedSecret(secretName);
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
  const deletePoller = await client.beginDeleteSecret(secretName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your secret from {keyVaultName} ...");
  await client.purgeDeletedSecret(secretName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>テストして検証する

1. 次のコマンドを実行して、アプリを実行します。

    ```terminal
    npm install
    node index.js
    ```

1. メッセージが表示されたら、シークレットの値を入力します (例: mySecretPassword)。

    次のような出力が表示されます。

    ```azurecli
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

このクイックスタートでは、キー コンテナーを作成し、シークレットを格納して、そのシークレットを取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure Key Vault シークレットの概要](about-secrets.md)を確認する
- [キー コンテナーへのアクセスをセキュリティで保護する](../general/secure-your-key-vault.md)方法
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
