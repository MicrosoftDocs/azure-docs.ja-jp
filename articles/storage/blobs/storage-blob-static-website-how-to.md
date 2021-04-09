---
title: Azure Storage で静的 Web サイトをホストする
description: Azure Storage GPv2 アカウントのコンテナーから静的コンテンツ (HTML、CSS、JavaScript、および画像ファイル) を直接提供する方法について説明します。
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2ba0e836f1be97681d677158f5cb81c5b033e915
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008503"
---
# <a name="host-a-static-website-in-azure-storage"></a>Azure Storage で静的 Web サイトをホストする

[汎用 V2](../common/storage-account-create.md) または [BlockBlobStorage](storage-blob-create-account-block-blob.md) アカウントのコンテナーから静的コンテンツ (HTML、CSS、JavaScript、および画像ファイル) を直接提供できます。 詳細については、「[Static website hosting in Azure Storage (Azure Storage での静的 Web サイト ホスティング)](storage-blob-static-website.md)」を参照してください。

この記事では、Azure portal、Azure CLI、または PowerShell を使用して、静的 Web サイトのホスティングを有効にする方法を示します。

## <a name="enable-static-website-hosting"></a>静的な Web サイトのホスティングを有効にします

静的 Web サイトのホスティングは、ストレージ アカウントで有効にする必要がある機能です。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. ストレージ アカウントを見つけて、アカウントの概要を表示します。

3. **[静的な Web サイト]** を選択して、静的な Web サイトの構成ページを表示します。

4. **[有効]** を選択して、ストレージ アカウントに対して静的な Web サイト ホスティングを有効にします。

5. **[インデックス ドキュメント名]** フィールドで、既定のインデックス ページを指定します。（*index.html* など）。 

   既定のインデックス ページは、ユーザーが静的な Web サイトのルートに移動したときに表示されます。  

6. **[エラー ドキュメントのパス]** フィールドで、既定のエラー ページを指定します。（*404 .html* など)。 

   既定のエラー ページは、ユーザーが静的な Web サイトに存在しないページに移動しようとしたときに表示されます。

7. **[保存]** をクリックします。 これで、Azure ポータルで静的な Web サイトのエンドポイントが表示されるようになります。 

    ![ストレージ アカウントに対して静的な Web サイト ホスティングを有効にする](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/) を使用して、静的な Web サイトのホスティングを有効にすることができます。

