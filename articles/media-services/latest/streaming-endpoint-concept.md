---
title: ストリーミング エンドポイント (配信元)
titleSuffix: Azure Media Services
description: コンテンツをクライアント プレーヤー アプリまたは CDN (Content Delivery Network) に直接配信するダイナミック パッケージおよびストリーミング サービスであるストリーミング エンドポイント (配信元) について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: inhenkel
ms.openlocfilehash: 9f17e8a09715ce9ff51715f17a449ec0a5b3f770
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297197"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure Media Services のストリーミング エンドポイント (配信元)

Microsoft Azure Media Services では、[ストリーミング エンドポイント](/rest/api/media/streamingendpoints)は、いずれかの一般的なストリーミング メディア プロトコル (HLS または DASH) を使用して、ライブのオンデマンド コンテンツをクライアント プレーヤー アプリに直接配信できるダイナミック (Just-In-Time) パッケージおよび配信元サービスを表します。 さらに、**ストリーミング エンドポイント**は、業界有数の DRM に動的 (Just-In-Time) 暗号化を提供します。 

Media Services アカウントを作成すると、**既定**のストリーミング エンドポイントが停止状態で作成されます。 **既定の**ストリーミング エンドポイントを削除することはできません。 このアカウントでは、さらに多くのストリーミング エンドポイントを作成できます ([クォータと制限](limits-quotas-constraints.md)に関するページを参照)。

> [!NOTE]
> ビデオのストリーミングを開始するには、ビデオをストリーミングする**ストリーミング エンドポイント**を開始する必要があります。
>
> ストリーミング エンドポイントが実行状態にある場合にのみ課金されます。

[ダイナミック パッケージ](dynamic-packaging-overview.md)に関するトピックも必ず参照してください。 

## <a name="naming-convention"></a>命名規則

ストリーミング URL のホスト名の形式は `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` です。ここで、`servicename` はストリーミング エンドポイントの名前またはライブ イベントの名前になります。

既定のストリーミング エンドポイントを使用する場合、`servicename` が省略され、URL は `{accountname}-{regionname}.streaming.azure.net` になります。

### <a name="limitations"></a>制限事項

* ストリーミング エンドポイント名の最大値は 24 文字です。
* 名前は、次の [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) パターン `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` に従う必要があります。

## <a name="types"></a>型

**ストリーミング エンドポイント**には、**Standard** (プレビュー) と **Premium** の 2 つの型があります。 型は、ストリーミング エンドポイントに割り当てられたスケール ユニットの数 (`scaleUnits`) によって定義されます。

