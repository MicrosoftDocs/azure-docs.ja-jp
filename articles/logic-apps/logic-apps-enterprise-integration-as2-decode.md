---
title: "AS2 メッセージをデコードする - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps で Enterprise Integration Pack の AS2 デコーダーを使用する方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: da3fc08cc5c506dba02ccda16940a28a4600a288
ms.lasthandoff: 03/10/2017


---
# <a name="decode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Enterprise Integration Pack を使用して Azure Logic Apps の AS2 メッセージをデコードする 

メッセージを転送するときに、セキュリティと信頼性を確保するには、Decode AS2 Message コネクタを使用します。 このコネクタでは、デジタル署名、暗号化解除、およびメッセージ処理通知 (MDN) による受信確認が可能になります。

## <a name="before-you-start"></a>開始する前に

必要な項目を次に示します。

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。 Decode AS2 Message コネクタを使用するには、統合アカウントが必要です。
* 統合アカウントで既に定義されている&2; つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)
* 統合アカウントで既に定義されている [AS2 契約](logic-apps-enterprise-integration-as2.md)

## <a name="decode-as2-messages"></a>AS2 メッセージをデコードする

1. [ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

2. Decode AS2 Message コネクタには、トリガーがありません。そのため、要求トリガーのように、ロジック アプリを起動するためのトリガーを追加する必要があります。 ロジック アプリ デザイナーで、ロジック アプリにトリガーを追加して、アクションを追加します。

3.    検索ボックスに、フィルターとして「AS2」と入力します。 **[AS2 - AS2 メッセージをデコードする]** を選択します。
   
    !["AS2" の検索](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage1.png)

4. 以前に統合アカウントへの接続を作成していない場合は、ここでその接続を作成するように求められます。 接続の名前を指定し、接続する統合アカウントを選択します。
   
    ![Create integration connection](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage2.png)

    アスタリスクが付いているプロパティは必須です。

    | プロパティ | 詳細 |
    | --- | --- |
    | 接続名 * |接続の任意の名前を入力します。 |
    | 統合アカウント * |統合アカウントの名前を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

5.    完了したら、接続の詳細は次の例のようになります。 接続の作成を完了するには、**[作成]** を選択します。

    ![統合の接続の詳細](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage3.png)

6. 接続が作成されたら、次の例に示すように、要求の出力から**本文**と**ヘッダー**を選択します。
   
    ![integration connection created](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage4.png) 

    次に例を示します。

    ![要求の出力から本文とヘッダーを選択します。](media/logic-apps-enterprise-integration-as2-decode/as2decodeimage5.png) 

## <a name="as2-decoder-details"></a>AS2 デコーダーの詳細

Decode AS2 コネクタは次のタスクを実行します。 

* AS2/HTTP ヘッダーを処理する
* 署名を検証する (構成されている場合)
* メッセージを解読する (構成されている場合)
* メッセージを展開する (構成されている場合)
* 受信した MDN と元の送信メッセージを調整する
* 否認不可データベースのレコードを更新し、関連付ける
* AS2 状態レポート用のレコードを書き込む
* 出力ペイロードの内容は、base64 でエンコードされる
* AS2 契約の構成に基づいて、MDN が必要かどうかや、MDN を同期にするか非同期にするかを判断する
* 同期または非同期の MDN を生成する (契約の構成に基づいて)
* MDN の関連付けトークンとプロパティを設定する

## <a name="try-this-sample"></a>このサンプルの試用

完全に動作するロジック アプリとサンプルの AS2 シナリオのデプロイを試すには、[AS2 ロジック アプリのテンプレートとシナリオ](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/)を参照してください。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md) 


