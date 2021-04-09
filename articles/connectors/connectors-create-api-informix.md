---
title: IBM Informix データベースに接続する
description: Azure Logic Apps を使用して IBM Informix に格納されているリソースを管理するタスクとワークフローを自動化する
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761646"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して IBM Informix データベース リソースを管理する

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) および [Informix コネクタ](/connectors/informix/)を使用すると、IBM Informix データベース内のリソースを管理する自動化されたタスクとワークフローを作成できます。 このコネクタには、Azure の仮想化で実行される IBM Informix for Windows などのクラウドベースのデータベース、[オンプレミス データ ゲートウェイ](../logic-apps/logic-apps-gateway-connection.md)を使用するときのオンプレミス データベースなどのリモート Informix サーバー コンピューターと TCP/IP ネットワークを介して通信する Microsoft クライアントが含まれます。 分散型リレーショナル データベース アーキテクチャ (DRDA) クライアント接続をサポートするように構成されている場合、これらの Informix プラットフォームおよびバージョンに接続できます。

* IBM Informix 12.1
* IBM Informix 11.7

このトピックでは、ロジック アプリでコネクタを使用してデータベース操作を処理する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* オンプレミス データベースの場合、[オンプレミス データ ゲートウェイをローカル コンピューターにダウンロードしてインストールし](../logic-apps/logic-apps-gateway-install.md)、次に [Azure portal で Azure データ ゲートウェイ リソースを作成します](../logic-apps/logic-apps-gateway-connection.md)。

* Informix データベースへのアクセスが必要なロジック アプリ。 このコネクタからはアクションのみが提供されるため、トリガー (たとえば[反復トリガー](../connectors/connectors-native-recurrence.md)) を使用してロジック アプリを起動しておく必要があります。 

## <a name="add-an-informix-action"></a>Informix アクションを追加する

