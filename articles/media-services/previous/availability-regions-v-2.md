---
title: Azure Media Services のリージョン別の提供状況 |Microsoft Docs
description: この記事は、Microsoft Azure Media Services の機能とサービスのリージョン別の提供状況の概要を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 125f2be205760618ab6ffa3175f719f5e91e8c05
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103012260"
---
# <a name="media-services-regional-availability"></a>Media Services のリージョン別の提供状況

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 には新機能は追加されません。 最新のバージョンである [Media Services v3](../latest/media-services-overview.md) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-v-2-v-3-migration-introduction.md)を参照してください。

Microsoft Azure Media Services (AMS) を使用すると、各種クライアント (テレビ、PC、モバイル デバイスなど) へのオンデマンド ストリーミング配信とライブ ストリーミング配信の両方で、ビデオやオーディオのコンテンツを安全にアップロード、保存、エンコード、パッケージ化できます。

AMS は世界中の複数のリージョンで動作するため、アプリケーションの構築先を柔軟に選択できます。 この記事は、Microsoft Azure Media Services の機能とサービスのリージョン別の提供状況の概要を示します。

Azure グローバル インフラストラクチャ全体の詳細については、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」を参照してください。

## <a name="ams-accounts"></a>AMS アカウント

特定のリージョンで Media Services が使用可能かどうかを判定するには、「[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all)」を使用してください。

## <a name="streaming-endpoints"></a>ストリーミング エンドポイント

Media Services のお客様は、**Standard** と **Premium** のいずれかのストリーミング エンドポイントを選択できます。

|名前|Status|リージョン
|---|---|---|
|Standard|GA|All|
|Premium|GA|All|

## <a name="live-encoding"></a>ライブ エンコード

すべてのリージョンで利用できます。例外:ドイツ、ブラジル南部、インド西部、インド南部、インド中部。

## <a name="encoding-media-processors"></a>Encoding メディア プロセッサ

AMS には、**Media Encoder Standard** と **Media Encoder Premium ワークフロー** という 2 つのオンデマンド エンコーダーが用意されています。 詳細については、「[Azure オンデマンド メディア エンコーダーの概要と比較](media-services-encode-asset.md)」を参照してください。

|メディア プロセッサ名|Status|リージョン
|---|---|---|
|メディア エンコーダー スタンダード|GA|All|
|メディア エンコーダー Premium ワークフロー|GA|中国を除くすべて|

## <a name="analytics-media-processors"></a>Analytics メディア プロセッサ

Media Analytics は音声および視覚コンポーネントの集合体であり、組織や企業がこれを活用することで、ビデオ ファイルから実用的な洞察を簡単に引き出すことができます。 詳細については、「 [Azure Media Services Analytics の概要](./legacy-components.md)」を参照してください。

> [!NOTE]
> 一部の分析メディア プロセッサはインベントリから削除されます。 提供終了日については、[レガシ コンポーネント](legacy-components.md)に関するトピックを参照してください。

|メディア プロセッサ名|Status|リージョン
|---|---|---|
|Azure Media Face Detector|プレビュー|All|
|Azure Media Indexer|GA|All|
|Azure Media Motion Detector|プレビュー|All|
|Azure Media OCR|プレビュー|All|
|Azure Media Redactor|GA|All|
|Azure Media Video Thumbnails|プレビュー|All|

## <a name="protection"></a>保護

Microsoft Azure Media Services を使用すると、メディアがコンピューターから離れてから、保存、処理、配信されるまでのセキュリティ保護が可能になります。 詳細については、[AMS コンテンツの保護](media-services-content-protection-overview.md)に関するページを参照してください。

|暗号化|Status|リージョン|
|---|---|---| 
|ストレージ|GA|All|
|AES-128 キー|GA|All|
|FairPlay|GA|All|
|PlayReady|GA|All|
|Widevine|GA|ドイツ、連邦政府、中国を除くすべて。

> [!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="reserved-units-rus"></a>予約ユニット (RU)

プロビジョニングされた予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。

すべてのリージョンで利用できます。

## <a name="reserved-unit-ru-type"></a>予約ユニット (RU) の種類

Media Services アカウントは、メディア処理タスクが実行される速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、次から選択できます:S1、S2、S3。

|RU の種類の名前|Status|リージョン
|---|---|---|
|S1|GA|All|
|S2|GA|ブラジル南部とインド西部を除くすべて|
|S3|GA|インド西部を除くすべて|

## <a name="next-steps"></a>次のステップ

[Media Services v3 への移行](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>フィードバックの提供

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]