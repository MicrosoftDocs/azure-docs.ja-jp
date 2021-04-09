---
title: Oracle Database に接続する
description: Oracle Database REST API と Azure Logic Apps を使用して、レコードを挿入および管理します
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674808"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Oracle Database コネクタの概要

Oracle Database コネクタを使用して、既存のデータベースのデータを使用する組織のワークフローを作成します。 このコネクタは、オンプレミスの Oracle Database、または Oracle Database がインストール済みの Azure 仮想マシンに接続できます。 このコネクタでは、次の操作を実行できます。

* 顧客データベースに新しい顧客を追加するか、注文データベースで注文を更新することで、ワークフローを構築します。
* データ行の取得、新しい行の挿入、行の削除を行うアクションを使用します。 たとえば、Dynamics CRM Online にレコードが作成されると (トリガー)、Oracle Database に行を挿入します (アクション)。 

このコネクタでは、次の項目はサポートされていません。

* ビュー 
* 複合キーが含まれるテーブル
* テーブル内の入れ子になったオブジェクトの種類
* 非スカラー値を含むデータベース関数

この記事では、ロジック アプリで Oracle Database コネクタを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* サポートされている Oracle のバージョン: 
    * Oracle 9 以降
    * Oracle クライアント ソフトウェア 8.1.7 以降

* オンプレミスのデータ ゲートウェイをインストールする。 手順については、「[ロジック アプリからオンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md)」を参照してください。 ゲートウェイは、オンプレミスの Oracle Database、または Oracle DB がインストールされている Azure VM に接続するために必要です。 

    > [!NOTE]
    > オンプレミスのデータ ゲートウェイはブリッジとして機能し、オンプレミスのデータ (クラウドにないデータ) とロジック アプリの間に、セキュリティで保護されたデータ転送を提供します。 そのゲートウェイは、複数のサービス、および複数のデータ ソースで使用できます。  したがって、ゲートウェイをインストールするのは 1 回だけで済みます。

