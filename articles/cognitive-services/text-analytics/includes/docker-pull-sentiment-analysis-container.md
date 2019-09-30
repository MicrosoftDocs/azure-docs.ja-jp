---
title: 感情分析コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー用の docker pull コマンド
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: df00d469052fa30c3f2aaa5afe1881ef74587f9a
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966803"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>感情分析コンテナー用の docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[感情分析](https://go.microsoft.com/fwlink/?linkid=2018654)コンテナーを参照してください。

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
