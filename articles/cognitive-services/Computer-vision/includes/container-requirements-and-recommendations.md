---
title: コンテナーの要件と推奨事項
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129818"
---
> [!NOTE]
> 要件と推奨事項は、29 行におよぶ合計 803 文字のビジネス レターのスキャン画像 (8 MB) を使用した、1 秒あたり 1 つの要求というベンチマークに基づいています。

#### <a name="readtabread"></a>[読み取り](#tab/read)

次の表に、各読み取りコンテナーに割り当てるリソースの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |TPS<br>(最小、最大)|
|-----------|---------|-------------|--|
| 読み取り | 1 コア、8 GB メモリ、0.24 TPS | 8 コア、16 GB メモリ、1.17 TPS | 0.24、1.17 |

#### <a name="recognize-texttabrecognize-text"></a>[Recognize Text](#tab/recognize-text)

次の表に、各テキスト認識コンテナーに割り当てるリソースの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |TPS<br>(最小、最大)|
|-----------|---------|-------------|--|
| テキスト認識 | 1 コア、8 GB メモリ、0.12 TPS | 8 コア、16 GB メモリ、0.60 TPS | 0.12、0.60 |

***

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。
* TPS - 1 秒あたりのトランザクション数。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。
