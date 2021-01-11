---
title: CDN 統合を使用してコンテンツをストリーミングする
titleSuffix: Azure Media Services
description: CDN 統合を使用したコンテンツのストリーミング、およびプリフェッチと Origin-Assist CDN-Prefetch について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: b60a86d09e5d6f7d1108595253349bbd0784e4d3
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799351"
---
# <a name="stream-content-with-cdn-integration"></a>CDN 統合を使用してコンテンツをストリーミングする

Azure Content Delivery Network (CDN) では、世界中に戦略的に配置された物理ノードにコンテンツをキャッシュすることによって、高帯域幅コンテンツをユーザーに高速配信するためのグローバル ソリューションを開発者に提供します。  

CDN を使うと、コーデックごと、ストリーミング プロトコルごと、ビットレートごと、コンテナー形式ごと、暗号化または DRM ごとに、Media Services の[ストリーミング エンドポイント (オリジン)](streaming-endpoint-concept.md) からストリーミングされたコンテンツをキャッシュできます。 コーデック、ストリーミング プロトコル、コンテナー形式、ビットレート、暗号化の組み合わせごとに、個別の CDN キャッシュが存在します。

ビデオ フラグメントがキャッシュされている限り、人気のコンテンツは CDN から直接配信されます。 通常、多くのユーザーがまったく同じものを視聴するライブ コンテンツは、キャッシュされる可能性が高くなります。 オンデマンド コンテンツは、人気のコンテンツとそうでないコンテンツが混在している可能性があるため、少し厄介な場合があります。 どれも人気のない (1 週間に視聴者が 1 人か 2 人だけ) ビデオ アセットが数百万個存在するが、数千人の視聴者がすべて異なるビデオを視聴している場合、CDN の効率は非常に悪くなります。

また、アダプティブ ストリーミングの動作も考慮する必要があります。 個々のビデオ フラグメントはそれぞれのエンティティとしてキャッシュされます。 たとえば、特定のビデオが初めて視聴されたとします。 視聴者があちこちを数秒だけ視聴してスキップすることを繰り返している場合は、その視聴者が視聴した内容に関連付けられたビデオ フラグメントのみが CDN にキャッシュされます。 アダプティブ ストリーミングでは通常、5 から 7 の異なるビットレートのビデオがあります。 ある視聴者があるビットレートを視聴し、別の視聴者が別のビットレートを視聴している場合は、これらがそれぞれ個別に CDN にキャッシュされます。 2 人が同じビットレートを視聴している場合でも、異なるプロトコル経由でストリーミングしている可能性があります。 各プロトコル (HLS、MPEG-DASH、スムーズ ストリーミング) は別個にキャッシュされます。 つまり、各ビットレートやプロトコルは別個にキャッシュされ、要求されたビデオ フラグメントのみがキャッシュされます。

Media Services の[ストリーミング エンドポイント](streaming-endpoint-concept.md)で CDN を有効にするかどうかを決定するときは、予想される視聴者数を考慮してください。 CDN は、コンテンツに対して多数の視聴者が予想される場合にのみ役立ちます。 CDN は同時実行に合わせて最適にスケーリングされるため、最大同時視聴者数が 500 未満の場合、CDN を無効にすることをお勧めします。

