---
title: コンテンツ配信の種類に応じて Azure CDN を最適化する
description: Azure Content Delivery Network でコンテンツの種類に基づいて配信を最適化する方法について説明します。 最適化のベスト プラクティスによりパフォーマンスと配信元の負荷軽減が向上します。
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 3d207ee09a76509a65a2645515b182f8d92753b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387922"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>コンテンツ配信の種類に応じて Azure CDN を最適化する

世界中の多数のユーザーにコンテンツを配信する場合、コンテンツの配信を確実に最適化することが重要です。 [Azure Content Delivery Network (CDN)](cdn-overview.md) を使用すると、コンテンツの種類に応じて最適な配信を行うことができます。 コンテンツには、Web サイト、ライブ ストリーム、動画、ダウンロード用のサイズの大きなファイルなどがあります。 CDN エンドポイントを作成するとき、**[最適化の対象]** オプションでシナリオを指定します。 この選択によって、CDN エンドポイントから提供されるコンテンツに適用される最適化が決まります。

最適化の選択肢は、ベスト プラクティス動作を使用して、コンテンツ配信のパフォーマンスと配信元のオフロードを向上するように設計されています。 シナリオの選択内容は、部分的なキャッシュ、オブジェクトのチャンキング、配信元のエラー再試行ポリシーの構成を変更することによってパフォーマンスに影響します。 

この記事では、さまざまな最適化機能と、これらをいつ使用すればよいかについての概要を示します。 機能と制限事項の詳細については、個々の最適化の種類に関する各ドキュメントをご覧ください。

> [!NOTE]
> CDN エンドポイントを作成するときに、**[最適化の対象]** オプションは、エンドポイントを作成するプロファイルの種類に基づいて変わる場合があります。 Azure CDN プロバイダーは、シナリオに応じて、さまざまな方法で拡張機能を適用します。 

## <a name="provider-options"></a>プロバイダー オプション

**Azure CDN Standard from Microsoft** プロファイルでは、次の最適化がサポートされます。

