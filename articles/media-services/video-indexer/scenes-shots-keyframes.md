---
title: Video Indexer のシーン、ショット、キーフレーム - Azure
titlesuffix: Azure Media Services
description: このトピックでは、Video Indexer サービスのシーン、ショット、キーフレームの概要を説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: cdabc1b6bfed519098f656710ef49a946e676cf2
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815657"
---
# <a name="scenes-shots-and-keyframes"></a>シーン、ショット、キーフレーム

Video Indexer では、構造とセマンティックのプロパティに基づくビデオのテンポラル単位へのセグメント化がサポートされています。 この機能を使用すると、さまざまな粒度に基づいてビデオ コンテンツを簡単に参照、管理、編集できます。 たとえば、このトピックの説明はシーン、ショット、キーフレームに基づいています。   

![シーン、ショット、キーフレーム](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>シーン検出  
 
Video Indexer では、視覚的な手掛かりに基づいて、ビデオ内でシーンが変化するタイミングが判定されます。シーンは単一のイベントを表し、意味的に関連する一連の連続したショットで構成されます。 シーンのサムネイルは、その基になるショットの最初のキーフレームです。 Video Indexer では、連続するショット間の色の一貫性に基づいてビデオがシーンにセグメント化され、各シーンの開始と終了の時間が取得されます。 シーンの検出は、ビデオのセマンティックな側面を定量化する必要があるので、困難な作業と見なされます。

> [!NOTE]
> 少なくとも 3 つのシーンが含まれるビデオに適用されます。

## <a name="shot-detection"></a>ショット検出

Video Indexer では、連続するフレームの配色での突然の遷移と段階的な遷移の両方を追跡することにより、視覚的な手掛かりに基づいてビデオでのショットの変化が特定されます。 ショットのメタデータには、開始と終了の時間、およびそのショットに含まれるキーフレームのリストが含まれます。 ショットは、同時に同じカメラで撮影された連続するフレームです。

## <a name="keyframe-detection"></a>キーフレームの検出

ショットを最適に表すフレームを選択します。 キーフレームは、審美的プロパティ (たとえば、コントラストや安定性) に基づいてビデオ全体から選択された代表的なフレームです。 Video Indexer では、キーフレームのサムネイルを抽出できる顧客に基づいて、ショットのメタデータの一部としてキーフレーム ID のリストが取得されます。 

キーフレームは、出力 JSON のショットと関連付けられます。 

## <a name="next-steps"></a>次の手順

[API によって生成される Video Indexer の出力の詳細](video-indexer-output-json-v2.md#scenes)
