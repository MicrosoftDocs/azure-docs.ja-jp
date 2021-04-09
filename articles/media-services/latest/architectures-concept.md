---
title: Media Services アーキテクチャ
description: この記事では、Media Services のアーキテクチャについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: inhenkel
ms.openlocfilehash: ad464eb1c0b6dec694c7c40868a0f95fcfeaf6e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98891490"
---
# <a name="media-services-architectures"></a>Media Services アーキテクチャ

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="live-streaming-digital-media"></a>デジタル メディアのライブ ストリーミング

ライブ ストリーミング ソリューションを使用すると、ビデオをリアルタイムでキャプチャし、ストリーミング インタビュー、カンファレンス、スポーツ イベントなどをリアルタイムでコンシューマーに配信できます。 このソリューションでは、ビデオはビデオ カメラによってキャプチャされ、チャネル入力エンドポイントに送信されます。 チャネルは、ライブ入力ストリームを受け取り、ストリーミング エンドポイントを介して Web ブラウザーやモバイルアプリでストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、チャネルはその際に使用するプレビュー監視エンドポイントも提供します。 さらにチャネルは後でストリーミングするために、取り込んだコンテンツの記録と保存もできます (ビデオ オン デマンド)。

このソリューションは、以下の Azure マネージド サービスに基づいて構築されています。Media Services と Content Delivery Network。 これらのサービスは高可用性環境で実行され、パッチが適用され、サポートされているので、実行される環境ではなく、ソリューションに注力できます。

Azure アーキテクチャ センターの「[デジタルメディアのライブス トリーミング](/azure/architecture/solution-ideas/articles/digital-media-live-stream)」を参照してください。

## <a name="video-on-demand-digital-media"></a>ビデオ オンデマンド デジタル メディア

動画、ニュース クリップ、スポーツ セグメント、トレーニング ビデオ、カスタマー サポート チュートリアルなど、記録されたビデオ コンテンツを、ビデオ対応のエンドポイント デバイス、モバイル アプリケーション、またはデスクトップ ブラウザーにストリーミングする機能を提供する基本的なビデオ オン デマンド ソリューションです。 ビデオ ファイルは、Azure BLOB ストレージにアップロードされ、マルチ ビットレートの標準形式にエンコードされた後、すべての主要なアダプティブ ビットレート ストリーミング プロトコル (HLS、MPEG-DASH、Smooth) を介して Azure Media Player クライアントに配布されます。

このソリューションは、以下の Azure で管理されるサービスに基づいて構築されています。Blob Storage、Content Delivery Network および Azure Media Player です。 これらのサービスは高可用性環境で実行され、パッチが適用され、サポートされているので、実行される環境ではなく、ソリューションに注力できます。

Azure アーキテクチャ センターの「[ビデオ オンデマンド デジタル メディア](/azure/architecture/solution-ideas/articles/digital-media-video)」を参照してください。

## <a name="gridwich-media-processing-system"></a>Gridwich メディア処理システム

Gridwich システムには、Azure でメディア資産を処理および配信するためのベスト プラクティスが実装されています。 Gridwich システムはメディア専用ですが、メッセージ処理およびイベント フレームワークは、任意のステートレス イベント処理ワークフローに適用できます。

Azure アーキテクチャ センターの「[Gridwich メディア処理システム](/azure/architecture/reference-architectures/media-services/gridwich-architecture)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [Azure Media Services の概要](media-services-overview.md)