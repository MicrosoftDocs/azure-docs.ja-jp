---
title: 言語検出コンテナー用の docker pull
titleSuffix: Azure Cognitive Services
description: 言語検出コンテナー用の docker pull コマンド
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966812"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>言語検出コンテナー用の docker pull

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して Microsoft Container Registry からコンテナー イメージをダウンロードします。

Text Analytics コンテナーで使用可能なタグの詳しい説明については、Docker Hub の[言語検出](https://go.microsoft.com/fwlink/?linkid=2018759)コンテナーを参照してください。

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
