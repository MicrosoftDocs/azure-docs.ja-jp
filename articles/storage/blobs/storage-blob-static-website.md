---
title: Azure Storage での静的 Web サイト ホスティング
description: Azure Storage 静的 Web サイト ホスティングは、最新の Web アプリケーションをホストするための、費用対効果の高いスケーラブルなソリューションを提供します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 67d3dcad4ec73ee09ec40282b2fbdea945daefe4
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58472771"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage での静的 Web サイト ホスティング
Azure Storage GPv2 アカウントでは、*$web* という名前のストレージ コンテナーから直接、静的コンテンツ (HTML、CSS、JavaScript、画像ファイル) を提供できます。 Azure Storage でのホスティングを活用すれば、[Azure Functions](/azure/azure-functions/functions-overview) やその他の PaaS サービスなど、サーバーレス アーキテクチャを使用できます。

静的 Web サイト ホスティングとは対照的に、サーバー側のコードに依存する動的サイトは、[Azure App Service](/azure/app-service/overview) を使用してホストするのが最適です。

## <a name="how-does-it-work"></a>それはどのように機能しますか?
ストレージ アカウントで静的 Web サイト ホスティングを有効にする場合、既定のファイルの名前を選択します。必要に応じて、カスタム 404 ページへのパスも指定します。 機能が有効になると、*$web* という名前のコンテナーが作成されます (まだ存在しない場合)。

*$web* コンテナー内のファイルは、

- 匿名のアクセス要求を通じて提供されます
- オブジェクト読み取り操作を通じてのみ利用可能です
- 大文字と小文字の区別
- 次のパターンに従っているパブリック Web で利用可能です
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- 次のパターンに従っている BLOB ストレージ エンドポイントを通じて利用可能です
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

ファイルのアップロードには BLOB ストレージ エンドポイントを使用します。 たとえば、次の場所にアップロードされたファイルは、

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

ブラウザーで次のような場所を指定して利用できます。

```bash
https://contoso.z4.web.core.windows.net/image.png
```

選択された既定のファイル名はルートで使用されるほか、ファイル名が指定されていない場合にサブディレクトリで使用されます。 エラー ドキュメントのパスを指定していないと、サーバーが 404 を返す際に既定の 404 ページがユーザーに返されます。

> [!NOTE]
> ファイルの既定のパブリック アクセス レベルは非公開です。 ファイルは匿名アクセス要求によって処理されるため、この設定は無視されます。 すべてのファイルへのパブリック アクセスがあり、RBAC のアクセス許可は無視されます。

## <a name="cdn-and-ssl-support"></a>CDN および SSL のサポート

静的 Web サイトのファイルを HTTPS 経由で利用できるようにするには、「[カスタム ドメインを用いた BLOB に Azure CDN から HTTPS 経由でアクセスする](storage-https-custom-domain-cdn.md)」を参照してください。 このプロセスの一環として、CDN が BLOB エンドポイントではなく "*Web エンドポイントを指す*" ようにする必要があります。 CDN 構成がすぐに実行されないため、コンテンツが表示されるまで数分待たなければならない場合があります。

静的 Web サイトを更新するときは、CDN エンドポイントを消去して、CDN エッジ サーバー上のキャッシュされたコンテンツを必ず消去してください。 詳細については、「[Azure CDN エンドポイントの消去](../../cdn/cdn-purge-endpoint.md)」を参照してください。

## <a name="custom-domain-names"></a>カスタム ドメイン名

静的 Web サイトをカスタム ドメイン経由で利用できるよう、[Azure ストレージ アカウントのカスタム ドメイン名を構成](storage-custom-domain-name.md)できます。 Azure でのドメインのホスティングについて詳しくは、[Azure DNS でのドメインのホスト](../../dns/dns-delegate-domain-azure-dns.md)に関するページを参照してください。

