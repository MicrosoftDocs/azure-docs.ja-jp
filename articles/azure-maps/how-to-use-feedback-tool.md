---
title: Azure Maps にデータのフィードバックを提供する | Microsoft Azure Maps
description: Microsoft Azure Maps のフィードバック ツールを使用してデータのフィードバックを提供します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4be775d8a6a8c476bcc659a902c7f43c12a6d6d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905334"
---
# <a name="provide-data-feedback-to-azure-maps"></a>Azure Maps にデータのフィードバックを提供する

Azure Maps は 2018 年 5 月から使用できるようになりました。 さまざまな種類のビジネス ユース ケースを持つ企業のお客様をサポートするために、Azure Maps では新しいマップ データ、使いやすい REST API、強力な SDK を提供しています。 現実の世界は刻々と変化しており、事実に基づいたデジタル表現をお客様に提供することが重要です。 お客様が施設のオープンまたはクローズを計画している場合は、マップを迅速に更新する必要があります。 そうすれば、適切な施設で配送、保守、またはカスタマー サービスを効率的に計画できます。 Microsoft では、お客様がデータのフィードバックを直接提供できるように Azure Maps データのフィードバック サイトを作成しました。 お客様のデータ フィードバックは、データ プロバイダーとそのマップ エディターに直接送信されます。 そこでは、フィードバックを迅速に評価し、マッピング製品に組み込むことができます。  

[Azure Maps Data (プレビュー) フィードバック サイト](https://feedback.azuremaps.com)を使用すると、お客様は、特にビジネスの目的地と居住地の住所に関するマップ データのフィードバックを簡単に提供できます。 この記事では、Azure Maps フィードバック ツールを使用してさまざまなフィードバックを提供する方法について説明します。

## <a name="add-a-business-place-or-a-residential-address"></a>事業所または居住地の住所を追加する 

掲載されていない目的地や居住地についてのフィードバックを提供することもできます。 これには 2 つ方法があります。 Azure Map データ フィードバック サイトを開き、掲載されていない場所の座標を検索し、[Add a place]\(場所の追加\) をクリックします

  ![不足している場所を検索する](./media/how-to-use-feedback-tool/search-poi.png)

マップを操作することもできます。 場所をクリックして座標をピンを配置し、[Add a place]\(場所の追加\) をクリックします。

  ![ピンを追加する](./media/how-to-use-feedback-tool/add-poi.png)

クリックすると、その場所に対応する詳細を入力するフォームが表示されます。

  ![場所を追加する](./media/how-to-use-feedback-tool/add-a-place.png)

## <a name="fix-a-business-place-or-a-residential-address"></a>事業所または居住地の住所を修正する 

フィードバック サイトを使うと、事業所や住所を検索して見つけることもできます。 住所やピンの位置が正しくない場合は、修正するフィードバックを提供することができます。 住所を修正するためのフィードバックを提供するには、検索バーを使用して事業所または居住地の住所を検索します。 結果の一覧から目的地をクリックします。 [Fix this place]\(この場所を修正\) をクリックします。

  ![修正する場所を検索する](./media/how-to-use-feedback-tool/fix-place.png)

住所を修正するためのフィードバックを提供するには、[Fix a place]\(場所の修正\) フォームに入力し、[submit]\(送信\) ボタンをクリックします。

  ![修正フォーム](./media/how-to-use-feedback-tool/fix-form.png)

場所のピンの位置が間違っている場合は、[Fix a place]\(場所の修正\) フォームで [The pin location is incorrect]\(ピンの位置が正しくありません\) というチェックボックスをオンにします。 ピンを正しい場所に移動し、[submit]\(送信\) ボタンをクリックします。

  ![ピン位置を移動する](./media/how-to-use-feedback-tool/move-pin.png)

## <a name="add-a-comment"></a>コメントを追加する 

フィードバック ツールでは、場所を検索できるだけでなく、場所に関連する詳細情報に自由形式のテキスト コメントを追加することもできます。 コメントを追加するには、場所を検索するか、場所をクリックします。 [Add a comment]\(コメントの追加\) をクリックし、コメントを入力して [Submit]\(送信\) をクリックします。

  ![コメントを追加する](./media/how-to-use-feedback-tool/add-comment.png)

## <a name="track-status"></a>状態の追跡 

リクエストの状態を追跡するには、リクエストの送信時に [I want to track status]\(状態を追跡する\) チェックボックスをオンにして、お客様のメール アドレスを入力してください。 リクエストの最新の状態を示す追跡リンクがメールで送信されます。 

  ![フィードバックの状態](./media/how-to-use-feedback-tool/feedback-status.png)


## <a name="next-steps"></a>次のステップ

Azure Maps に関する技術的な質問を投稿するには、以下にアクセスしてください。

* [Microsoft Q & A](/answers/topics/azure-maps.html)