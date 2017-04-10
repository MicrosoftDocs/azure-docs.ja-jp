---
title: "Azure CDN におけるファイル圧縮のトラブルシューティング | Microsoft Docs"
description: "Azure CDN のファイル圧縮に関する問題のトラブルシューティングを行います。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 27561c3c401f93d4024974a19581d79cda9c4eee


---
# <a name="troubleshooting-cdn-file-compression"></a>CDN ファイルの圧縮のトラブルシューティング
この記事では、 [CDN ファイルの圧縮](cdn-improve-performance.md)に関する問題のトラブルシューティングについて説明します。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]**をクリックします。

## <a name="symptom"></a>症状
エンドポイントの圧縮が有効になっているにもかかわらず、ファイルが圧縮されていない状態で戻されています。

> [!TIP]
> ファイルが圧縮されている状態で戻されているかどうかをチェックするには、[Fiddler](http://www.telerik.com/fiddler) などのツールや、ブラウザーの[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)を使用する必要があります。  キャッシュされた CDN コンテンツと共に返される HTTP 応答ヘッダーをチェックします。  値が **gzip**、**bzip2**、**deflate** のいずれかである `Content-Encoding` という名前のヘッダーがある場合、コンテンツは圧縮されています。
> 
> ![Content-Encoding header](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>原因
以下のような原因が考えられます。

* 要求されたコンテンツが圧縮の対象ではありません。
* 要求されたファイルの種類の圧縮が有効になっていません。
* HTTP 要求に、有効な圧縮の種類を要求するヘッダーが含まれていません。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
> [!TIP]
> 新しいエンドポイントをデプロイする場合と同様に、CDN の構成の変更がネットワークを通じて反映されるまでには多少の時間がかかります。  通常、変更は 90 分以内に適用されます。  今回初めて CDN エンドポイントの圧縮を設定した場合は、圧縮設定が確実に POP に反映されるように 1 ～ 2 時間の待機時間を検討してください。 
> 
> 

### <a name="verify-the-request"></a>要求を確認する
最初に、要求でクイック サニティ チェックを行う必要があります。  ブラウザーの [開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) を使用して、実行中の要求を表示できます。

* 送信元ではなく、エンドポイントの URL ( `<endpointname>.azureedge.net`) に要求が送信されていることを確認します。
* 要求に **Accept-Encoding** ヘッダーが含まれており、このヘッダーの値に **gzip**、**deflate**、または **bzip2** が含まれていることを確認します。

> [!NOTE]
> **Azure CDN from Akamai** プロファイルは、**gzip** エンコーディングのみをサポートしています。
> 
> 

![CDN 要求ヘッダー](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>圧縮の設定を確認する (Standard CDN プロファイル)
> [!NOTE]
> この手順が適用されるのは、CDN プロファイルが **Azure CDN Standard from Verizon** プロファイルまたは **Azure CDN Standard from Akamai** プロファイルの場合のみです。 
> 
> 

[Azure Portal](https://portal.azure.com) でエンドポイントに移動し、 **[構成]** ボタンをクリックします。

* 圧縮が有効になっていることを確認します。
* 圧縮するコンテンツの MIME の種類が、圧縮形式の一覧に含まれていることを確認します。

![CDN の圧縮設定](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>圧縮の設定を確認する (Premium CDN プロファイル)
> [!NOTE]
> この手順が適用されるのは、CDN プロファイルが **Azure CDN Premium from Verizon** プロファイルの場合のみです。
> 
> 

[Azure Portal](https://portal.azure.com) でエンドポイントに移動し、 **[管理]** ボタンをクリックします。  補助ポータルが開きます。  **[HTTP Large (HTTP ラージ)]** タブ、**[キャッシュの設定]** フライアウトの順にマウスのカーソルを合わせます。  **[圧縮]**をクリックします。 

* 圧縮が有効になっていることを確認します。
* **[ファイルの種類]** 一覧に MIME の種類のコンマ区切り一覧 (スペースなし) が含まれていることを確認します。
* 圧縮するコンテンツの MIME の種類が、圧縮形式の一覧に含まれていることを確認します。

![CDN Premium の圧縮設定](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>コンテンツがキャッシュされていることを確認する
> [!NOTE]
> この手順が適用されるのは、CDN プロファイルが **Azure CDN from Verizon** プロファイル (Standard または Premium) の場合のみです。
> 
> 

ブラウザーの開発者ツールを使用して、応答ヘッダーを確認し、ファイルが要求されているリージョンでキャッシュされていることを確認します。

* **Server** 応答ヘッダーを確認します。  この応答ヘッダーの形式は、次の例に示すとおり **プラットフォーム (POP/サーバー ID)**である必要があります。
* **X-Cache** 応答ヘッダーを確認します。  ヘッダーによって **HIT**が読み取られる必要があります。  

![CDN 応答ヘッダー](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>ファイルがサイズ要件を満たしていることを確認する
> [!NOTE]
> この手順が適用されるのは、CDN プロファイルが **Azure CDN from Verizon** プロファイル (Standard または Premium) の場合のみです。
> 
> 

圧縮の対象であるには、ファイルは次のサイズ要件を満たす必要があります。

* 128 バイトより大きい
* 1 MB 未満

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>配信元サーバーで要求の **Via** ヘッダーをチェックする
**Via** HTTP ヘッダーは、その要求がプロキシ サーバーを介して送信されていることを Web サーバーに伝えます。  既定では、要求に **Via** ヘッダーが含まれている場合、Microsoft IIS Web サーバーは応答を圧縮しません。  この動作を上書きするには、次の作業を実行します。

* **IIS 6**: [IIS のメタベース プロパティで HcNoCompressionForProxies="FALSE" に設定する](https://msdn.microsoft.com/library/ms525390.aspx)
* **IIS 7 以降**: [サーバーの構成で **noCompressionForHttp10** と **noCompressionForProxies** を False に設定する](http://www.iis.net/configreference/system.webserver/httpcompression)




<!--HONumber=Nov16_HO3-->


