---
title: LUIS リソースの作成
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95972516"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Azure portal で LUIS リソースを作成する

1. [このリンク](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)を使用して、Azure portal で LUIS リソースの作成を開始します。

1. すべての必須設定を入力します。

    |名前|目的|
    |--|--|
    |サブスクリプション | リソースに対して課金されるサブスクリプション。|
    |Resource group| 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。|
    |名前| 選択したカスタム名。 作成および予測エンドポイント クエリのカスタム サブドメインとして使用されます。|
    |オーサリングの場所|モデルに関連付けられている領域。|
    |オーサリングの価格レベル|1 秒および 1 か月あたりの最大トランザクション数が決まります。|
    |予測の場所|発行される予測エンドポイント ランタイムに関連付けられているリージョン。|
    |予測価格レベル|1 秒および 1 か月あたりの最大トランザクション数が決まります。|

    > [!div class="mx-imgBorder"]
    > [![[作成] の下の [基本] タブを示すスクリーンショット。](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. **[確認と作成]** を選択して、リソースが作成されるまで待ちます。
1. 両方のリソースが作成された後、引き続き Azure portal で新しい作成リソースを選択します。 次に、 **[キーとエンドポイント]** を選択して、プログラムで作成を行うための作成 **エンドポイント URL** および **キー** を取得します。

> [!TIP]
> リソースを使用するには、LUIS ポータルで[リソースを割り当て](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)ます。
