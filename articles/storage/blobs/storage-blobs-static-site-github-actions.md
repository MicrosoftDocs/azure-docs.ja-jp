---
title: GitHub Actions を使用して静的サイトを Azure Storage にデプロイする
description: GitHub Actions を使用した Azure Storage の静的 Web サイトのホスティング
author: juliakm
ms.service: storage
ms.topic: how-to
ms.author: jukullam
ms.reviewer: dineshm
ms.date: 01/11/2021
ms.subservice: blobs
ms.custom: devx-track-javascript, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 2192cdb3072edba2e5597a697feef99ba4d2070d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210259"
---
# <a name="set-up-a-github-actions-workflow-to-deploy-your-static-website-in-azure-storage"></a>GitHub Actions ワークフローを設定して、静的 Web サイトを Azure Storage にデプロイする

ワークフローを使用して静的サイトを Azure Storage アカウントにデプロイすることによって、[GitHub Actions](https://docs.github.com/en/actions) を開始します。 GitHub Actions ワークフローを設定すると、サイトのコードに変更を加えたときに、GitHub から Azure にサイトを自動的にデプロイできるようになります。

> [!NOTE]
> [Azure Static Web Apps](../../static-web-apps/index.yml) を使用している場合は、GitHub Actions ワークフローを手動で設定する必要はありません。
> Azure Static Web Apps によって GitHub Actions ワークフローが自動的に作成されます。 

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションと GitHub アカウント。 

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub リポジトリと静的 Web サイト コード。 GitHub アカウントをお持ちでない場合は、[無料でサインアップ](https://github.com/join)できます。  
- Azure Storage でホストされている作業用の静的 Web サイト。 Azure Storage で静的 Web サイトをホストする方法については、[こちら](storage-blob-static-website-how-to.md)をご覧ください。 この例を実行するには、[Azure CDN](static-website-content-delivery-network.md) のデプロイも必要になります。

> [!NOTE]
> コンテンツ配信ネットワーク (CDN) を使用して、世界中のユーザーの待機時間を短縮し、ストレージ アカウントに対するトランザクションの数を減らすことが一般的です。 静的コンテンツをクラウドベースのストレージ サービスにデプロイすると、高価なコンピューティング インスタンスが必要になる可能性が低くなります。 詳細については、「[静的コンテンツ ホスティング パターン](/azure/architecture/patterns/static-content-hosting)」を参照してください。

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

プレースホルダー `myStaticSite` を Azure Storage でホストされているサイトの名前に置き換えます。 

```azurecli-interactive
   az ad sp create-for-rbac --name {myStaticSite} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} --sdk-auth
```

上記の例で、プレースホルダーを実際のサブスクリプション ID とリソース グループ名に置き換えます。 これにより、以下のような、ご自分のストレージ アカウントにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

1. [GitHub](https://github.com/) でリポジトリを参照します。

1. **[Settings]\(設定\) > [Secrets]\(シークレット\) > [New secret]\(新しいシークレット\)** の順に選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` などの名前を付けます。

    後でワークフロー ファイルを構成する場合は、Azure ログイン アクションの入力 `creds` にシークレットを使用します。 次に例を示します。

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

## <a name="add-your-workflow"></a>ワークフローを追加する

1. GitHub リポジトリの **[Actions]\(アクション\)** にアクセスします。 

    :::image type="content" source="media/storage-blob-static-website/storage-blob-github-actions-header.png" alt-text="GitHub の [Actions]\(アクション\) メニュー項目":::

1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。 

1. ワークフロー ファイルの `on:` セクションの後にあるすべてのものを削除します。 たとえば、残りのワークフローは次のようになります。 

    ```yaml
    name: CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]
    ```

1. ワークフロー `Blob storage website CI` の名前を変更し、チェックアウトとログインのアクションを追加します。 これらのアクションは、サイト コードをチェックアウトし、先ほど作成した `AZURE_CREDENTIALS` GitHub シークレットを使用して Azure で認証を行います。 

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Azure CLI アクションを使用して、コードを BLOB ストレージにアップロードし、CDN エンドポイントを消去します。 `az storage blob upload-batch` の場合、プレースホルダーを実際のストレージ アカウント名に置き換えます。 スクリプトが `$web` コンテナーにアップロードされます。 `az cdn endpoint purge` の場合、プレースホルダーを CDN プロファイル名、CDN エンドポイント名、およびリソース グループに置き換えます。

    ```yaml
        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
    ``` 

1. Azure のログアウトにアクションを追加して、ワークフローを完成させます。 完成したワークフローを次に示します。 このファイルは、リポジトリの `.github/workflows` フォルダー内に表示されます。

    ```yaml
    name: Blob storage website CI

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:            
        - uses: actions/checkout@v2
        - uses: azure/login@v1
          with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}

        - name: Upload to blob storage
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
                az storage blob upload-batch --account-name <STORAGE_ACCOUNT_NAME> -d '$web' -s .
        - name: Purge CDN endpoint
          uses: azure/CLI@v1
          with:
            azcliversion: 2.0.72
            inlineScript: |
               az cdn endpoint purge --content-paths  "/*" --profile-name "CDN_PROFILE_NAME" --name "CDN_ENDPOINT" --resource-group "RESOURCE_GROUP"
      
      # Azure logout 
        - name: logout
          run: |
                az logout
    ```

## <a name="review-your-deployment"></a>デプロイを確認する

1. GitHub リポジトリの **[Actions]\(アクション\)** にアクセスします。 

1. 最初の結果を開くと、ワークフローの実行の詳細なログが表示されます。 
 
    :::image type="content" source="../media/index/github-actions-run.png" alt-text="GitHub アクション実行のログ":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

静的 Web サイトと GitHub リポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Static Web Apps について学習する](../../static-web-apps/index.yml)
