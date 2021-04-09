---
title: EDIFACT メッセージをエンコードする
description: Enterprise Integration Pack を備えた Azure Logic Apps 向けの EDIFACT メッセージ エンコーダーを使用して、EDI の検証と XML の生成を行います
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82106527"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Enterprise Integration Pack を備えた Azure Logic Apps の EDIFACT メッセージをエンコードする

Encode EDIFACT Message コネクタでは、EDI およびパートナー固有のプロパティを検証したり、各トランザクション セットに対して XML ドキュメントを生成したり、技術確認と機能確認のいずれかまたは両方を要求したりできます。
このコネクタを使用するには、ロジック アプリの既存のトリガーにコネクタを追加する必要があります。

## <a name="before-you-start"></a>開始する前に

必要な項目を次に示します。

* Azure アカウント。[無料アカウント](https://azure.microsoft.com/free)を作成できます。
* 既に定義され、Azure サブスクリプションに関連付けられている[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)。 Encode EDIFACT Message コネクタを使用するには、統合アカウントが必要です。 
* 統合アカウントで既に定義されている 2 つ以上の[パートナー](logic-apps-enterprise-integration-partners.md)
* 統合アカウントで既に定義されている [EDIFACT 契約](logic-apps-enterprise-integration-edifact.md)

## <a name="encode-edifact-messages"></a>EDIFACT メッセージをエンコードする

> [!IMPORTANT]
> EDIFACT コネクタでは、UTF-8 文字のみがサポートされます。
> 出力に予期しない文字が含まれている場合は、EDIFACT メッセージで UTF-8 文字セットが使用されていることを確認します。

1. [ロジック アプリを作成](quickstart-create-first-logic-app-workflow.md)します。

2. Encode EDIFACT Message コネクタには、トリガーがありません。そのため、要求トリガーのように、ロジック アプリを起動するためのトリガーを追加する必要があります。 ロジック アプリ デザイナーで、ロジック アプリにトリガーを追加して、アクションを追加します。

3.  検索ボックスに、フィルターとして「EDIFACT」と入力します。 **[契約名で EDIFACT メッセージにエンコードする]** または **[ID で EDIFACT メッセージにエンコードする]** を選択します。
   
    ![search EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. 以前に統合アカウントへの接続を作成していない場合は、ここでその接続を作成するように求められます。 接続の名前を指定し、接続する統合アカウントを選択します。

    ![create integration account connection](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    アスタリスクが付いているプロパティは必須です。

    | プロパティ | 詳細 |
    | --- | --- |
    | 接続名 * |接続の任意の名前を入力します。 |
    | 統合アカウント * |統合アカウントの名前を入力します。 統合アカウントとロジック アプリが同じ Azure の場所にあることを確認してください。 |

5.  完了したら、接続の詳細は次の例のようになります。 接続の作成を完了するには、 **[作成]** を選択します。

    ![integration account connection details](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    接続が作成されました。

    ![integration account connection created](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>契約名による Encode EDIFACT Message

契約名で EDIFACT メッセージをエンコードすることを選択した場合は、**[EDIFACT 契約の名前]** リストを開き、EDIFACT 契約名を入力または選択します。 エンコードする XML メッセージを入力します。

![EDIFACT 契約名とエンコードする XML メッセージの入力](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>ID による Encode EDIFACT Message

ID で EDIFACT メッセージをエンコードすることを選択した場合は、送信者の識別子、送信者の修飾子、受信者の識別子、および受信者の修飾子を、EDIFACT 契約で構成されているとおりに入力します。 エンコードする XML メッセージを選択します。

![送信者と受信者の識別子の指定、エンコードする XML メッセージの選択](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>EDIFACT エンコードの詳細

Encode EDIFACT コネクタは次のタスクを実行します。 

* 送信者の修飾子および識別子を受信者の修飾子および識別子と照合することで、契約を解決する
* XML エンコード メッセージをインターチェンジ内の EDI トランザクション セットに変換して、EDI インターチェンジをシリアル化する
* トランザクション セット ヘッダーおよびトレーラー セグメントを適用する
* 各送信インターチェンジのインターチェンジ制御番号、グループ制御番号、およびトランザクション セット制御番号を生成する
* ペイロード データの区切り記号を置換する
* EDI およびパートナー固有のプロパティを検証する
  * メッセージ スキーマと照らし合わせたトランザクション セット データ要素のスキーマ検証
  * トランザクション セット データ要素に対して実行される EDI 検証
  * トランザクション セット データ要素に対して実行される拡張検証
* 各トランザクション セットの XML ドキュメントを生成する
* 技術確認または機能確認を要求する (構成されている場合)
  * 技術確認として、CONTRL メッセージはインターチェンジの受信を示します。
  * 機能確認として、CONTRL メッセージは受信したインターチェンジ、グループ、またはメッセージの受理または拒否を、エラーまたはサポートされていない機能の一覧と共に示します。

## <a name="view-swagger-file"></a>Swagger ファイルを表示する
EDIFACT コネクタの Swagger の詳細を表示するには、「[EDIFACT](/connectors/edifact/)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Enterprise Integration Pack についての詳細情報](logic-apps-enterprise-integration-overview.md "Enterprise Integration Pack について学習する") 

