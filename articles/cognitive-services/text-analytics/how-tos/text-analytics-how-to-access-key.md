---
title: Text Analytics API のアクセス キーを取得する
titleSuffix: Azure Cognitive Services
description: Text Analytics 操作に対してアクセス キーを取得し、Cognitive Services で処理するために生テキストを送信します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 813659b9b1a25e03bf599b0aa1dc9d6af67f1935
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002206"
---
# <a name="how-to-find-endpoints-and-access-keys-for-the-text-analytics-cognitive-service"></a>Text Analytics Cognitive Service のエンドポイントとアクセス キーを見つける方法

Text Analytics にサインアップするとき、ご自身のサブスクリプションに対して一意の個人用に設定されたアクセス キーを取得します。 このキーは、Text Analytics API への呼び出しごとに必要です。 [サインアップ](text-analytics-how-to-signup.md)していない場合は、サインアップしてキーを取得してください。 

キーを見つけるためにヘルプが必要な場合、またはご自分のサブスクリプションに既に Text Analytics が含まれているかどうかを判断するには、次の手順を使って必要な情報を取得します。 

## <a name="find-your-service-endpoint-and-access-key"></a>ご自分のサービス エンドポイントとアクセス キーを見つける

1. [Azure ポータル](https://portal.azure.com)にサインインします。

2. 左側のナビゲーション ウィンドウで、**[すべてのサービス]** を選択します。

3. [フィルター] に「*Cognitive Services*」と入力します。 ご自分のサブスクリプションに Text Analytics がある場合は、**Text Analytics API** の API の種類と共にリストに表示されます。

4. リンクをクリックすると、サービス ブレードが開きます。 **[リソース] > [キー]** から、または [要点] ウィンドウの **[Show access keys]\(アクセス キーを表示\)** をクリックすることで、キーを取得できます。 [要点] ウィンドウには、エンドポイントも表示されます。

   ![エンドポイントとキーを含むポータル ページ](../media/portal-keys-endpoint.png)

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics API を呼び出す](text-analytics-how-to-call-api.md)
