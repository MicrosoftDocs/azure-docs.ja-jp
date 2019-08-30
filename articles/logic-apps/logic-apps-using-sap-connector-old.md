---
title: SAP システムに接続する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps を使用してワークフローを自動化することにより、SAP のリソースにアクセスしてそれを管理する方法について説明します。
author: ecfan
ms.author: estfan
ms.date: 05/31/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, divswa, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 31f6a3fc281b8dc309ddcd237246c870c85ae20b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971642"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps から SAP システムに接続する

> [!NOTE]
> この SAP コネクタは非推奨になる予定です。 [より新しくより高度な SAP コネクタ](./logic-apps-using-sap-connector.md)を使用するか、それに移行してください。 

この記事では、SAP アプリケーション サーバー コネクタと SAP メッセージ サーバー コネクタを使用して、ロジック アプリ内から SAP のリソースにアクセスする方法を紹介します。 これにより、SAP のデータやリソースを管理するタスク、プロセス、ワークフローを自動化することができます。

この例では、HTTP 要求でトリガーすることのできるロジック アプリを使用します。 このロジック アプリは、SAP サーバーに IDoc (Intermediate Document) を送信し、そのロジック アプリを呼び出した要求元に応答を返します。
現在の SAP コネクタには、アクションはありますが、トリガーがありません。そこで、この例では、ロジック アプリのワークフローにおける最初のステップとして、[HTTP 要求トリガー](../connectors/connectors-native-reqres.md)を使用しています。 SAP コネクタに固有の技術情報については、次のリファレンス記事を参照してください。 

* <a href="https://docs.microsoft.com/connectors/sap" target="blank">SAP アプリケーション サーバー コネクタ</a>
* <a href="https://docs.microsoft.com/connectors/sap/#send-message-to-sap" target="blank">SAP メッセージ サーバー コネクタ</a>

Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

## <a name="prerequisites"></a>前提条件

この記事に沿って作業を行うには、次の要件を満たす必要があります。

* SAP システムへのアクセスに使用するロジック アプリとそのロジック アプリのワークフローを開始するトリガー。 現在 SAP コネクタに用意されているのはアクションだけです。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP アプリケーション サーバー</a>または <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP メッセージ サーバー</a>。

* オンプレミスの任意のコンピューターに最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)をダウンロードしてインストールします。 事前に Azure portal でゲートウェイをセットアップしておいてください。 ゲートウェイを使用することで、オンプレミスのデータやリソースに安全にアクセスすることができます。 詳細については、「[Azure Logic Apps 向けのオンプレミス データ ゲートウェイをインストールする](../logic-apps/logic-apps-gateway-install.md)」を参照してください。

* オンプレミス データ ゲートウェイと同じコンピューターに最新の SAP クライアント ライブラリをダウンロードしてインストールします。現時点では、<a href="https://softwaredownloads.sap.com/file/0020000000086282018" target="_blank">SAP Connector (NCo) 3.0.20.0 for Microsoft .NET Framework 4.0 and Windows 64bit (x64)</a> が最新となります。 このバージョン以降をインストールしてください。これには次の理由があります。

  * 以前のバージョンの SAP NCo は、複数の IDoc メッセージが同時に送信されるとデッドロック状態に陥ることがあります。 
  この状態になると、以後、SAP ターゲットに送信されたすべてのメッセージがブロックされ、メッセージがタイムアウトになります。

  * オンプレミス データ ゲートウェイは 64 ビット システムでのみ動作します。 
  データ ゲートウェイ ホスト サービスは 32 ビットのアセンブリをサポートしないので、それ以外の場合、"イメージが無効" であることを示すエラーが発生します。

  * データ ゲートウェイ ホスト サービスと Microsoft SAP アダプターでは、どちらも .NET Framework 4.5 が使用されます。 SAP NCo for .NET Framework 4.0 は、.NET ランタイム 4.0 から 4.7.1 を使用するプロセスとの組み合わせで正しく動作します。 
  SAP NCo for .NET Framework 2.0 が正しく動作するのは、.NET ランタイム 2.0 から 3.5 を使用するプロセスとの組み合わせにおいてであり、最新のオンプレミス データ ゲートウェイとの組み合わせはサポートされません。

