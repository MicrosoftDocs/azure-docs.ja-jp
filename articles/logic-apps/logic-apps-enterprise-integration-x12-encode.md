---
title: X12 メッセージをエンコードする
description: Enterprise Integration Pack を備えた Azure Logic Apps で、X12 メッセージ エンコーダーを使用して EDI の検証および XML エンコード メッセージの変換を行います
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792349"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack を備えた Azure Logic Apps で X12 メッセージをエンコードする

Encode X12 Message コネクタでは、EDI およびパートナー固有のプロパティを検証したり、XML エンコード メッセージをインターチェンジ内の EDI トランザクション セットに変換したり、技術確認と機能確認のいずれかまたは両方を要求したりできます。
このコネクタを使用するには、ロジック アプリの既存のトリガーにコネクタを追加する必要があります。

## <a name="before-you-start"></a>開始する前に

必要な項目を次に示します。

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。 Encode X12 Message コネクタを使用するには、統合アカウントが必要です。
* 統合アカウントで既に定義されている 2 つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)
* 統合アカウントで既に定義されている [X12 契約](logic-apps-enterprise-integration-x12.md)

## <a name="encode-x12-messages"></a>X12 メッセージをエンコードする

1. [ロジック アプリを作成](quickstart-create-first-logic-app-workflow.md)します。

2. Encode X12 Message コネクタには、トリガーがありません。そのため、要求トリガーのように、ロジック アプリを起動するためのトリガーを追加する必要があります。 ロジック アプリ デザイナーで、ロジック アプリにトリガーを追加して、アクションを追加します。

3.  検索ボックスに、フィルターとして「x12」と入力します。 **[X12 - 契約名で X12 メッセージにエンコードする]** または **[X12 - ID で X12 メッセージにエンコードする]** を選択します。
   
    !["x12" の検索](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. 以前に統合アカウントへの接続を作成していない場合は、ここでその接続を作成するように求められます。 接続の名前を指定し、接続する統合アカウントを選択します。 
   
    ![integration account connection](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    アスタリスクが付いているプロパティは必須です。

    | プロパティ | 詳細 |
    | --- | --- |
    | 接続名 * |接続の任意の名前を入力します。 |
    | 統合アカウント * |統合アカウントの名前を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

5.  完了したら、接続の詳細は次の例のようになります。 接続の作成を完了するには、 **[作成]** を選択します。

    ![integration account connection created](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    接続が作成されました。

    ![integration account connection details](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>契約名に基づく X12 メッセージのエンコード

契約名で X12 メッセージをエンコードすることを選択した場合は、 **[X12 契約の名前]** リストを開き、既存の X12 契約を入力または選択します。 エンコードする XML メッセージを入力します。

![X12 契約名とエンコードする XML メッセージの入力](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>ID に基づく X12 メッセージのエンコード

ID で X12 メッセージをエンコードすることを選択した場合は、送信者の識別子、送信者の修飾子、受信者の識別子、および受信者の修飾子を、X12 契約で構成されているとおりに入力します。 エンコードする XML メッセージを選択します。
   
![送信者と受信者の識別子の指定、エンコードする XML メッセージの選択](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>X12 エンコードの詳細

X12 Encode コネクタは次のタスクを実行します。

* 送信者と受信者のコンテキスト プロパティの照合による契約解決
* XML エンコード メッセージをインターチェンジ内の EDI トランザクション セットに変換して、EDI インターチェンジをシリアル化する
* トランザクション セット ヘッダーおよびトレーラー セグメントを適用する
* 各送信インターチェンジのインターチェンジ制御番号、グループ制御番号、およびトランザクション セット制御番号を生成する
* ペイロード データの区切り記号を置換する
* EDI およびパートナー固有のプロパティを検証する
  * メッセージ スキーマと照らし合わせた、トランザクション セット データ要素のスキーマ検証
  * トランザクション セット データ要素に対して実行される EDI 検証
  * トランザクション セット データ要素に対して実行される拡張検証
* 技術確認または機能確認を要求する (構成されている場合)
  * 技術確認は、ヘッダー検証の結果として生成されます。 技術確認は、アドレス受信者によるインターチェンジ ヘッダーとトレーラーの処理の状態を報告します。
  * 機能確認は、本文検証の結果として生成されます。 機能確認は、受信したドキュメントの処理中に発生した各エラーを報告します。

## <a name="view-the-swagger"></a>Swagger の表示
[Swagger の詳細](/connectors/x12/)を参照してください。 

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack について学習する") 

