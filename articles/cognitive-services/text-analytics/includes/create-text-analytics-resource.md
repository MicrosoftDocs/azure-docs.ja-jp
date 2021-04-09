---
title: Cognitive Services Text Analytics リソースを作成する
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics リソースを作成する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750071"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics リソースを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Create a resource]\(リソースを作成)** を選択して、**AI + Machine Learning** > **Text Analytics** に移動します。
   または、[[Create Text Analytics]\(Text Analytics の作成\)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) に移動します。
1. すべての必須設定を入力します。

    |設定|値|
    |--|--|
    |名前|名前 (2 文字から 64 文字) を入力します。|
    |サブスクリプション|適切なサブスクリプションを選択します。|
    |場所|近くの場所を選択します。|
    |Pricing tier| 「**S**」 (Standard 価格レベル) と入力します。|
    |リソース グループ|使用可能なリソース グループを選択します。|

1. **[作成]** を選択し、リソースが作成されるまで待ちます。 ブラウザーが、新たに作成されたリソースのページに自動的にリダイレクトされます。
1. 構成された `endpoint` と API キーを収集します。

    |ポータルでの [リソース] タブ|設定|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` のように表示されます。|
    |**[キー]**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`> です。|
