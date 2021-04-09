---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: feb79d047a6c3b25176a13dcc3c3afd53a51459e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444753"
---
## <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector リソースを作成する

1. <a href="https://portal.azure.com" target="_blank">Azure portal</a> にサインインします。
1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">[Anomaly Detector の作成]</a> リソースを選択します。
1. すべての必須設定を入力します。

    |設定|値|
    |--|--|
    |名前|任意の名前 (2 から 64 文字)|
    |サブスクリプション|適切なサブスクリプションを選択します|
    |場所|近くにある任意の使用可能な場所を選択します|
    |価格レベル|`F0` - 1 秒あたり 10 回の呼び出し、1 か月あたり 2 万件のトランザクション。 <br> または:<br> `S0` - 1 秒あたり 80 回の呼び出し|
    |リソース グループ|使用可能なリソース グループを選択します|

1. **[作成]** をクリックして、リソースが作成されるまで待ちます。 作成後、リソース ページに移動します。
1. 構成された `endpoint` と API キーを収集します。

    |ポータルの [キーとエンドポイント] タブ|設定|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 それは、` https://<your-resource-name>.cognitiveservices.azure.com/` のようなものです|
    |**[キー]**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|



