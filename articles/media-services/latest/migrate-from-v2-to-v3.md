---
title: Azure Media Services v2 から v3 への移行 | Microsoft Docs
description: この記事では、Azure Media Services v3 で導入された変更点と、2 つのバージョンの違いについて説明します。 Media Services v2 から v3 への移行のガイダンスも提供します。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b4e79a2aab5ca72ff8263bfc5734757bbff41005
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297741"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>Media Services v2 から v3 への移行のガイダンス

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>この URL (`https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us`) をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

この記事では、Media Services v2 から v3 への移行のガイダンスも提供します。

もしあなたが今日、[従来の Media Services v2 API](../previous/media-services-overview.md) 上に動画サービスを開発した場合、v3 API に移行する前に次のガイドラインと考慮事項を確認してください。 v3 API には Media Services の開発者エクスペリエンスと機能を向上させる多くのメリットおよび新機能があります。 ただし、この記事の「[既知の問題](#known-issues)」セクションで示すように、API バージョン間の変更によるいくつかの制限事項もあります。 このページは、v3 API に対して Media Services チームが継続的な改善を行い、バージョン間の差異に対処するたびに更新されます。 

## <a name="prerequisites"></a>前提条件

* 「[Media Services v2 対 v3](media-services-v2-vs-v3.md)」を確認してください
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>Media Services v3 のメリット
  
### <a name="api-is-more-approachable"></a>API がわかりやすい

*  v3 は、Azure Resource Manager 上に構築された管理と操作の両方の機能を公開する統一された API サーフェスに基づいています。 Azure Resource Manager テンプレートを使って、変換、ストリーミング エンドポイント、ライブ イベントなどを作成して展開できます。
* [OpenAPI の仕様 (以前は Swagger と呼ばれていた)](https://aka.ms/ams-v3-rest-sdk) に関するドキュメント。
    ファイル ベースのエンコードを含むすべてのサービス コンポーネントのスキーマを公開します。
* [.NET](https://aka.ms/ams-v3-dotnet-ref)、.Net Core、[Node.js](/javascript/api/overview/azure/mediaservices/management)、[Python](https://aka.ms/ams-v3-python-ref)、[Java](https://aka.ms/ams-v3-java-ref)、[Go](https://aka.ms/ams-v3-go-ref)、およびRuby で利用できる SDK。
* 単純なスクリプト サポートのための [Azure CLI](https://aka.ms/ams-v3-cli-ref) 統合。

### <a name="new-features"></a>新機能

* ファイル ベースのジョブ処理では、入力として HTTP (S) URL を使用できます。<br/>Azure にコンテンツをあらかじめ格納しておく必要がなく、アセットを作成する必要もありません。
* ファイル ベースのジョブ処理のための[変換](transforms-jobs-concept.md)の概念が導入されました。 変換を使用して、再利用可能な構成を構築し、Azure Resource Manager テンプレートを作成し、複数の顧客またはテナント間の処理の設定を分離することができます。
* 1 つのアセットで、[ダイナミック パッケージ](dynamic-packaging-overview.md)と動的暗号化の設定が異なる複数の[ストリーミング ロケーター](streaming-locators-concept.md)を持つことができます。
* [コンテンツ保護](content-key-policy-concept.md)はマルチキー機能をサポートしています。
* Media Services を使用してシングル ビットレートのコントリビューション フィードをマルチ ビットレートの出力ストリームにコード変換すると、最大 24 時間のライブ イベントをストリーミングできます。
* ライブ イベントに対する新しい低待機時間ライブ ストリーミング サポート。 詳しくは、「[待機時間](live-event-latency.md)」をご覧ください。
* ライブ イベント プレビューは、[ダイナミック パッケージ](dynamic-packaging-overview.md)と動的暗号化をサポートしています。 そのため、プレビューだけでなく、DASH および HLS パッケージに対するコンテンツ保護が可能です。
* ライブ出力は、v2 API の Program エンティティよりも使用方法が簡単です。 
* RTMP サポートの強化 (安定性の向上およびソース エンコーダー サポートの強化)。
* RTMPS のセキュアな取り込み。<br/>ライブ イベントを作成すると、4 つの取り込み URL を取得します。 4 つの取り込み URL はほとんど同じで。ストリーミング トークン (AppId) は同じですが、ポート番号の部分のみが異なります。 URL のうち 2 つは RTMPS のプライマリとバックアップです。   
* エンティティに対するロールベースのアクセス制御 (RBAC) があります。 

## <a name="known-issues"></a>既知の問題

*  現時点では、[Azure portal](https://portal.azure.com/) を使って次のことを実行できます。

    * Media Services v3 の[ライブ イベント](live-events-outputs-concept.md)を管理する 
    * v3 の[アセット](assets-concept.md)を表示する (管理ではない) 
    * [API のアクセスに関する情報を取得する](./access-api-howto.md)。 

    他のすべての管理タスク ([変換とジョブ](transforms-jobs-concept.md)や[コンテンツ保護](content-protection-overview.md)など) については、[REST API](/rest/api/media/)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。
* ジョブ (特にビデオまたはオーディオ分析を伴うもの) のコンカレンシーとパフォーマンスを制御するには、アカウントでメディア占有ユニット (MRU) をプロビジョニングする必要があります。 詳細については、[メディア処理のスケール設定](../previous/media-services-scale-media-processing-overview.md)に関するページを参照してください。 MRU の管理には、[CLI 2.0 for Media Services v3](media-reserved-units-cli-how-to.md)、[Azure portal](../previous/media-services-portal-scale-media-processing.md)、または [v2 API](../previous/media-services-dotnet-encoding-units.md) を使用できます。 Media Services v2 と v3 のどちらの API を使用する場合でも、MRU をプロビジョニングする必要があります。
* v3 API を使用して作成された Media Services エンティティは v2 API で管理できません。  
* V2 API 内のすべてのエンティティが V3 API に自動的に表示されるわけではありません。  2 つのバージョンで互換性のないエンティティの例を次に示します。  
    * v2 で作成されたジョブと タスクは変換に関連付けられていないため、v3 で表示されません。 v3 の変換およびジョブに切り替えることをお勧めします。 切り替え中は、処理中の v2 ジョブを比較的短い期間でモニターする必要があります。
    * v2 で作成されたチャンネルとプログラム (v3 でライブ イベントとライブ出力にマップされる) は、v3 での管理を続行できません。 適切なタイミングでチャンネルを停止して、v3 のライブ イベントとライブ出力に切り替えることをお勧めします。<br/>現時点では、実行を継続中のチャネルを移行することはできません。  

> [!NOTE]
> このページは、v3 API に対して Media Services チームが継続的な改善を行い、バージョン間の差異に対処するたびに更新されます。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

[チュートリアル:リモート ファイルを URL に基づいてエンコードし、ビデオをストリーム配信する - .NET](stream-files-dotnet-quickstart.md)
