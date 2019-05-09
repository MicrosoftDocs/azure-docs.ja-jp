---
title: SAP システムに接続する - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps を使用してワークフローを自動化することにより、SAP のリソースにアクセスしてそれを管理する方法について説明します。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 09/14/2018
tags: connectors
ms.openlocfilehash: 468e73c64037a76da612cba8d6c2e9507dd3ac87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57887087"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps から SAP システムに接続する

この記事では、SAP ERP Central Component (ECC) コネクタを使用し、Logic Apps 内からオンプレミス SAP リソースにアクセスする方法を紹介します。 コネクタは、オンプレミスの ECC と S/4 HANA の両方のシステムで動作します。 SAP ECC コネクタでは、Intermediate Document (IDoc)、Business Application Programming Interface (BAPI)、Remote Function Call (RFC) を介し、SAP Netweaver ベースのシステムとの間でメッセージやデータが統合されます。

SAP ECC コネクタでは <a href="https://support.sap.com/en/product/connectors/msnet.html">SAP .NET Connector (NCo) ライブラリ</a>が使用され、次のような動作が行われます。

- **SAP に送信する**: SAP システムに tRFC で IDoc を送信したり、BAPI 関数を呼び出したりします。
- **SAP から受信する**: SAP システムから tRFC で IDoc や BAPI 関数呼び出しを受信します。
- **スキーマを生成する**: IDoc、BAPI、RFC 用に SAP アーティファクトのスキーマを生成します。

SAP コネクタは、[オンプレミスのデータ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)を介してオンプレミスの SAP システムと統合されます。 送信シナリオの場合、たとえば、Logic Apps から SAP システムにメッセージを送信するとき、データ ゲートウェイが RFC クライアントとして機能し、Logic Apps から受信した要求を SAP に転送します。
同様に、受信シナリオの場合、SAP から要求を受信し、Logic Apps に転送する RFC サーバーとしてデータ ゲートウェイが機能します。 

この記事では、SAP と統合されるサンプル ロジック アプリを作成する方法と前に説明した統合シナリオを紹介します。

## <a name="prerequisites"></a>前提条件

この記事に沿って作業を行うには、次の要件を満たす必要があります。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* SAP システムへのアクセスに使用するロジック アプリとそのロジック アプリのワークフローを開始するトリガー。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

* <a href="https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server" target="_blank">SAP アプリケーション サーバー</a>または <a href="https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm" target="_blank">SAP メッセージ サーバー</a>。

* オンプレミスの任意のコンピューターに最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)をダウンロードしてインストールします。 事前に Azure portal でゲートウェイをセットアップしておいてください。 ゲートウェイを使用することで、オンプレミスのデータやリソースに安全にアクセスすることができます。 詳細については、「[Azure Logic Apps 向けのオンプレミス データ ゲートウェイをインストールする](../logic-apps/logic-apps-gateway-install.md)」を参照してください。

* オンプレミス データ ゲートウェイと同じコンピューターに最新の SAP クライアント ライブラリをダウンロードしてインストールします。現時点では、<a href="https://softwaredownloads.sap.com/file/0020000001865512018" target="_blank">SAP Connector (NCo) 3.0.21.0 for Microsoft .NET Framework 4.0 and Windows 64bit (x64)</a> が最新となります。 このバージョン以降をインストールしてください。これには次の理由があります。

  * 以前のバージョンの SAP NCo は、複数の IDoc メッセージが同時に送信されるとデッドロック状態に陥ることがあります。 
  この状態になると、以後、SAP ターゲットに送信されたすべてのメッセージがブロックされ、メッセージがタイムアウトになります。

  * オンプレミス データ ゲートウェイは 64 ビット システムでのみ動作します。 
  データ ゲートウェイ ホスト サービスは 32 ビットのアセンブリをサポートしないので、それ以外の場合、"イメージが無効" であることを示すエラーが発生します。

  * データ ゲートウェイ ホスト サービスと Microsoft SAP アダプターでは、どちらも .NET Framework 4.5 が使用されます。 SAP NCo for .NET Framework 4.0 は、.NET ランタイム 4.0 から 4.7.1 を使用するプロセスとの組み合わせで正しく動作します。 
  SAP NCo for .NET Framework 2.0 が正しく動作するのは、.NET ランタイム 2.0 から 3.5 を使用するプロセスとの組み合わせにおいてであり、最新のオンプレミス データ ゲートウェイとの組み合わせはサポートされません。

