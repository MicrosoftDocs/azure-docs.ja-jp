---
title: GitHub Actions で Key Vault を使用して Azure Spring Cloud を認証する
description: GitHub Actions を使用して Azure Spring Cloud の CI/CD ワークフローでキー コンテナーを使用する方法
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0ea0db1faf8c452958b8d95c193d45506057777c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877617"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>GitHub Actions で Key Vault を使用して Azure Spring Cloud を認証する

**この記事の適用対象:** ✔️ Java ✔️ C#

キー コンテナーは、キーを安全に格納できる場所です。 エンタープライズ ユーザーは、管理対象のスコープ内にある CI/CD 環境の資格情報を保存する必要があります。 キー コンテナー内の資格情報を取得するキーは、リソース スコープに制限する必要があります。  これは、Azure スコープ全体ではなく、キー コンテナー スコープに対してのみアクセスできます。 これは、建物内のすべてのドアを開くことができるマスター キーではなく、頑丈な箱のみを開くことができるキーのようなものです。 キーを別のキーで取得する方法であり、CICD ワークフローで役立ちます。 

## <a name="generate-credential"></a>資格情報を生成する
キー コンテナーにアクセスするキーを生成するには、ローカル コンピューター上で次のコマンドを実行します。

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
`--scopes` パラメーターでスコープを指定すると、リソースへのキーのアクセスが制限されます。  これを使うと、頑丈な箱のみにアクセスできます。

結果:
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
次に、[GitHub リポジトリを設定して Azure で認証する](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)方法の説明に従って、結果を GitHub の **シークレット** に保存します。

## <a name="add-access-policies-for-the-credential"></a>資格情報のアクセス ポリシーを追加する
上記で作成した資格情報を使うと、キー コンテナーに関する一般的な情報のみを取得できます。保存されているコンテンツは取得できません。  キー コンテナーに保存されているシークレットを取得するには、資格情報のアクセス ポリシーを設定する必要があります。

Azure portal の **[Key Vault]** ダッシュボードに移動し、 **[アクセス制御]** メニューをクリックして、 **[ロールの割り当て]** タブを開きます。 **[種類]** に **[アプリ]** を選択し、 **[スコープ]** に `This resource` を選択します。  前の手順で作成した資格情報が表示されます。

 ![アクセス ポリシーを設定する](./media/github-actions/key-vault1.png)

資格情報の名前 (`azure-cli-2020-01-19-04-39-02` など) をコピーします。 **[アクセス ポリシー]** メニューを開き、**[+ アクセス ポリシーの追加]** リンクをクリックします。  **[テンプレート]** に `Secret Management` を選択し、**[プリンシパル]** を選択します。 **[プリンシパル]** / **[選択]** 入力ボックスに資格情報の名前を貼り付けます。

 ![選択](./media/github-actions/key-vault2.png)

 **[アクセス ポリシーの追加]** ダイアログの **[追加]** ボタンをクリックし、**[保存]** をクリックします。

## <a name="generate-full-scope-azure-credential"></a>完全なスコープの Azure 資格情報を生成する
これは、建物のすべてのドアを開くことができるマスター キーです。 この手順は前の手順と似ていますが、ここではスコープを変更してマスター キーを生成します。

```azurecli
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

この場合も、結果は次のようになります。
```output
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
JSON 文字列全体をコピーします。  **[Key Vault]** ダッシュボードに戻ります。 **[シークレット]** メニューを開き、**[生成]/[インポート]** ボタンをクリックします。 `AZURE-CREDENTIALS-FOR-SPRING` などのシークレット名を入力します。 JSON の資格情報文字列を **[値]** 入力ボックスに貼り付けます。 値の入力ボックスは、複数行のテキスト領域ではなく、1 行のテキスト フィールドであることがわかります。  ここには、完全な JSON 文字列を貼り付けることができます。

 ![完全なスコープの資格情報](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>GitHub Actions で資格情報を結合する
CICD パイプラインの実行時に使用する資格情報を設定します。

```console
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>次の手順
* [Spring Cloud GitHub Actions](./spring-cloud-howto-github-actions.md)
