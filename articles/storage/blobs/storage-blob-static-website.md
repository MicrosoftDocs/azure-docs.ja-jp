---
title: Azure Storage での静的 Web サイト ホスティング
description: Azure Storage 静的 Web サイト ホスティングは、最新の Web アプリケーションをホストするための、費用対効果の高いスケーラブルなソリューションを提供します。
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 09/04/2020
ms.subservice: blobs
ms.custom: devx-track-js
ms.openlocfilehash: bbb996362df1f99a8702de310bc11f6828cc303a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008452"
---
# <a name="static-website-hosting-in-azure-storage"></a>Azure Storage での静的 Web サイト ホスティング

*$web* という名前のストレージ コンテナーから直接に、静的コンテンツ (HTML、CSS、JavaScript、画像ファイル) を提供できます。 Azure Storage でコンテンツをホスティングすることで、[Azure Functions](../../azure-functions/functions-overview.md) やその他のサービスとしてのプラットフォーム (PaaS) サービスなど、サーバーレス アーキテクチャを使用できます。 Web サーバーでコンテンツをレンダリングする必要がない場合、Azure Storage の静的 Web サイト ホスティングは優れた選択肢です。

[App Service Static Web Apps](https://azure.microsoft.com/services/app-service/static/) は、Azure Storage の静的 Web サイト ホスティングに代わる優れた選択肢であり、Web サイトでコンテンツのレンダリングが必要ない場合にも適してします。 App Service Static Web Apps では、GitHub ソースからグローバル デプロイまでのフル マネージドの継続的インテグレーションと継続的デリバリー (CI/CD) ワークフローが提供されます。

Web サーバーでコンテンツのレンダリングが必要な場合は、[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用できます。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up-a-static-website"></a>静的 Web サイトの設定

静的 Web サイトのホスティングは、ストレージ アカウントで有効にする必要がある機能です。

静的 Web サイトのホスティングを有効にするには、既定のファイルの名前を選択してから、必要に応じて、カスタム 404 ページへのパスを指定します。 アカウントに **$web** という名前の BLOB ストレージ コンテナーがまだ存在しない場合は、自動的に作成されます。 このコンテナーに、サイトのファイルを追加します。

手順を追ったガイダンスについては、「[Host a static website in Azure Storage](storage-blob-static-website-how-to.md)」 (Azure Storage で静的 Web サイトをホストする) を参照してください。

![Azure Storage 静的 Web サイトのメトリック: メトリック](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

**$web** コンテナー内のファイルは大文字と小文字が区別され、匿名アクセス要求によって処理され、読み取り操作によってのみ使用できます。

## <a name="uploading-content"></a>コンテンツのアップロード

これらの任意のツールを使用して、コンテンツを **$web** コンテナーにアップロードできます。

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell モジュール](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure 記憶域エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code 拡張機能](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>コンテンツの表示

ユーザーは、Web サイトのパブリック URL を使用して、ブラウザーからサイトのコンテンツを表示できます。 URL を見つけるには、Azure portal、Azure CLI、または PowerShell を使用します。 「[Web サイトの URL を検索する](storage-blob-static-website-how-to.md#portal-find-url)」を参照してください。

サーバーが 404 エラーを返し、Web サイトを有効にしたときに、エラー ドキュメントを指定していない場合、既定の 404 ページがユーザーに返されます。

> [!NOTE]
> [Azure Storage でのクロス オリジン リソース共有 (CORS) のサポート](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)は、静的な Web サイトではサポートされていません。

### <a name="regional-codes"></a>リージョン コード

サイトの URL には、リージョン コードが含まれます。 たとえば、URL `https://contosoblobaccount.z22.web.core.windows.net/` にはリージョン コード `z22` が含まれています。

そのコードは URL に残しておく必要がありますが、内部的に使用されるだけであり、他にそのコードを使用する必要はありません。

ユーザーがサイトを開き、特定のファイル (例: `https://contosoblobaccount.z22.web.core.windows.net`) を指定しない場合、静的 Web サイトのホスティングを有効にしたときに指定したインデックス ドキュメントが表示されます。

### <a name="secondary-endpoints"></a>セカンダリ エンドポイント

[セカンダリ リージョンでの冗長性](../common/storage-redundancy.md#redundancy-in-a-secondary-region)を設定した場合は、セカンダリ エンドポイントを使用して Web サイトのコンテンツにアクセスすることもできます。 データはセカンダリ リージョンに非同期にレプリケートされるため、セカンダリ エンドポイントで使用できるファイルが、プライマリ エンドポイントで使用できるファイルと常に同期されているとは限りません。

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Web コンテナーのパブリック アクセス レベルの設定の影響

**$web** コンテナーのパブリック アクセス レベルを変更できますが、プライマリ静的 Web サイト エンドポイントに対しては、これらのファイルが匿名アクセス要求によって処理されるため、この変更が無効になります。 つまり、すべてのファイルへのパブリック (読み取り専用) アクセスです。

次のスクリーンショットは、Azure Portal のパブリック アクセス レベル設定を示しています。

![ポータルでパブリック アクセス レベルを設定する方法を示すスクリーンショット](./media/anonymous-read-access-configure/configure-public-access-container.png)

プライマリ静的 Web サイト エンドポイントは影響を受けませんが、パブリック アクセス レベルの変更は、プライマリ BLOB サービス エンドポイントには有効です。

**$web** コンテナーのパブリック アクセス レベルを **プライベート (匿名アクセスなし)** から **BLOB (BLOB 専用の匿名読み取りアクセス)** に変更した場合、プライマリ静的 Web サイト エンドポイント `https://contosoblobaccount.z22.web.core.windows.net/index.html` へのパブリック アクセスのレベルは変更されません。

しかし、プライマリ BLOB サービス エンドポイント `https://contosoblobaccount.blob.core.windows.net/$web/index.html` へのパブリック アクセスは、プライベートからパブリックに変更されます。 ユーザーはこれらの 2 つのエンドポイントのいずれかを使用して、このファイルを開けるようになりました。

ストレージ アカウントでパブリック アクセスを無効にしても、そのストレージ アカウントでホストされている静的な Web サイトには影響しません。 詳細については、[コンテナーと BLOB の匿名パブリック読み取りアクセスの構成](anonymous-read-access-configure.md)に関するページを参照してください。

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>カスタム ドメインを静的 Web サイトの URL にマップする

静的 Web サイトをカスタム ドメイン経由で利用できるようにすることができます。

カスタム ドメインの HTTP アクセスは、Azure Storage でネイティブにサポートされているため、簡単に有効にすることができます。 HTTPS を有効にするには、Azure CDN を使用する必要があります。これは、Azure Storage がカスタム ドメインを使用した HTTPS をまだネイティブにサポートしていないためです。 手順を追ったガイダンスについては、「[カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)」を参照してください。

ストレージ アカウントが HTTPS 経由での[安全な転送を必要とする](../common/storage-require-secure-transfer.md)ように構成されている場合、ユーザーは HTTPS エンドポイントを使用する必要があります。

> [!TIP]
> Azure でドメインをホストすることを検討してください。 詳しくは、「[Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)」を参照してください。

## <a name="adding-http-headers"></a>HTTP ヘッダーの追加

静的 Web サイト機能の一部としてヘッダーを構成する方法はありません。 ただし、Azure CDN を使用してヘッダーを追加したり、ヘッダー値を追加 (または上書き) したりすることができます。 「[Azure CDN の Standard ルール エンジン リファレンス](../../cdn/cdn-standard-rules-engine-reference.md)」を参照してください。

ヘッダーを使用してキャッシュを制御する場合は、「[キャッシュ規則で Azure CDN キャッシュの動作を制御する](../../cdn/cdn-caching-rules.md)」を参照してください。

## <a name="multi-region-website-hosting"></a>複数リージョンでの Web サイトのホスティング

複数の地域で Web サイトをホストすることを計画している場合は、リージョンのキャッシュに [Content Delivery Network](../../cdn/index.yml) を使用することをお勧めします。 各リージョンで異なるコンテンツを提供する場合は、[Azure Front Door](../../frontdoor/index.yml) を使用します。 これにより、フェールオーバー機能も提供されます。 カスタム ドメインを使用する予定の場合、[Azure Traffic Manager](../../traffic-manager/index.yml) はお勧めしません。 Azure Storage でのカスタム ドメイン名の確認方法が原因で問題が発生する可能性があります。


## <a name="pricing"></a>価格

静的 Web サイトのホスティングは無料で有効にできます。 サイトで利用した BLOB ストレージと、運用コストに対してのみ課金されます。 Azure Blob Storage の価格について詳しくは、[Azure Blob Storage の料金に関するページ](https://azure.microsoft.com/pricing/details/storage/blobs/)をご覧ください。

## <a name="metrics"></a>メトリック

静的 Web サイトのページでメトリックを有効にできます。 メトリックを有効にすると、 **$web** コンテナーのファイルに関するトラフィック統計情報が、メトリック ダッシュボードでレポートされます。

静的 Web サイトのページでメトリックを有効にするには、「[Enable metrics on static website pages](storage-blob-static-website-how-to.md#metrics)」 (静的 Web サイト ページでメトリックを有効にする) を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Storage で静的 Web サイトをホストする](storage-blob-static-website-how-to.md)
* [カスタム ドメインを Azure Blob Storage エンドポイントにマップする](storage-custom-domain-name.md)
* [Azure Functions](../../azure-functions/functions-overview.md)
* [Azure App Service](../../app-service/overview.md)
* [最初のサーバーレス Web アプリを作成する](/azure/functions/tutorial-static-website-serverless-api-with-database)
* [チュートリアル:Azure DNS でドメインをホストする](../../dns/dns-delegate-domain-azure-dns.md)