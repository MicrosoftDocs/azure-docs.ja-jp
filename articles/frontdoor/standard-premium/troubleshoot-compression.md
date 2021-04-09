---
title: Azure Front Door Standard/Premium におけるファイル圧縮のトラブルシューティング
description: Azure Front Door でのファイル圧縮に関する問題をトラブルシューティングする方法について説明します。 この記事では、考えられるさまざまな原因について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098267"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Azure Front Door Standard/Premium のファイル圧縮のトラブルシューティング

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

この記事では、Azure Front Door Standard/Premium のファイル圧縮に関する問題のトラブルシューティングについて説明します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="symptom"></a>症状

ルートの圧縮が有効になっているにもかかわらず、ファイルが圧縮されていない状態で戻されています。

> [!TIP]
> ファイルが圧縮されている状態で戻されているかどうかをチェックするには、[Fiddler](https://www.telerik.com/fiddler) などのツールや、ブラウザーの[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)を使用する必要があります。  キャッシュされた CDN コンテンツと共に返される HTTP 応答ヘッダーをチェックします。  値が **gzip**、**bzip2**、**deflate** のいずれかである `Content-Encoding` という名前のヘッダーがある場合、コンテンツは圧縮されています。
> 
> ![Content-Encoding header](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>原因

以下のような原因が考えられます。

* 要求されたコンテンツが圧縮の対象ではありません。
* 要求されたファイルの種類の圧縮が有効になっていません。
* HTTP 要求に、有効な圧縮の種類を要求するヘッダーが含まれていません。
* 配信元からチャンク コンテンツが送信されています。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

> [!TIP]
> 新しいエンドポイントをデプロイする場合と同様に、AFD の構成の変更がネットワークを通じて反映されるまでには多少の時間がかかります。  通常、変更は 90 分以内に適用されます。  今回初めて CDN エンドポイントの圧縮を設定した場合は、圧縮設定が確実に POP に反映されるように 1 ～ 2 時間の待機時間を検討してください。 
> 

### <a name="verify-the-request"></a>要求を確認する

最初に、要求で二重チェックを行う必要があります。 ブラウザーの **[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** を使用して、実行中の要求を表示できます。

* 送信元ではなく、エンドポイントの URL (`<endpointname>.z01.azurefd.net`) に要求が送信されていることを確認します。
* 要求に **Accept-Encoding** ヘッダーが含まれており、このヘッダーの値に **gzip**、**deflate**、または **bzip2** が含まれていることを確認します。

![CDN 要求ヘッダー](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>圧縮の設定を確認する

[Azure portal](https://portal.azure.com) でエンドポイントに移動し、[ルート] パネルの **[構成]** ボタンを選択します。 圧縮が **有効** になっていることを確認します。

![CDN の圧縮設定](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>配信元サーバーで要求の **Via** ヘッダーをチェックする

**Via** HTTP ヘッダーは、その要求がプロキシ サーバーを介して送信されていることを Web サーバーに伝えます。  既定では、要求に **Via** ヘッダーが含まれている場合、Microsoft IIS Web サーバーは応答を圧縮しません。  この動作をオーバーライドするには、次の作業を実行します。

* **IIS 6**: IIS のメタベース プロパティで HcNoCompressionForProxies="FALSE" に設定する。 詳細については、[IIS 6 の圧縮](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))に関するページを参照してください。
* **IIS 7 以降**: サーバーの構成で **noCompressionForHttp10** と **noCompressionForProxies** を *False* に設定する。 詳細については、「[HTTP 圧縮](https://www.iis.net/configreference/system.webserver/httpcompression)」を参照してください。
