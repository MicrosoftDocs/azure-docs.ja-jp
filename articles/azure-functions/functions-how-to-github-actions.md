---
title: GitHub Actions を使用した Azure Functions のコードの更新
description: GitHub Actions を使用し、GitHub に Azure Functions プロジェクトをビルドおよびデプロイするワークフローを定義する方法について説明します。
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 02f5399e89900a438fb94f973c497a54dc05cfee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210166"
---
# <a name="continuous-delivery-by-using-github-action"></a>GitHub Actions を使用した継続的デリバリー

[GitHub Actions](https://github.com/features/actions) を使用すると、Azure の関数アプリにお使いの関数コードを自動的にビルドしてデプロイするワークフローを定義できます。 

GitHub Actions の[ワークフロー](https://help.github.com/articles/about-github-actions#workflow)とは、お使いの GitHub リポジトリに定義する自動化されたプロセスです。 このプロセスによって、GitHub 上にお使いの関数アプリ プロジェクトをビルドしてデプロイする方法が GitHub に対して指示されます。 

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。 

Azure Functions のワークフロー ファイルには、次の 3 つのセクションがあります。 

| Section | タスク |
| ------- | ----- |
| **Authentication** | <ol><li>サービス プリンシパルを定義します。</li><li>発行プロファイルをダウンロードします。</li><li>GitHub シークレットを作成します。</li></ol>|
| **Build** | <ol><li>環境を設定します。</li><li>関数アプリを構築します。</li></ol> |
| **Deploy** | <ol><li>関数アプリをデプロイします。</li></ol>|

> [!NOTE]
> 認証に発行プロファイルを使用する場合は、サービス プリンシパルを作成する必要はありません。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[Azure CLI](/cli/azure/) の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用すると、[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成できます。 このコマンドは、Azure portal の [Azure Cloud Shell](https://shell.azure.com) を使用するか、 **[使ってみる]** ボタンを選択して実行できます。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

この例のリソースのプレースホルダーは、ご自分のサブスクリプション ID、リソース グループ、および関数アプリ名に置き換えます。 これにより、ご自分の関数アプリにアクセスするためのロールの割り当て資格情報が出力されます。 この JSON オブジェクトをコピーします。このオブジェクトは、GitHub に対する認証に使用します。

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 これが、前の例の範囲がリソース グループ全体ではなく、特定の関数アプリに限定されている理由です。

## <a name="download-the-publishing-profile"></a>発行プロファイルのダウンロード

関数アプリの発行プロファイルをダウンロードするには、次を操作を行います。

1. 関数アプリの **[概要]** ページを選択し、 **[発行プロファイルの取得]** を選択します。

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="発行プロファイルのダウンロード":::

1. 発行設定ファイルの内容を保存し、コピーします。

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

1. [GitHub](https://github.com) でご自分のリポジトリを参照し、 **[設定]**  >  **[シークレット]**  >  **[Add a new secret]** \(新しいシークレットの追加\) を選択します。

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="シークレットの追加":::

1. 新しいシークレットを追加します。

   * Azure CLI を使用して作成したサービス プリンシパルを使用している場合は、 **[名前]** に `AZURE_CREDENTIALS` を使用します。 次に、コピーした JSON オブジェクト出力を **[値]** に貼り付け、 **[シークレットの追加]** を選択します。
   * 発行プロファイルを使用している場合は、`SCM_CREDENTIALS`[名前]**に** を使用します。 次に、発行プロファイルのファイルの内容を **[値]** に使用し、 **[シークレットの追加]** を選択します。

これで GitHub は、お使いの Azure の関数アプリに認証できるようになりました。

## <a name="set-up-the-environment"></a>環境をセットアップする 

環境のセットアップは、言語固有の発行セットアップアクションを使用して行います。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、`actions/setup-node` アクションを使用して環境をセットアップするワークフローの一部を示しています：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

次の例は、`actions/setup-python` アクションを使用して環境をセットアップするワークフローの一部を示しています：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

次の例は、`actions/setup-dotnet` アクションを使用して環境をセットアップするワークフローの一部を示しています：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

次の例は、`actions/setup-java` アクションを使用して環境をセットアップするワークフローの一部を示しています：

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>関数アプリのビルド

これは、言語および Azure Functions でサポートされる言語によって異なります。このセクションは、各言語の標準的なビルド手順です。

次の例は、言語固有の関数 アプリを構築するワークフローの一部を示しています：

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>関数アプリをデプロイする

関数アプリにご自分のコードをデプロイするには、`Azure/functions-action` アクションを使用する必要があります。 このアクションには、次の 2 つのパラメーターがあります。

|パラメーター |説明  |
|---------|---------|
|**_app-name_** | (必須) お使いの関数アプリの名前です。 |
|_**slot-name**_ | (省略可能) デプロイする[デプロイ スロット](functions-deployment-slots.md)の名前です。 このスロットは、お使いの関数アプリに既に定義されている必要があります。 |


次の例では、`functions-action` のバージョン 1 を使用しています。

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>次のステップ

.yaml ファイルの完全なワークフローを確認するには、[Azure GitHub Actions ワークフローのサンプル リポジトリ](https://aka.ms/functions-actions-samples)にあるファイルで、名前に `functionapp` が含まれるもののうち 1 つを参照してください。 これらのサンプルは、ご自分のワークフローの出発点として使用できます。

> [!div class="nextstepaction"]
> [GitHub Actions について](https://help.github.com/en/articles/about-github-actions)
