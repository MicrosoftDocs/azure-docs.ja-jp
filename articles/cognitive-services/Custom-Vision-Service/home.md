---
title: Custom Vision Service 機械学習の概要
description: これを使えばあなたのアプリケーションに機械学習の力を与えることができます。
services: cognitive-services
author: v-royhar
manager: juliakuz
ms.service: cognitive-services
ms.technology: custom vision service
ms.topic: article
ms.date: 07/26/2017
ms.author: v-royhar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 51a4db987a6125daab7bb74affc04a5427ea3437
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="overview"></a>概要

## <a name="custom-vision-service-brings-the-power-of-machine-learning-to-your-apps"></a>Custom Vision Service でアプリケーションに機械学習の力を与える

Custom Vision Service は画像のカスタム分類子を構築するためのツールです。画像の分類子を簡単に素早く構築、デプロイし、その性能を向上させることができます。画像をアップロードしてトレーニングするための REST API や Web インターフェイスを提供します。

## <a name="what-can-custom-vision-service-do-well-what-cant-it-do"></a>Custom Vision Service で出来ること、出来ないこと

Custom Vision Service は画像のカスタム分類子を構築し、時間の経過とともにその性能を向上させます。例えば、"ひなぎく"、"すいせん"、"ダリア"の画像を特定するツールが必要な時は、分類子をトレーニングすることで実現できます。 認識したいタグごとの画像を Custom Vision Service に提供することで行います。

Custom Vision Service は、分類したい項目が画像の中で目立つ場合に最も良く動作します。 Custom Vision Service は "画像の分類" を行いますが、"オブジェクトの検出" は行いません。つまり、Custom Vision Service は画像が特定のオブジェクトのものであるかどうかは識別しますが、オブジェクトがその画像のどこにあるかは識別しません。

分類子を作るのに必要な画像はほんの少数です。クラスごとに 30 枚もあれば、プロトタイプを作り始めることができます。Custom Vision Service が使用する方法は差異の影響を受けづらいため、データが少なくてもプロトタイプを作り始めることができるのです。しかしこれは、ごく微妙な違いを検出したいシナリオ (例えば品質保証のシナリオにおける小さな割れやへこみなど) には Custom Vision Service があまり適さないということを意味します。

Custom Vision Service は 簡単に分類子の構築を始められるように、そして時間の経過とともに分類子の性能向上を手助けするように設計されています。


## <a name="next-steps"></a>次のステップ

[分類子の構築](getting-started-build-a-classifier.md)

