---
title: Oracle Database に接続する - Azure Logic Apps | Microsoft Docs
description: Oracle Database REST API と Azure Logic Apps を使用して、レコードを挿入および管理します
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/29/2017
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 06f65aef203b4f0d765f21b9d17b90081de85c94
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551757"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Oracle Database コネクタの概要

Oracle Database コネクタを使用して、既存のデータベースのデータを使用する組織のワークフローを作成します。 このコネクタは、オンプレミスの Oracle Database、または Oracle Database がインストール済みの Azure 仮想マシンに接続できます。 このコネクタでは、次の操作を実行できます。

* 顧客データベースに新しい顧客を追加するか、注文データベースで注文を更新することで、ワークフローを構築します。
* データ行の取得、新しい行の挿入、行の削除を行うアクションを使用します。 たとえば、Dynamics CRM Online にレコードが作成されると (トリガー)、Oracle Database に行を挿入します (アクション)。 

この記事では、ロジック アプリで Oracle Database コネクタを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

* サポートされている Oracle のバージョン: 
    * Oracle 9 以降
    * Oracle クライアント ソフトウェア 8.1.7 以降

* オンプレミスのデータ ゲートウェイをインストールする。 手順については、「[ロジック アプリからオンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md)」を参照してください。 ゲートウェイは、オンプレミスの Oracle Database、または Oracle DB がインストールされている Azure VM に接続するために必要です。 

    > [!NOTE]
    > オンプレミスのデータ ゲートウェイはブリッジとして機能し、オンプレミスのデータ (クラウドにないデータ) とロジック アプリの間に、セキュリティで保護されたデータ転送を提供します。 そのゲートウェイは、複数のサービス、および複数のデータ ソースで使用できます。 したがって、ゲートウェイをインストールするのは 1 回だけで済みます。

* オンプレミスのデータ ゲートウェイをインストールしたコンピューターで Oracle クライアントをインストールする。 必ず Oracle から 64 ビット Oracle Data Provider for .NET をインストールします。  

  [64 ビット ODAC 12c Release 4 (12.1.0.2.4) for Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Oracle クライアントがインストールされていない場合、接続の作成または使用を試みると、エラーが発生します。 この記事の一般的なエラーに関する説明を参照してください。


## <a name="add-the-connector"></a>コネクタの追加

> [!IMPORTANT]
> このコネクタにはトリガーがありません。 含まれているのはアクションだけです。 したがって、ロジック アプリを作成するときは、**[スケジュール - 繰り返し]**、**[要求/応答 - 応答]** など、他のトリガーを追加してロジック アプリを開始します。 

1. [Azure Portal](https://portal.azure.com) で、空のロジック アプリを作成します。

2. ロジック アプリを開始するときに、**[要求/応答 - 要求]** トリガーを選択します。 

    ![](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. **[保存]** を選択します。 保存すると、要求 URL が自動的に生成されます。 

4. **[新しいステップ]**、**[アクションの追加]** の順に選択します。 「`oracle`」と入力して、使用できるアクションを表示します。 

    ![](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > この方法を使用すると、すべてのコネクタで使用できるトリガーとアクションを、最も簡単に確認することもできます。 `oracle` など、コネクタ名の一部を入力すると、 その名前に一致するトリガーとアクションの一覧が表示されます。 

5. **[Oracle Database - Get row (Oracle Database - 行を取得する)]** など、アクションのいずれかを選択します。 **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。 Oracle サーバー名、認証方法、ユーザー名、パスワードを入力し、ゲートウェイを選択します。

    ![](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. 接続されたら、一覧からテーブルを選択し、テーブルに行 ID を入力します。 テーブルの識別子も必要です。 テーブルの識別子がわからない場合は、Oracle DB の管理者に連絡し、`select * from yourTableName` の出力から取得します。 これにより、操作を続行するために必要な、テーブルを特定できる情報が提供されます。

    次の例では、人事データベースからジョブ データが返されました。 

    ![](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. この手順では、その他のコネクタのいずれかを使用してワークフローを構築します。 Oracle からデータを取得しているかどうかをテストするには、Office 365、Gmail など、電子メール送信コネクタのいずれかを使用して、Oracle データを含む電子メールを自分宛てに送信します。 Oracle テーブルからの動的トークンを使用して、電子メールの `Subject` および `Body` を作成します。

    ![](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. ロジック アプリを**保存**して、**[実行]** を選択します。 デザイナーを閉じて、実行履歴で状態を確認します。 失敗した場合は、失敗したことを示すメッセージの行を選択します。 デザイナーが開き、失敗した手順と、エラー情報が表示されます。 成功した場合は、追加した情報が電子メールで通知されます。


### <a name="workflow-ideas"></a>ワークフローについて

* oracle というハッシュタグを監視し、データベースにツイートを入力して、そのツイートに対してクエリを実行できるようにするほか、他のアプリケーションで使用できるようにします。 ロジック アプリで `Twitter - When a new tweet is posted` トリガーを追加し、**#oracle** ハッシュタグを入力します。 次に、`Oracle Database - Insert row` アクションを追加し、テーブルを選択します。

    ![](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* メッセージが Service Bus キューに送信されます。 そのメッセージを取得し、データベースに入力します。 ロジック アプリで `Service Bus - when a message is received in a queue` トリガーを追加し、キューを選択します。 次に、`Oracle Database - Insert row` アクションを追加し、テーブルを選択します。

    ![](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>一般的なエラー

#### <a name="error-cannot-reach-the-gateway"></a>**エラー**: ゲートウェイに到達できません

**原因**:オンプレミスのデータ ゲートウェイがクラウドに接続できません。 

**対応策**:ゲートウェイをインストールしたオンプレミスのコンピューターで、そのゲートウェイが実行されていること、およびインターネットに接続できることを確認します。  オフまたはスリープ状態のコンピューターにはゲートウェイをインストールしないことをお勧めします。 オンプレミスのデータ ゲートウェイ サービス (PBIEgwService) を再起動することもできます。

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**エラー**: 使用中のプロバイダーは非推奨です: System.Data.OracleClient には Oracle クライアント ソフトウェア version 8.1.7 以降が必要です。 [https://go.microsoft.com/fwlink/p/?LinkID=272376](https://go.microsoft.com/fwlink/p/?LinkID=272376) を参照して、公式のプロバイダーをインストールしてください。

**原因**:オンプレミスのデータ ゲートウェイが実行されているコンピューターに Oracle クライアント SDK がインストールされていません。  

**解決方法**:Oracle クライアント SDK をダウンロードして、オンプレミスのデータ ゲートウェイと同じコンピューターにインストールします。

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**エラー**: テーブル '[Tablename]' には、キー列が定義されていません

**原因**:テーブルに主キーがありません。  

**解決方法**:Oracle Database コネクタでは、主キー列が含まれるテーブルを使用する必要があります。

#### <a name="currently-not-supported"></a>以下は現在サポートされていません

* ビュー 
* 複合キーが含まれるテーブル
* テーブル内の入れ子になったオブジェクトの種類
 
## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/oracle/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="get-some-help"></a>ヘルプ情報

[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)では、質問の投稿や質問への回答を行うことができるほか、他の Logic Apps ユーザーがどのようなことを行っているかがわかります。 

[https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) では、Logic Apps やコネクタの機能向上に役立つ皆様の投票や提案をお待ちしています。 


## <a name="next-steps"></a>次の手順
[ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)し、[API の一覧](apis-list.md)で Logic Apps で使用できるコネクタを確認します。
