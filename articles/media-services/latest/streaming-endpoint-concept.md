---
title: Azure Media Services のストリーミング エンドポイント (配信元) | Microsoft Docs
description: Azure Media Services では、ストリーミング エンドポイント (配信元) は、コンテンツをクライアント プレーヤー アプリケーションや、再配布のための Content Delivery Network (CDN) に直接配信できるダイナミック パッケージおよびストリーミング サービスを表します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: 831ba217e99d1610383320ddf5706c6acfcdf48a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848904"
---
# <a name="streaming-endpoints"></a>ストリーミング エンドポイント 

Microsoft Azure Media Services では、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints)は、ダイナミック (Just-In-Time) パッケージおよび配信元サービスを表します。これは、いずれかの一般的なストリーミング メディア プロトコル (HLS または DASH) を使用して、ライブのオンデマンド コンテンツをクライアント プレーヤー アプリケーションに直接配信できます。 また、**ストリーミング エンドポイント**は、業界最先端の DRM に動的 (Just-In-Time) 暗号化を提供します。

Media Services アカウントを作成すると、**既定**のストリーミング エンドポイントが停止状態で作成されます。 **既定**のストリーミング エンドポイントは削除できません。 このアカウントで追加のストリーミング エンドポイントを作成できます ([クォータと制限](limits-quotas-constraints.md)に関する記事を参照)。 

> [!NOTE]
> ビデオのストリーミングを開始するには、ビデオをストリーミングする**ストリーミング エンドポイント**を開始する必要があります。 
>  
> ストリーミング エンドポイントが実行状態の場合のみ課金されます。

## <a name="naming-convention"></a>命名規則

既定のエンドポイントの場合: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

追加エンドポイントの場合: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>型  

**ストリーミング エンドポイント**には、**Standard** (プレビュー) と **Premium** の 2 つの型があります。 型は、ストリーミング エンドポイントに割り当てられたスケール ユニットの数 (`scaleUnits`) によって定義されます。 

次の表で、型について説明します。  

|Type|スケール ユニット|説明|
|--------|--------|--------|  
|**Standard**|0|既定のストリーミング エンドポイントは **Standard** 型ですが、`scaleUnits` を調整することで Premium 型に変更できます。|
|**Premium**|>0|**Premium** ストリーミング エンドポイントは専用のスケーラブルな帯域幅の容量を提供するため、高度なワークロードに適しています。 **Premium** 型には、`scaleUnits` (ストリーミング ユニット) を調整することで移行します。 `scaleUnits` は 200 Mbps 単位で購入できる専用の送信容量を提供します。 **Premium** 型を使用するときは、有効になっている各ユニットがアプリケーションに追加の帯域幅容量を提供します。 |

> [!NOTE]
> 多数のインターネットの対象ユーザーにコンテンツを配信することを検討しているお客様には、ストリーミング エンドポイントで CDN を有効にすることをお勧めします。

SLA については、[価格と SLA](https://azure.microsoft.com/pricing/details/media-services/) に関する記事をご覧ください。

## <a name="comparing-streaming-types"></a>ストリーミング タイプの比較

機能|Standard|Premium
---|---|---
スループット |最大 600 Mbps であり、CDN を使用した場合に実効スループットが大幅に向上します。|ストリーミング ユニット (SU) あたり 200 Mbps。 CDN を使用した場合に実効スループットが大幅に向上します。
CDN|Azure CDN、サード パーティ製 CDN、または CDN なし。|Azure CDN、サード パーティ製 CDN、または CDN なし。
課金は日割り計算| 毎日|毎日
動的な暗号化|はい|はい
動的パッケージ|はい|はい
スケール|対象スループットまで自動スケールアップ。|追加の SU
IP フィルタリング/G20/カスタム ホスト  <sup>1</sup>|はい|はい
プログレッシブ ダウンロード|はい|はい
推奨使用量 |大半のストリーミング シナリオに推奨。|プロフェッショナルな使用量。

<sup>1</sup> エンドポイントで CDN が有効でない場合にのみ、ストリーミング エンドポイントで直接使用します。<br/>

## <a name="properties"></a>properties 

このセクションでは、ストリーミング エンドポイントの一部のプロパティについて詳しく説明します。 新しいストリーミング エンドポイントを作成する方法の例と全プロパティの説明については、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)に関する記事をご覧ください。 

- `accessControl` - このストリーミング エンドポイントのセキュリティ設定(このエンドポイントに接続することが許可された Akamai 署名認証キーと IP アドレス) 構成するために使用されます。<br />このプロパティは、`cdnEnabled` が false に設定されているときにのみ設定できます。
- `cdnEnabled` - このストリーミング エンドポイントに対して Azure CDN 統合が有効になっているかどうかを示します (既定では無効)。 `cdnEnabled` を true に設定した場合、`customHostNames` と `accessControl` の構成が無効になります。
  
    すべてのデータ センターが Azure CDN 統合をサポートしているわけではありません。 対象のデータ センターで Azure CDN 統合を利用できるかどうかを確認するには、次の操作を行います。
 
  - `cdnEnabled` を true に設定してみます。
  - \- "Streaming endpoint CdnEnabled property cannot be set to true as CDN capability is not available in the current region"\(現在のリージョンで CDN 機能を利用できないため、ストリーミング エンドポイントの cdnEnabled プロパティを true に設定できません\) というメッセージと共に返された `HTTP Error Code 412` (PreconditionFailed) の結果を確認します。 

    このエラーが発生した場合、そのデータ センターはサポートされていません。 別のデータ センターをお試しください。