* SAP サーバーに送信できるメッセージの内容 (サンプル IDoc ファイルなど)。 この内容は XML 形式とし、使用する SAP アクションの名前空間を含んでいる必要があります。

<a name="add-trigger"></a>

## <a name="add-http-request-trigger"></a>HTTP 要求トリガーの追加

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

この例では、ロジック アプリに "*HTTP POST 要求*" を送信できるよう、Azure にエンドポイントを持つロジック アプリを作成します。 ロジック アプリがこれらの HTTP 要求を受信すると、トリガーが作動してワークフロー内の次のステップが実行されます。

1. Azure portal で空のロジック アプリを作成して、ロジック アプリ デザイナーを開きます。 

2. 検索ボックスに、フィルターとして「HTTP 要求」と入力します。 トリガーの一覧から、 **[要求 - HTTP 要求の受信時]** を選択します。

   ![HTTP 要求トリガーの追加](./media/logic-apps-using-sap-connector-old/add-trigger.png)

3. ロジック アプリに使用するエンドポイントの URL を生成するために、ここでロジック アプリを保存します。
デザイナーのツール バーで、 **[保存]** を選択します。 

   次のように、エンドポイントの URL がトリガーに表示されます。

   ![エンドポイントの URL の生成](./media/logic-apps-using-sap-connector-old/generate-http-endpoint-url.png)

<a name="add-action"></a>

## <a name="add-sap-action"></a>SAP アクションの追加

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例に沿って作業を進める場合、まだロジック アプリにトリガーを追加していない方は、[こちらのセクションで説明されているトリガーを追加](#add-trigger)してください。

1. ロジック アプリ デザイナーで、トリガーの下に表示される **[新しいステップ]**  >  **[アクションの追加]** を順に選択します。

   ![アクションを追加する](./media/logic-apps-using-sap-connector-old/add-action.png) 

2. 検索ボックスに、フィルターとして「SAP サーバー」と入力します。 アクションの一覧から、目的の SAP サーバーのアクションを選択します。 

   * **[SAP Application Server - Send to SAP]\(SAP アプリケーション サーバー - SAP に送信\)**
   * **[SAP Message Server - Send to SAP]\(SAP メッセージ サーバー - SAP に送信\)**

   この例では、次のアクションを使用します。 **[SAP Application Server - Send to SAP]\(SAP アプリケーション サーバー - SAP に送信\)**

   !["SAP アプリケーション サーバー" または "SAP メッセージ サーバー" を選択](media/logic-apps-using-sap-connector-old/select-sap-action.png)

3. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 それ以外の場合、接続が既に存在する場合は、次の手順に進んで SAP アクションを設定してください。 

   **オンプレミス SAP 接続の作成**

   1. オンプレミス接続のプロパティを表示するために、 **[ゲートウェイ]** の **[オンプレミスのデータ ゲートウェイ経由で接続]** をオンにします。

   2. SAP サーバーの接続情報を指定します。 
   **gateway** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択してください (次の例を参照)。

      **SAP アプリケーション サーバー**

      ![SAP アプリケーション サーバー接続を作成](./media/logic-apps-using-sap-connector-old/create-SAP-app-server-connection.png)  

      **SAP メッセージ サーバー**

      ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector-old/create-SAP-message-server-connection.png) 

   2. 操作が完了したら、 **[作成]** を選択します。

      Logic Apps により、接続のセットアップとテストが行われ、適切に機能していることが確認されます。

