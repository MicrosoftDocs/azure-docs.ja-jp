---
title: X12 メッセージをデコードする
description: Enterprise Integration Pack が適用された Azure Logic Apps で X12 メッセージ デコーダーを使用して EDI の検証および受信確認の生成を行います
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74792376"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack が適用された Azure Logic Apps で X12 メッセージをデコードする

Decode X12 メッセージ コネクタを使用すると、取引先契約に対するエンベロープの検証、EDI および取引先固有のプロパティの検証、トランザクション セットへのインターチェンジの分割またはインターチェンジ全体の保持、処理されたトランザクションの受信確認の生成を行うことができます。 このコネクタを使用するには、ロジック アプリの既存のトリガーにコネクタを追加する必要があります。

## <a name="before-you-start"></a>開始する前に

必要な項目を次に示します。

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。 Decode X12 Message コネクタを使用するには、統合アカウントが必要です。
* 統合アカウントで既に定義されている 2 つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)
* 統合アカウントで既に定義されている [X12 契約](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>X12 メッセージをデコードする

1. [ロジック アプリを作成](quickstart-create-first-logic-app-workflow.md)します。

2. Decode X12 Message コネクタには、トリガーがありません。そのため、要求トリガーのように、ロジック アプリを起動するためのトリガーを追加する必要があります。 ロジック アプリ デザイナーで、ロジック アプリにトリガーを追加して、アクションを追加します。

3.  検索ボックスに、フィルターとして「x12」と入力します。 **[X12 - X12 メッセージをデコードする]** を選択します。
   
    !["x12" の検索](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. 以前に統合アカウントへの接続を作成していない場合は、ここでその接続を作成するように求められます。 接続の名前を指定し、接続する統合アカウントを選択します。 

    ![統合アカウントの接続の詳細の指定](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    アスタリスクが付いているプロパティは必須です。

    | プロパティ | 詳細 |
    | --- | --- |
    | 接続名 * |接続の任意の名前を入力します。 |
    | 統合アカウント * |統合アカウントの名前を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

5.  完了したら、接続の詳細は次の例のようになります。 接続の作成を完了するには、 **[作成]** を選択します。
   
    ![integration account connection details](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. 接続が作成されたら、次の例に示すように、デコードする X12 フラット ファイル メッセージを選択します。

    ![integration account connection created](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    次に例を示します。

    ![デコードする X12 フラット ファイル メッセージの選択](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > メッセージ配列の実際のメッセージ内容またはペイロードは、良い悪いにかかわらず、base64 でエンコードされます。 そのため、このコンテンツを処理する式を指定する必要があります。
   > コードビューで入力できる XML として、またはデザイナーで式ビルダーを使用して、コンテンツを処理する例を示します。
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![コンテンツの例](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>X12 Decode の詳細

X12 Decode コネクタは次のタスクを実行します。

* 取引パートナー契約と照らし合わせてエンベロープを検証する
* EDI およびパートナー固有のプロパティを検証する
  * EDI 構造検証、および拡張スキーマ検証
  * インターチェンジ エンベロープの構造の検証
  * 制御スキーマと照らし合わせたエンベロープのスキーマ検証
  * メッセージ スキーマと照らし合わせたトランザクション セット データ要素のスキーマ検証
  * トランザクション セット データ要素に対して実行される EDI 検証 
* インターチェンジ、グループ、およびトランザクション セットの制御番号が重複していないことを検証する
  * 以前に受信したインターチェンジと照らし合わせて、インターチェンジ制御番号を確認する。
  * インターチェンジ内の他のグループ制御番号と照らし合わせて、グループ制御番号を確認する。
  * グループ内の他のトランザクション セット制御番号と照らし合わせて、トランザクション セット制御番号を確認する。
* インターチェンジをトランザクション セットに分割するか、インターチェンジ全体を保持する。
  * インターチェンジをトランザクション セットとして分割 - エラー発生時にトランザクション セットを中断インターチェンジをトランザクション セットに分割し、各トランザクション セットを解析します。 
  X12 Decode アクションは、検証が失敗したこれらのトランザクション セットのみを `badMessages` に出力し、残りのトランザクション セットを `goodMessages` に出力します。
  * インターチェンジをトランザクション セットとして分割 - エラー発生時にインターチェンジを中断インターチェンジをトランザクション セットに分割し、各トランザクション セットを解析します。 
  インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、X12 Decode アクションはそのインターチェンジのすべてのトランザクション セットを `badMessages` に出力します。
  * インターチェンジの保存 - エラー発生時にトランザクション セットを中断インターチェンジを保持し、バッチ インターチェンジ全体を処理します。 
  X12 Decode アクションは、検証が失敗したこれらのトランザクション セットのみを `badMessages` に出力し、残りのトランザクション セットを `goodMessages` に出力します。
  * インターチェンジの保存 - エラー発生時にインターチェンジを中断インターチェンジを保持し、バッチ インターチェンジ全体を処理します。 
  インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、X12 Decode アクションはそのインターチェンジのすべてのトランザクション セットを `badMessages` に出力します。 
* 技術確認または機能確認を生成する (構成されている場合)
  * 技術確認は、ヘッダー検証の結果として生成されます。 技術確認は、アドレス受信者によるインターチェンジ ヘッダーとトレーラーの処理の状態を報告します。
  * 機能確認は、本文検証の結果として生成されます。 機能確認は、受信したドキュメントの処理中に発生した各エラーを報告します。

## <a name="view-the-swagger"></a>Swagger の表示
[Swagger の詳細](/connectors/x12/)を参照してください。 

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack について学習する") 

