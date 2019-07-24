---
title: IBM Informix データベースに接続する - Azure Logic Apps | Microsoft Docs
description: IBM Informix REST API と Azure Logic Apps を使用して、リソースを管理します
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58165902"
---
# <a name="get-started-with-the-informix-connector"></a>Informix コネクタの概要
Microsoft Connector for Informix は、IBM Informix データベースに格納されているリソースに Logic Apps を接続します。 Informix コネクタには、TCP/IP ネットワーク経由でリモート Informix サーバー コンピューターと通信する Microsoft クライアントが含まれています。 これには、Azure 仮想化で実行されている IBM Informix for Windows などのクラウド データベースと、オンプレミス データ ゲートウェイを使用するオンプレミス データベースが含まれます。 (このトピックの) IBM Informix のプラットフォームとバージョンの [サポート対象一覧](connectors-create-api-informix.md#supported-informix-platforms-and-versions) をご覧ください。

このコネクタでは、次のデータベース操作をサポートしています。

* データベース テーブルの一覧表示
* SELECT を使用した 1 つの行の読み取り
* SELECT を使用したすべての行の読み取り
* INSERT を使用した 1 つの行の追加
* UPDATE を使用した 1 つの行の変更
* DELETE を使用した 1 つの行の削除

このトピックでは、ロジック アプリでコネクタを使用してデータベース操作を処理する方法を説明します。

Logic Apps の詳細については、 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

## <a name="available-actions"></a>使用可能なアクション
このコネクタでサポートされているロジック アプリのアクションは、次のとおりです。

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>テーブルをリストする
操作に関するロジック アプリの作成は、Microsoft Azure ポータルを通じて実行する多くの手順で構成されています。

ロジック アプリ内で、Informix データベースのテーブルのリストを表示するアクションを追加できます。 このアクションでは、Informix スキーマ ステートメント (`CALL SYSIBM.SQLTABLES` など) を処理するようコネクタに指示します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します
1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **名前** (例: `InformixgetTables`)、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### <a name="add-a-trigger-and-action"></a>トリガーとアクションを追加する
1. **Logic Apps デザイナー**の **[テンプレート]** 一覧で、**[空の LogicApp]** を選択します。
2. **トリガー**の一覧で、**[繰り返し]** を選択します。 
3. **[繰り返し]** トリガーで **[編集]** を選択します。**[頻度]** ドロップダウンをクリックして **[日]** を選択し、**[間隔]** を選択して「**7**」と入力します。  
4. **[+ 新しいステップ]** ボックスをクリックし、**[アクションの追加]** を選択します。
5. **アクション**の一覧で、**[他のアクションを検索してください]** 編集ボックスに「**informix**」と入力し、**[Informix - Get tables (Preview) (Informix - テーブルの取得 (プレビュー))]** を選択します。
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. **[Informix - Get tables (Informix - テーブルの取得)]** 構成ウィンドウで、**[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** **チェックボックス**をオンにして有効にします。 設定がクラウドからオンプレミスに変更されていることを確認します。
   
   * "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **[サーバー]** の値を入力します。 たとえば、「 `ibmserver01:9089`」と入力します。
   * **[データベース]** の値を入力します。 たとえば、「 `nwind`」と入力します。
   * **[認証]** の値を選択します。 たとえば、 **[Basic]** を選択します。
   * **[ユーザー名]** の値を入力します。 たとえば、「 `informix`」と入力します。
   * **[パスワード]** の値を入力します。 たとえば、「 `Password1`」と入力します。
   * **[ゲートウェイ]** の値を選択します。 たとえば、 **[datagateway01]** を選択します。
7. **[作成]** をクリックし、**[保存]** を選択します。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. **[InformixgetTables]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
9. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_tables]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、テーブルのリストが含まれています。
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>接続を作成する
このコネクタでは、次の接続プロパティを使用した、オンプレミスおよびクラウドのデータベースへの接続をサポートします。 

| プロパティ | 説明 |
| --- | --- |
| サーバー |必須。 (コロンで区切られた後に) TCP/IP ポート番号が続く、IPv4 または IPv6 形式の TCP/IP アドレスまたはエイリアスを表す文字列値を受け入れます。 |
| database |必須。 DRDA リレーショナル データベース名 (RDBNAM) を表す文字列値を受け入れます。 Informix は、128 バイトの文字列を受け入れます (database は IBM Informix データベース名 (dbname) です)。 |
| 認証 |省略可能。 リスト項目値 (Basic または Windows (kerberos)) を受け入れます。 |
| ユーザー名 |必須。 文字列値を受け入れます。 |
| password |必須。 文字列値を受け入れます。 |
| ゲートウェイ |必須。 ストレージ グループ内の Logic Apps に定義されたオンプレミス データ ゲートウェイを表す、リスト項目値を受け入れます。 |

## <a name="create-the-on-premises-gateway-connection"></a>オンプレミス ゲートウェイ接続を作成する
このコネクタは、オンプレミス データ ゲートウェイを使用してオンプレミスの Informix データベースにアクセスできます。 詳細については、ゲートウェイのトピックをご覧ください。 

1. **[ゲートウェイ]** 構成ウィンドウで、**チェック ボックス**をオンにして**ゲートウェイ経由の接続**を有効にします。 クラウドからオンプレミスへの設定の変更を確認します。
2. "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **[サーバー]** の値を入力します。 たとえば、「 `ibmserver01:9089`」と入力します。
3. **[データベース]** の値を入力します。 たとえば、「 `nwind`」と入力します。
4. **[認証]** の値を選択します。 たとえば、 **[Basic]** を選択します。
5. **[ユーザー名]** の値を入力します。 たとえば、「 `informix`」と入力します。
6. **[パスワード]** の値を入力します。 たとえば、「 `Password1`」と入力します。
7. **[ゲートウェイ]** の値を選択します。 たとえば、 **[datagateway01]** を選択します。
8. **[作成]** をクリックして続行します。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>クラウド接続を作成する
このコネクタは、クラウド Informix データベースにアクセスできます。 

1. **[ゲートウェイ]** 構成ウィンドウで、**チェックボックス**をオンにせず、**ゲートウェイ経由の接続**を無効のままにしておきます。 
2. **[接続名]** の値を入力します。 たとえば、「 `hisdemo2`」と入力します。
3. "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **Informix サーバー名**の値を入力します。 たとえば、「 `hisdemo2.cloudapp.net:9089`」と入力します。
4. **Informix データベース名**の値を入力します。 たとえば、「 `nwind`」と入力します。
5. **[ユーザー名]** の値を入力します。 たとえば、「 `informix`」と入力します。
6. **[パスワード]** の値を入力します。 たとえば、「 `Password1`」と入力します。
7. **[作成]** をクリックして続行します。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>SELECT を使用してすべての行を取得する
Informix テーブル内のすべての行を取得するロジック アプリのアクションを作成できます。 このアクションでは、Informix の SELECT ステートメント ( `SELECT * FROM AREA`など) を処理するようコネクタに指示します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します
1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **名前** (例: "**InformixgetRows**")、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### <a name="add-a-trigger-and-action"></a>トリガーとアクションを追加する
1. **Logic Apps デザイナー**の **[テンプレート]** 一覧で、**[空の LogicApp]** を選択します。
2. **トリガー**の一覧で、**[繰り返し]** を選択します。 
3. **[繰り返し]** トリガーで **[編集]** を選択します。**[頻度]** ドロップダウンをクリックして **[日]** を選択し、**[間隔]** を選択して「**7**」と入力します。 
4. **[+ 新しいステップ]** ボックスをクリックし、**[アクションの追加]** を選択します。
5. **アクション**の一覧で、**[他のアクションを検索してください]** 編集ボックスに「**informix**」と入力し、**[Informix - Get rows (Preview) (Informix - 行の取得 (プレビュー))]** を選択します。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** を選択します。
7. **[接続]** 構成ウィンドウで、**[新規作成]** を選択します。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. **[ゲートウェイ]** 構成ウィンドウで、**チェックボックス**をオンにせず、**ゲートウェイ経由の接続**を無効のままにしておきます。
   
   * **[接続名]** の値を入力します。 たとえば、「 `HISDEMO2`」と入力します。
   * "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **Informix サーバー名**の値を入力します。 たとえば、「 `HISDEMO2.cloudapp.net:9089`」と入力します。
   * **Informix データベース名**の値を入力します。 たとえば、「 `NWIND`」と入力します。
   * **[ユーザー名]** の値を入力します。 たとえば、「 `informix`」と入力します。
   * **[パスワード]** の値を入力します。 たとえば、「 `Password1`」と入力します。
9. **[作成]** をクリックして続行します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
11. 必要に応じて、 **[詳細オプションの表示]** をクリックしてクエリ オプションを指定します。
12. **[保存]** を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. **[InformixgetRows]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
14. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、行のリストが含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>INSERT を使用した 1 つの行の追加
Informix テーブル内の 1 つの行を追加するロジック アプリのアクションを作成できます。 このアクションでは、Informix INSERT ステートメント (`INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` など) を処理するようコネクタに指示します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します
1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **名前** (例: `InformixinsertRow`)、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### <a name="add-a-trigger-and-action"></a>トリガーとアクションを追加する
1. **Logic Apps デザイナー**の **[テンプレート]** 一覧で、**[空の LogicApp]** を選択します。
2. **トリガー**の一覧で、**[繰り返し]** を選択します。 
3. **[繰り返し]** トリガーで **[編集]** を選択します。**[頻度]** ドロップダウンをクリックして **[日]** を選択し、**[間隔]** を選択して「**7**」と入力します。 
4. **[+ 新しいステップ]** ボックスをクリックし、**[アクションの追加]** を選択します。
5. **アクション**の一覧で、**[他のアクションを検索してください]** 編集ボックスに「**informix**」と入力し、**[Informix - 行の挿入 (プレビュー)]** を選択します。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** を選択します。 
7. **[接続]** 構成ウィンドウで、接続をクリックして選択します。 たとえば、 **[hisdemo2]** を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」、[AREADESC] に「`Area 99999`」、**[REGIONID]** に「`102`」と入力します。 
10. **[保存]** を選択します。
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. **[InformixinsertRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
12. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、新しい行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>SELECT を使用して 1 つの行を取得する
Informix テーブル内の 1 つの行を取得するロジック アプリのアクションを作成できます。 このアクションでは、Informix の SELECT WHERE ステートメント (`SELECT FROM AREA WHERE AREAID = '99999'` など) を処理するようコネクタに指示します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します
1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **名前** (例: `InformixgetRow`)、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### <a name="add-a-trigger-and-action"></a>トリガーとアクションを追加する
1. **Logic Apps デザイナー**の **[テンプレート]** 一覧で、**[空の LogicApp]** を選択します。
2. **トリガー**の一覧で、**[繰り返し]** を選択します。 
3. **[繰り返し]** トリガーで **[編集]** を選択します。**[頻度]** ドロップダウンをクリックして **[日]** を選択し、**[間隔]** を選択して「**7**」と入力します。 
4. **[+ 新しいステップ]** ボックスをクリックし、**[アクションの追加]** を選択します。
5. **アクション**の一覧で、**[他のアクションを検索してください]** 編集ボックスに「**informix**」と入力し、**[Informix - Get rows (Preview) (Informix - 行の取得 (プレビュー))]** を選択します。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** を選択します。 
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。 たとえば、 **[hisdemo2]** を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」と入力します。 
10. 必要に応じて、 **[詳細オプションの表示]** をクリックしてクエリ オプションを指定します。
11. **[保存]** を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. **[InformixgetRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
13. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>UPDATE を使用して 1 つの行を変更する
Informix テーブル内の 1 つの行を変更するロジック アプリのアクションを作成できます。 このアクションでは、Informix の UPDATE ステートメント (`UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)` など) を処理するようコネクタに指示します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します
1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **名前** (例: `InformixupdateRow`)、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### <a name="add-a-trigger-and-action"></a>トリガーとアクションを追加する
1. **Logic Apps デザイナー**の **[テンプレート]** 一覧で、**[空の LogicApp]** を選択します。
2. **トリガー**の一覧で、**[繰り返し]** を選択します。 
3. **[繰り返し]** トリガーで **[編集]** を選択します。**[頻度]** ドロップダウンをクリックして **[日]** を選択し、**[間隔]** を選択して「**7**」と入力します。 
4. **[+ 新しいステップ]** ボックスをクリックし、**[アクションの追加]** を選択します。
5. **アクション**の一覧で、**[他のアクションを検索してください]** 編集ボックスに「**informix**」と入力し、**[Informix - Update row (Preview) (Informix - 行の更新 (プレビュー))]** を選択します。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** を選択します。 
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。 たとえば、 **[hisdemo2]** を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」、[AREADESC] に「`Updated 99999`」、**[REGIONID]** に「`102`」と入力します。 
10. **[保存]** を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. **[InformixupdateRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
12. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、新しい行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>DELETE を使用した 1 つの行の削除
Informix テーブル内の 1 つの行を削除するロジック アプリのアクションを作成できます。 このアクションでは、Informix の DELETE ステートメント (`DELETE FROM AREA WHERE AREAID = '99999'` など) を処理するようコネクタに指示します。

### <a name="create-a-logic-app"></a>ロジック アプリを作成します
1. **Azure のスタート画面**で、**[+]** (プラス記号)、**[Web + モバイル]**、**[Logic App]** の順に選択します。
2. **名前** (例: `InformixdeleteRow`)、**サブスクリプション**、**リソース グループ**、**場所**、**App Service プラン**を入力します。 **[ダッシュボードにピン留めする]** チェック ボックスをオンにし、**[作成]** を選択します。

### <a name="add-a-trigger-and-action"></a>トリガーとアクションを追加する
1. **Logic Apps デザイナー**の **[テンプレート]** 一覧で、**[空の LogicApp]** を選択します。
2. **トリガー**の一覧で、**[繰り返し]** を選択します。 
3. **[繰り返し]** トリガーで **[編集]** を選択します。**[頻度]** ドロップダウンをクリックして **[日]** を選択し、**[間隔]** を選択して「**7**」と入力します。 
4. **[+ 新しいステップ]** ボックスをクリックし、**[アクションの追加]** を選択します。
5. **アクション**の一覧で、**[他のアクションを検索してください]** ボックスに「**informix**」と入力し、**[Informix - Delete row (Preview) (Informix - 行の削除 (プレビュー))]** を選択します。
6. **[Get rows (Preview) (行の取得 (プレビュー))]** アクションで、**[接続の変更]** を選択します。 
7. **[接続]** 構成ウィンドウで、既存の接続を選択します。 たとえば、 **[hisdemo2]** を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」と入力します。 
10. **[保存]** を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. **[InformixdeleteRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
12. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、削除された行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>サポートされている Informix のプラットフォームとバージョン
分散型リレーショナル データベース アーキテクチャ (DRDA) クライアント接続に対応するように構成されている場合、このコネクタでは IBM Informix の次のバージョンをサポートします。

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/informix/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次の手順
[ロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。