1. [Azure Portal](https://portal.azure.com) のロジック アプリ デザイナーでロジック アプリを開きます (まだ開いていない場合)。

1. Informix アクションを追加するステップで、 **[新しいステップ]** を選択します。

   既存のステップの間にアクションを追加するには、接続矢印の上にマウスを移動します。 表示されるプラス記号 ( **+** ) を選択してから、 **[アクションの追加]** を選択します。

1. 検索ボックスに、フィルターとして「`informix`」と入力します。 アクション一覧で、必要なアクションを選択します。次に例を示します。

   ![実行する Informix アクションを選択する](./media/connectors-create-api-informix/select-informix-connector-action.png)

   コネクタには、対応するデータベース操作を実行する次のアクションが用意されています。

   * テーブルを取得する - `CALL` ステートメントを使用してデータベース テーブルを一覧表示する
   * 行を取得する - `SELECT *` ステートメントを使用してすべての行を読み取る
   * 行を取得する - `SELECT WHERE` ステートメントを使用して 1 行を読み取る
   * `INSERT` ステートメントを使用して 1 行を追加する
   * `UPDATE` ステートメントを使用して 1 行を編集する
   * `DELETE` ステートメントを使用して 1 行を削除する

1. Informix データベースの接続の詳細を指定するように求められたら、[接続を作成する手順](#create-connection)を実行し、次の手順に進みます。

1. 選択したアクションの情報を指定します。

   | アクション | 説明 | プロパティと説明 |
   |--------|-------------|-----------------------------|
   | **テーブルを取得する** | Informix CALL ステートメントを実行して、データベース テーブルを一覧表示します。 | なし |
   | **行を取得する** | Informix の `SELECT *` ステートメントを実行して、指定したテーブル内のすべての行をフェッチします。 | **テーブル名**:使用する Informix テーブルの名前 <p><p>このアクションに他のプロパティを追加するには、 **[新しいパラメーターの追加]** 一覧から選択します。 詳細については、[コネクタのリファレンス トピック](/connectors/informix/)を参照してください。 |
   | **行を取得する** | Informix の `SELECT WHERE` ステートメントを実行して、指定したテーブルから行をフェッチします。 | - **テーブル名**:使用する Informix テーブルの名前 <br>- **行 ID**:行の一意の ID。たとえば、`9999` などです。 |
   | **行を挿入する** | Informix の `INSERT` ステートメントを実行して、指定した Informix テーブルに行を追加します。 | - **テーブル名**:使用する Informix テーブルの名前 <br>- **アイテム**:追加する値が含まれている行 |
   | **行を更新する** | Informix の `UPDATE` ステートメントを実行して、指定した Informix テーブルの行を変更します。 | - **テーブル名**:使用する Informix テーブルの名前 <br>- **行 ID**:更新する行の一意の ID です。たとえば、`9999` です。 <br>- **行**:更新された値を含む行。たとえば、`102` です。 |
   | **行を削除する** | Informix の `DELETE` ステートメントを実行して、指定した Informix テーブルから行を削除します。 | - **テーブル名**:使用する Informix テーブルの名前 <br>- **行 ID**:削除する行の一意の ID です。たとえば、`9999` です。 |
   ||||

1. ロジック アプリを保存します。 次は[ロジック アプリをテスト](#test-logic-app)するか、ロジック アプリの構築を続けます。

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Informix に接続する

1. ロジック アプリからオンプレミス データベースに接続する場合、 **[オンプレミス データ ゲートウェイ経由で接続]** を選択します。

1. この接続情報を入力し、 **[作成]** を選択します。

   | プロパティ | JSON プロパティ | 必須 | 値の例 | 説明 |
   |----------|---------------|----------|---------------|-------------|
   | [接続名] | `name` | はい | `informix-demo-connection` | Informix データベースへの接続に使用する名前 |
   | サーバー | `server` | はい | - クラウド: `informixdemo.cloudapp.net:9089` <br>- オンプレミス: `informixdemo:9089` | IPv4 または IPv6 形式の TCP/IP アドレスまたはエイリアスの後に、コロンと TCP/IP ポート番号が続きます |
   | データベース | `database` | はい | `nwind` | DRDA リレーショナル データベース名 (RDBNAM) または Informix データベース名 (dbname)。 Informix は、128 バイトの文字列を受け入れます。 |
   | 認証 | `authentication` | オンプレミスのみ | **[基本]** または **[Windows]** (kerberos) | Informix データベースに必要な認証の種類。 このプロパティは、 **[オンプレミス データ ゲートウェイ経由で接続]** を選択した場合にのみ表示されます。 |
   | ユーザー名 | `username` | いいえ | <*database-user-name*> | データベースのユーザー名 |
   | Password | `password` | いいえ | <*database-password*> | データベースのパスワード |
   | Gateway | `gateway` | オンプレミスのみ | - <*Azure-subscription*> <br>- <*Azure-on-premises-data-gateway-resource*> | Azure portal で作成したオンプレミス データ ゲートウェイの Azure サブスクリプションと Azure リソース名。 **[ゲートウェイ]** プロパティとサブプロパティは、 **[オンプレミス データ ゲートウェイ経由で接続]** を選択すると表示されます。 |
   ||||||

   次に例を示します。

   * **クラウド データベース**

     ![クラウド データベースの接続情報](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **オンプレミス データベース**

     ![オンプレミス データベースの接続情報](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. ロジック アプリを保存します。

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>ロジック アプリをテストする

1. ロジック アプリ デザイナーのツール バーの **[実行]** を選択します。 ロジック アプリを実行すると、その実行の出力を確認できます。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 [概要] ペインの **[概要]**  >  **[実行履歴]** で最新の実行を選択します。

1. **[ロジック アプリの実行]** で、 **[実行の詳細]** を選択します。

1. アクション一覧から、表示する出力のアクション (たとえば、**Get_tables**) を選択します。

   アクションが成功した場合、その **[状態]** プロパティは "**成功**" とマークされます。

1. 入力を表示するには、 **[入力のリンク]** の下にある URL リンクを選択します。 出力を表示するには、 **[出力のリンク]** リンクの下にある URL リンクを選択します。 出力例を次に示します。

   * **Get_tables** を実行すると、テーブルの一覧が表示されます。

     !["テーブルの取得" アクションの出力](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** を実行すると、行の一覧が表示されます。

     !["行の取得" アクションからの出力](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** を実行すると、指定した行が表示されます。

     !["行の取得" アクションからの出力](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** を実行すると、新しい行が表示されます。

     !["行の挿入" アクションからの出力](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** を実行すると、更新された行が表示されます。

     !["行の更新" アクションからの出力](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** を実行すると、削除された行が表示されます。

     !["行の削除" アクションからの出力](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>コネクタ固有の詳細

コネクタの Swagger の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、[コネクタのリファレンス ページ](/connectors/informix/)を確認してください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](apis-list.md)を確認します。
