---
title: IBM MQ サーバーへの接続 - Azure Logic Apps
description: Azure またはオンプレミスの IBM MQ サーバーと Azure Logic Apps を使用して、メッセージを送信および取得します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273126"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Azure Logic Apps からの IBM MQ サーバーへの接続

IBM MQ コネクタは、オンプレミスの、または Azure 内の IBM MQ サーバーに保管されているメッセージの送信と取得を行います。 このコネクタには、TCP/IP ネットワーク経由でリモート IBM MQ サーバーと通信する Microsoft MQ クライアントが含まれています。 この記事は、MQ コネクタを使用するためのスターター ガイドです。 まずキューの 1 つのメッセージを参照することから始め、次いでその他のアクションを試してみることができます。

IBM MQ コネクタには以下のアクションが含まれていますが、トリガーが用意されていません。

- IBM MQ サーバーから削除しないで 1 つのメッセージを参照する
- IBM MQ サーバーから削除しないで一群のメッセージを参照する
- 1 つのメッセージを受信し、IBM MQ サーバーから削除する
- 一群のメッセージを受信し、IBM MQ サーバーから削除する
- IBM MQ サーバーに 1 つのメッセージを送信する

## <a name="prerequisites"></a>前提条件

* オンプレミスの MQ サーバーを使用している場合は、ご使用のネットワーク内のサーバーに[オンプレミスのデータ ゲートウェイをインストール](../logic-apps/logic-apps-gateway-install.md)します。 MQ コネクタが動作するには、オンプレミスのデータ ゲートウェイがインストールされているサーバーに .NET Framework 4.6 もインストールする必要があります。 オンプレミス データ ゲートウェイ向けの Azure リソースも作成する必要があります。 詳細については、[データ ゲートウェイ接続の設定に関する記事](../logic-apps/logic-apps-gateway-connection.md)を参照してください。

  ただし、MQ サーバーが一般に公開されている、または Azure 内で使用できる場合、データ ゲートウェイを使用する必要はありません。

* 正式にサポートされている以下のバージョンの IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* MQ アクションを追加するロジック アプリ。 このロジック アプリは、オンプレミス データ ゲートウェイ接続と同じ場所を使用し、ワークフローを開始するトリガーが既にある必要があります。 

  MQ コネクタにはトリガーがないので、最初にロジック アプリにトリガーを追加する必要があります。 たとえば、繰り返しトリガーを使用できます。 ロジック アプリを初めて使用する場合は、[初めてのロジック アプリを作成する方法に関するクイックスタート](../logic-apps/quickstart-create-first-logic-app-workflow.md)を試してみてください。 

## <a name="browse-a-single-message"></a>1 つのメッセージの参照

1. ロジック アプリのトリガーまたは別のアクションで、 **[新しいステップ]** を選択します。 

1. 検索ボックスに「mq」と入力し、次のアクションを選択します。**メッセージの参照**

   ![メッセージの参照](media/connectors-create-api-mq/Browse_message.png)

1. 既存の MQ 接続がない場合は、次の手順に従って接続を作成します。  

   1. アクションで、 **[オンプレミスのデータ ゲートウェイ経由で接続する]** を選択します。
   
   1. MQ サーバーのプロパティを入力します。  

      **[サーバー]** には、MQ サーバーの名前を入力するか、IP アドレスに続けてコロンとポート番号を入力します。
    
   1. **ゲートウェイ**の一覧を開きます。これには、以前に構成されていたゲートウェイ接続が表示されます。 ゲートウェイを選択します。
    
   1. 操作が完了したら、 **[作成]** を選択します。 
   
      この接続は次の例のようになります。

      ![接続のプロパティ](media/connectors-create-api-mq/Connection_Properties.png)

1. アクションのプロパティを設定します。

   * **Queue**: 接続とは異なるキューを指定します。

   * **MessageId**、 **CorrelationId**、 **GroupId**、およびその他のプロパティ: さまざまな MQ メッセージ プロパティに基づいてメッセージを参照します。

   * **IncludeInfo**: **True** を設定すると、メッセージに関する追加情報が出力に含まれます。 または、**False** を設定すると、メッセージに関する追加情報が出力に含まれません。

   * **タイムアウト**:値を入力して、空のキューにメッセージが到着するまで待機する時間を決定します。 何も入力しない場合は、キューの最初のメッセージが取得され、その後にメッセージの出現を待機する時間はありません。

     ![メッセージの参照のプロパティ](media/connectors-create-api-mq/Browse_message_Props.png)

1. 変更内容を **[保存]** し、ロジック アプリを **[実行]** します。

   ![保存と実行](media/connectors-create-api-mq/Save_Run.png)

   実行が完了すると、実行の手順が表示され、出力を確認することができます。

1. 各手順の詳細を確認するには、緑色のチェックマークを選択します。 出力データの詳細を確認するには、 **[未加工出力の表示]** を選択します。

   ![メッセージの参照の出力](media/connectors-create-api-mq/Browse_message_output.png)  

   未加工出力の例を次に示します。

   ![メッセージの参照の未加工出力](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. **IncludeInfo** を True に設定した場合は、次の出力が表示されます。

   ![メッセージの参照に含まれる情報](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>複数のメッセージの参照

**[Browse messages]\(メッセージの参照\)** アクションには、キューから返されるメッセージの数を指定する **[BatchSize]** オプションが含まれます。  **[BatchSize]** を指定しないと、すべてのメッセージが返されます。 返される出力は、メッセージの配列です。

1. **[Browse messages] (メッセージの参照)** アクションを追加すると、以前に構成された最初の接続が既定で選択されます。 新しい接続を作成するには、 **[接続の変更]** を選択します。 または、別の接続を選択します。

1. ロジック アプリの実行完了後の **[Browse messages] (メッセージの参照)** アクションの出力例を示します。

   ![メッセージの参照の出力](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>1 つのメッセージの受信

**[メッセージの受信]** アクションの入力と出力は、 **[Browse message]\(メッセージの参照\)** アクションと同じです。 **[メッセージの受信]** を使用する場合、メッセージはキューから削除されます。

## <a name="receive-multiple-messages"></a>複数のメッセージの受信

**[メッセージの受信]** アクションの入力と出力は、 **[Browse messages]\(メッセージの参照\)** アクションと同じです。 **[メッセージの受信]** を使用する場合、メッセージはキューから削除されます。

キューにメッセージが含まれていない場合に参照や受信を行うと、手順が失敗して次の出力が表示されます。  

![MQ のメッセージなしエラー](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>メッセージを送信する

**[Send messages] (メッセージの送信)** アクションを追加すると、以前に構成された最初の接続が既定で選択されます。 新しい接続を作成するには、 **[接続の変更]** を選択します。 または、別の接続を選択します。

1. 有効なメッセージの種類を選択します: **[データグラム]** 、 **[返信]** 、または **[要求]**  

   ![メッセージの送信のプロパティ](media/connectors-create-api-mq/Send_Msg_Props.png)

1. ロジック アプリの実行完了後の **[Send messages] (メッセージの送信)** アクションの出力例を示します。

   ![メッセージの送信の出力](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているアクションおよび制限に関する技術的な詳細については、[コネクタのリファレンス ページ](/connectors/mq/)を参照してください。

## <a name="next-steps"></a>次の手順

* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
