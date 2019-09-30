---
title: コンテナーの要件と推奨事項。
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034485"
---
次の表に、各テキスト認識コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |TPS<br>(最小、最大)|
|-----------|---------|-------------|--|
|テキスト認識|1 コア、8 GB メモリ、0.5 TPS|2 コア、8 GB メモリ、1 TPS|0.5、1|

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。