ストリーミング ユニットの上限は、通常 10 です。 お使いのアカウントの上限を引き上げるには、[こちら](https://azure.microsoft.com/support/create-ticket/)までご連絡ください。

次の表で、型について説明します。

|Type|スケール ユニット|説明|
|--------|--------|--------|  
|**Standard**|0|既定のストリーミング エンドポイントは **Standard** 型です。これは、`scaleUnits` を調整することによって Premium 型に変更できます。|
|**Premium**|>0|**Premium** ストリーミング エンドポイントは高度なワークロードに適しており、専用のスケーラブルな帯域幅容量を提供します。 **Premium** 型には、`scaleUnits` (ストリーミング ユニット) を調整することで移行します。 `scaleUnits` は 200 Mbps 単位で購入できる専用の送信容量を提供します。 **Premium** 型を使用している場合は、有効になっている各ユニットがアプリに追加の帯域幅容量を提供します。 |

> [!NOTE]
> 多数のインターネットの対象ユーザーにコンテンツを配信することを検討しているお客様には、ストリーミング エンドポイントで CDN を有効にすることをお勧めします。

SLA については、[価格と SLA](https://azure.microsoft.com/pricing/details/media-services/) に関する記事をご覧ください。

## <a name="comparing-streaming-types"></a>ストリーミング タイプの比較

特徴量|Standard|Premium
---|---|---
スループット |最大 600 Mbps であり、CDN を使用した場合に実効スループットが大幅に向上します。|ストリーミング ユニット (SU) あたり 200 Mbps。 CDN を使用した場合に実効スループットが大幅に向上します。
CDN|Azure CDN、サードパーティ CDN、または CDN なし。|Azure CDN、サードパーティ CDN、または CDN なし。
課金は日割り計算| 毎日|毎日
動的な暗号化|はい|はい
動的パッケージ|はい|はい
スケール|対象スループットまで自動スケールアップ。|追加の SU
IP フィルタリング/G20/カスタム ホスト <sup>1</sup>|はい|はい
プログレッシブ ダウンロード|はい|はい
推奨使用量 |大半のストリーミング シナリオに推奨。|プロフェッショナルな使用量。

<sup>1</sup> エンドポイントで CDN が有効になっていない場合にのみ、ストリーミング エンドポイントで直接使用されます。<br/>

## <a name="streaming-endpoint-properties"></a>ストリーミング エンドポイントのプロパティ

このセクションでは、ストリーミング エンドポイントの一部のプロパティについて詳しく説明します。 新しいストリーミング エンドポイントを作成する方法の例と全プロパティの説明については、[ストリーミング エンドポイント](/rest/api/media/streamingendpoints/create)に関する記事をご覧ください。

- `accessControl`:このストリーミング エンドポイントのセキュリティ設定である、このエンドポイントに接続することを許可された Akamai 署名ヘッダー認証キーと IP アドレスを構成するために使用されます。 このプロパティは、`cdnEnabled` が false に設定されているときにのみ設定できます。

- `cdnEnabled`:このストリーミング エンドポイントに対する Azure CDN 統合が有効になっているかどうかを示します (既定では無効)。 `cdnEnabled` を true に設定した場合、`customHostNames` と `accessControl` の構成が無効になります。

    すべてのデータ センターが Azure CDN 統合をサポートしているわけではありません。 データ センターで Azure CDN 統合を使用できるかどうかを確認するには、次の手順を実行します。

  - `cdnEnabled` を true に設定してみます。
  - 返された結果に "Streaming endpoint CdnEnabled property can't be set to true as CDN capability is not available in the current region (現在のリージョンで CDN 機能を使用できないため、ストリーミング エンドポイントの CdnEnabled プロパティを true に設定できません)" のメッセージと共に `HTTP Error Code 412` (PreconditionFailed) があるかどうかを確認します。

    このエラーが表示されている場合、そのデータ センターではサポートされていません。 別のデータ センターを試してください。

- `cdnProfile`:`cdnEnabled` が true に設定されていると、`cdnProfile` の値を渡すこともできます。 `cdnProfile` は CDN エンドポイントのポイントが作成される、CDN プロファイルの名前です。 既存の cdnProfile を指定するか、新しいものを使用できます。 値が NULL で `cdnEnabled` が true の場合、既定値 "AzureMediaStreamingPlatformCdnProfile" が使用されます。 指定された `cdnProfile` が既に存在する場合、その下にエンドポイントが作成されます。 プロファイルが存在しない場合は、新しいプロファイルが自動的に作成されます。
- `cdnProvider`:CDN が有効になっていると、`cdnProvider` の値を渡すこともできます。 `cdnProvider` は使用されるプロバイダーを制御します。 現時点では、"StandardVerizon"、"PremiumVerizon"、および "StandardAkamai" の 3 つの値がサポートされています。 値が指定されておらず、`cdnEnabled` が true である場合は、"StandardVerizon" (既定値) が使用されます。
- `crossSiteAccessPolicies`:さまざまなクライアントのクロス サイト アクセス ポリシーを指定するために使用されます。 詳しくは、[Cross-domain ポリシー ファイルの仕様](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)と「[Making a Service Available Across Domain Boundaries](/previous-versions/azure/azure-services/gg185950(v=azure.100))」(ドメインの境界を越えてサービスを利用できるようにする) をご覧ください。 設定は、Smooth Streaming にのみ適用されます。
- `customHostNames`:カスタム ホスト名に転送されたトラフィックを受け入れるようにストリーミング エンドポイントを構成するために使用されます。 このプロパティは Standard と Premium のストリーミング エンドポイントで有効であり、`cdnEnabled`: false のときに設定できます。

    ドメイン名の所有権は、Media Services によって確認される必要があります。 Media Services は、使用中のドメインに追加されるコンポーネントとして Media Services アカウント ID が含まれる `CName` レコードを要求することで、ドメイン名の所有権を検証します。 例として、ストリーミング エンドポイントのカスタム ホスト名として "sports.contoso.com" が使用されるには、Media Services の検証ホスト名の 1 つにポイントするよう、`<accountId>.contoso.com` のレコードを構成する必要があります。 検証ホスト名は、verifydns.\<mediaservices-dns-zone> で構成されます。

    さまざまな Azure リージョンでのレコードの検証で使用されることが想定される DNS ゾーンを次に示します。
  
  - 北米、ヨーロッパ、シンガポール、香港特別行政区、日本:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 中国:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    たとえば、"945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" を "verifydns.media.azure.net" にマップする `CName` レコードは、Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad が contoso.com ドメインの所有権を持っていることを証明することによって、contoso.com の下の任意の名前を、そのアカウントの下のストリーミング エンドポイントのカスタム ホスト名として使用できるようになります。 メディア サービス ID の値は、[Azure portal](https://portal.azure.com/) に移動してお使いのメディア サービス アカウントを選択することで確認できます。 **アカウント ID** はページの右上に表示されます。

    `CName` レコードの適切な検証なしでカスタム ホスト名を設定しようとした場合、DNS 応答は失敗し、その後しばらくの間キャッシュされます。 適切なレコードが配置されると、キャッシュされた応答が再検証されるまで少し時間がかかる場合があります。 カスタム ドメインの DNS プロバイダーによっては、レコードの再検証に数分から 1 時間ほどかかります。

    `<accountId>.<parent domain>` を `verifydns.<mediaservices-dns-zone>` にマップする `CName` に加え、カスタム ホスト名 (例: `sports.contoso.com`) を Media Services ストリーミング エンドポイントのホスト名 (例: `amstest-usea.streaming.media.azure.net`) にマップする別の `CName` も作成する必要があります。

    > [!NOTE]
    > 同じデータ センターにあるストリーミング エンドポイントが同じカスタム ホスト名を共有することはできません。

    現在、Media Services ではカスタム ドメインを指定した TLS はサポートされていません。

- `maxCacheAge` - メディア フラグメントとオンデマンドのマニフェスト上のストリーミング エンドポイントで設定されている、既定の max-age HTTP キャッシュ コントロール ヘッダーをオーバーライドします。 値は秒単位で設定されます。
- `resourceState` -

    - Stopped (停止済み): 作成後のストリーミング エンドポイントの初期状態
    - Starting (開始中): 実行中の状態に移行しています
    - Running (実行中): コンテンツをクライアントにストリーミングできます
    - Scaling (スケーリング中): スケール ユニットが増加または減少しています
    - Stopping (停止中): 停止済みの状態に移行しています
    - Deleting (削除中): 削除されているところです

- `scaleUnits`:200 Mbps の増分で購入できる専用の送信容量を提供します。 **Premium** 型に移行する必要がある場合は、`scaleUnits` を調整します。

## <a name="why-use-multiple-streaming-endpoints"></a>複数のストリーミング エンドポイントを使用する理由

1 つのストリーミング エンドポイントでライブ ビデオとオンデマンド ビデオの両方をストリーミングでき、ほとんどの顧客は 1 つのストリーミング エンドポイントしか使用していません。 このセクションでは、複数のストリーミング エンドポイントを使用する必要がある理由について、いくつかの例を示します。

* 予約ユニットごとに 200 Mbps の帯域幅が許容されています。 2,000 Mbps (2 Gbps) を超える帯域幅が必要な場合は、2 番目のストリーミング エンドポイントと負荷分散を使用して、追加の帯域幅を提供できます。

    ただし、CDN はストリーミング コンテンツのスケールアウトを実現するのに最適な方法ですが、CDN がプルしている 2 Gbps を超える大量のコンテンツを配信する場合は、追加のストリーミング エンドポイント (配信元) を追加することができます。 この場合は、2 つのストリーミング エンドポイント間でバランスが取れたコンテンツ URL を渡す必要があります。 この方法では、各配信元に要求を (たとえば、トラフィック マネージャー経由で) ランダムに送信するよりも、キャッシュが向上します。 
    
    > [!TIP]
    > 通常、CDN が 2 Gbps を超えてプルしている場合は、何かが正しく構成されていない可能性があります (たとえば配信元のシールドがないなど)。
    
* さまざまな CDN プロバイダーを負荷分散します。 たとえば、Verizon CDN を使用するように既定のストリーミング エンドポイントを設定し、Akamai を使用するように 2 つ目のストリーミング エンドポイントを作成することができます。 次に、2 つの間に負荷分散を追加して、マルチ CDN 分散を実現します。 

    ただし、多くの場合、顧客は 1 つの配信元を使用して複数の CDN プロバイダー間で負荷分散を行います。
* 混合コンテンツのストリーミング:ライブとビデオ オン デマンド。 

    ライブとオンデマンドのコンテンツのアクセス パターンは大きく異なります。 ライブ コンテンツは、同じコンテンツに対して多くの要求を一度に受け取る傾向があります。 ビデオ オンデマンド コンテンツ (インスタンスのロングテール アーカイブ コンテンツ) では、同じコンテンツの使用率は低くなります。 そのため、キャッシュはライブ コンテンツに対しては非常に有効ですが、ロングテール コンテンツには適していません。

    顧客は、ライブ コンテンツを主に視聴していて、オンデマンド コンテンツはたまに視聴するだけで、それを同じストリーミング エンドポイントから提供しているシナリオについて考えてみましょう。 使用率の低いオンデマンド コンテンツによって、ライブ コンテンツ用に残しておいた方がよいキャッシュ領域が占有される可能性があります。 このシナリオでは、1 つのストリーミング エンドポイントからライブ コンテンツを提供し、別のストリーミング エンドポイントからロングテール コンテンツを提供することをお勧めします。 これにより、ライブ イベント コンテンツのパフォーマンスが向上します。
    
## <a name="scaling-streaming-with-cdn"></a>CDN を使用したストリーミングのスケーリング

次の記事をご覧ください。

- [CDN の概要](../../cdn/cdn-overview.md)
- [CDN を使用したストリーミングのスケーリング](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>質問する、および更新を取得する

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="see-also"></a>関連項目

[ダイナミック パッケージ](dynamic-packaging-overview.md)

## <a name="next-steps"></a>次のステップ

[ストリーミング エンドポイントの管理](manage-streaming-endpoints-howto.md)