1. まず、[Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

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

---

## <a name="upload-files"></a>ファイルをアップロードする 

### <a name="portal"></a>[ポータル](#tab/azure-portal)

次の手順では、Azure portal に表示される Storage Explorer のバージョンを使用してファイルをアップロードする方法について説明します。 ただし、Azure portal の外部で実行される [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) のバージョンを使用することもできます。 [AzCopy](../common/storage-use-azcopy-v10.md)、PowerShell、CLI、またはお使いのアカウントの **$web** コンテナーにファイルをアップロードできる任意のカスタムアプリケーションを使用できます。 Visual Studio Code を使用してファイルをアップロードする詳細なチュートリアルについては、[ チュートリアルを参照してください。Host a static website on Blob Storage](./storage-blob-static-website-host.md)」 (チュートリアル: Blob Storage で静的な Web サイトをホストする) を完了します。

1. **ストレージ エクスプローラー (プレビュー)** を選択します。

2. **[BLOB コンテナー]** ノードを展開し、 **$web** コンテナーを選択します。

3. ファイルをアップロードするには、 **[アップロード]** ボタンを選択します。

   ![ファイルをアップロードする](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. ブラウザーでファイルの内容を表示する場合は、そのファイルのコンテンツの種類が `text/html` に設定されていることを確認します。 

   ![その他のコンテンツ タイプ](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer は、`.html` などの一般的に認識される拡張機能に対して、このプロパティを自動的に `text/html` に設定します。 ただし、場合によっては、これを自分で設定する必要があります。 このプロパティを `text/html` に設定しなかった場合、ブラウザーは、内容を表示する代わりに、ファイルのダウンロードをユーザーに要求します。 このプロパティを設定するには、ファイルを右クリックして、 **[プロパティ]** をクリックします。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ソース ディレクトリから *$web* コンテナーにオブジェクトをアップロードします。

この例では、Azure Cloud Shell セッションからコマンドを実行していることを前提としています。

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> ブラウザーが内容を表示せずに、ファイルのダウンロードをユーザーに要求する場合は、コマンドに `--content-type 'text/html; charset=utf-8'` を追加します。 

* `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

* `<source-path>` プレースホルダーを、アップロードするファイルの場所へのパスに置き換えます。

> [!NOTE]
> Azure CLI のローカル インストールを使用している場合は、ローカル コンピューターの任意の場所へのパスを使用できます (例: `C:\myFolder`)。
>
> Azure Cloud Shell を使用している場合は、Cloud Shell が認識できるファイル共有を参照する必要があります。 この場所は、クラウド共有自体のファイル共有、または Cloud Shell からマウントする既存のファイル共有にすることができます。 そうする方法については、「[Persist files in Azure Cloud Shell (Azure Cloud Shell でファイルを永続化する)](../../cloud-shell/persisting-shell-storage.md)」を参照してください。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

ソース ディレクトリから *$web* コンテナーにオブジェクトをアップロードします。

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> ブラウザーが内容を表示せずに、ファイルのダウンロードをユーザーに要求する場合は、コマンドに `-Properties @{ ContentType = "text/html; charset=utf-8";}` を追加します。

* `<path-to-file>` プレースホルダーの値を、アップロードするファイルへの完全修飾パス (例: `C:\temp\index.html`) に置き換えます。

* `<blob-name>` プレースホルダーの値を、結果の BLOB に付ける名前 (例: `index.html`) に置き換えます。

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Web サイトの URL を検索する

Web サイトのパブリック URL を使用して、ブラウザーからお使いのサイトのページを表示できます。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

ストレージ アカウントの概要ページの横に表示されるウィンドウで、 **[静的な Web サイト]** を選択します。 サイトの URL が、 **[プライマリ エンドポイント]** フィールドに表示されます。

![Azure Storage 静的 Web サイトのメトリック: メトリック](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次のコマンドを使用して、静的な Web サイトのパブリック URL を検索します。

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

* `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

次のコマンドを使用して、静的な Web サイトのパブリック URL を検索します。

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

* `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>静的な Web サイトのページでメトリックを有効にする

メトリックを有効にすると、 **$web** コンテナーのファイルに関するトラフィック統計情報が、メトリック ダッシュボードでレポートされます。

1. ストレージ アカウント メニューの **[Monitor]\(監視\)** セクションの **[Metrics]\(メトリック\)** をクリックします。

   > [!div class="mx-imgBorder"]
   > ![メトリック リンク](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > メトリック データは、さまざまなメトリック API に接続することで生成されます。 データを返す API メンバーだけに注目するために、ポータルには一定期間内に使用されたメンバーのみが表示されます。 必要な API メンバーを選択できるようにするための最初の手順は、期間を展開することです。

2. 期間ボタンをクリックして期間を選択し、 **[適用]** をクリックします。

   ![Azure Storage 静的 Web サイトのメトリック: 時間の範囲](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. "*名前空間*" のドロップ ダウンから **[BLOB]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: 名前空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. メトリックとして **[エグレス]** を選択します。

   ![Azure Storage 静的 Web サイトのエグレス メトリックを示すスクリーンショット。](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. *[集計]* セレクターから **[合計]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: 集計](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. **[フィルターの追加]** ボタンをクリックし、 *[プロパティ]* セレクターから **[API 名]** を選択します。

   ![Azure Storage 静的 Web サイトのメトリック: API 名](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. *[値]* セレクターで **[GetWebContent]** の横にあるチェック ボックスをオンにして、メトリック レポートを設定します。

   ![Azure Storage 静的 Web サイトのメトリック: GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > **[GetWebContent]** チェック ボックスが表示されるのは、その API メンバーが特定の期間内に使用された場合のみです。 データを返す API メンバーだけに注目するために、ポータルには一定期間内に使用されたメンバーのみが表示されます。 このリストに特定の API メンバーが見つからない場合は、期間を拡大してください。

## <a name="next-steps"></a>次のステップ

* 静的な Web サイトでカスタム ドメインを構成する方法を学習します。 「[カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)」を確認します。