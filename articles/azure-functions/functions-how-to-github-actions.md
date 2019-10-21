---
title: GitHub Actions を使用した Azure Functions のコードの更新
description: GitHub Actions を使用し、GitHub に Azure Functions プロジェクトをビルドおよびデプロイするワークフローを定義する方法について説明します。
author: ahmedelnably
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: 483ac9380fa8d58f294112cb6c80e0393fa01589
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028985"
---
# <a name="continuous-delivery-by-using-github-action"></a>GitHub Actions を使用した継続的デリバリー

[GitHub Actions](https://github.com/features/actions) を使用すると、Azure の関数アプリにお使いの関数コードを自動的にビルドしてデプロイするワークフローを定義できます。 

> [!IMPORTANT]  
> GitHub Actions は現在ベータ版です。 まず、ご自分の GitHub アカウントを使用し、[新規登録し、プレビューに参加](https://github.com/features/actions)する必要があります。

GitHub Actions の[ワークフロー](https://help.github.com/articles/about-github-actions#workflow)とは、お使いの GitHub リポジトリに定義する自動化されたプロセスです。 このプロセスによって、GitHub 上にお使いの関数アプリ プロジェクトをビルドしてデプロイする方法が GitHub に対して指示されます。 

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。 

Azure Functions のワークフロー ファイルには、次の 3 つのセクションがあります。 

| Section | タスク |
| ------- | ----- |
| **認証** | <ol><li>サービス プリンシパルを定義します。</li><li>発行プロファイルをダウンロードします。</li><li>GitHub シークレットを作成します。</li></ol>|
| **ビルド** | <ol><li>環境を設定します。</li><li>関数アプリを構築します。</li></ol> |
| **デプロイする** | <ol><li>関数アプリをデプロイします。</li></ol>|

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[Azure CLI](/cli/azure/) の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用すると、[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成できます。 このコマンドは、Azure portal の [Azure Cloud Shell](https://shell.azure.com) を使用するか、 **[試してみる]** ボタンを選択して実行できます。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

この例のリソースのプレースホルダーは、ご自分のサブスクリプション ID、リソース グループ、および関数アプリ名に置き換えます。 これにより、ご自分の関数アプリにアクセスするためのロールの割り当て資格情報が出力されます。 この JSON オブジェクトをコピーします。このオブジェクトは、GitHub に対する認証に使用します。

> [!NOTE]
> 認証に発行プロファイルを使用する場合は、サービス プリンシパルを作成する必要はありません。

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 これが、前の例の範囲がリソース グループ全体ではなく、特定の関数アプリに限定されている理由です。

## <a name="download-the-publishing-profile"></a>発行プロファイルのダウンロード

functionapp の発行プロファイルをダウンロードするには、アプリの **[概要]** ページに移動し、 **[発行プロファイルの取得]** をクリックします。

   ![[発行プロファイルのダウンロード]](media/functions-how-to-github-actions/get-publish-profile.png)

ファイルの内容をコピーします。

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

1. [GitHub](https://github.com) でご自分のリポジトリを参照し、 **[設定]**  >  **[シークレット]**  >  **[Add a new secret]** \(新しいシークレットの追加\) を選択します。

   ![シークレットの追加](media/functions-how-to-github-actions/add-secret.png)

1. **[Name]\(名前\)** には `AZURE_CREDENTIALS` を、 **[Value]\(値\)** にはコピーしたコマンド出力を使用し、 **[Add secret]\(シークレットの追加\)** を選択します。 発行プロファイルを使用している場合は、 **[Name]\(名前\)** には `SCM_CREDENTIALS`、 **[Value]\(値\)** にはファイルの内容を使用します。

これで GitHub は、お使いの Azure の関数アプリに認証できるようになりました。

## <a name="set-up-the-environment"></a>環境をセットアップする 

環境を設定するには、発行設定操作のいずれかを使用します。

|言語 | 設定操作 |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**    | `actions/setup-java` |
|**JavaScript**     | `actions/setup-node` |
|**Python**   | `actions/setup-python` |

次の例は、サポートされているさまざまな言語の環境を設定するワークフローの一部です。

**JavaScript**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

**Python**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-function-app"></a>関数アプリのビルド

これは、言語および Azure Functions でサポートされる言語によって異なります。このセクションは、各言語の標準的なビルド手順です。

次の例は、サポートされているさまざまな言語で関数アプリをビルドするワークフローの一部です。

**JavaScript**

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

**Python**

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

**.NET**

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

**Java**

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

## <a name="next-steps"></a>次の手順

.yaml の完全なワークフローを確認するには、[Azure GitHub Actions ワークフローのサンプルのリポジトリ](https://github.com/Azure/actions-workflow-samples) 名前に `functionapp` があるファイルのうち 1 つを参照してください。 これらのサンプルは、ご自分のワークフローの出発点として使用できます。

> [!div class="nextstepaction"]
> [GitHub Actions について](https://help.github.com/en/articles/about-github-actions)
