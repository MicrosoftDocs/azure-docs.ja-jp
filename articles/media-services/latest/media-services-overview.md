---
title: Azure Media Services v3 の概要
titleSuffix: Azure Media Services
description: Azure Media Services v3 の概要と、クイック スタート、チュートリアル、コード サンプルへのリンク。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: mvc
ms.openlocfilehash: b11cd602850d23ac6ec37a9be51912adceb9afbc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267617"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 の概要

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

クラウドベースのプラットフォームである Azure Media Services では、ブロードキャスト品質のビデオ ストリーミングを実現し、アクセス性と配信を強化し、コンテンツを分析するソリューションを構築できます。 アプリ開発者、コール センター、政府機関、またはエンターテイメント会社のいずれであっても、Media Services を利用すると、今日の最も一般的なモバイル デバイスとブラウザーを使用する多くのユーザーに、優れた品質のメディア エクスペリエンスを提供するアプリを作成できます。

Media Services v3 SDK は [Media Services v3 OpenAPI の仕様 (Swagger)](https://aka.ms/ams-v3-rest-sdk) に基づいています。

> [!NOTE]
> 現在、[Azure portal](https://portal.azure.com/) を使用して、Media Services v3 の[ライブ イベント](live-events-outputs-concept.md)の管理、v3 の[資産](assets-concept.md)の表示 (管理ではない)、[API へのアクセスに関する情報の取得](./access-api-howto.md)を行うことができます。 他のすべての管理タスク ([変換とジョブ](transforms-jobs-concept.md)や[コンテンツ保護](content-protection-overview.md)など) については、[REST API](/rest/api/media/)、[CLI](https://aka.ms/ams-v3-cli-ref)、またはサポートされているいずれかの [SDK](media-services-apis-overview.md#sdks) を使用します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>コンプライアンス、プライバシー、セキュリティ

重要な注意事項として、Azure Media Services を使用する際は、適用されるすべての法律に従う必要があります。また、他者の権利を侵害したり、他者に害を及ぼしたりする可能性のある方法で Media Services またはその他の Azure サービスを使用することはできません。

Media Services にビデオまたは画像をアップロードする前に、ビデオまたは画像を使用するための適切な権限をすべて取得している必要があります。これには、法律で義務づけられている場合において、ビデオまたは画像に含まれる人物から、Media Services および Azure でのデータの使用、処理、および保存について、必要なすべての同意を得ることが含まれます (このような人物が存在する場合)。 一部の法的管轄区域では、生体認証データなどの特定のカテゴリのデータの収集、オンライン処理、および保管に関して特別な法的要件が課せられる場合があります。 特別な法的要件の対象となるデータの処理と保管のために Media Services と Azure を使用する前に、適用される可能性のあるこのような法的要件に準拠していることを確認する必要があります。

Media Services のコンプライアンス、プライバシー、セキュリティについては、Microsoft [セキュリティ センター](https://www.microsoft.com/trust-center/?rtc=1)にアクセスしてください。 Microsoft のプライバシー義務、データの取り扱いと保持の慣行 (データの削除方法など) については、Microsoft の[プライバシー ステートメント](https://privacy.microsoft.com/PrivacyStatement)、[オンライン サービス規約](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("OST")、および[データ処理の補遺](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA") に関するページをご確認ください。 Media Services を使用することによって、OST、DPA およびプライバシー ステートメントに従うことに同意したものと見なされます。
 
## <a name="what-can-i-do-with-media-services"></a>Media Services の機能

Media Services を使用すると、クラウドでさまざまなメディア ワークフローを構築できます。 Media Services でできることの例を次に示します。

* 広範なブラウザーやデバイスで再生できるように、さまざまな形式のビデオを提供します。 さまざまなクライアント (モバイル デバイス、TV、PC など) にオンデマンドとライブ ストリーミングの両方を提供するには、ビデオ コンテンツとオーディオ コンテンツを適切にエンコードしてパッケージ化する必要があります。 このようなコンテンツを配信およびストリーミングする方法については、[ファイルのエンコードとストリームに関するクイック スタート](stream-files-dotnet-quickstart.md)を参照してください。
* サッカー、野球、大学や高校のスポーツなど、多数のオンライン視聴者にライブ スポーツ イベントをストリーム配信します。
* 町役場、市議会の会議、立法機関などの公開の会議やイベントをブロードキャストします。
* 録画されたビデオやオーディオ コンテンツを分析します。 たとえば、より高い顧客満足度を実現するため、音声からテキストを抽出して、検索インデックスやダッシュボードを作成できます。 これにより、一般的な苦情、苦情の原因、その他の関連データに関する知見を引き出すことができます。
* 顧客 (たとえば映画スタジオ) が独自の著作権所有作品のアクセスや使用を制限する必要がある場合は、サブスクリプション ビデオ サービスを作成して、DRM で保護されたコンテンツをストリーム配信します。
* 飛行機、列車、自動車で再生するためのオフライン コンテンツを提供します。 顧客は、ネットワークから切断される可能性があるときは、携帯電話やタブレットにコンテンツをダウンロードして再生する必要があります。
* 音声からのテキスト キャプション作成、多言語への翻訳などのために、Azure Media Services と [Azure Cognitive Services APIs](../../index.yml?pivot=products&panel=ai) で教育用 E ラーニング ビデオ プラットフォームを実装します。
* Azure Media Services を [Azure Cognitive Services APIs](../../index.yml?pivot=products&panel=ai) と共に使用して、より広範な視聴者 (たとえば、聴覚障碍を持つ人や、別の言語で読みたい人など) に対応できるよう、ビデオに字幕とキャプションを追加します。
* 瞬間的高負荷 (製品発表イベントの開始時など) を処理しやすくする大規模なスケーリングを Azure CDN が実現できるようにします。

## <a name="how-can-i-get-started-with-v3"></a>v3 の利用を始める方法 

Media Services v3 を使用して、コンテンツのエンコードとパッケージ化、オンデマンドでのビデオのストリーム配信、ライブ ブロードキャスト、ビデオの分析を行う方法について学習します。 チュートリアルや API リファレンスなどのドキュメントでは、ビデオ ストリーミングやオーディオ ストリーミングをオンデマンドやライブで安全に行い、数百万規模のユーザーに拡張する方法を紹介します。

> [!TIP]
> 開発を開始する前に、以下を確認してください。<br/>* [基本的な概念](concepts-overview.md) (パッケージ化、エンコード、保護などの重要な概念を含む)<br/>* [Media Services v3 API を使用した開発](media-services-apis-overview.md) (API へのアクセスや名前付け規則などに関する情報を含む)

### <a name="sdks"></a>SDK

[Azure Media Services v3 クライアント SDK](media-services-apis-overview.md#sdks) を使用して開発を始めます。

### <a name="quickstarts"></a>クイックスタート  

このクイック スタートでは、新しいお客様が Media Services を簡単に試すことができるよう、基礎について 1 日で説明します。

* [動画ファイルのストリーム配信 - .NET](stream-files-dotnet-quickstart.md)
* [動画ファイルのストリーム配信 - CLI](stream-files-cli-quickstart.md)
* [動画ファイルのストリーム配信 - Node.js](stream-files-nodejs-quickstart.md)

### <a name="tutorials"></a>チュートリアル

このチュートリアルでは、Media Services でよく行われるタスクについて、シナリオベースで手順を示します。

* [リモート ファイルのエンコードとビデオのストリーミング – REST](stream-files-tutorial-with-rest.md)
* [アップロードされたファイルのエンコードとビデオのストリーム配信 - .NET](stream-files-tutorial-with-api.md)
* [ライブ ストリーム配信 - .NET](stream-live-tutorial-with-api.md)
* [ビデオの分析 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動的暗号化 - .NET](protect-with-aes128.md)

### <a name="samples"></a>サンプル

[このサンプル ブラウザー](/samples/browse/?products=azure-media-services)を使用して、Azure Media Services のコード サンプルを参照します。

### <a name="how-to-guides"></a>操作方法ガイド

タスクの完了方法を示すコード サンプルを含むハウツー ガイド。 このセクションでは、多くの例を見つけることができます。 いくつかを次に示します。

* [アカウントの作成 - CLI](./create-account-howto.md)
* [API へのアクセス - CLI](./access-api-howto.md)
* [HTTPS をジョブの入力とするエンコード - .NET](job-input-from-http-how-to.md)  
* [イベントの監視 - ポータル](monitor-events-portal-how-to.md)
* [マルチ DRM を使用した動的な暗号化 - .NET](protect-with-drm.md) 
* [カスタム変換を使用してエンコードする方法 - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

[基本的な概念を理解する](concepts-overview.md)