* [一般的な Web 配信](#general-web-delivery)。 この最適化は、メディア ストリーミングと大きなファイルのダウンロードにも使用できます。

> [!NOTE]
> Microsoft の動的サイト アクセラレーションは、[Azure Front Door Service](../frontdoor/front-door-overview.md) により提供されます。

**Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルでは、次の最適化がサポートされます。

* [一般的な Web 配信](#general-web-delivery)。 この最適化は、メディア ストリーミングと大きなファイルのダウンロードにも使用できます。

* [動的サイト アクセラレーション](#dynamic-site-acceleration) 


**Azure CDN Standard from Akamai** プロファイルでは、次の最適化がサポートされます。

* [一般的な Web 配信](#general-web-delivery) 

* [一般的なメディア ストリーミング](#general-media-streaming)

* [ビデオ オン デマンド メディア ストリーミング](#video-on-demand-media-streaming)

* [大きなファイルのダウンロード](#large-file-download)

* [動的サイト アクセラレーション](#dynamic-site-acceleration) 

プロバイダー間のパフォーマンスのバラツキをテストして、対象の配信に最適なプロバイダーを選択することをお勧めします。

## <a name="select-and-configure-optimization-types"></a>最適化の種類の選択と構成

CDN エンドポイントを作成するときに、エンドポイントが配信するコンテンツの種類とシナリオに最も適した最適化の種類を選択します。 

**General web delivery** (一般的な Web 配信) が既定値です。 **Azure CDN Standard from Akamai** エンドポイントの最適化のみ、いつでも更新できます。 

**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Verizon**、**Azure CDN Premium from Verizon** の場合はできません。

1. **Azure CDN Standard from Akamai** プロファイルで、エンドポイントを選択します。

    ![エンドポイントの選択](./media/cdn-optimization-overview/01_Akamai.png)

2. [設定] で、 **[最適化]** を選択します。 **[最適化の種類]** ドロップダウン リストで種類を選択します。

    ![最適化と種類の選択](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>特定のシナリオの最適化

次のいずれかのシナリオの CDN エンドポイントを最適化できます。 

### <a name="general-web-delivery"></a>一般的な Web 配信

一般的な Web 配信は、最も一般的な最適化オプションです。 これは、Web ページや Web アプリケーションなど、一般的な Web コンテンツの最適化用に設計されています。 この最適化は、ファイルやビデオのダウンロードにも使用できます。

一般的な Web サイトには、静的コンテンツと動的コンテンツが含まれています。 静的コンテンツには、イメージ、JavaScript ライブラリ、スタイル シートが含まれています。これらは、キャッシュしてさまざまなユーザーに配信できます。 動的コンテンツは、ユーザー プロファイルに合わせたニュース項目など、個々のユーザーにカスタマイズされています。 ショッピング カートの内容などの動的コンテンツは、ユーザーごとに一意であるため、キャッシュされません。 一般的な Web 配信は、Web サイト全体を最適化できます。 

> [!NOTE]
> **Azure CDN Standard from Akamai** プロファイルを使用していて、平均ファイル サイズが 10 MB より小さい場合には、この最適化の種類を選択します。 それ以外の場合、平均ファイル サイズが 10 MB より大きい場合は、**[最適化の対象]** ドロップダウン リストから **[大きなファイルのダウンロード]** を選択します。

### <a name="general-media-streaming"></a>一般的なメディア ストリーミング

ライブ ストリーミングとビデオ オン デマンド ストリーミングの両方のエンドポイントを使用する必要がある場合は、一般的なメディア ストリームの最適化の種類を選択します。

メディア ストリーミングは時間に厳しく、クライアントへのパケットの到着が遅れるとビデオ コンテンツのバッファリングが頻繁に発生するなど画像の品質が低下します。 メディア ストリーミングの最適化は、メディア コンテンツ配信の遅延を低減し、ユーザーにスムーズなストリーミングを提供します。 

これは、Azure メディア サービスのお客様に一般的に当てはまるシナリオです。 Azure メディア サービスを使用する場合、ライブとオン デマンド ストリーミングの両方に使用できる 1 つのストリーミング エンドポイントを取得します。 このシナリオでは、お客様は、ライブからオン デマンド ストリーミングに変更するときに別のエンドポイントに切り替える必要はありません。 一般的なメディア ストリーミングの最適化は、この種類のシナリオがサポートします。

**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Verizon**、および **Azure CDN Premium from Verizon** については、一般的な Web 配信の最適化の種類を使用して、一般的なストリーミング メディア コンテンツを配信します。

メディア ストリーミングの最適化の詳細については、[メディア ストリーミングの最適化](cdn-media-streaming-optimization.md)に関するページをご覧ください。

### <a name="video-on-demand-media-streaming"></a>ビデオ オン デマンド メディア ストリーミング

ビデオ オン デマンド (VOD) メディア ストリーミングの最適化は、ビデオ オン デマンドのストリーミング コンテンツを向上します。 ビデオ オン デマンド ストリーミングのエンドポイントを使用する場合は、このオプションを使用します。

**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Verizon**、および **Azure CDN Premium from Verizon** の各プロファイルについては、一般的な Web 配信の最適化の種類を使用して、ビデオ オンデマンド ストリーミング メディア コンテンツを配信します。

メディア ストリーミングの最適化の詳細については、[メディア ストリーミングの最適化](cdn-media-streaming-optimization.md)に関するページをご覧ください。

> [!NOTE]
> CDN エンドポイントが主にビデオ オンデマンドのコンテンツを処理する場合は、この種類の最適化を使用します。 この最適化の種類と一般的なメディア ストリーミングの最適化の種類の主な違いは、接続の再試行タイムアウトです。ライブ ストリーミング シナリオの場合、非常に短い時間でタイムアウトします。
>

### <a name="large-file-download"></a>大容量ファイルのダウンロード

**Azure CDN Standard from Akamai** プロファイルについては、大容量ファイルのダウンロードが、10 MB より大きいコンテンツに対して最適化されています。 平均ファイル サイズが 10 MB より小さい場合は、一般的な Web 配信を使用してください。 平均ファイル サイズが常に 10 MB より大きい場合、大容量ファイル用に個別のエンドポイントを作成する方がより効率的な場合があります。 たとえば、ファームウェアやソフトウェアの更新プログラムは、通常、サイズの大きなファイルになります。 1.8 GB より大きいファイルを配信するには、大容量ファイルのダウンロードの最適化が必要です。

**Azure CDN Standard from Microsoft**、**Azure CDN Standard from Verizon**、および **Azure CDN Premium from Verizon** の各プロファイルについては、一般的な Web 配信の最適化の種類を使用して、大容量ファイルのダウンロード コンテンツを配信します。 ファイルのダウンロード サイズに制限はありません。

大きなファイルの最適化の詳細については、「[大きなファイルの最適化](cdn-large-file-optimization.md)」をご覧ください。

### <a name="dynamic-site-acceleration"></a>動的サイト アクセラレーション

 動的サイト アクセラレーション (DSA) は、**Azure CDN Standard from Akamai** プロファイル、**Azure CDN Standard from Verizon** プロファイル、および **Azure CDN Premium from Verizon** プロファイルで使用できます。 この最適化を使用すると追加料金が発生します。詳細については、「[Content Delivery Network の価格](https://azure.microsoft.com/pricing/details/cdn/)」を参照してください。

> [!NOTE]
> Microsoft の動的サイト アクセラレーションは、Microsoft のプライベート グローバル ネットワークを利用してご自身のアプリのワークロードを提供するグローバル [エニー キャスト](https://en.wikipedia.org/wiki/Anycast) サービス、[Azure Front Door Service](../frontdoor/front-door-overview.md) によって提供されます。

DSA には、動的なコンテンツの遅延とパフォーマンスを改善するさまざまな技術が取り入れられています。 それらの技術には、ルートとネットワークの最適化、TCP の最適化などがあります。 

この最適化で、キャッシュ不可能な多数の応答を含む Web アプリを高速化することができます。 たとえば、検索結果、チェック アウト トランザクション、リアルタイム データなどを高速化できます。 静的なデータでは、主要な Azure CDN のキャッシュ機能を継続して使用することができます。 

動的サイトの高速化の詳細については、[動的サイトの高速化](cdn-dynamic-site-acceleration.md)に関するページを参照してください。