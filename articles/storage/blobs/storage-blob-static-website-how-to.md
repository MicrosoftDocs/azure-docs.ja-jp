---
title: Azure Storage で静的 Web サイトをホストする
description: Azure Storage GPv2 アカウントのコンテナーから静的コンテンツ (HTML、CSS、JavaScript、および画像ファイル) を直接提供する方法について説明します。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 5ab24a99b22fae172b5308ba7477953f27ecfd44
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435948"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure Storage で静的 Web サイトをホストする

Azure Storage GPv2 アカウントのコンテナーから静的コンテンツ (HTML、CSS、JavaScript、および画像ファイル) を直接提供できます。 詳細については、「[Static website hosting in Azure Storage (Azure Storage での静的 Web サイト ホスティング)](storage-blob-static-website.md)」を参照してください。

この記事では、Azure portal、Azure CLI、または PowerShell を使用して、静的 Web サイトのホスティングを有効にする方法を示します。

<a id="portal" />

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

ステップ バイ ステップのチュートリアルについては、「[チュートリアル: Host a static website on Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)」 (チュートリアル: Blob Storage で静的な Web サイトをホストする) を完了します。

静的 Web サイトのホスティングを有効にした後は、Web サイトのパブリック URL を使用して、ブラウザーから自分のサイトのページを表示できます。

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Azure portal を使用して Web サイトの URL を見つける

ストレージ アカウントの概要ページの横に表示されるウィンドウで、 **[静的な Web サイト]** を選択します。 サイトの URL が、 **[プライマリ エンドポイント]** フィールドに表示されます。

![Azure Storage 静的 Web サイトのメトリック: メトリック](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Azure CLI の使用

[Azure コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) を使用して、静的な Web サイトのホスティングを有効にすることができます。

1. まず、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) を開きます。または、Azure CLI をローカルに[インストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. 静的な Web サイトのホスティングを有効にします。

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

   * `<error-document-name>` プレースホルダーを、サイト上に存在しないページをブラウザーが要求したときにユーザーに表示されるエラー ドキュメントの名前に置き換えます。

   * `<index-document-name>` プレースホルダーをインデックス ドキュメントの名前に置き換えます。 このドキュメントは、通常、"index.html" です。

4. ソース ディレクトリから *$web* コンテナーにオブジェクトをアップロードします。

   > [!NOTE]
   > Azure Cloud Shell を使用している場合は、`$web` コンテナーを参照するときに、必ず `\` エスケープ文字を追加してください (例: `\$web`)。 Azure CLI のローカル インストールを使用している場合は、エスケープ文字を使用する必要はありません。

   この例では、Azure Cloud Shell セッションからコマンドを実行していることを前提としています。

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

   * `<source-path>` プレースホルダーを、アップロードするファイルの場所へのパスに置き換えます。

   > [!NOTE]
   > Azure CLI のローカル インストールを使用している場合は、ローカル コンピューターの任意の場所へのパスを使用できます (例: `C:\myFolder`)。
   >
   > Azure Cloud Shell を使用している場合は、Cloud Shell が認識できるファイル共有を参照する必要があります。 この場所は、クラウド共有自体のファイル共有、または Cloud Shell からマウントする既存のファイル共有にすることができます。 そうする方法については、「[Persist files in Azure Cloud Shell (Azure Cloud Shell でファイルを永続化する)](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)」を参照してください。

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Azure CLI を使用して Web サイトの URL を見つける

Web サイトのパブリック URL を使用して、ブラウザーからコンテンツを表示できます。

次のコマンドを使用して、URL を見つけます。

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

* `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

<a id="powershell" />

## <a name="use-powershell"></a>PowerShell の使用

Azure PowerShell モジュールを使用して、静的な Web サイトのホスティングを有効にできます。

1. Windows PowerShell コマンド ウィンドウを開きます。

2. Azure PowerShell モジュール Az バージョン 0.7 以降があることを確認します。

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。

3. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

4. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、静的 Web サイトをホストするストレージ アカウントのサブスクリプションに設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

5. 使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

6. 静的な Web サイトのホスティングを有効にします。

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * `<error-document-name>` プレースホルダーを、サイト上に存在しないページをブラウザーが要求したときにユーザーに表示されるエラー ドキュメントの名前に置き換えます。

   * `<index-document-name>` プレースホルダーをインデックス ドキュメントの名前に置き換えます。 このドキュメントは、通常、"index.html" です。

7. ソース ディレクトリから *$web* コンテナーにオブジェクトをアップロードします。

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * `<path-to-file>` プレースホルダーの値を、アップロードするファイルへの完全修飾パス (例: `C:\temp\index.html`) に置き換えます。

   * `<blob-name>` プレースホルダーの値を、結果の BLOB に付ける名前 (例: `index.html`) に置き換えます。

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>PowerShell を使用して Web サイトの URL を見つける

Web サイトのパブリック URL を使用して、ブラウザーからコンテンツを表示できます。

次のコマンドを使用して、URL を見つけます。

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

* `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>静的な Web サイトのページでメトリックを有効にする

メトリックを有効にすると、 **$web** コンテナーのファイルに関するトラフィック統計情報が、メトリック ダッシュボードでレポートされます。

1. **[設定]**  >  **[監視]**  >  **[メトリック]** の順にクリックします。

   メトリック データは、さまざまなメトリック API に接続することで生成されます。 データを返す API メンバーだけに注目するために、ポータルには一定期間内に使用されたメンバーのみが表示されます。 必要な API メンバーを選択できるようにするための最初の手順は、期間を展開することです。

2. 期間のボタンをクリックして **[過去 24 時間]** を選択し、 **[適用]** をクリックします。

   ![Azure Storage 静的 Web サイトのメトリック: 時間の範囲](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. "*名前空間*" のドロップ ダウンから **[BLOB]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: 名前空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. メトリックとして **[エグレス]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: メトリック](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *[集計]* セレクターから **[合計]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: 集計](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. **[フィルターの追加]** ボタンをクリックし、 *[プロパティ]* セレクターから **[API 名]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: API 名](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. *[値]* セレクターで **[GetWebContent]** の横にあるチェック ボックスをオンにして、メトリック レポートを設定します。

   ![Azure Storage 静的 Web サイトのメトリック: GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>次の手順

* [Azure Storage での静的な Web サイトのホスティング](storage-blob-static-website.md)
* [Azure CDN を使用して HTTPS 経由でカスタム ドメイン付きの BLOB にアクセスする](storage-https-custom-domain-cdn.md)
* [BLOB または Web エンドポイントのカスタム ドメイン名の構成](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [最初のサーバーレス Web アプリを作成する](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [チュートリアル:Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)
