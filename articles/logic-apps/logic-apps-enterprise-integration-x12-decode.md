---
title: "X12 メッセージをデコードする - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps で Enterprise Integration Pack の X12 メッセージ デコーダーを使用して EDI の検証およびトランザクション セットの XML の生成を行います"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 717069dbe211ea9cc04925875e0f28c85ef25ac2
ms.lasthandoff: 03/10/2017


---
# <a name="decode-x12-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Enterprise Integration Pack を使用して Azure Logic Apps の X12 メッセージをデコードする

Decode X12 Message コネクタでは、EDI およびパートナー固有のプロパティを検証したり、各トランザクション セットに対して XML ドキュメントを生成したり、処理したトランザクションの受信確認を生成したりできます。 このコネクタを使用するには、ロジック アプリの既存のトリガーにコネクタを追加する必要があります。

## <a name="before-you-start"></a>開始する前に

必要な項目を次に示します。

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。 Decode X12 Message コネクタを使用するには、統合アカウントが必要です。
* 統合アカウントで既に定義されている&2; つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)
* 統合アカウントで既に定義されている [X12 契約](logic-apps-enterprise-integration-x12.md)

## <a name="decode-x12-messages"></a>X12 メッセージをデコードする

1. [ロジック アプリを作成](logic-apps-create-a-logic-app.md)します。

2. Decode X12 Message コネクタには、トリガーがありません。そのため、要求トリガーのように、ロジック アプリを起動するためのトリガーを追加する必要があります。 ロジック アプリ デザイナーで、ロジック アプリにトリガーを追加して、アクションを追加します。

3.    検索ボックスに、フィルターとして「x12」と入力します。 **[X12 - X12 メッセージをデコードする]** を選択します。
   
    !["x12" の検索](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. 以前に統合アカウントへの接続を作成していない場合は、ここでその接続を作成するように求められます。 接続の名前を指定し、接続する統合アカウントを選択します。 

    ![統合アカウントの接続の詳細の指定](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    アスタリスクが付いているプロパティは必須です。

    | プロパティ | 詳細 |
    | --- | --- |
    | 接続名 * |接続の任意の名前を入力します。 |
    | 統合アカウント * |統合アカウントの名前を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

5.    完了したら、接続の詳細は次の例のようになります。 接続の作成を完了するには、**[作成]** を選択します。
   
    ![integration account connection details](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. 接続が作成されたら、次の例に示すように、デコードする X12 フラット ファイル メッセージを選択します。

    ![integration account connection created](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    次に例を示します。

    ![デコードする X12 フラット ファイル メッセージの選択](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

## <a name="x12-decode-details"></a>X12 Decode の詳細

X12 Decode コネクタは次のタスクを実行します。

* 取引パートナー契約と照らし合わせてエンベロープを検証する
* 各トランザクション セットの XML ドキュメントを生成する
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
* インターチェンジ全体を XML に変換する 
  * [インターチェンジをトランザクション セットとして分割 - エラー発生時にトランザクション セットを中断]: インターチェンジの各トランザクション セットを個別の XML ドキュメントとして解析します。 インターチェンジの&1; つ以上のトランザクション セットが検証に失敗した場合、X12 Decode は失敗したトランザクション セットのみを中断します。
  * [インターチェンジをトランザクション セットとして分割 - エラー発生時にインターチェンジを中断]: インターチェンジの各トランザクション セットを個別の XML ドキュメントとして解析します。  インターチェンジの&1; つ以上のトランザクション セットが検証に失敗した場合、X12 Decode はインターチェンジ全体を中断します。
  * [インターチェンジの保存 - エラー発生時にトランザクション セットを中断]: バッチ インターチェンジ全体に対する XML ドキュメントを作成します。 X12 Decode は、検証に失敗したトランザクション セットだけを中断し、他のすべてのトランザクション セットの処理を継続します。
  * [インターチェンジの保存 - エラー発生時にインターチェンジを中断]: バッチ インターチェンジ全体に対する XML ドキュメントを作成します。 インターチェンジの&1; つ以上のトランザクション セットが検証に失敗した場合、X12 Decode はインターチェンジ全体を中断します。 
* 技術確認または機能確認を生成する (構成されている場合)
  * 技術確認は、ヘッダー検証の結果として生成されます。 技術確認は、アドレス受信者によるインターチェンジ ヘッダーとトレーラーの処理の状態を報告します。
  * 機能確認は、本文検証の結果として生成されます。 機能確認は、受信したドキュメントの処理中に発生した各エラーを報告します。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](../logic-apps/logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報") 


