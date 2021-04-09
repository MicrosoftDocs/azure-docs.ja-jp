---
title: 感情分析コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー用の docker pull コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90906121"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>感情分析コンテナー v3 用の docker pull

複数の言語で、感情分析コンテナー v3 のコンテナーを使用できます。 英語のコンテナー用のコンテナーをダウンロードするには、次のコマンドを使用します。 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
```

別の言語用のコンテナーをダウンロードするには、`en` を次のいずれかの言語コードに置き換えます。 

| Text Analytics コンテナー | 言語コード |
|--|--|
| 英語 | `en` |
| スペイン語 | `es` |
| フランス語 | `fr` |
| イタリア語 | `it` |
| ドイツ語 | `de` |
| 簡体中国語 | `zh` |
| 繁体中国語 | `zht` |
| 日本語 | `ja` |
| Portuguese | `pt` |
| オランダ語 | `nl` |

Text Analytics コンテナーで使用可能なタグの詳しい説明については、[Docker Hub](https://go.microsoft.com/fwlink/?linkid=2018654) に関する記事を参照してください。