- `cdnProfile` - `cdnEnabled` が true に設定されている場合は、`cdnProfile` の値を渡すこともできます。 `cdnProfile` は CDN エンドポイントのポイントが作成される、CDN プロファイルの名前です。 既存の cdnProfile を指定するか、新しいものを使用できます。 値が NULL で `cdnEnabled` が true の場合、既定値 "AzureMediaStreamingPlatformCdnProfile" が使用されます。 指定された `cdnProfile` が既に存在する場合、その下にエンドポイントが作成されます。 プロファイルが存在しない場合は、新しいプロファイルが自動的に作成されます。
- `cdnProvider` - CDN が有効になっている場合は、`cdnProvider` の値を渡すこともできます。 `cdnProvider` は使用されるプロバイダーを制御します。 現時点では、"StandardVerizon"、"PremiumVerizon"、および "StandardAkamai" の 3 つの値がサポートされています。 値が指定されておらず、`cdnEnabled` が true の場合、"StandardVerizon" (既定値) が使用されます。
- `crossSiteAccessPolicies` - さまざまなクライアントのクロス サイト アクセス ポリシーを指定するために使用します。 詳しくは、[Cross-domain ポリシー ファイルの仕様](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)と「[Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)」(ドメインの境界を越えてサービスを利用できるようにする) をご覧ください。<br/>設定は、Smooth Streaming にのみ適用されます。
- `customHostNames` - カスタム ホスト名に送信されるトラフィックを受け入れるようにストリーミング エンドポイントを構成するために使用します。  このプロパティは Standard と Premium のストリーミング エンドポイントで有効であり、`cdnEnabled`: false のときに設定できます。
    
    ドメイン名の所有権は、Media Services によって確認される必要があります。 Media Services は、使用中のドメインに追加されるコンポーネントとして Media Services アカウント ID が含まれる `CName` レコードを要求することで、ドメイン名の所有権を検証します。 例として、ストリーミング エンドポイントのカスタム ホスト名として "sports.contoso.com" が使用されるには、Media Services の検証ホスト名の 1 つにポイントするよう、`<accountId>.contoso.com` のレコードを構成する必要があります。 検証ホスト名は、verifydns.\<mediaservices-dns-zone> で構成されます。 

    さまざまな Azure リージョンでのレコードの検証で使用されることが想定される DNS ゾーンを次に示します。
  
  - 北米、ヨーロッパ、シンガポール、香港特別行政区、日本:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - 中国:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    たとえば、"945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" を "verifydns.media.azure.net" にマップする `CName` レコードは、Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad が contoso.com ドメインの所有権を持っていることを証明することによって、contoso.com の下の任意の名前を、そのアカウントの下のストリーミング エンドポイントのカスタム ホスト名として使用できるようになります。 メディア サービス ID の値は、[Azure portal](https://portal.azure.com/) に移動してお使いのメディア サービス アカウントを選択することで確認できます。 **アカウント ID** はページの右上に表示されます。
        
    `CName` レコードの適切な検証なしにカスタム ホスト名を設定しようとした場合、DNS の応答は失敗し、その後しばらくの間キャッシュされます。 適切なレコードが配置されると、キャッシュされた応答が再検証されるまで少し時間がかかる場合があります。 カスタム ドメインの DNS プロバイダーによっては、レコードの再検証に数分から数時間かかることがあります。
        
    `<accountId>.<parent domain>` を `verifydns.<mediaservices-dns-zone>` にマップする `CName` に加え、カスタム ホスト名 (例: `sports.contoso.com`) を Media Services ストリーミング エンドポイントのホスト名 (例: `amstest-usea.streaming.media.azure.net`) にマップする別の `CName` も作成する必要があります。
 
    > [!NOTE]
    > 同じデータ センターにあるストリーミング エンドポイントで、同じカスタム ホスト名を共有することはできません。

    現在、Media Services ではカスタム ドメインを使用した SSL はサポートされていません。 
    
- `maxCacheAge` - メディア フラグメントとオンデマンドのマニフェスト上のストリーミング エンドポイントで設定されている、既定の max-age HTTP キャッシュ コントロール ヘッダーをオーバーライドします。 値は秒単位で設定されます。
- `resourceState` -

    - Stopped (停止) - ストリーミング エンドポイント作成後の初期状態です。
    - Starting (起動中) - 実行状態に遷移しています。
    - Running (実行中) - クライアントにコンテンツをストリーミングできます。
    - Scaling (スケーリング中) - スケール ユニットを増減中です。
    - Stopping (停止中) - 停止状態に遷移しています。
    - Deleting (削除中) - 削除中です。
    
- `scaleUnits` - 200 Mbps 単位で購入できる専用の送信容量を提供します。 **Premium** 型に移行する必要がある場合は、`scaleUnits` を調整します。

## <a name="working-with-cdn"></a>CDN の操作

CDN はほとんどのケースで有効にしておくことをお勧めします。 ただし、CDN はコンカレンシーに合わせて最適なスケーリングを行うため、最大コンカレンシーが 500 に満たないことが予想される場合は、CDN を無効にすることをお勧めします。

### <a name="considerations"></a>考慮事項

* CDN を有効にするかどうかを問わず、ストリーミング エンドポイント `hostname` とストリーミング URL は同じままにします。
* CDN の有無にかかわらずコンテンツをテストする必要がある場合は、CDN が有効になっていない別のストリーミング エンドポイントを作成できます。

### <a name="detailed-explanation-of-how-caching-works"></a>キャッシュのしくみに関する詳しい説明

CDN を追加するケースに、具体的な帯域幅の値はありません。CDN が有効なストリーミング エンドポイントに必要な帯域幅の量は場合によって変わるためです。 多くはコンテンツの種類、その人気、ビットレート、プロトコルによって変わります。 CDN は要求されたもののみをキャッシュします。 つまり、ビデオ フラグメントがキャッシュされている限り、人気のコンテンツは CDN から直接配信されます。 通常、多くのユーザーがまったく同じものを視聴するライブ コンテンツは、キャッシュされる可能性が高くなります。 人気のコンテンツもあればそうでないものもあるオンデマンドのコンテンツは、やや複雑になる場合があります。 あまり人気のない (視聴者が週に 1 人か 2 人) ビデオ アセットが何百万もあるものの、何千ものユーザーが全員異なるビデオを視聴している場合、CDN はほとんど効力を発揮しません。 このキャッシュ ミスにより、ストリーミング エンドポイントの負荷が高くなります。
 
また、アダプティブ ストリーミングの動作も考慮する必要があります。 個々のビデオ フラグメントはそれぞれのエンティティとしてキャッシュされます。 たとえば、ある特定のビデオが初めて視聴され、そのユーザーがあちこち数秒ずつ見てスキップすることを繰り返している場合、そのユーザーが視聴したビデオに関連するビデオ フラグメントのみが CDN にキャッシュされます。 アダプティブ ストリーミングでは通常、5 から 7 の異なるビットレートのビデオがあります。 あるユーザーがあるビットレートで視聴しており、別のユーザーが異なるビットレートで視聴している場合、CDN ではそれぞれ別個にキャッシュされます。 2 人が同じビットレートで視聴していても、異なるプロトコルでストリーミングしている可能性もあります。 各プロトコル (HLS、MPEG-DASH、スムーズ ストリーミング) は別個にキャッシュされます。 つまり、各ビットレートやプロトコルは別個にキャッシュされ、要求されたビデオ フラグメントのみがキャッシュされます。

### <a name="enable-azure-cdn-integration"></a>Azure CDN 統合を有効にする

CDN を有効にした状態でストリーミング エンドポイントがプロビジョニングされた後、ストリーミング エンドポイントを CDN エンドポイントにマップするための DNS 更新が行われる前に、Media Services に定義されている待機時間があります。

後で CDN を有効/無効にする場合は、ストリーミング エンドポイントを**停止**状態にする必要があります。 Azure CDN 統合が有効になり、すべての CDN POP で変更がアクティブになるまでに、最大で 2 時間かかる場合があります。 ただし、ストリーミング エンドポイントの起動は可能で、ストリーミング エンドポイントから中断なくストリーミングを行うことができます。統合が完了すると、CDN からストリーミングが提供されます。 プロビジョニング期間中はストリーミング エンドポイントが**開始中**の状態になり、パフォーマンスが低下します。

Standard ストリーミング エンドポイントが作成されるとき、既定では Standard Verizon を使用して構成されます。 REST API を使用して、Premium Verizon または Standard Akamai プロバイダーを構成することができます。 

CDN 統合は、中国および連邦政府地域を除くすべての Azure データ センターで有効になります。

> [!IMPORTANT]
> Azure CDN との Azure Media Services 統合は、Standard ストリーミング エンドポイント用の **Azure CDN from Verizon** で実装されます。 Premium ストリーミング エンドポイントは、すべての **Azure CDN 価格レベルとプロバイダー**を使用して構成できます。 Azure CDN 機能の詳細については、「 [CDN の概要](../../cdn/cdn-overview.md)」を参照してください。

### <a name="determine-if-dns-change-has-been-made"></a>DNS の変更が行われたかどうかを確認する

ストリーミング エンドポイントで DNS の変更が行われた (トラフィックが Azure CDN に配信されている) かどうかは、 https://www.digwebinterface.com を使用して確認できます。 その結果に azureedge.net domain の名前が含まれていれば、トラフィックは現在 CDN に配信されています。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの提供、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次の手順

[このリポジトリ](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)のサンプルは、既定のストリーミング エンドポイントを .NET を使用して開始する方法について示します。