このトピックでは、[CDN 統合](#enable-azure-cdn-integration)を有効にする方法について説明します。 また、プリフェッチ (アクティブ キャッシュ) と [Origin-Assist CDN-Prefetch](#origin-assist-cdn-prefetch) の概念についても説明します。

## <a name="considerations"></a>考慮事項

* CDN を有効にするかどうかを問わず、[ストリーミング エンドポイント](streaming-endpoint-concept.md) `hostname` とストリーミング URL は同じままにします。
* CDN の有無にかかわらずコンテンツをテストできる必要がある場合は、CDN が有効になっていない別のストリーミング エンドポイントを作成してください。

## <a name="enable-azure-cdn-integration"></a>Azure CDN 統合を有効にする

> [!IMPORTANT]
> 試用版または学生向けの Azure アカウントに対して CDN を有効にすることはできません。
>
> CDN 統合は、連邦政府および中国地域を除くすべての Azure データ センターで有効になります。

CDN が有効になった状態でストリーミング エンドポイントがプロビジョニングされた後、ストリーミング エンドポイントを CDN エンドポイントにマップするために DNS の更新が実行される前に、Media Services には定義された待機時間が存在します。

後で CDN を有効/無効にする場合は、ストリーミング エンドポイントを**停止**状態にする必要があります。 ストリーミング エンドポイントが開始されると、Azure CDN 統合が有効になり、すべての CDN POP で変更がアクティブになるまでに、最大で 2 時間かかる場合があります。 ただし、ストリーミング エンドポイントを開始し、そのストリーミング エンドポイントからの中断なしでストリーミングすることができます。 統合が完了すると、ストリームは CDN から配信されます。 プロビジョニング期間中、ストリーミング エンドポイントは**開始中**の状態になり、パフォーマンスが低下する可能性があります。

Standard ストリーミング エンドポイントは作成されるとき、既定では Standard Verizon で構成されます。 REST API を使用して、Premium Verizon または Standard Akamai プロバイダーを構成することができます。

Azure CDN との Azure Media Services 統合は、Standard ストリーミング エンドポイント用の **Azure CDN from Verizon** で実装されます。 Premium ストリーミング エンドポイントは、すべての **Azure CDN 価格レベルとプロバイダー**を使用して構成できます。

> [!NOTE]
> Azure CDN の詳細については、[CDN の概要](../../cdn/cdn-overview.md)を参照してください。

## <a name="determine-if-a-dns-change-was-made"></a>DNS の変更が行われたかどうかを確認する

ストリーミング エンドポイントで DNS の変更が行われた (トラフィックが Azure CDN に転送されている) かどうかは、<https://www.digwebinterface.com> を使用して確認できます。 結果に azureedge.net のドメイン名が表示されていれば、トラフィックは現在 CDN に配信されています。

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-Prefetch

CDN のキャッシュは、リアクティブなプロセスです。 CDN で次のオブジェクトの要求を予測できる場合、CDN では次のオブジェクトを事前に要求してキャッシュできます。 このプロセスを使用すると、すべての (またはほとんどの) オブジェクトのキャッシュヒットを達成できるため、パフォーマンスが向上します。

プリフェッチの概念では、プレーヤーから差し迫ってオブジェクトが要求される場合を想定して、"インターネットのエッジ" にオブジェクトを配置することを目指しています。これによりオブジェクトがプレーヤーに配信される時間が短縮されます。

この目標を達成するには、ストリーミング エンドポイント (オリジン) と CDN が 2 つの方法で連携して動作する必要があります。

- Media Services オリジンには、プリフェッチする次のオブジェクトを CDN に通知するために "インテリジェンス" (Origin-Assist) が必要です。
- CDN によってプリフェッチとキャッシュが行われます (CDN-Prefetch パート)。 また、CDN には、プリフェッチか通常のフェッチかをオリジンに通知し、404 応答と無限のプリフェッチ ループを回避する方法を処理するための "インテリジェンス" が必要です。

### <a name="benefits"></a>メリット

*Origin-Assist CDN-Prefetch* 機能には、次のような利点があります。

- プリフェッチを使うと、ビデオの再生品質が向上します。再生中にエッジで予想されるビデオ セグメントを事前に配置し、視聴者の待機時間を減らし、ビデオ セグメントのダウンロード時間を短縮できます。 この結果、ビデオの起動時間が短縮され、再バッファー処理の発生が少なくなります。
- この概念は、メディアに限定されるのではなく、一般的な CDN オリジン シナリオに適用されます。
- Akamai はこの機能を [Akamai Cloud Embed (ACE) ](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html) に追加しました。

> [!NOTE]
> この機能は、Media Services ストリーミング エンドポイントと統合された Akamai CDN にはまだ適用されません。 ただし、既存の Akamai コントラクトがあり、Akamai CDN と Media Services オリジンとのカスタム統合が必要な Media Services のお客様が利用できます。

### <a name="how-it-works"></a>しくみ

ライブ ストリーミングとビデオ オンデマンド ストリーミングの両方の `Origin-Assist CDN-Prefetch` ヘッダーに対する CDN サポートを Akamai CDN と直接契約しているお客様が利用できます。 この機能では、Akamai CDN と Media Services オリジン間の次の HTTP ヘッダー交換が行われます。

|HTTP ヘッダー|値|送信者|受信者|目的|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (既定) または 0 |CDN|Origin (配信元)|CDN がプリフェッチ対応であることを示すこと。|
|`CDN-Origin-Assist-Prefetch-Path`| 例: <br/>フラグメント(ビデオ= 1400000000, フォーマット = mpd-time-cmaf)|Origin (配信元)|CDN|CDN にプリフェッチ パスを提供すること。|
|`CDN-Origin-Assist-Prefetch-Request`|1 (プリフェッチ要求) または 0 (通常の要求)|CDN|Origin (配信元)|CDN からの要求がプリフェッチであることを示すこと。|

ヘッダー交換の一部を実際に確認するには、次の手順を実行します。

1. Postman または cURL を使用して、オーディオまたはビデオのセグメントまたはフラグメントの Media Services 配信元に要求を発行します。 要求にヘッダー `CDN-Origin-Assist-Prefetch-Enabled: 1` を必ず追加します。
2. 応答には、その値として相対パスが指定されたヘッダー `CDN-Origin-Assist-Prefetch-Path` が表示されます。

### <a name="supported-streaming-protocols"></a>サポートされているストリーミング プロトコル

`Origin-Assist CDN-Prefetch` 機能は、ライブおよびオンデマンド ストリーミング用に次のストリーミング プロトコルをサポートしています。

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* スムーズ ストリーミング

### <a name="faqs"></a>FAQ

* プリフェッチパス URL が無効なため、CDN のプリフェッチで 404 を受け取る場合はどうすればよいですか。

    CDN では、404 応答 (または他の構成値) が 10 秒間だけキャッシュされます。

* オンデマンド ビデオがあるとします。 CDN プリフェッチを有効にした場合、クライアントから最初のビデオ セグメントが要求されると、プリフェッチのループが始まり、後続のすべてのビデオ セグメントが同じビットレートでプリフェッチされることになりますか。

    いいえ。CDN プリフェッチは、クライアントから開始された要求および応答の後にのみ実行されます。 プリフェッチ ループを回避するために、CDN プリフェッチはプリフェッチによってトリガーされることはありません。

* Origin-Assist CDN-Prefetch 機能は常に有効ですか。 有効と無効を切り替えるにはどうすればよいですか。

    この機能は既定では無効です。 Akamai API を使用して有効にする必要があります。

* ライブ ストリーミングで、次のセグメントまたはフラグメントをまだ使用できない場合、Origin-Assist はどうなりますか。

    この場合、Media Services オリジンから `CDN-Origin-Assist-Prefetch-Path` ヘッダーが提供されないため、CDN プリフェッチは発生しません。

* `Origin-Assist CDN-Prefetch` は動的マニフェスト フィルターとどのように連携しますか。

    この機能は、マニフェスト フィルターとは独立して機能します。 次のフラグメントがフィルター ウィンドウに収まらない場合でも、未処理のクライアント マニフェストが検索されてその URL が特定され、CDN プリフェッチの応答ヘッダーとして返されます。 したがって、DASH、HLS、Smooth マニフェストから除外されたフラグメントの URL が CDN によって取得されます。 ただし、そのフラグメントはプレーヤーが保持する DASH、HLS、Smooth マニフェストには含まれていない (プレーヤーはそのフラグメントの存在を認識していない) ため、プレーヤーはそのフラグメントを取得するために CDN に対して GET 要求を行いません。

* DASH MPD、HLS プレイリスト、Smooth マニフェストはプリフェッチできますか。

    いいえ。DASH MPD、HLS マスター プレイリスト、HLS バリアント プレイリスト、または smooth マニフェストの URL はプリフェッチ ヘッダーに追加されません。

* プリフェッチの URL は相対ですか、それとも絶対ですか。

    Akamai CDN では両方を使用できますが、絶対 URL を使用しても明らかな利点がないため、Media Services オリジンではプリフェッチ パスに相対 URL のみを提供しています。

* この機能は、DRM で保護されたコンテンツでは動作しますか。

    はい。この機能は HTTP レベルで動作するため、セグメントまたはフラグメントがデコードまたは解析されることはありません。 コンテンツが暗号化されているかどうかは考慮されません。

* この機能は、サーバー側広告挿入 (SSAI) では動作しますか。
    
    元の、またはメイン コンテンツ (広告挿入前の元のビデオ コンテンツ) の場合、SSAI を使っても Media Services オリジンのソース コンテンツのタイムスタンプは変わらないため、動作します。 この機能が広告コンテンツで動作するかどうかは、広告オリジンが Origin-Assist をサポートしているかどうかによって変わります。 たとえば、広告コンテンツも Azure Media Services (同じオリジンまたは別のオリジン) でホストされている場合、広告コンテンツもプリフェッチされます。

* この機能は UHD/HEVC コンテンツで動作しますか。

    はい。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

* [ストリーミング エンドポイント (オリジン)](streaming-endpoint-concept.md) に関するドキュメントを必ず確認してください。
* [このリポジトリ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のサンプルは、既定のストリーミング エンドポイントを .NET を使用して開始する方法について示します。