4. 次に、SAP サーバーのアクションを検索して選択します。 

   1. **[SAP アクション]** ボックスのフォルダー アイコンを選択します。 
   フォルダー一覧から、使用するアクションを検索して選択します。 

      この例では、IDoc アクションの **[IDOC]** カテゴリを選択しています。 

      ![IDoc アクションを検索して選択](./media/logic-apps-using-sap-connector-old/SAP-app-server-find-action.png)

      目的のアクションが見つからない場合は、パスを手動で入力してください。その例を次に示します。

      ![IDoc アクションのパスを手動で入力](./media/logic-apps-using-sap-connector-old/SAP-app-server-manually-enter-action.png)

      IDoc の操作の詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

   2. **[入力メッセージ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 
   このリストの **[HTTP 要求の受信時]** で、 **[本文]** フィールドを選択します。 

      このステップで、HTTP 要求トリガーから本文コンテンツが取り込まれ、その出力が SAP サーバーに送信されます。

      ![[本文] フィールドを選択](./media/logic-apps-using-sap-connector-old/SAP-app-server-action-select-body.png)

      完成した SAP アクションは次の例のようになります。

      ![SAP アクションの完成](./media/logic-apps-using-sap-connector-old/SAP-app-server-complete-action.png)

6. ロジック アプリを保存し、 デザイナーのツール バーで、 **[保存]** を選択します。

<a name="add-response"></a>

## <a name="add-http-response-action"></a>HTTP 応答アクションの追加

ロジック アプリのワークフローに応答アクションを追加し、SAP アクションからの出力を取り込みます。 こうして SAP サーバーから受け取った結果が、ロジック アプリから要求元に返されます。 

1. ロジック アプリ デザイナーで、SAP アクションの下に表示される **[新しいステップ]**  >  **[アクションの追加]** を順に選択します。

2. 検索ボックスに、フィルターとして「応答」と入力します。 アクションの一覧から、次のアクションを選択します。 **[要求と応答]**

3. **[本文]** ボックス内をクリックして、動的コンテンツ リストを表示します。 このリストの **[Send to SAP]\(SAP に送信\)** で、 **[本文]** フィールドを選択します。 

   ![SAP アクションの完成](./media/logic-apps-using-sap-connector-old/select-sap-body-for-response-action.png)

4. ロジック アプリを保存し、 

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. まだロジック アプリが有効になっていない場合は、ロジック アプリのメニューで **[概要]** を選択します。 ツール バーで、 **[有効]** を選択します。 

2. ロジック アプリ デザイナーのツール バーの **[実行]** を選択します。 この手順では、ロジック アプリを手動で起動します。

3. HTTP 要求トリガーの URL に HTTP POST 要求を送信してロジック アプリをトリガーし、要求にメッセージの内容を追加します。 要求は、[Postman](https://www.getpostman.com/apps) などのツールを使用して送信することができます。 

   この記事の要求で送信するのは IDoc ファイルです。このファイルは XML 形式であること、また、使用する SAP アクションの名前空間を含んでいる必要があります。その例を次に示します。 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//620/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. HTTP 要求を送信したら、ロジック アプリからの応答を待機します。

> [!NOTE]
> 応答に必要なすべての手順が[要求タイムアウト制限](./logic-apps-limits-and-config.md)内に完了しない場合、ロジック アプリがタイムアウトします。 この状況に陥ると、要求がブロック状態になります。 問題の診断については、[ロジック アプリをチェックしたり監視したりする方法](../logic-apps/logic-apps-monitor-your-logic-apps.md)に関するページを参照してください。

お疲れさまでした。SAP サーバーとやり取りすることのできるロジック アプリは、これで完成です。 ロジック アプリに使用する SAP 接続をセットアップしたら、BAPI や RFC など、他に利用できる SAP アクションを探してみましょう。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタに関して、その Swagger ファイルで記述される技術的詳細については、次のリファレンス記事を参照してください。 

* [SAP アプリケーション サーバー](/connectors/sap)
* [SAP メッセージ サーバー](/connectors/sap/#send-message-to-sap)

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [ロジック アプリからオンプレミスのシステムに接続](../logic-apps/logic-apps-gateway-connection.md)します。
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) を使用して、検証や変換など、各種のメッセージ操作を実行する方法を確認します。
* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
