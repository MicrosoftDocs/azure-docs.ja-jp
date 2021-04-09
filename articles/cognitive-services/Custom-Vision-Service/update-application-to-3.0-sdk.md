---
title: プロジェクトを 3.0 API に更新する方法
titleSuffix: Azure Cognitive Services
description: 以前のバージョンの API から 3.0 API に Custom Vision のプロジェクトを更新する方法について説明します。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "73647498"
---
# <a name="update-to-the-30-api"></a>3.0 API に更新する

Custom Vision の一般提供が開始され、API が更新されました。
この更新には、いくつかの新機能と重要な変更が含まれています。

* Prediction API がプロジェクト タイプに基づいて 2 つに分割されました。
* Vision AI Developer Kit (VAIDK) のエクスポート オプションでは、特定の方法でプロジェクトを作成する必要があります。
* 既定のイテレーションが削除され、指定されたイテレーションが発行/発行取り消しされるようになりました。

このガイドでは、新しいバージョンの API と共に機能するようにプロジェクトを更新する方法を説明します。 変更の詳細については、[リリース ノート](release-notes.md)を参照してください。

## <a name="use-the-updated-prediction-api"></a>更新された Prediction API の使用

2.x API では、画像分類器およびオブジェクト検出器の両方のプロジェクトに対して、同じ予測呼び出しが使用されました。 **PredictImage** および **PredictImageUrl** の呼び出しでは、両方のプロジェクト タイプを使用できました。 3.0 以降では、この API が分割されたため、プロジェクト タイプと呼び出しを一致させる必要があります。

* 画像分類プロジェクトの予測を取得するには、**[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** および **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** を使用します。
* オブジェクト検出プロジェクトの予測を取得するには、**[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** および **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** を使用します。

## <a name="use-the-new-iteration-publishing-workflow"></a>新しいイテレーション発行ワークフローの使用

2.x API では、予測に使用するイテレーションを選択する際、既定のイテレーションまたは指定のイテレーション ID が使用されました。 3.0 以降では、発行フローが採用されています。これにより、最初にトレーニング API から指定の名前のイテレーションを発行します。 次に、その名前を予測メソッドに渡して、使用するイテレーションを指定します。

> [!IMPORTANT]
> 3.0 API では、既定のイテレーション機能は使用されません。 古い API が廃止されるまで、引き続き 2.x API を使用して、既定のイテレーションを切り替えることができます。 これらの API は一定期間保持され、**[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** メソッドを呼び出して、イテレーションを既定としてマークできます。

### <a name="publish-an-iteration"></a>イテレーションの発行

イテレーションをトレーニングすると、**[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** メソッドでの予測に使用できるようになります。 イテレーションを発行するには、CustomVision Web サイトの設定ページで入手できる予測リソース ID が必要です。

![Custom Vision Web サイトの設定ページに予測リソース ID が示されています。](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> この情報を [Azure Portal](https://portal.azure.com) から取得するには、[Custom Vision Prediction] リソースに移動して、**[プロパティ]** を選択します。

イテレーションが発行されると、アプリでは、Prediction API 呼び出し内にその名前を指定することにより、イテレーションを使用して予測を行うことができます。 イテレーションを予測呼び出しで使用できないようにするには、**[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API を使用します。

## <a name="next-steps"></a>次のステップ

* [API リファレンス ドキュメント (REST) のトレーニング](https://go.microsoft.com/fwlink/?linkid=865446)
* [Prediction API リファレンス ドキュメント (REST)](https://go.microsoft.com/fwlink/?linkid=865445)