## <a name="pricing"></a>価格
静的 Web サイト ホスティングは、追加コストなしで提供されます。 Azure Blob Storage の価格について詳しくは、[Azure Blob Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

## <a name="quickstart"></a>クイック スタート

### <a name="azure-portal"></a>Azure ポータル
最初に https://portal.azure.com で Azure portal を開いて、GPv2 ストレージ アカウントで以下の手順を実行します。

1. **[設定]** をクリックします。
2. **[静的な Web サイト]** をクリックします。
3. "*インデックス ドキュメント名*" を入力します  (一般的な値は *index.html* です)。
4. 必要に応じて、カスタム 404 ページへの "*エラー ドキュメントのパス*" を入力します  (一般的な値は *404.html* です)。

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

次に、Azure portal または [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して *$web* コンテナーにアセットをアップロードします。これにより、ディレクトリ全体をアップロードします。 必ず、機能を有効にするときに選択した "*インデックス ドキュメント名*" に一致するファイルを含めてください。

最後に、Web エンドポイントに移動して Web サイトをテストします。

### <a name="azure-cli"></a>Azure CLI
ストレージ プレビュー拡張機能をインストールします。

```azurecli-interactive
az extension add --name storage-preview
```
サブスクリプションが複数ある場合は、有効にしたい GPv2 ストレージ アカウントのサブスクリプションに CLI を設定します。

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
機能を有効にします。 山かっこも含め、すべてのプレースホルダーの値は、実際の値に置き換えてください。

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Web エンドポイント URL のクエリを実行します。

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

ソース ディレクトリから *$web* コンテナーにオブジェクトをアップロードします。 コマンド内での *$web* コンテナーに対する参照を正しくエスケープしてください。 たとえば、Azure portal の CloudShell から Azure CLI を使用する場合は、*$web* コンテナーを次のようにエスケープします。

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Deployment

コンテンツをストレージ コンテナーにデプロイするために使用できる方法には、以下があります。

- [AzCopy](../common/storage-use-azcopy.md)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
- [Visual Studio Code 拡張機能](https://code.visualstudio.com/tutorials/static-website/getting-started)

どの場合も、ファイルを *$web* コンテナーにコピーしてください。

## <a name="metrics"></a>メトリック

静的 Web サイトのページでメトリックを有効にするには、**[設定]** > **[監視]** > **[メトリック]** の順にクリックします。

メトリック データは、さまざまなメトリック API に接続することで生成されます。 データを返す API メンバーだけに注目するために、ポータルには一定期間内に使用されたメンバーのみが表示されます。 必要な API メンバーを選択できるようにするために、最初の手順で期間を展開します。

期間のボタンをクリックして **[過去 24 時間]** を選択し、**[適用]** をクリックします。

![Azure Storage 静的 Web サイトのメトリック: 時間の範囲](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

次に、"*名前空間*" のドロップ ダウンから **[BLOB]** を選択します。

![Azure Storage 静的 Web サイトのメトリック: 名前空間](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

メトリックとして **[エグレス]** を選択します。

![Azure Storage 静的 Web サイトのメトリック: メトリック](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

*[集計]* セレクターから **[合計]** を選択します。

![Azure Storage 静的 Web サイトのメトリック: 集計](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

次に、**[フィルターの追加]** ボタンをクリックし、*[プロパティ]* セレクターから **[API 名]** を選択します。

![Azure Storage 静的 Web サイトのメトリック: API 名](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

最後に、*[値]* セレクターで **[GetWebContent]** の横にあるチェック ボックスをオンにして、メトリック レポートを設定します。

![Azure Storage 静的 Web サイトのメトリック: GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

有効にすると、*$web* コンテナーのファイルに関するトラフィック統計情報が、メトリック ダッシュボードでレポートされます。

## <a name="faq"></a>FAQ

**静的 Web サイト機能は、すべての種類のストレージ アカウントで使用できますか?**  
いいえ、静的 Web サイト ホスティングは、GPv2 標準ストレージ アカウントでのみ使用できます。

**Storage VNET とファイアウォールのルールは、新しい Web エンドポイントでサポートされますか?**  
はい、新しい Web エンドポイントは、ストレージ アカウント用に構成された VNET とファイアウォールのルールに従って機能します。

**Web エンドポイントでは、大文字と小文字が区別されますか?**  
はい、Web エンドポイントでは、BLOB エンドポイントと同じように、大文字と小文字が区別されます。 

## <a name="next-steps"></a>次の手順
* [カスタム ドメインを用いた BLOB にAzure CDN から HTTPS 経由でアクセスする](storage-https-custom-domain-cdn.md)
* [BLOB または Web エンドポイントのカスタム ドメイン名の構成](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [最初のサーバーレス Web アプリを作成する](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [チュートリアル:Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)
