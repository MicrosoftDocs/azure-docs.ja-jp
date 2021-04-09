---
title: Video Indexer を使用したアニメーション化されたキャラクターの検出
titleSuffix: Azure Media Services
description: このトピックでは、Video Indexer でアニメーション化されたキャラクターの検出を使用する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: f1b19f178a60671108ec32ef6fbed1afe73a0cee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854752"
---
# <a name="animated-character-detection-preview"></a>アニメーション化されたキャラクターの検出 (プレビュー)

Azure Media Services Video Indexer では、[Cognitive Services の Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) との統合によって、アニメーション化されたコンテンツのキャラクターの検出、グループ化、認識がサポートされています。 この機能は、ポータルと API の両方で使用できます。

特定のアニメーション モデルを使用してアニメーション ビデオをアップロードすると、Video Indexer では、キーフレームの抽出、それらのフレーム内のアニメーション化されたキャラクターの検出、類似したキャラクターのグループ化、最適なサンプルの選択が行われます。 次に、グループ化されたキャラクターが Custom Vision に送信され、トレーニングされたモデルに基づいてキャラクターが識別されます。 

モデルのトレーニングを開始する前に、キャラクターが名前なしで検出されます。 名前を追加してモデルをトレーニングすると、Video Indexer によってキャラクターが認識され、それに応じて名前が付けられます。

## <a name="flow-diagram"></a>フロー図

次の図は、アニメーション化されたキャラクターの検出プロセスのフローを示しています。

![フロー図](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>アカウント

Video Indexer アカウントの種類に応じて、異なる機能セットを使用できます。 アカウントを Azure に接続する方法の詳細については、「[Azure に接続された Video Indexer アカウントを作成する](connect-to-azure.md)」を参照してください。

* 試用版アカウント:Video Indexer では内部の Custom Vision アカウントが使用され、モデルが作成されて、お使いの Video Indexer アカウントに接続されます。 
* 有料アカウント: Custom Vision アカウントを自分の Video Indexer アカウントに接続します (まだアカウントをお持ちではない場合は、最初にアカウントを作成する必要があります)。

### <a name="trial-vs-paid"></a>試用版と有料版

|機能|試用版|有料|
|---|---|---|
|Custom Vision アカウント|Video Indexer によってバックグラウンドで管理されます。 |Custom Vision アカウントが Video Indexer に接続されます。|
|アニメーション モデルの数|1 つ|アカウントあたり最大 100 個のモデル (Custom Vision の制限)。|
|モデルのトレーニング|Video Indexer では、新しいキャラクターに対してモデルがトレーニングされ、既存のキャラクターの例が追加されます。|アカウント所有者は、変更を行う準備ができたらモデルをトレーニングします。|
|Custom Vision の詳細オプション|Custom Vision ポータルへのアクセス権がありません。|ご自身で、モデルを Custom Vision ポータルで調整することができます。|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>ポータルおよび API でアニメーション化されたキャラクターの検出を使用する

詳細については、「[ポータルおよび API でアニメーション化されたキャラクターの検出を使用する](animated-characters-recognition-how-to.md)」を参照してください。

## <a name="limitations"></a>制限事項

* 現時点では、"アニメーション ID" 機能は東アジア リージョンでサポートされていません。
* ビデオの品質が低い場合、ビデオ内でサイズが小さいキャラクターや遠くに見えるキャラクターは正しく識別されない可能性があります。
* アニメーション化されたキャラクター セットごとにモデルを使用することをお勧めします (アニメーション化された系列ごと、など)。

## <a name="next-steps"></a>次のステップ

[Video Indexer の概要](video-indexer-overview.md)