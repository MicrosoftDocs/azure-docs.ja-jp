---
title: "ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する | Microsoft Docs"
description: "ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する方法について説明します。"
services: media-services
documentationcenter: 
author: anilmur
manager: erikre
editor: 
ms.assetid: 004815f2-0761-4706-87a1-675ba36e0322
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: juliako;johndeu;anilmur
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: bf208f65ef5509e56373a84bb096da39d6bafc1f


---
# <a name="create-advanced-encoding-workflows-with-workflow-designer"></a>ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する
## <a name="overview"></a>概要
**ワークフロー デザイナー**は、**メディア エンコーダー プレミアム ワークフロー**でエンコードするカスタム ワークフローの設計と構築に使用する Windows デスクトップ ツールです。
ワークフロー デザイナー ツールの機能を利用することで、 **メディア エンコーダー プレミアム**で実行する複雑なワークフローを設計し、作成できます。  

ワークフローには、入力ソース ファイルのプロパティに基づいて顧客の意志決定ロジックとブランチを含めることができます。 上書き可能なプロパティと動的な値を含むワークフローを作成すれば、最も複雑なエンコーディング タスクをクラウドで簡単に繰り返し、カスタマイズできます。

作成可能なワークフローの例は次のとおりです。

* 解決を求めてソース コンテンツを検査し、必要な出力トラックだけをエンコードする決定基準のワークフロー。  不注意でソース コンテンツをスケールアップし、生成される無駄なトラックをなくするので便利です。
* 複数の入力ファイルを使用し、キャプション、オーバーレイ、結合コンテンツをサポートできます。 

このツールはまた、 [公開済みワークフロー](media-services-workflow-designer.md#existing_workflows)の変更に使用できます。 

> [!NOTE]
> ワークフロー デザイナー ツールのコピーを取得するには、mepd@microsoft.com にお問い合わせください。
> 
> 

作成されたワークフロー ファイルはアセットとしてアップロードした後、メディア ファイルのエンコードに使用できます。 **.NET** を使用した**メディア エンコーダー プレミアム ワークフロー**によるエンコード方法の詳細については、「[メディア エンコーダー プレミアム ワークフローでの高度なエンコード](media-services-encode-with-premium-workflow.md)」を参照してください。

## <a name="a-idexistingworkflowsamodify-existing-workflows"></a><a id="existing_workflows"></a>既存のワークフローを変更する
デザイナー ツールを使用して、 [既定の公開済みワークフロー](media-services-workflow-designer.md#existing_workflows) を変更できます。 既定のワークフロー ファイルは [こちら](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)から入手できます。 フォルダーにはこれらのファイルの説明も含まれています。

以下のビデオでは、デザイナーの使用方法を示しています。

### <a name="day-1--getting-started"></a>Day 1 - 作業の開始
Day 1 のビデオの内容は次のとおりです。

* デザイナーの概要
* 基本的なワークフロー - "Hello World"
* Azure Media Services のストリーミングで使用する複数の出力 MP4 ファイルの作成

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-1/player]
> 
> 

### <a name="day-2"></a>Day 2
Day 2 のビデオの内容は次のとおりです。

* さまざまなソース ファイル シナリオ - オーディオの処理
* 高度なロジックを含むワークフロー
* グラフ ステージ

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-2/player]
> 
> 

### <a name="day-3"></a>Day 3
Day 3 のビデオの内容は次のとおりです。

* ワークフローと設計図の内部スクリプト
* 現在のエンコーダの制限事項
* Q & A

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Premium-Encoder-Workflow-Designer-Training-Videos-Day-3/player]
> 
> 

## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

ワークフロー デザイナー ツールでカスタム ワークフローを作成するとき、サポートが必要になりましたら、あるいはご質問がございましたら、mepd@microsoft.com までメールを送信してください。

## <a name="see-also"></a>関連項目
[Azure プレミアム エンコーダー ワークフロー デザイナーのトレーニング ビデオ](http://johndeutscher.com/2015/07/06/azure-premium-encoder-workflow-designer-training-videos/)




<!--HONumber=Jan17_HO5-->


