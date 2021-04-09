---
title: docker run コマンドのコンテナー実行の例
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー用の docker run コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 96b41c27b282bdc05dafa8c69398a11e8dbf3b1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91779658"
---
"*感情分析 v3* " コンテナーを実行するには、次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | 自分の Text Analytics リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Text Analytics API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *感情分析* コンテナーを実行します。
* 1 つの CPU コアと 8 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。