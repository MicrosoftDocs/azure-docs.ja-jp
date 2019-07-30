---
title: SAP システムに接続する - Azure Logic Apps
description: Azure Logic Apps を使用してワークフローを自動化することにより、SAP のリソースにアクセスしてそれを管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: 8232bf90b4dc160583959345a257846aaabad690
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458929"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Azure Logic Apps から SAP システムに接続する

この記事では、SAP コネクタを使用して、ロジック アプリ内からご利用のオンプレミス SAP リソースにアクセスする方法を紹介します。 このコネクタは、SAP の従来のリリース (オンプレミスの R/3 や ECC システム) で動作します。 また、このコネクタにより、S/4 HANA などのより新しい HANA ベースの SAP システムとの統合も、それらがオンプレミスまたはクラウド内のどちらにホストされているかに関係なく可能になります。 この SAP コネクタでは、Intermediate Document (IDoc)、Business Application Programming Interface (BAPI)、または Remote Function Call (RFC) を介して、SAP NetWeaver ベースのシステムとの間のメッセージやデータの統合がサポートされます。

SAP コネクタでは [SAP .NET Connector (NCo) ライブラリ](https://support.sap.com/en/product/connectors/msnet.html)が使用され、次のような動作が行われます。

* **SAP に送信する**: SAP システムで、tRFC 経由で IDoc を送信したり、RFC 経由で BAPI 関数を呼び出したり、RFC/tRFC を呼び出したりします。
* **SAP から受信する**: SAP システムで、tRFC 経由で IDoc を受信したり、tRFC 経由で BAPI 関数を呼び出したり、RFC/tRFC を呼び出したりします。
* **スキーマを生成する**: IDoc、BAPI、RFC 用に SAP 成果物のスキーマを生成します。

これらの操作に対して、SAP コネクタでは、ユーザー名とパスワードを使用した基本認証がサポートされています。 また、[Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true) もサポートされています。 SNC は、SAP NetWeaver シングル サインオン (SSO) または外部のセキュリティ製品によって提供される追加のセキュリティ機能に対して使用できます。

SAP コネクタは、[オンプレミスのデータ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を介してオンプレミスの SAP システムと統合されます。 送信シナリオの場合、たとえば、ロジック アプリから SAP システムにメッセージが送信されると、データ ゲートウェイが RFC クライアントとして機能し、ロジック アプリから受信した要求を SAP に転送します。
同様に、受信シナリオの場合、SAP から要求を受信し、これらをロジック アプリに転送する RFC サーバーとしてデータ ゲートウェイが機能します。

この記事では、SAP と統合されるサンプル ロジック アプリを作成する方法と前に説明した統合シナリオを紹介します。

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>前提条件

この記事に沿って作業を行うには、次の要件を満たす必要があります。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。
* SAP システムへのアクセスに使用するロジック アプリとそのロジック アプリのワークフローを開始するトリガー。 ロジック アプリを初めて使用する場合は、「[Azure Logic Apps とは](../logic-apps/logic-apps-overview.md)」と[クイック スタートの初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。
* [SAP アプリケーション サーバー](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server)または [SAP メッセージ サーバー](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)。
* オンプレミスの任意のコンピューターに最新の[オンプレミス データ ゲートウェイ](https://www.microsoft.com/download/details.aspx?id=53127)をダウンロードしてインストールします。 事前に Azure portal でゲートウェイをセットアップしておいてください。 ゲートウェイを使用することで、オンプレミスのデータやリソースに安全にアクセスすることができます。 詳細については、「[Azure Logic Apps 用のオンプレミス データ ゲートウェイのインストール](../logic-apps/logic-apps-gateway-install.md)」を参照してください。
* SNC を SSO で使用する場合は、ゲートウェイが、SAP ユーザーに対してマップされたユーザーとして実行されていることを確認してください。 既定のアカウントを変更するには、 **[アカウントを変更]** を選択し、ユーザーの資格情報を入力します。

  ![ゲートウェイのアカウントを変更する](./media/logic-apps-using-sap-connector/gateway-account.png)

* 外部のセキュリティ製品を使用して SNC を有効にする場合は、ゲートウェイがインストールされているのと同じコンピューター上に SNC ライブラリまたはファイルをコピーします。 SNC 製品の例をいくつか挙げると、[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos、NTLM などがあります。
* オンプレミス データ ゲートウェイと同じコンピューターに最新の SAP クライアント ライブラリをダウンロードしてインストールします。現時点では、[SAP Connector (NCo) 3.0.21.0 for Microsoft .NET Framework 4.0 and Windows 64 bit (x64)](https://softwaredownloads.sap.com/file/0020000001865512018) が最新となります。 このバージョン以降をインストールしてください。これには次の理由があります。

  * 以前のバージョンの SAP NCo は、複数の IDoc メッセージが同時に送信されるとデッドロック状態に陥ることがあります。 この状態になると、以後、SAP の宛先に送信されたすべてのメッセージがブロックされ、メッセージがタイムアウトになります。
  * オンプレミス データ ゲートウェイは 64 ビット システムでのみ動作します。 データ ゲートウェイ ホスト サービスは 32 ビットのアセンブリをサポートしないので、それ以外の場合、"イメージが無効" であることを示すエラーが発生します。
  * データ ゲートウェイ ホスト サービスと Microsoft SAP アダプターでは、どちらも .NET Framework 4.5 が使用されます。 SAP NCo for .NET Framework 4.0 は、.NET ランタイム 4.0 から 4.7.1 を使用するプロセスとの組み合わせで正しく動作します。 SAP NCo for .NET Framework 2.0 は、.NET ランタイム 2.0 から 3.5 を使用するプロセスで正しく動作しますが、最新のオンプレミス データ ゲートウェイでは動作しなくなりました。

* SAP サーバーに送信できるメッセージの内容 (サンプル IDoc ファイルなど) は、XML 形式である必要があり、使用する SAP アクションの名前空間を含める必要があります。

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>SAP に送信する

この例では、HTTP 要求でトリガーすることのできるロジック アプリを使用します。 このロジック アプリは、SAP サーバーに IDoc を送信し、そのロジック アプリを呼び出した要求元に応答を返します。 

### <a name="add-an-http-request-trigger"></a>HTTP 要求トリガーを追加する

Azure Logic Apps では、すべてのロジック アプリは、必ず[トリガー](../logic-apps/logic-apps-overview.md#logic-app-concepts)から起動されます。トリガーは、特定のイベントが起こるか特定の条件が満たされたときに発生します。 トリガーが発生するたびに、Logic Apps エンジンによってロジック アプリ インスタンスが作成され、アプリのワークフローが開始されます。

この例では、ロジック アプリに "*HTTP POST 要求*" を送信できるよう、Azure にエンドポイントを持つロジック アプリを作成します。 ロジック アプリがこれらの HTTP 要求を受信すると、トリガーが作動してワークフロー内の次のステップが実行されます。

1. [Azure portal](https://portal.azure.com) で空のロジック アプリを作成し、ロジック アプリ デザイナーを開きます。

1. 検索ボックスに、フィルターとして「HTTP 要求」と入力します。 **[トリガー]** の一覧から、 **[HTTP 要求の受信時]** を選択します。

   ![HTTP 要求トリガーの追加](./media/logic-apps-using-sap-connector/add-trigger.png)

1. ロジック アプリのエンドポイント URL を生成できるように、ここでロジック アプリを保存します。 デザイナーのツール バーで、 **[保存]** を選択します。

   次のように、エンドポイントの URL がトリガーに表示されます。

   ![エンドポイントの URL の生成](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>SAP アクションを追加する

Azure Logic Apps では、[アクション](../logic-apps/logic-apps-overview.md#logic-app-concepts)とは、トリガーまたは別のアクションに続くワークフロー内のステップです。 この例に沿って作業を進める場合、まだロジック アプリにトリガーを追加していない方は、[こちらのセクションで説明されているトリガーを追加](#add-trigger)してください。

1. ロジック アプリ デザイナーで、トリガーの下の **[新しいステップ]** を選択します。

   ![[新しいステップ] を選択](./media/logic-apps-using-sap-connector/add-action.png)

1. 検索ボックスに、フィルターとして「sap」と入力します。 **[アクション]** の一覧から、 **[Send message to SAP]\(SAP にメッセージを送信する\)** を選択します。
  
   ![SAP 送信アクションの選択](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   あるいは、検索の代わりに、 **[Enterprise]** タブを選択し、SAP アクションを選択します。

   ![[Enterprise] タブから SAP 送信アクションを選択する](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 それ以外の場合、接続が既に存在する場合は、次の手順に進んで SAP アクションを設定してください。

   **オンプレミス SAP 接続を作成する**

    1. SAP サーバーの接続情報を指定します。 **[データ ゲートウェイ]** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択します。

         - **[ログオンの種類]** プロパティが **[アプリケーション サーバー]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

            ![SAP アプリケーション サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

         - **[ログオンの種類]** プロパティが **[グループ]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

            ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

           既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

    1. 完了したら、 **[作成]** をクリックします。

       Logic Apps により、接続のセットアップとテストが行われ、適切に機能していることが確認されます。

1. 次に、SAP サーバーのアクションを検索して選択します。

    1. **[SAP アクション]** ボックスのフォルダー アイコンを選択します。 ファイルの一覧から、使用する SAP メッセージを検索して選択します。 一覧内を移動するには矢印を使用します。

       この例では、種類が **[Orders]** の IDoc を選択しています。

       ![IDoc アクションを検索して選択](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

       目的のアクションが見つからない場合は、パスを手動で入力してください。その例を次に示します。

       ![IDoc アクションのパスを手動で入力](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

       > [!TIP]
       > 式エディターで **SAP アクション**の値を入力します。 これにより、異なる種類のメッセージに対して同じアクションを使用できます。

       IDoc の操作の詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

    1. **[入力メッセージ]** ボックス内をクリックして、動的コンテンツ リストを表示します。 このリストの **[HTTP 要求の受信時]** で、 **[本文]** フィールドを選択します。

       このステップで、HTTP 要求トリガーから本文コンテンツが取り込まれ、その出力が SAP サーバーに送信されます。

       ![[本文] フィールドを選択](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

       完成した SAP アクションは次の例のようになります。

       ![SAP アクションの完成](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. ロジック アプリを保存し、 デザイナーのツール バーで、 **[保存]** を選択します。

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>HTTP 応答アクションを追加する

ロジック アプリのワークフローに応答アクションを追加し、SAP アクションからの出力を取り込みます。 こうして SAP サーバーから受け取った結果が、ロジック アプリから要求元に返されます。

1. ロジック アプリ デザイナーで、SAP アクションの下に表示される **[新しいステップ]** を選択します。

1. 検索ボックスに、フィルターとして「応答」と入力します。 **[アクション]** の一覧で、 **[応答]** を選択します。

1. **[本文]** ボックス内をクリックして、動的コンテンツ リストを表示します。 このリストの **[Send message to SAP]\(SAP にメッセージを送信する\)** で、 **[本文]** フィールドを選択します。

   ![SAP アクションの完成](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. ロジック アプリを保存し、

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. まだロジック アプリが有効になっていない場合は、ロジック アプリのメニューで **[概要]** を選択します。 ツールバーで、 **[Enable]\(有効化\)** を選択します。

1. デザイナーのツール バーで、 **[実行]** を選択します。 この手順では、ロジック アプリを手動で起動します。

1. HTTP 要求トリガーの URL に HTTP POST 要求を送信してロジック アプリをトリガーします。
要求にメッセージの内容を追加します。 要求は、[Postman](https://www.getpostman.com/apps) などのツールを使用して送信することができます。

   この記事の要求で送信するのは IDoc ファイルです。このファイルは XML 形式であること、また、使用する SAP アクションの名前空間を含んでいる必要があります。その例を次に示します。

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. HTTP 要求を送信したら、ロジック アプリからの応答を待機します。

   > [!NOTE]
   > 応答に必要なすべての手順が[要求タイムアウト制限](./logic-apps-limits-and-config.md)内に完了しない場合、ロジック アプリがタイムアウトします。 この状況に陥ると、要求がブロック状態になります。 問題の診断については、[ロジック アプリをチェックしたり監視したりする方法](../logic-apps/logic-apps-monitor-your-logic-apps.md)に関するページを参照してください。

これで、SAP サーバーとやり取りすることのできるロジック アプリが完成しました。 ロジック アプリに使用する SAP 接続をセットアップしたら、BAPI や RFC など、他に利用できる SAP アクションを探してみましょう。

## <a name="receive-from-sap"></a>SAP から受信する

この例では、アプリが SAP システムからメッセージを受信するときにトリガーするロジック アプリを使用します。

### <a name="add-an-sap-trigger"></a>SAP トリガーを追加する

1. Azure portal で空のロジック アプリを作成して、ロジック アプリ デザイナーを開きます。

1. 検索ボックスに、フィルターとして「sap」と入力します。 **[トリガー]** の一覧から、 **[When a message is received from SAP]\(SAP からメッセージを受信したとき\)** というトリガーを選択します。

   ![SAP トリガーを追加する](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   あるいは、 **[Enterprise]** タブに移動し、トリガーを選択することもできます。

   ![[Enterprise] タブから SAP トリガーを追加する](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 接続が既に存在する場合は、次の手順に進んで SAP アクションを設定します。

   **オンプレミス SAP 接続を作成する**

   - SAP サーバーの接続情報を指定します。 **[データ ゲートウェイ]** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択します。

      - **[ログオンの種類]** プロパティが **[アプリケーション サーバー]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

         ![SAP アプリケーション サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      - **[ログオンの種類]** プロパティが **[グループ]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

          ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

1. SAP システム構成に基づいて必須パラメーターを指定します。

   任意で 1 つまたは複数の SAP アクションを指定できます。 このアクション リストにより、データ ゲートウェイを介して SAP サーバーからトリガーが受信するメッセージが指定されます。 リストが空の場合、トリガーはすべてのメッセージを受信します。 リストに複数のメッセージが含まれる場合、リストで指定されているメッセージのみをトリガーは受信します。 他のメッセージが SAP サーバーから送信されても、ゲートウェイによって拒否されます。

   SAP アクションはファイル ピッカーから選択できます。

   ![SAP アクションの選択](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   あるいは、次のようにアクションを手動で指定できます。

   ![SAP アクションの手動入力](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   複数のメッセージを受信するようにトリガーを設定したときのアクションの表示例を次に示します。

   ![トリガー例](media/logic-apps-using-sap-connector/example-trigger.png)  

   SAP アクションの詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

1. それでは、SAP システムからメッセージを受信できるようにロジック アプリを保存します。
デザイナーのツール バーで、 **[保存]** を選択します。

これでロジック アプリは SAP システムからメッセージを受信できます。

> [!NOTE]
> SAP トリガーはポーリング トリガーではなく、Webhook ベースのトリガーです。 このトリガーはメッセージが存在するときにのみゲートウェイから呼び出されます。そのため、ポーリングは必要ありません。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. ロジック アプリをトリガーするには、SAP システムからメッセージを送信します。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 **[実行の履歴]** に、ロジック アプリの新しい実行がないか確認します。

1. 最新の実行を開きます。トリガー出力セクションに SAP システムから送信されたメッセージが表示されます。

## <a name="generate-schemas-for-artifacts-in-sap"></a>SAP でアーティファクトのスキーマを生成する

この例では、HTTP 要求でトリガーすることのできるロジック アプリを使用します。 この SAP アクションにより、指定の IDoc と BAPI 向けのスキーマの生成要求が SAP システムに送信されます。 応答で返されたスキーマは、Azure Resource Manager コネクタを使用して、統合アカウントにアップロードされます。

### <a name="add-an-http-request-trigger"></a>HTTP 要求トリガーを追加する

1. Azure portal で空のロジック アプリを作成して、ロジック アプリ デザイナーを開きます。

1. 検索ボックスに、フィルターとして「HTTP 要求」と入力します。 **[トリガー]** の一覧から、 **[HTTP 要求の受信時]** を選択します。

   ![HTTP 要求トリガーの追加](./media/logic-apps-using-sap-connector/add-trigger.png)

1. ロジック アプリに使用するエンドポイントの URL を生成するために、ここでロジック アプリを保存します。
デザイナーのツール バーで、 **[保存]** を選択します。

   次のように、エンドポイントの URL がトリガーに表示されます。

   ![エンドポイントの URL の生成](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>スキーマを生成する SAP アクションを追加する

1. ロジック アプリ デザイナーで、トリガーの下の **[新しいステップ]** を選択します。

   ![[新しいステップ] を選択](./media/logic-apps-using-sap-connector/add-action.png)

1. 検索ボックスに、フィルターとして「sap」と入力します。 **[アクション]** の一覧から **[スキーマの生成]** を選択します。
  
   ![SAP 送信アクションの選択](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   あるいは、 **[Enterprise]** タブを選択し、SAP アクションを選択することもできます。

   ![[Enterprise] タブから SAP 送信アクションを選択する](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. 接続の詳細の入力を求められたら、SAP 接続を今すぐ作成します。 接続が既に存在する場合は、次の手順に進んで SAP アクションを設定します。

   **オンプレミス SAP 接続を作成する**

    1. SAP サーバーの接続情報を指定します。 **[データ ゲートウェイ]** プロパティには、ゲートウェイのインストール用に Azure portal で作成したデータ ゲートウェイを選択します。

       - **[ログオンの種類]** プロパティが **[アプリケーション サーバー]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

         ![SAP アプリケーション サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

       - **[ログオンの種類]** プロパティが **[グループ]** に設定されている場合、通常は任意として表示される次のプロパティが必須になります。

         ![SAP メッセージ サーバー接続を作成](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

        既定では、厳密な型指定は、スキーマに照らした XML 検証を実行することによって無効な値をチェックするために使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 詳しくは、[[安全な型指定] オプション](#safe-typing)に関する記事をご覧ください。

    1. 完了したら、 **[作成]** をクリックします。 
   
       Logic Apps により、接続のセットアップとテストが行われ、適切に機能していることが確認されます。

1. スキーマを生成するアーティファクトのパスを指定します。

   SAP アクションはファイル ピッカーから選択できます。

   ![SAP アクションの選択](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   あるいは、アクションを手動で入力できます。

   ![SAP アクションの手動入力](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   複数のアーティファクトにスキーマを生成するには、次のようにアーティファクトごとに SAP アクション詳細を指定します。

   ![新しい項目の追加を選択する](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![2 つの項目を表示する](media/logic-apps-using-sap-connector/schema-generator-example.png)

   SAP アクションの詳細については、「[IDOC 操作のメッセージ スキーマ](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)」を参照してください。

1. ロジック アプリを保存し、 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. デザイナーのツール バーで **[実行]** を選択し、ロジック アプリの実行をトリガーします。

1. 実行を開き、 **[スキーマの生成]** アクションの出力を確認します。

   出力には、指定のメッセージ リストに対して生成されたスキーマが表示されます。

### <a name="upload-schemas-to-an-integration-account"></a>統合アカウントにスキーマをアップロードする

任意で、BLOB、ストレージ、統合アカウントなど、リポジトリにある生成済みのスキーマをダウンロードしたり、保存したりできます。 統合アカウントの場合、他の XML アクションでこの上ない操作性が与えられます。そこで、この例では、Azure Resource Manager コネクタを使用し、同じロジック アプリ用の統合アカウントにスキーマをアップロードする方法を示します。

1. ロジック アプリ デザイナーで、トリガーの下の **[新しいステップ]** を選択します。

1. 検索ボックスに、フィルターとして「Resource Manager」と入力します。 **[Create or update a resource]\(リソースの作成または更新\)** を選択します。

   ![Azure Resource Manager アクションの選択](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Azure サブスクリプション、Azure リソース グループ、統合アカウントなど、アクションの詳細を入力します。 フィールドに SAP トークンを追加するには、これらのフィールドのボックス内をクリックし、表示される動的コンテンツ リストから選択します。

    1. **[新しいパラメーターの追加]** リストを開き、 **[Location]\(場所\)** および **[Properties]\(プロパティ\)** フィールドを選択します。

    1. 次の例で示すように、これらの新しいフィールドに詳細情報を指定します。

       ![Azure Resource Manager アクションの詳細入力](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   SAP の**スキーマ生成**アクションにより、スキーマがコレクションとして生成されます。そのため、デザイナーによって **For each** ループがアクションに自動的に追加されます。 このアクションは次の例のように表示されます。

   ![Azure Resource Manager アクションと "for each" ループ](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

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

1. ロジック アプリを保存し、 デザイナーのツール バーで、 **[保存]** を選択します。

### <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. デザイナーのツール バーで **[実行]** を選択し、ロジック アプリを手動でトリガーします。

1. 実行が成功したら、統合アカウントに移動し、生成されたスキーマが存在することを確認します。

## <a name="enable-secure-network-communications"></a>Secure Network Communications を有効にする

開始する前に、上記に一覧した次の[前提条件](#pre-reqs)が満たされていることを確認してください。

* オンプレミス データ ゲートウェイが、ご利用の SAP システムと同じネットワーク内にあるコンピューターにインストールされている。
* SSO のために、ゲートウェイが、SAP ユーザーにマップされたユーザーとして実行されている。
* 追加のセキュリティ機能を提供する SNC ライブラリが、データ ゲートウェイと同じコンピューターにインストールされている。 例をいくつか挙げると、[sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm)、Kerberos、NTLM などがあります。

SAP システムとの間でやりとりされる要求に対して SNC を有効にするには、SAP 接続の **[SNC を使用する]** チェックボックスを選択し、次のプロパティを指定します。

   ![接続で SAP SNC を構成する](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | プロパティ | 説明 |
   |----------| ------------|
   | **SNC Library Path (SNC ライブラリ パス)** | SNC ライブラリ名、または NCo インストール場所の相対パス、または絶対パス 例として、`sapsnc.dll`、`.\security\sapsnc.dll`、または `c:\security\sapsnc.dll` があります。 |
   | **SNC SSO** | SNC 経由で接続する場合、SNC ID は、通常、呼び出し元を認証するために使用されます。 もう 1 つのオプションは、ユーザーおよびパスワード情報を使用して呼び出し元を認証できるようにオーバーライドすることです。ただし、行は引き続き暗号化されます。 |
   | **SNC の自分の名前** | ほとんどの場合、このプロパティは省略することができます。 インストールされている SNC ソリューションは、通常、独自の SNC 名を認識しています。 複数の ID をサポートするソリューションの場合のみ、この特定の宛先またはサーバーに使用される ID の指定が必要になる場合があります。 |
   | **SNC パートナー名** | バックエンド SNC の名前。 |
   | **保護の SNC 品質** | この特定の宛先またはサーバーの SNC 通信に使用されるサービスの品質。 既定値は、バックエンド システムによって定義されます。 最大値は、SNC のために使用されるセキュリティ製品によって定義されます。 |
   |||

   > [!NOTE]
   > データ ゲートウェイと SNC ライブラリが置かれているコンピューター上で、環境変数 SNC_LIB および SNC_LIB_64 は設定しないでください。 設定した場合、それらが、コネクタを介して渡される SNC ライブラリ値よりも優先されます。

<a name="safe-typing"></a>

## <a name="safe-typing"></a>安全な型指定

既定では、SAP 接続を作成するときに、スキーマに照らした XML 検証を実行することによって無効な値をチェックするには、厳密な型指定を使用します。 この動作により、問題を初期段階で検出できます。 **[安全な型指定]** オプションは、旧バージョンとの互換性のために使用でき、文字列の長さのみをチェックします。 **[安全な型指定]** を選択すると、SAP の DATS 型と TIMS 型は、対応する XML の `xs:date` と `xs:time` (`xmlns:xs="http://www.w3.org/2001/XMLSchema"`) ではなく、文字列として扱われます。 安全な型指定は、すべてのスキーマ生成、"送信済み" ペイロードと "受信済み" 応答の両方のメッセージ送信、およびトリガーの動作に影響します。 

厳密な型指定を使用すると ( **[安全な型指定]** が有効になっていない場合)、スキーマにより DATS 型と TIMS 型がよりわかりやすい XML 型にマップされます。

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

厳密な型指定を使用してメッセージを送信する場合、DATS および TIMS 応答は一致する XML 型の形式に準拠します。

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

**[安全な型指定]** が有効になっている場合は、スキーマにより、DATS 型と TIMS 型は、長さだけ制限された XML 文字列フィールドにマップされます。次に例を示します。

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

**[安全な型指定]** が有効になっている状態でメッセージを送信した場合、DATS と TIMS の応答は次の例のようになります。

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>既知の問題と制限

SAP コネクタに関して現在知られている問題と制限は次のようになります。

* SAP への送信呼び出しまたはメッセージが 1 つしか tRFC と連動しない。 同じセッションで複数の tRFC を呼び出すなど、BAPI コミット パターンはサポートされていません。
* SAP トリガーで、SAP からのバッチ IDoc の受信がサポートされていない。 このアクションの結果として、SAP システムとデータ ゲートウェイの間で RFC 接続に失敗することがあります。
* SAP トリガーでデータ ゲートウェイ クラスターがサポートされない。 フェールオーバーで、SAP システムと通信するデータ ゲートウェイ ノードがアクティブなノードと異なる場合があり、結果として、予想外の動作が発生します。 送信シナリオの場合、データ ゲートウェイ クラスターがサポートされます。
* 現在、SAP コネクタは SAP ルーター文字列をサポートしていない。 オンプレミス データ ゲートウェイは、接続する SAP システムと同じ LAN 上に存在する必要があります。

## <a name="connector-reference"></a>コネクタのレファレンス

コネクタの OpenAPI (以前の Swagger) の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、[コネクタのリファレンス ページ](/connectors/sap/)を参照してください。

## <a name="next-steps"></a>次の手順

* Azure Logic Apps から[オンプレミス システムに接続します](../logic-apps/logic-apps-gateway-connection.md)。
* [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) を使用して、検証、変換、およびその他のメッセージ操作を使用する方法を確認します。
* 他の [Logic Apps コネクタ](../connectors/apis-list.md)を確認します。
