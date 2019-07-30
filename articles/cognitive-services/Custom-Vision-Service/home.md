---
title: Azure Custom Vision とは
titlesuffix: Azure Cognitive Services
description: Custom Vision サービスを使用して、Azure クラウド内にカスタム画像分類器を構築する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 8a5dd16948724e3a79863450212702aa8aeb2347
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605141"
---
# <a name="what-is-azure-custom-vision"></a>Azure Custom Vision とは

Azure Custom Vision は、独自の画像分類器を構築、デプロイ、改良できるようにする認識サービスです。 画像分類器は、視覚特性に基づいて画像にラベル ("_クラス_" を表す) を適用する AI サービスです。 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) サービスとは異なり、Custom Vision では適用するラベルをユーザーが決定できます。

## <a name="what-it-does"></a>実行内容

Custom Vision サービスでは、画像にラベルを適用するために機械学習アルゴリズムを使用します。 開発者は、着目する特性を備えた画像グループと、それを欠いた画像グループを提出する必要があります。 提出時に自分で画像にラベルを付けます。 次に、アルゴリズムをこのデータでトレーニングし、同じ画像に対するテストを行って、アルゴリズム自体の精度を計算します。 アルゴリズムのトレーニング後、テスト、再トレーニングを行い、最終的にはアルゴリズムを使用して、新しい画像をアプリのニーズに合わせて分類することができます。 オフラインで使用するために、モデル自体をエクスポートすることもできます。

### <a name="classification-and-object-detection"></a>分類とオブジェクトの検出

Custom Vision の機能は、2 つに分割できます。 **画像の分類**では、画像に 1 つまたは複数のラベルを適用します。 **オブジェクトの検出**では、同様のことを行いますが、適用されたラベルを見つけることができる画像内の座標も返されます。

### <a name="optimization"></a>最適化

Custom Vision サービスは、画像間の主な違いをすばやく認識するために最適化されているため、少量のデータを含むモデルでプロトタイプの作成を始めることができます。 ラベルあたり 50 個の画像が、通常は適切な出発点です。 しかし、このサービスは、画像の微妙な違いの検出 (たとえば、品質保証のシナリオにおける小さな割れやへこみの検出) には最適ではありません。

また、Custom Vision アルゴリズムのいくつかの種類の中から、特定の対象素材 (たとえば、ランドマークや小売り品目) が含まれている画像に適したものを選択することもできます。 詳細については、[分類器の構築](getting-started-build-a-classifier.md)に関するガイドを参照してください。

## <a name="what-it-includes"></a>備えている機能

Custom Vision Service は、[Custom Vision ホーム ページ](https://customvision.ai/)の Web ベースのインターフェイスを通じて利用するだけでなく、ネイティブ SDK のセットとしても利用できます。 いずれかのインターフェイス、または両方のインターフェイスを使用して、モデルの作成、テスト、およびトレーニングを行うことができます。

![Chrome ブラウザー ウィンドウでの Custom Vision ホーム ページ](media/browser-home.png)

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Custom Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

[分類器の構築](getting-started-build-a-classifier.md)に関するガイドに従って、Web で Custom Vision の使用を開始するか、[画像分類のチュートリアル](csharp-tutorial.md)を実行して、コードで基本的なシナリオを実装します。
