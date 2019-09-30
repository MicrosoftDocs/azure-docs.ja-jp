---
title: Azure Maps にデータのフィードバックを提供する方法 | Microsoft Docs
description: Azure Maps のフィードバック ツールを使用してデータのフィードバックを提供します。
author: walsehgal
ms.author: v-musehg
ms.date: 08/19/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 076f98cb240014bcc88a395902203413e31fe0f1
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641871"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure Maps にデータのフィードバックを提供する

Azure Maps は 2018 年 5 月から一般提供が始まり、さまざまなビジネス ユース ケースについて企業のお客様をサポートできるよう、最新のマップ データ、使いやすい REST API、強力な SDK が提供されています。 現実の世界は刻々と変化しており、事実に基づいたデジタル表現をお客様に提供することが重要です。 施設の開設または閉鎖を計画しているお客様は、適切な施設で納品、保守、カスタマー サービスを効率的に計画できるように、Microsoft のマップを迅速に更新する必要があります。 Microsoft では、お客様がデータのフィードバックを直接提供できるように Azure Maps データのフィードバック ツールを作成しました。 お客様のデータのフィードバックは、フィードバックを迅速に評価して Microsoft のマッピング製品に組み込むことができる Microsoft のデータ プロバイダーとそのマップの編集者に直接送信されます。  

[Azure Maps データ フィードバック ツール](https://feedback.azuremaps.com)を使用すると、お客様は、特にビジネスの目的地と居住地の住所に関するマップ データのフィードバックを簡単に提供できます。 この記事では、Azure Maps フィードバック ツールを使用してさまざまなフィードバックを提供する方法について説明します。

## <a name="add-a-business-place-or-a-residential-address"></a>事業所または居住地の住所を追加する 

マップ上に不足している目的地または居住地の住所に関するフィードバックを提供することができます。 方法は 2 つあり、Azure Maps データ フィードバック ツールを開き、不足している場所の座標を検索して、[場所の追加] をクリックします

  ![不足している場所を検索する](./media/how-to-use-feedback-tool/search-poi.png)

または、マップを操作し、場所をクリックしてその座標にピンを配置し、[場所の追加] をクリックすることができます。 

  ![ピンを追加する](./media/how-to-use-feedback-tool/add-poi.png)

クリックすると、その場所に対応する詳細を入力するフォームが表示されます。

  ![場所を追加する](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>事業所または居住地の住所を修正する 

また、フィードバック ツールでは、事業所または住所を検索して特定する、住所またはピン位置が正しくない場合に修正するためのフィードバックを提供することもできます。 住所を修正するためのフィードバックを提供するには、検索バーを使用して事業所または居住地の住所を検索します。 結果リストから目的の場所をクリックし、[Fix this place]\(この場所の修正\) をクリックします。

  ![修正する場所を検索する](./media/how-to-use-feedback-tool/fix-place.png)

住所を修正するためのフィードバックを提供するには、[Fix a place]\(場所の修正\) フォームに入力し、[submit]\(送信\) ボタンをクリックします。

  ![修正フォーム](./media/how-to-use-feedback-tool/fix-form.png)

場所のピン位置が正しくない場合は、[Fix a place]\(場所の修正\) フォームの [The pin location is incorrect]\(ピン位置が正しくない\) というチェックボックスをオンにして、ピンを正しい位置に移動し、[submit]\(送信\) ボタンをクリックします。

  ![ピン位置を移動する](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>コメントを追加する 

フィードバック ツールでは、場所を検索できるだけでなく、場所に関連する詳細情報に自由形式のテキスト コメントを追加することもできます。 コメントを追加するには、場所を検索するか、場所をクリックして [コメントの追加] をクリックし、コメントを入力して [Submit]\(送信\) をクリックします。 

  ![コメントを追加する](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>状態の追跡 

リクエストの状態を追跡するには、リクエストの送信時に [I want to track status]\(状態を追跡する\) チェックボックスをオンにして、お客様のメール アドレスを入力してください。 リクエストの最新の状態を示す追跡リンクがメールで送信されます。 

  ![フィードバックの状態](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>次の手順

Azure Maps に関する技術的な質問を投稿するには、以下にアクセスしてください。

* [Azure Maps Stack Overflow](https://stackoverflow.com/questions/tagged/azure-maps)
* [Azure Maps フィードバック フォーラム](https://feedback.azure.com/forums/909172-azure-maps)