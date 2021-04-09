---
title: クイックスタート - JavaScript 用 Azure Key Vault 証明書クライアント ライブラリ (バージョン 4)
description: JavaScript クライアント ライブラリを使用して Azure キー コンテナーに証明書を作成し、それを取得および削除する方法について学習します
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 1064c6a1e2dddaae98e94ccceca7b1d550897393
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97930856"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>クイックスタート: JavaScript 用 Azure Key Vault 証明書クライアント ライブラリ (バージョン 4)

JavaScript 用 Azure Key Vault 証明書クライアント ライブラリを使ってみます。 [Azure Key Vault](../general/overview.md) は、証明書のセキュリティで保護されたストアを提供するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このクイックスタートでは、JavaScript クライアント ライブラリを使用して Azure キー コンテナーに証明書を作成し、それを取得および削除する方法について説明します

Key Vault クライアント ライブラリのリソースは、次のとおりです。

[API リファレンスのドキュメント](/javascript/api/overview/azure/key-vault-index) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [パッケージ (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Key Vault と証明書の詳細については、以下を参照してください。
- [Key Vault の概要](../general/overview.md)
- [証明書の概要](about-certificates.md)。

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

```azurecli
mkdir key-vault-node-app
```

1. 新しく作成した *key-vault-node-app* ディレクトリに移動し、"init" コマンドを実行してノード プロジェクトを初期化します。

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Key Vault パッケージをインストールする

コンソール ウィンドウから、Node.js 用 Azure Key Vault [証明書ライブラリ](https://www.npmjs.com/package/@azure/keyvault-certificates)をインストールします。

```azurecli
npm install @azure/keyvault-certificates
```

キー コンテナーに対する認証を行うために、[azure.identity](https://www.npmjs.com/package/@azure/identity) パッケージをインストールします

```azurecli
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

自分のユーザー アカウントに証明書のアクセス許可を付与するアクセス ポリシーをキー コンテナーに対して作成します

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>コード例

以下のコード サンプルでは、クライアントの作成、証明書の設定、証明書の取得、証明書の削除を行う方法を示します。 

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
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

このクイックスタートでは、ログイン ユーザーを使用してキー コンテナーに対する認証を行います。ローカル開発では、これが推奨される方法となります。 Azure にデプロイされるアプリケーションの場合は、App Service または仮想マシンにマネージド ID を割り当てる必要があります。詳細については、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

以下の例では、キー コンテナーの名前は、"https://\<your-key-vault-name\>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。 この例では、[Azure ID ライブラリ](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme)の ["DefaultAzureCredential()"](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) クラスを使用しています。環境や使用するオプションが変わっても、同じコードを使用して ID を提供することができます。 キー コンテナーに対する認証について詳しくは、[開発者ガイド](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)を参照してください。

次のコードを "main()" 関数に追加します

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>証明書の保存

アプリケーションが認証されたら、[beginCreateCertificate メソッド](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_)を使用して、キー コンテナーに証明書を設定できます。これには、証明書の名前、[証明書ポリシー](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy)、[証明書ポリシー プロパティ](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)が必要です

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> 証明書名が存在する場合は、上のコードによって、その証明書の新しいバージョンが作成されます。
### <a name="retrieve-a-certificate"></a>証明書の取得

先ほど設定した値は、[getCertificate メソッド](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption)を使用して取得できます。

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>証明書の削除

最後に、[beginDeleteCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) メソッドと [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_) メソッドを使用して、キー コンテナーから証明書を削除して消去します。

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>サンプル コード

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

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

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>テストして検証する

次のコマンドを実行して、アプリを実行します。

```azurecli
npm install
npm index.js
```

次のような出力が表示されます。

```azurecli
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、証明書を格納して、その証明書を取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [証明書の概要](about-certificates.md)を読む
- [App Service アプリケーションから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-create-vault-azure-web-app.md)を参照する
- [仮想マシンから Key Vault にアクセスする方法についてのチュートリアル](../general/tutorial-net-virtual-machine.md)を参照する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
