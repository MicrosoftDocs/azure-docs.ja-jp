---
title: EDIFACT メッセージをデコードする
description: Azure Logic Apps と Enterprise Integration Pack の EDIFACT メッセージ デコーダーを使用して、EDI の検証および受信確認の生成を行います
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000013"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Enterprise Integration Pack を使用して Azure Logic Apps の EDIFACT メッセージをデコードする

Decode EDIFACT メッセージ コネクタを使用すると、EDI と取引先固有のプロパティの検証、トランザクション セットへのインターチェンジの分割またはインターチェンジ全体の保持、処理されたトランザクションの受信確認の生成を行うことができます。 このコネクタを使用するには、ロジック アプリの既存のトリガーにコネクタを追加する必要があります。

## <a name="before-you-start"></a>開始する前に

必要な項目を次に示します。

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。 Decode EDIFACT Message コネクタを使用するには、統合アカウントが必要です。 
* 統合アカウントで既に定義されている 2 つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)
* 統合アカウントで既に定義されている [EDIFACT 契約](logic-apps-enterprise-integration-edifact.md)

## <a name="decode-edifact-messages"></a>EDIFACT メッセージをデコードする

> [!IMPORTANT]
> EDIFACT コネクタでは、UTF-8 文字のみがサポートされます。
> 出力に予期しない文字が含まれている場合は、EDIFACT メッセージで UTF-8 文字セットが使用されていることを確認します。 

1. [ロジック アプリを作成](quickstart-create-first-logic-app-workflow.md)します。

2. Decode EDIFACT Message コネクタには、トリガーがありません。そのため、要求トリガーのように、ロジック アプリを起動するためのトリガーを追加する必要があります。 ロジック アプリ デザイナーで、ロジック アプリにトリガーを追加して、アクションを追加します。

3. 検索ボックスに、フィルターとして「EDIFACT」と入力します。 **[EDIFACT メッセージをデコードする]** を選択します。
   
    ![search EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. 以前に統合アカウントへの接続を作成していない場合は、ここでその接続を作成するように求められます。 接続の名前を指定し、接続する統合アカウントを選択します。
   
    ![create integration account](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    アスタリスクが付いているプロパティは必須です。

    | プロパティ | 詳細 |
    | --- | --- |
    | 接続名 * |接続の任意の名前を入力します。 |
    | 統合アカウント * |統合アカウントの名前を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

4. 接続の作成が完了したら、 **[作成]** を選択します。 接続の詳細は次の例のようになります。

    ![統合アカウントの詳細](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. 接続が作成されたら、次の例に示すように、デコードする EDIFACT フラット ファイル メッセージを選択します。

    ![integration account connection created](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    次に例を示します。

    ![デコードする EDIFACT フラット ファイル メッセージの選択](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>EDIFACT デコーダーの詳細

Decode EDIFACT コネクタは次のタスクを実行します。 

* 取引先契約と照らし合わせてエンベロープを検証する。
* 送信者の修飾子および識別子を受信者の修飾子および識別子と照合することで、契約を解決する。
* トランザクションに契約の受信設定構成に基づく 2 つ以上のトランザクションがある場合は、インターチェンジを複数のトランザクションに分割する。
* インターチェンジを逆アセンブルする
* EDI およびパートナー固有のプロパティに対して、次のような検証を行う。
  * インターチェンジ エンベロープの構造の検証
  * 制御スキーマと照らし合わせたエンベロープのスキーマ検証
  * メッセージ スキーマと照らし合わせたトランザクション セット データ要素のスキーマ検証
  * トランザクション セット データ要素に対して実行される EDI 検証
* インターチェンジ、グループ、およびトランザクション セットの制御番号が重複していないことを検証する (構成されている場合) 
  * 以前に受信したインターチェンジと照らし合わせて、インターチェンジ制御番号を確認する。 
  * インターチェンジ内の他のグループ制御番号と照らし合わせて、グループ制御番号を確認する。 
  * グループ内の他のトランザクション セット制御番号と照らし合わせて、トランザクション セット制御番号を確認する。
* インターチェンジをトランザクション セットに分割するか、インターチェンジ全体を保持する。
  * インターチェンジをトランザクション セットとして分割 - エラー発生時にトランザクション セットを中断インターチェンジをトランザクション セットに分割し、各トランザクション セットを解析します。 
  EDIFACT Decode アクションによって、検証が失敗したこれらのトランザクション セットのみが `badMessages` に出力され、残りのトランザクション セットが `goodMessages` に出力されます。
  * インターチェンジをトランザクション セットとして分割 - エラー発生時にインターチェンジを中断インターチェンジをトランザクション セットに分割し、各トランザクション セットを解析します。 
  インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、EDIFACT Decode アクションによって、そのインターチェンジのすべてのトランザクション セットが `badMessages` に出力されます。
  * インターチェンジの保存 - エラー発生時にトランザクション セットを中断インターチェンジを保持し、バッチ インターチェンジ全体を処理します。 
  EDIFACT Decode アクションによって、検証が失敗したこれらのトランザクション セットのみが `badMessages` に出力され、残りのトランザクション セットが `goodMessages` に出力されます。
  * インターチェンジの保存 - エラー発生時にインターチェンジを中断インターチェンジを保持し、バッチ インターチェンジ全体を処理します。 
  インターチェンジの 1 つ以上のトランザクション セットが検証に失敗した場合、EDIFACT Decode アクションによって、そのインターチェンジのすべてのトランザクション セットが `badMessages` に出力されます。
* 技術 (制御) 確認または機能確認を生成する (構成されている場合)
  * 技術確認または制御確認は、完全に受信したインターチェンジの構文チェックの結果を報告します。
  * 機能確認は、受信したインターチェンジまたはグループの承認または拒否を確認します。

## <a name="view-swagger-file"></a>Swagger ファイルを表示する
EDIFACT コネクタの Swagger の詳細を表示するには、「[EDIFACT](/connectors/edifact/)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack について学習する") 

