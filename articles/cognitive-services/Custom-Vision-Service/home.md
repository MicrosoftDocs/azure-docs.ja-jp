---
title: Custom Vision Service の概要 | Microsoft Docs
description: Custom Vision Service を使ってアプリケーションに機械学習のパワーをもたらそう
services: cognitive-services
author: v-royhar
manager: juliakuz
ms.service: cognitive-services
ms.technology: custom vision service
ms.topic: article
ms.date: 05/03/2017
ms.author: v-royhar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 51a4db987a6125daab7bb74affc04a5427ea3437
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="overview"></a>Custom Vision Service の概要

## <a name="custom-vision-service-brings-the-power-of-machine-learning-to-your-apps"></a>Custom Vision Service はアプリケーションに機械学習のパワーをもたらします

Custom Vision Service は、独自の画像分類器を構築するためのツールです。画像分類器の構築、デプロイ、改善を容易かつ迅速に行います。画像のアップロードとトレーニングするためのREST APIとウェブインターフェースを提供しています。

## <a name="what-can-custom-vision-service-do-well-what-cant-it-do"></a>Custom Vision Service でできること・できないこと 

Custom Vision Service は、独自の画像分類器を構築し、時間の経過とともに改善するためのツールです。例えば、"デイジー"、"ラッパズイセン"、"ダリア"の画像を識別できるツールが必要な場合、そのための分類器をトレーニングすることができます。Custom Vision Service に、認識したいタグ毎の画像を提供することで実現できます。

Custom Vision Service は、分類したいものが画像の中で顕著であるときに良い結果をもたらします。Custom Vision Service は、"オブジェクト検出" ではなく "画像分類" を行います。つまり、そのオブジェクトが画像内のどこにあるかではなく、画像が特定のオブジェクトであるかどうかを識別します。

分類器を作成するのに多くの画像は必要ありません。プロトタイプを作成するのであれば、1クラスにつき30枚の画像があれば十分です。Custom Vision Service が使っている手法は、違いの検知に強いため、少ないデータでプロトタイプの作成を行えます。しかしこれは、Custom Vision Service が、微妙な違い（例えば、品質保証シナリオでのわずかな亀裂や窪みなど）を検出するシナリオには適していないことを意味します。

Custom Vision Service は、分類器の構築を容易にし、時間の経過とともに分類器の品質を向上させるのに役立ちます。

## <a name="next-steps"></a>次のステップ

[分類器の構築](getting-started-build-a-classifier.md)