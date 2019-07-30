---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics リソースを作成する方法について説明します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455077"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics リソースを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. **[+ リソースの作成]** を選択し、 **[AI + Machine Learning]、[テキスト分析]** の順に移動するか、[**テキスト分析の作成**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)をクリックします
1. すべての必須設定を入力します。

    |Setting|値|
    |--|--|
    |Name|任意の名前 (2 から 64 文字)|
    |サブスクリプション|適切なサブスクリプションを選択します|
    |Location|近くの場所を選択します|
    |価格レベル|`S` - Standard 価格レベル|
    |リソース グループ|使用可能なリソース グループを選択します|

1. **[作成]** をクリックして、リソースが作成されるまで待ちます。 作成されると、ブラウザーが、新たに作成されたリソースのページに自動的にリダイレクトされます
1. 構成された `endpoint` と API キーを収集します。

    |ポータルでの [リソース] タブ|Setting|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 それは、`https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` のようなものです|
    |**キー**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|