* オンプレミスのデータ ゲートウェイをインストールしたコンピューターで Oracle クライアントをインストールする。  必ず Oracle から 64 ビット Oracle Data Provider for .NET をインストールします。  

  [64 ビット ODAC 12c Release 4 (12.1.0.2.4) for Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Oracle クライアントがインストールされていない場合、接続の作成または使用を試みると、エラーが発生します。 この記事の一般的なエラーに関する説明を参照してください。


## <a name="add-the-connector"></a>コネクタの追加

> [!IMPORTANT]
> このコネクタにはトリガーがありません。 含まれているのはアクションだけです。 したがって、ロジック アプリを作成するときは、 **[スケジュール - 繰り返し]** 、 **[要求/応答 - 応答]** など、他のトリガーを追加してロジック アプリを開始します。 

1. [Azure Portal](https://portal.azure.com) で、空のロジック アプリを作成します。

2. ロジック アプリを開始するときに、 **[要求/応答 - 要求]** トリガーを選択します。 

    ![ダイアログ ボックスには、すべてのトリガーを検索するボックスがあります。 "要求/応答 - 要求" という単一のトリガーも、選択ボタンと共に表示されます。](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. **[保存]** を選択します。 保存すると、要求 URL が自動的に生成されます。 

4. **[新しいステップ]** 、 **[アクションの追加]** の順に選択します。 「`oracle`」と入力して、使用できるアクションを表示します。 

    ![検索ボックスには「oracle」と入力されています。 検索では、"Oracle Database" というラベルが付いた 1 つのヒットが生成されます。 タブ付きページがあり、1 つのタブには "トリガー (0)" と表示され、もう 1 つには "アクション (6)" と表示されます。 一覧には、6 個のアクションがあります。 それらの 1 つ目は、"行の取得のプレビュー" です。](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > この方法を使用すると、すべてのコネクタで使用できるトリガーとアクションを、最も簡単に確認することもできます。 `oracle` など、コネクタ名の一部を入力すると、 その名前に一致するトリガーとアクションの一覧が表示されます。 

5. **[Oracle Database - Get row (Oracle Database - 行を取得する)]** など、アクションのいずれかを選択します。 **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。 Oracle サーバー名、認証方法、ユーザー名、パスワードを入力し、ゲートウェイを選択します。

    ![ダイアログ ボックスのタイトルは、[Oracle Database - Get row]\(Oracle Database - 行の取得\) です。 [オンプレミスのデータ ゲートウェイ経由で接続] というラベルの、オンになっているボックスがあります。 その下には、他に 5 つのテキスト ボックスがあります。](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. 接続されたら、一覧からテーブルを選択し、テーブルに行 ID を入力します。 テーブルの識別子も必要です。 テーブルの識別子がわからない場合は、Oracle DB の管理者に連絡し、`select * from yourTableName` の出力から取得します。 これにより、操作を続行するために必要な、テーブルを特定できる情報が提供されます。

    次の例では、人事データベースからジョブ データが返されました。 

    ![[Get row (Preview)]\(行の取得 (プレビュー)\) というタイトルのダイアログ ボックスには、次の 2 つのテキスト ボックスがあります。[テーブル名] には "HRJOBS" と表示されていて、ドロップダウン リストがあり、[行 ID] には "SA_REP" と表示されています。](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. この手順では、その他のコネクタのいずれかを使用してワークフローを構築します。 Oracle からデータを取得しているかどうかをテストするには、Office 365 Outlook など、メール送信コネクタのいずれかを使用して、Oracle データを含むメールを自分宛てに送信します。 Oracle テーブルからの動的トークンを使用して、電子メールの `Subject` および `Body` を作成します。

    ![2 つのダイアログ ボックスがあります。 [メールの送信] には、電子メールの "本文"、"件名"、および "宛先" アドレスを指定するためのボックスがあります。 [動的なコンテンツの追加] ダイアログ ボックスを使用すると、フローのアプリやサービスの動的コンテンツを検索できます。](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. ロジック アプリを **保存** して、 **[実行]** を選択します。 デザイナーを閉じて、実行履歴で状態を確認します。 失敗した場合は、失敗したことを示すメッセージの行を選択します。 デザイナーが開き、失敗した手順と、エラー情報が表示されます。 成功した場合は、追加した情報が電子メールで通知されます。


### <a name="workflow-ideas"></a>ワークフローについて

* oracle というハッシュタグを監視し、データベースにツイートを入力して、そのツイートに対してクエリを実行できるようにするほか、他のアプリケーションで使用できるようにします。 ロジック アプリで `Twitter - When a new tweet is posted` トリガーを追加し、 **#oracle** ハッシュタグを入力します。 次に、`Oracle Database - Insert row` アクションを追加し、テーブルを選択します。

    ![[新しいツイートが投稿されたら] ダイアログ ボックスには、検索テキストとして "hashtag oracle" と表示され、確認の頻度を指定できます。 このダイアログ ボックスの後には、[Oracle Database] ダイアログ ボックスが表示され、アクションを選択できます。](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* メッセージが Service Bus キューに送信されます。 そのメッセージを取得し、データベースに入力します。 ロジック アプリで `Service Bus - when a message is received in a queue` トリガーを追加し、キューを選択します。 次に、`Oracle Database - Insert row` アクションを追加し、テーブルを選択します。

    ![[メッセージを受信したとき] ダイアログ ボックスには、[キュー名] として "orders" と表示され、確認の頻度を指定できます。 このボックスの後には、[Insert row (Preview)]\(行の挿入 (プレビュー)\) ダイアログ ボックスが表示され、[テーブル名] を選択できます。](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>一般的なエラー

#### <a name="error-cannot-reach-the-gateway"></a>**Error**: ゲートウェイに到達できません

**原因**:オンプレミスのデータ ゲートウェイがクラウドに接続できません。 

**対応策**:ゲートウェイをインストールしたオンプレミスのコンピューターで、そのゲートウェイが実行されていること、およびインターネットに接続できることを確認します。    オフまたはスリープ状態のコンピューターにはゲートウェイをインストールしないことをお勧めします。  オンプレミスのデータ ゲートウェイ サービス (PBIEgwService) を再起動することもできます。

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Error**: 使用中のプロバイダーは非推奨です: System.Data.OracleClient には Oracle クライアント ソフトウェア version 8.1.7 以降が必要です。 [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) を参照して、公式のプロバイダーをインストールしてください。

**原因**:オンプレミスのデータ ゲートウェイが実行されているコンピューターに Oracle クライアント SDK がインストールされていません。  

**解決方法**:Oracle クライアント SDK をダウンロードして、オンプレミスのデータ ゲートウェイと同じコンピューターにインストールします。

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Error**: テーブル '[Tablename]' には、キー列が定義されていません

**原因**:テーブルに主キーがありません。  

**解決方法**:Oracle Database コネクタでは、主キー列が含まれるテーブルを使用する必要があります。
 
## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/oracle/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="get-some-help"></a>ヘルプ情報

[Microsoft Q&A の Azure Logic Apps に関する質問ページ](/answers/topics/azure-logic-apps.html)では、質問の投稿や質問への回答を行うことができるほか、他の Logic Apps ユーザーがどのようなことを行っているかがわかります。 

[https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) では、Logic Apps やコネクタの機能向上に役立つ皆様の投票や提案をお待ちしています。 


## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)し、[API の一覧](apis-list.md)で Logic Apps で使用できるコネクタを確認します。