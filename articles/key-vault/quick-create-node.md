---
title: クイック スタート - Node Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う | Microsoft Docs
description: このクイック スタートでは、Node Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行います。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 02b9c439a932a4b35700871e68bdad7f03451110
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003506"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>クイック スタート:Node Web アプリを使用して Azure Key Vault との間でシークレットの設定と取得を行う 

このクイック スタートでは、Web アプリを使って Azure Key Vault にシークレットを格納する方法とシークレットを取得する方法について説明します。 Key Vault の使用は、情報のセキュリティ維持につながります。 シークレット値を確認するには、Azure 上でこのクイック スタートを実行する必要があります。 クイック スタートでは、Node.js と Azure リソースのマネージド ID を使用します。 学習内容は次のとおりです。

* Key Vault を作成します。
* キー コンテナーにシークレットを格納する。
* キー コンテナーからシークレットを取得する。
* Azure AD Web アプリケーションを作成する。
* Web アプリの[マネージド ID](../active-directory/managed-service-identity/overview.md) を有効にする
* Web アプリケーションに必要なアクセス許可を付与して、キー コンテナーからデータを読み取る

先に進む前に、[Key Vault の基本的な概念](basic-concepts.md)について理解しておいてください。

> [!NOTE]
> Key Vault は、プログラムでシークレットを格納できる中央リポジトリです。 しかしこれを実行するには、アプリケーションとユーザーが最初に Key Vault に対する認証を行う (シークレットを提示する) 必要があります。 セキュリティのベスト プラクティスに従うために、最初のシークレットのローテーションが定期的に行われる必要があります。 
>
> [Azure リソースのマネージド サービス ID](../active-directory/managed-identities-azure-resources/overview.md) を使用すると、Azure で実行されるアプリケーションには、Azure が自動的に管理する ID が付与されます。 これにより、*シークレット導入問題*が解決されます。ユーザーとアプリケーションはベスト プラクティスに従うことができ、最初のシークレットのローテーションについて心配する必要がありません

## <a name="prerequisites"></a>前提条件

* [Node.JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 以降。 このクイック スタートでは、Azure CLI をローカルで実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest)」を参照してください。
* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure CLI を使用して Azure にログインするには、次のように入力します。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group#az-group-create) コマンドを使ってリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

リソース グループ名を選択し、プレース ホルダーを入力します。
次の例では、米国東部リージョンにリソース グループを作成します。

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

作成したリソース グループは、この記事の全体を通して使用します。

## <a name="create-a-key-vault"></a>Key Vault を作成します

次に、前の手順で作成したリソース グループを使用して、キー コンテナーを作成します。 この記事では "ContosoKeyVault" という名前を使用していますが、一意の名前を使用する必要があります。 次の情報を指定します。

* キー コンテナー名。
* リソース グループ名。 名前の文字数は 3 から 24 文字です。使用できる文字は 0-9、a-z、A-Z、およびハイフン (-) のみです。
* 場所:**米国東部**。

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

この時点で、使用している Azure アカウントのみが、この新しいコンテナーで任意の操作を実行することが許可されます。

## <a name="add-a-secret-to-the-key-vault"></a>キー コンテナーにシークレットを追加する

シークレットのしくみをよく理解できるように、シークレットを追加します。 SQL 接続文字列やその他の情報を機密として保持する必要があるのに、アプリケーションで使用可能になるように保管している場合があります。 このチュートリアルでは、パスワードを **AppSecret** と呼び、このパスワード内に **MySecret** という値を格納します。

次のコマンドを入力して、**AppSecret** というキー コンテナーにシークレットを作成します。 このシークレットには、値 **MySecret** が格納されます。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

このコマンドは、URI を含むシークレットの情報を表示します。 これらの手順を完了すると、キー コンテナーのシークレットへの URI がわかります。 この情報をメモしてください。 後の手順で必要になります。

## <a name="clone-the-repo"></a>リポジトリを複製する

リポジトリを複製して、ソースを編集できるローカル コピーを作成します。 次のコマンドを実行します。

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>依存関係をインストールする

次のコマンドを実行して依存関係をインストールします。

```
cd key-vault-node-quickstart
npm install
```

このプロジェクトでは、[ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) および [azure-keyvault](https://www.npmjs.com/package/azure-keyvault) という 2 つのノード モジュールを使用します。

## <a name="publish-the-web-app-to-azure"></a>Web アプリを Azure に発行する

[Azure App Service](https://azure.microsoft.com/services/app-service/) プランを作成します。 このプランには複数の Web アプリを格納することができます。

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
次に、Web アプリを作成します。 次の例では、`<app_name>` をグローバルに一意のアプリ名に置き換えます (有効な文字は a から z、0 から 9、および - です)。 ランタイムは NODE|6.9 に設定されています。 サポートされているすべてのランタイムを確認するには、`az webapp list-runtimes` を実行します。

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Web アプリが作成されると、Azure CLI によって次の例のような出力が表示されます。

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
新しく作成された Web アプリに移動すると、稼働していることが確認できます。 `<app_name>` を一意のアプリ名に置き換えます。

    ```
    http://<app name>.azurewebsites.net
    ```
また、上記のコマンドでは、ローカル Git リポジトリから Azure にデプロイできる Git 対応のアプリが作成されます。 ローカル Git リポジトリには次の URL が構成されます: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`。

上記のコマンドが完了した後、ローカル Git リポジトリに Azure リモートを追加できます。 `<url>` を Git リポジトリの URL に置き換えます。

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Web アプリのマネージド ID を有効にする

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 [Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。

assign-identity コマンドを実行して、このアプリケーションの ID を作成します。

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

このコマンドは、ポータルに移動して、Web アプリケーション プロパティの **[Identity / System assigned]\(ID/システム割り当て済み\)** を **[オン]** に切り替えることと同等です。

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>アプリケーションにアクセス許可を割り当ててキー コンテナーからシークレットを読み取る

前のコマンドの出力をメモしておきます。 次のような形式になっています。
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
次に、キー コンテナーの名前と **principalId** の値を使用して、次のコマンドを実行します。

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Node アプリを Azure にデプロイしてシークレット値を取得する

次のコマンドを実行して、アプリを Azure にデプロイします。

```
git push azure master
```

この後、ブラウザーから `https://<app_name>.azurewebsites.net` にアクセスすると、シークレット値を確認できます。 `<YourKeyVaultName>` の名前は、必ず実際のコンテナー名に置き換えてください。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](key-vault-overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](key-vault-developers-guide.md)」を参照する
- [キー、シークレット、証明書](about-keys-secrets-and-certificates.md)について学習する
- [Azure Key Vault のベスト プラクティス](key-vault-best-practices.md)を確認する