---
title: MQ サーバーに接続する - Azure Logic Apps | Microsoft Docs
description: Azure またはオンプレミスの MQ サーバーと Azure Logic Apps を使用して、メッセージを送信および取得します
author: valrobb
ms.author: valthom
ms.date: 06/01/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 9e6ae5cb0afd75a1e87fe4d4d0cf307abab5a02a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167883"
---
# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>MQ コネクタを使用してロジック アプリから IBM MQ サーバーに接続する

Microsoft Connector for MQ は、オンプレミスの、または Azure 内の MQ サーバーに保管されているメッセージの送信と取得を行います。 このコネクタには、TCP/IP ネットワーク経由でリモート IBM MQ サーバーと通信する Microsoft MQ クライアントが含まれています。 このドキュメントは、MQ コネクタを使用するためのスターター ガイドです。 まずキューの 1 つのメッセージを参照することから始め、次いでその他のアクションを試してみるようお勧めします。

MQ コネクタには、次のアクションがあります。 トリガーはありません。

- IBM MQ サーバーから削除しないで 1 つのメッセージを参照する
- IBM MQ サーバーから削除しないで一群のメッセージを参照する
- 1 つのメッセージを受信し、IBM MQ サーバーから削除する
- 一群のメッセージを受信し、IBM MQ サーバーから削除する
- IBM MQ サーバーに 1 つのメッセージを送信する

## <a name="prerequisites"></a>前提条件

* オンプレミスの MQ サーバーを使用している場合は、ご使用のネットワーク内のサーバーに[オンプレミスのデータ ゲートウェイをインストール](../logic-apps/logic-apps-gateway-install.md)します。 MQ サーバーが一般に公開されている、または Azure 内で使用できる場合、データ ゲートウェイは使用されない、または不要です。

    > [!NOTE]
    > MQ コネクタが動作するには、オンプレミスのデータ ゲートウェイがインストールされているサーバーに .NET Framework 4.6 もインストールする必要があります。

* オンプレミスのデータ ゲートウェイ用に Azure リソースを作成します (「[データ ゲートウェイ接続を設定する](../logic-apps/logic-apps-gateway-connection.md)」)。

* 正式にサポートされている以下のバージョンの IBM WebSphere MQ:
    * MQ 7.5
    * MQ 8.0

## <a name="create-a-logic-app"></a>ロジック アプリを作成します

1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **[名前]** (例: MQTestApp)、**[サブスクリプション]**、**[リソース グループ]**、**[場所]** (オンプレミスのデータ ゲートウェイ接続が設定されている場所を使用) を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。  
![ロジック アプリの作成](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>トリガーの追加

> [!NOTE]
> MQ コネクタにはトリガーがありません。 そのため、**繰り返し**トリガーなどの別のトリガーを使って、ロジック アプリを開始します。

1. **Logic Apps デザイナー**が表示され、一般的なトリガーの一覧で **[繰り返し]** を選択します。
2. 繰り返しトリガーで **[編集]** を選択します。
3. **[頻度]** を **[日]** に、**[間隔]** を **[7]** に設定します。

## <a name="browse-a-single-message"></a>1 つのメッセージの参照
1. **[+ New step]\(+ 新しいステップ\)**、**[アクションの追加]** の順に選択します。
2. 検索ボックスで `mq` と入力し、**[MQ - Browse message]\(MQ - メッセージの参照\)** を選択します。  
![メッセージの参照](media/connectors-create-api-mq/Browse_message.png)

3. 既存の MQ 接続がない場合は、次の手順に従って接続を作成します。  

    1. **[オンプレミスのデータ ゲートウェイ経由で接続]** を選択し、MQ サーバーのプロパティを入力します。  
    **[サーバー]** には、MQ サーバーの名前を入力するか、IP アドレスに続けてコロンとポート番号を入力します。
    2. **[ゲートウェイ]** ドロップダウンには、構成済みの既存のゲートウェイ接続が一覧表示されます。 ゲートウェイを選択します。
    3. 入力し終えたら **[作成]** を選択します。 接続は次のようになります。  
    ![接続のプロパティ](media/connectors-create-api-mq/Connection_Properties.png)

4. アクションのプロパティでは、次のことができます。  

    * **[キュー]** プロパティを使用して、接続に定義されているものと異なるキュー名にアクセスする
    * **[MessageId]**、**[CorrelationId]**、**[GroupId]** などのプロパティを使用し、さまざまな MQ メッセージのプロパティに基づいてメッセージを参照する
    * **[IncludeInfo]** を **[True]** に設定して、メッセージに関する追加情報を出力に含める。 メッセージに関する追加情報を出力に含めない場合は、**[False]** に設定します。
    * **[タイムアウト]** の値を入力して、空のキューにメッセージが到着するまで待機する時間を決定する。 何も入力しない場合は、キューの最初のメッセージが取得され、その後にメッセージの出現を待機する時間はありません。  
    ![メッセージの参照のプロパティ](media/connectors-create-api-mq/Browse_message_Props.png)

5. 変更内容を **[保存]** し、ロジック アプリを **[実行]** します。  
![保存と実行](media/connectors-create-api-mq/Save_Run.png)

6. 数秒後に実行の手順が表示され、出力を確認できます。 各手順の詳細を表示するには、緑色のチェックマークを選択します。 出力データの追加情報を表示するには、**[未加工出力の表示]** を選択します。  
![メッセージの参照の出力](media/connectors-create-api-mq/Browse_message_output.png)  

    未加工出力:  
    ![メッセージの参照の未加工出力](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. **[IncludeInfo]** オプションが True に設定されている場合は、次の出力が表示されます。  
![メッセージの参照に含まれる情報](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>複数のメッセージの参照
**[Browse messages]\(メッセージの参照\)** アクションには、キューから返されるメッセージの数を指定する **[BatchSize]** オプションが含まれます。  **[BatchSize]** を指定しないと、すべてのメッセージが返されます。 返される出力は、メッセージの配列です。

1. **[Browse messages]\(メッセージの参照\)** アクションを追加すると、構成されている最初の接続が既定で選択されます。 新しい接続を作成するか、別の接続を選択するには、**[Change connection]\(接続の変更\)** を選択します。

2. メッセージの参照の出力は次のようになります。  
![メッセージの参照の出力](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>1 つのメッセージの受信
**[メッセージの受信]** アクションの入力と出力は、**[Browse message]\(メッセージの参照\)** アクションと同じです。 **[メッセージの受信]** を使用する場合、メッセージはキューから削除されます。

## <a name="receive-multiple-messages"></a>複数のメッセージの受信
**[メッセージの受信]** アクションの入力と出力は、**[Browse messages]\(メッセージの参照\)** アクションと同じです。 **[メッセージの受信]** を使用する場合、メッセージはキューから削除されます。

キューにメッセージが含まれていない場合に参照や受信を行うと、手順が失敗して次の出力が表示されます。  
![MQ のメッセージなしエラー](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>メッセージの送信
1. **[メッセージの送信]** アクションを追加すると、構成されている最初の接続が既定で選択されます。 新しい接続を作成するか、別の接続を選択するには、**[Change connection]\(接続の変更\)** を選択します。 有効な **[メッセージの種類]** は **[Datagram]\(データグラム\)**、**[返信]**、**[要求]** です。  
![メッセージの送信のプロパティ](media/connectors-create-api-mq/Send_Msg_Props.png)

2. メッセージの送信の出力は次のようになります。  
![メッセージの送信の出力](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/mq/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="next-steps"></a>次の手順
[ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。