* SAP サーバーに送信できるメッセージの内容 (サンプル IDoc ファイルなど)。 この内容は XML 形式とし、使用する SAP アクションの名前空間を含んでいる必要があります。

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>SAP に送信する

この例では、HTTP 要求でトリガーすることのできるロジック アプリを使用します。 このロジック アプリは、SAP サーバーに IDoc (Intermediate Document) を送信し、そのロジック アプリを呼び出した要求元に応答を返します。 

### <a name="add-http-request-trigger"></a>HTTP 要求トリガーの追加

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

この例では、ロジック アプリに "*HTTP POST 要求*" を送信できるよう、Azure にエンドポイントを持つロジック アプリを作成します。 ロジック アプリがこれらの HTTP 要求を受信すると、トリガーが作動してワークフロー内の次のステップが実行されます。

1. [Azure portal](https://portal.azure.com) で空のロジック アプリを作成し、ロジック アプリ デザイナーを開きます。 

2. 検索ボックスに、フィルターとして「HTTP 要求」と入力します。 トリガーの一覧から、**[要求 - HTTP 要求の受信時]** を選択します。

   ![HTTP 要求トリガーの追加](./media/logic-apps-using-sap-connector/add-trigger.png)

3. ロジック アプリに使用するエンドポイントの URL を生成するために、ここでロジック アプリを保存します。
デザイナーのツール バーで、**[保存]** を選択します。 

   次のように、エンドポイントの URL がトリガーに表示されます。

   ![エンドポイントの URL の生成](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>SAP アクションの追加

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例に沿って作業を進める場合、まだロジック アプリにトリガーを追加していない方は、[こちらのセクションで説明されているトリガーを追加](#add-trigger)してください。

1. ロジック アプリ デザイナーで、トリガーの下に表示される **[新しいステップ]** > **[アクションの追加]** を順に選択します。

   ![アクションを追加する](./media/logic-apps-using-sap-connector/add-action.png) 

2. 検索ボックスに、フィルターとして「sap」と入力します。 アクションの一覧から、次のアクションを選択します。**[Send message to SAP]\(SAP にメッセージを送信する\)**
  
   ![SAP 送信アクションの選択](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   あるいは、検索の代わりに、**[Enterprise]** タブを選択し、SAP アクションを選択します。

   ![[Enterprise] タブから SAP 送信アクションを選択する](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

3. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 それ以外の場合、接続が既に存在する場合は、次の手順に進んで SAP アクションを設定してください。 

   **オンプレミス SAP 接続の作成**

   1. SAP サーバーの接続情報を指定します。 
   **[データ ゲートウェイ]** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択します。

      **[ログオンの種類]** プロパティが **[アプリケーション サーバー]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

      ![SAP アプリケーション サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      **[ログオンの種類]** プロパティが **[グループ]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。 

      ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 操作が完了したら、**[作成]** を選択します。 
   
      Logic Apps により、接続のセットアップとテストが行われ、適切に機能していることが確認されます。

4. 次に、SAP サーバーのアクションを検索して選択します。 

   1. **[SAP アクション]** ボックスのフォルダー アイコンを選択します。 
   ファイルの一覧から、使用する SAP メッセージを検索して選択します。 
   一覧内を移動するには矢印を使用します。

      この例では、種類が **[Order]** の IDoc を選択しています。 

      ![IDoc アクションを検索して選択](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      目的のアクションが見つからない場合は、パスを手動で入力してください。その例を次に示します。

      ![IDoc アクションのパスを手動で入力](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > 式エディターで SAP アクションの値を入力します。 その方法により、異なる種類のメッセージに対して同じアクションを使用できます。

      IDoc の操作の詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

   2. **[入力メッセージ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 
   このリストの **[HTTP 要求の受信時]** で、**[本文]** フィールドを選択します。 

      このステップで、HTTP 要求トリガーから本文コンテンツが取り込まれ、その出力が SAP サーバーに送信されます。

      ![[本文] フィールドを選択](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      完成した SAP アクションは次の例のようになります。

      ![SAP アクションの完成](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

5. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。

<a name="add-response"></a>

### <a name="add-http-response-action"></a>HTTP 応答アクションの追加

ロジック アプリのワークフローに応答アクションを追加し、SAP アクションからの出力を取り込みます。 こうして SAP サーバーから受け取った結果が、ロジック アプリから要求元に返されます。 

1. ロジック アプリ デザイナーで、SAP アクションの下に表示される **[新しいステップ]** > **[アクションの追加]** を順に選択します。

2. 検索ボックスに、フィルターとして「応答」と入力します。 アクションの一覧から、次のアクションを選択します。**[要求と応答]**

3. **[本文]** ボックス内をクリックして、動的コンテンツ リストを表示します。 このリストの **[Send to SAP]\(SAP に送信\)** で、**[本文]** フィールドを選択します。 

   ![SAP アクションの完成](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

4. ロジック アプリを保存し、 

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. まだロジック アプリが有効になっていない場合は、ロジック アプリのメニューで **[概要]** を選択します。 ツール バーで、**[有効]** を選択します。 

2. ロジック アプリ デザイナーのツール バーの **[実行]** を選択します。 この手順では、ロジック アプリを手動で起動します。

3. HTTP 要求トリガーの URL に HTTP POST 要求を送信してロジック アプリをトリガーし、要求にメッセージの内容を追加します。 要求は、[Postman](https://www.getpostman.com/apps) などのツールを使用して送信することができます。 

   この記事の要求で送信するのは IDoc ファイルです。このファイルは XML 形式であること、また、使用する SAP アクションの名前空間を含んでいる必要があります。その例を次に示します。 

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

4. HTTP 要求を送信したら、ロジック アプリからの応答を待機します。

   > [!NOTE]
   > 応答に必要なすべての手順が[要求タイムアウト制限](./logic-apps-limits-and-config.md)内に完了しない場合、ロジック アプリがタイムアウトします。 この状況に陥ると、要求がブロック状態になります。 問題の診断については、[ロジック アプリをチェックしたり監視したりする方法](../logic-apps/logic-apps-monitor-your-logic-apps.md)に関するページを参照してください。

お疲れさまでした。SAP サーバーとやり取りすることのできるロジック アプリは、これで完成です。 ロジック アプリに使用する SAP 接続をセットアップしたら、BAPI や RFC など、他に利用できる SAP アクションを探してみましょう。

## <a name="receive-from-sap"></a>SAP から受信する

この例では、SAP システムからメッセージを受信するときにトリガーするロジック アプリを使用します。 

### <a name="add-sap-trigger"></a>SAP トリガーを追加する

1. Azure portal で空のロジック アプリを作成して、ロジック アプリ デザイナーを開きます。 

2. 検索ボックスに、フィルターとして「sap」と入力します。 トリガーの一覧から、**[When a message is received from SAP]\(SAP からメッセージを受信したとき\)** というトリガーを選択します。

   ![SAP トリガーを追加する](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   あるいは、[Enterprise] タブに移動し、トリガーを選択できます。

   ![[Enterprise] タブから SAP トリガーを追加する](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

3. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 それ以外の場合、接続が既に存在する場合は、次の手順に進んで SAP アクションを設定してください。 

   **オンプレミス SAP 接続の作成**

   1. SAP サーバーの接続情報を指定します。 
   **[データ ゲートウェイ]** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択します。

      **[ログオンの種類]** プロパティが **[アプリケーション サーバー]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

      ![SAP アプリケーション サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      **[ログオンの種類]** プロパティが **[グループ]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

      ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

4. SAP システム構成に基づいて必須パラメーターを指定します。 

   任意で 1 つまたは複数の SAP アクションを指定できます。 
   このアクション リストにより、データ ゲートウェイを介して SAP サーバーからトリガーが受信するメッセージが指定されます。 
   リストが空の場合、トリガーはすべてのメッセージを受信します。 
   リストに複数のメッセージが含まれる場合、リストで指定されているメッセージのみをトリガーは受信します。 他のメッセージが SAP サーバーから送信されても、ゲートウェイによって拒否されます。

   SAP アクションはファイル ピッカーから選択できます。

   ![SAP アクションの選択](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   あるいは、次のようにアクションを手動で指定できます。

   ![SAP アクションの手動入力](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   複数のメッセージを受信するようにトリガーを設定したときのアクションの表示例を次に示します。

   ![トリガー例](media/logic-apps-using-sap-connector/example-trigger.png)  

   SAP アクションの詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

5. それでは、SAP システムからメッセージを受信できるようにロジック アプリを保存します。
デザイナーのツール バーで、**[保存]** を選択します。 

これでロジック アプリは SAP システムからメッセージを受信できます。 

> [!NOTE]
> SAP トリガーはポーリング トリガーではなく、Webhook ベースのトリガーです。 このトリガーはメッセージが存在するときにのみゲートウェイから呼び出されます。そのため、ポーリングは必要ありません。 

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. ロジック アプリをトリガーするには、SAP システムからメッセージを送信します。

2. ロジック アプリのメニューで **[概要]** を選択し、ロジック アプリに新しい実行がないか、**[実行の履歴]** で確認します。 

3. 最新の実行を開きます。トリガー出力セクションに SAP システムから送信されたメッセージが表示されます。

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP でアーティファクトのスキーマを生成する

この例では、HTTP 要求でトリガーすることのできるロジック アプリを使用します。 この SAP アクションにより、指定の Intermediate Document (IDoc) と BAPI 向けのスキーマの生成要求が SAP システムに送信されます。 応答で返されたスキーマは、Azure Resource Manager コネクタを利用し、統合アカウントにアップロードされます。

### <a name="add-http-request-trigger"></a>HTTP 要求トリガーの追加

1. Azure portal で空のロジック アプリを作成して、ロジック アプリ デザイナーを開きます。 

2. 検索ボックスに、フィルターとして「HTTP 要求」と入力します。 トリガーの一覧から、**[要求 - HTTP 要求の受信時]** を選択します。

   ![HTTP 要求トリガーの追加](./media/logic-apps-using-sap-connector/add-trigger.png)

3. ロジック アプリに使用するエンドポイントの URL を生成するために、ここでロジック アプリを保存します。
デザイナーのツール バーで、**[保存]** を選択します。 

   次のように、エンドポイントの URL がトリガーに表示されます。

   ![エンドポイントの URL の生成](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>スキーマを生成する SAP アクションの追加

1. ロジック アプリ デザイナーで、トリガーの下に表示される **[新しいステップ]** > **[アクションの追加]** を順に選択します。

   ![アクションを追加する](./media/logic-apps-using-sap-connector/add-action.png) 

2. 検索ボックスに、フィルターとして「sap」と入力します。 アクションの一覧から、次のアクションを選択します。**[スキーマの生成]**
  
   ![SAP 送信アクションの選択](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   あるいは、**[Enterprise]** タブを選択し、SAP アクションを選択することもできます。

   ![[Enterprise] タブから SAP 送信アクションを選択する](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

3. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 それ以外の場合、接続が既に存在する場合は、次の手順に進んで SAP アクションを設定してください。 

   **オンプレミス SAP 接続の作成**

   1. SAP サーバーの接続情報を指定します。 
   **[データ ゲートウェイ]** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択します。

      **[ログオンの種類]** プロパティが **[アプリケーション サーバー]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

      ![SAP アプリケーション サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png) 

      **[ログオンの種類]** プロパティが **[グループ]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。
   
      ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png) 

   2. 操作が完了したら、**[作成]** を選択します。 Logic Apps により、接続のセットアップとテストが行われ、適切に機能していることが確認されます。

4. スキーマを生成するアーティファクトのパスを指定します。

   SAP アクションはファイル ピッカーから選択できます。

   ![SAP アクションの選択](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   あるいは、アクションを手動で入力できます。

   ![SAP アクションの手動入力](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png) 

   複数のアーティファクトにスキーマを生成するには、次のようにアーティファクトごとに SAP アクション詳細を指定します。

   ![新しい項目の追加を選択する](media/logic-apps-using-sap-connector/schema-generator-array-pick.png) 

   ![2 つの項目を表示する](media/logic-apps-using-sap-connector/schema-generator-example.png) 

   SAP アクションの詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

5. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. デザイナーのツール バーで **[実行]** を選択し、ロジック アプリの実行をトリガーします。

2. 実行を開き、**[スキーマの生成]** アクションの出力を確認します。 

   出力には、指定のメッセージ リストに対して生成されたスキーマが表示されます。

### <a name="upload-schemas-to-integration-account"></a>統合アカウントにスキーマをアップロードする

任意で、BLOB、ストレージ、統合アカウントなど、リポジトリにある生成済みのスキーマをダウンロードしたり、保存したりできます。 統合アカウントの場合、他の XML アクションでこの上ない操作性が与えられます。そこで、この例では、Azure Resource Manager コネクタを使用し、同じロジック アプリ用の統合アカウントにスキーマをアップロードする方法を示します。

1. ロジック アプリ デザイナーのトリガーの下で **[新しいステップ]**、**[アクションの追加]** の順に選択します。 検索ボックスに、フィルターとして「resource manager」と入力します。 このアクションを選択: **[Create or update a resource]\(リソースの作成または更新\)**

   ![Azure Resource Manager アクションの選択](media/logic-apps-using-sap-connector/select-arm-action.png) 

2. Azure サブスクリプション、Azure リソース グループ、統合アカウントなどの詳細を入力します。 その他のフィールドについては、次の例のように指定します。

   ![Azure Resource Manager アクションの詳細入力](media/logic-apps-using-sap-connector/arm-action.png)

   SAP の**スキーマ生成**アクションにより、スキーマがコレクションとして生成されます。そのため、デザイナーによって **For each** ループがアクションに自動的に追加されます。 
   このアクションは次の例のように表示されます。

   ![Azure Resource Manager アクションと "for each" ループ](media/logic-apps-using-sap-connector/arm-action-foreach.png)  

   > [!NOTE]
   > スキーマでは base64 でコード化された形式が使用されます。 統合アカウントにスキーマをアップロードするには、`base64ToString()` 関数で複合する必要があります。 `"properties"` 要素のコードは次の例のようになります。
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

3. ロジック アプリを保存し、 デザイナーのツール バーで、**[保存]** を選択します。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. デザイナーのツール バーで **[実行]** を選択し、ロジック アプリを手動でトリガーします。

2. 実行が成功したら、統合アカウントに進み、スキーマが生成されていることを確認します。

## <a name="known-issues-and-limitations"></a>既知の問題と制限

SAP コネクタに関して現在知られている問題と制限は次のようになります。

* SAP トリガーが SAP からバッチ IDoc を受信できない。 このアクションの結果として、SAP システムとデータ ゲートウェイの間で RFC 接続に失敗することがあります。

* SAP トリガーでデータ ゲートウェイ クラスターがサポートされない。 フェールオーバーでは、SAP システムと通信するデータ ゲートウェイ ノードがアクティブなノードと異なる場合があり、結果として、予想外の動作が発生します。 送信シナリオの場合、データ ゲートウェイ クラスターがサポートされます。

* 受信シナリオで、非 null の応答が返されない。 トリガーと応答アクションが与えられたロジック アプリは予想外の動作を起こします。 

* SAP への送信呼び出しまたはメッセージが 1 つしか tRFC と連動しない。 同じセッションで複数の tRFC を呼び出すなど、Business Application Programming Interface (BAPI) コミット パターンはサポートされていません。

* SAP への送信アクションとスキーマの生成アクションの両方で、添付ファイルのある RFC がサポートされない。

* 現在、SAP コネクタは SAP ルーター文字列をサポートしていない。 オンプレミス データ ゲートウェイは、接続する SAP システムと同じ LAN 上に存在する必要があります。

* オンプレミス データ ゲートウェイの場合、DATS フィールドと TIMS SAP フィールドの absent (null)、empty、minimum、maximum 値の変換が後の更新で変更されることがある。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](https://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [ロジック アプリからオンプレミスのシステムに接続](../logic-apps/logic-apps-gateway-connection.md)します。
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) を使用して、検証や変換など、各種のメッセージ操作を実行する方法を確認します。
* 他の[Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
