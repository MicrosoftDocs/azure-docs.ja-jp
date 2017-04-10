---
title: "ロジック アプリに Informix コネクタを追加する | Microsoft Docs"
description: "Informix コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: gplarsen
manager: erikre
editor: 
tags: connectors
ms.assetid: ca2393f0-3073-4dc2-8438-747f5bc59689
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/26/2016
ms.author: plarsen
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 614400a8787fdd2081fa8e981c0fc6b6dd794a58
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-informix-connector"></a>Informix コネクタの概要
Microsoft Connector for Informix は、IBM Informix データベースに格納されているリソースに Logic Apps を接続します。 Informix コネクタには、TCP/IP ネットワーク経由でリモート Informix サーバー コンピューターと通信する Microsoft クライアントが含まれています。 これには、Azure 仮想化で実行されている IBM Informix for Windows などのクラウド データベースと、オンプレミス データ ゲートウェイを使用するオンプレミス データベースが含まれます。 (このトピックの) IBM Informix のプラットフォームとバージョンの [サポート対象一覧](connectors-create-api-informix.md#supported-informix-platforms-and-versions) をご覧ください。

> [!NOTE]
> 本記事は、一般公開された Logic Apps の一般公開 (GA) を対象としています。 
> 
> 

このコネクタでは、次のデータベース操作をサポートしています。

* データベース テーブルの一覧表示
* SELECT を使用した&1; つの行の読み取り
* SELECT を使用したすべての行の読み取り
* INSERT を使用した&1; つの行の追加
* UPDATE を使用した&1; つの行の変更
* DELETE を使用した&1; つの行の削除

このトピックでは、ロジック アプリでコネクタを使用してデータベース操作を処理する方法を説明します。

Logic Apps の詳細については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

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

ロジック アプリ内で、Informix データベースのテーブルのリストを表示するアクションを追加できます。 このアクションでは、Informix スキーマ ステートメント ( `CALL SYSIBM.SQLTABLES`など) を処理するようコネクタに指示します。

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
   
   * "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **[サーバー]**の値を入力します。 たとえば、「 `ibmserver01:9089`」と入力します。
   * **[データベース]**の値を入力します。 たとえば、「 `nwind`」と入力します。
   * **[認証]**の値を選択します。 たとえば、 **[Basic]**を選択します。
   * **[ユーザー名]**の値を入力します。 たとえば、「 `informix`」と入力します。
   * **[パスワード]**の値を入力します。 たとえば、「 `Password1`」と入力します。
   * **[ゲートウェイ]**の値を選択します。 たとえば、 **[datagateway01]**を選択します。
7. **[作成]** をクリックし、**[保存]** を選択します。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. **[InformixgetTables]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
9. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_tables]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、テーブルのリストが含まれています。
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>接続を作成する
このコネクタでは、次の接続プロパティを使用した、オンプレミスおよびクラウドのデータベースへの接続をサポートします。 

| プロパティ | Description |
| --- | --- |
| [サーバー] |必須。 (コロンで区切られた後に) TCP/IP ポート番号が続く、IPv4 または IPv6 形式の TCP/IP アドレスまたはエイリアスを表す文字列値を受け入れます。 |
| [データベース] |必須。 DRDA リレーショナル データベース名 (RDBNAM) を表す文字列値を受け入れます。 Informix は、128 バイトの文字列を受け入れます (database は IBM Informix データベース名 (dbname) です)。 |
| [認証] |省略可能。 リスト項目値 (Basic または Windows (kerberos)) を受け入れます。 |
| [ユーザー名] |必須。 文字列値を受け入れます。 |
| [パスワード] |必須。 文字列値を受け入れます。 |
| [ゲートウェイ] |必須。 ストレージ グループ内の Logic Apps に定義されたオンプレミス データ ゲートウェイを表す、リスト項目値を受け入れます。 |

## <a name="create-the-on-premises-gateway-connection"></a>オンプレミス ゲートウェイ接続を作成する
このコネクタは、オンプレミス データ ゲートウェイを使用してオンプレミスの Informix データベースにアクセスできます。 詳細については、ゲートウェイのトピックをご覧ください。 

1. **[ゲートウェイ]** 構成ウィンドウで、**チェック ボックス**をオンにして**ゲートウェイ経由の接続**を有効にします。 クラウドからオンプレミスへの設定の変更を確認します。
2. "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **[サーバー]**の値を入力します。 たとえば、「 `ibmserver01:9089`」と入力します。
3. **[データベース]**の値を入力します。 たとえば、「 `nwind`」と入力します。
4. **[認証]**の値を選択します。 たとえば、 **[Basic]**を選択します。
5. **[ユーザー名]**の値を入力します。 たとえば、「 `informix`」と入力します。
6. **[パスワード]**の値を入力します。 たとえば、「 `Password1`」と入力します。
7. **[ゲートウェイ]**の値を選択します。 たとえば、 **[datagateway01]**を選択します。
8. **[作成]** をクリックして続行します。 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>クラウド接続を作成する
このコネクタは、クラウド Informix データベースにアクセスできます。 

1. **[ゲートウェイ]** 構成ウィンドウで、**チェックボックス**をオンにせず、**ゲートウェイ経由の接続**を無効のままにしておきます。 
2. **[接続名]**の値を入力します。 たとえば、「 `hisdemo2`」と入力します。
3. "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **Informix サーバー名**の値を入力します。 たとえば、「 `hisdemo2.cloudapp.net:9089`」と入力します。
4. **Informix データベース名**の値を入力します。 たとえば、「 `nwind`」と入力します。
5. **[ユーザー名]**の値を入力します。 たとえば、「 `informix`」と入力します。
6. **[パスワード]**の値を入力します。 たとえば、「 `Password1`」と入力します。
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
   
   * **[接続名]**の値を入力します。 たとえば、「 `HISDEMO2`」と入力します。
   * "アドレスまたはエイリアス、コロン (:)、ポート番号" の形式で、 **Informix サーバー名**の値を入力します。 たとえば、「 `HISDEMO2.cloudapp.net:9089`」と入力します。
   * **Informix データベース名**の値を入力します。 たとえば、「 `NWIND`」と入力します。
   * **[ユーザー名]**の値を入力します。 たとえば、「 `informix`」と入力します。
   * **[パスワード]**の値を入力します。 たとえば、「 `Password1`」と入力します。
9. **[作成]** をクリックして続行します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
11. 必要に応じて、 **[詳細オプションの表示]** をクリックしてクエリ オプションを指定します。
12. [ **保存**] を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. **[InformixgetRows]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
14. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、行のリストが含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>INSERT を使用した&1; つの行の追加
Informix テーブル内の&1; つの行を追加するロジック アプリのアクションを作成できます。 このアクションでは、Informix の INSERT ステートメント ( `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`など) を処理するようコネクタに指示します。

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
7. **[接続]** 構成ウィンドウで、接続をクリックして選択します。 たとえば、 **[hisdemo2]**を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」、[AREADESC] に「`Area 99999`」、**[REGIONID]** に「`102`」と入力します。 
10. [ **保存**] を選択します。
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. **[InformixinsertRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
12. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、新しい行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>SELECT を使用して&1; つの行を取得する
Informix テーブル内の&1; つの行を取得するロジック アプリのアクションを作成できます。 このアクションでは、Informix の SELECT WHERE ステートメント ( `SELECT FROM AREA WHERE AREAID = '99999'`など) を処理するようコネクタに指示します。

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
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。 たとえば、 **[hisdemo2]**を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」と入力します。 
10. 必要に応じて、 **[詳細オプションの表示]** をクリックしてクエリ オプションを指定します。
11. [ **保存**] を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. **[InformixgetRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
13. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>UPDATE を使用して&1; つの行を変更する
Informix テーブル内の&1; つの行を変更するロジック アプリのアクションを作成できます。 このアクションでは、Informix の UPDATE ステートメント ( `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`など) を処理するようコネクタに指示します。

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
7. **[接続]** 構成ウィンドウで、既存の接続をクリックして選択します。 たとえば、 **[hisdemo2]**を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」、[AREADESC] に「`Updated 99999`」、**[REGIONID]** に「`102`」と入力します。 
10. [ **保存**] を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. **[InformixupdateRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
12. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、新しい行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>DELETE を使用した&1; つの行の削除
Informix テーブル内の&1; つの行を削除するロジック アプリのアクションを作成できます。 このアクションでは、Informix の DELETE ステートメント ( `DELETE FROM AREA WHERE AREAID = '99999'`など) を処理するようコネクタに指示します。

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
7. **[接続]** 構成ウィンドウで、既存の接続を選択します。 たとえば、 **[hisdemo2]**を選択します。
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. **[テーブル名]** 一覧の**下向き矢印**をクリックし、**[AREA]** を選択します。
9. 必要な列すべてに値を入力します (赤いアスタリスクを参照)。 たとえば、**[AREAID]** に「`99999`」と入力します。 
10. [ **保存**] を選択します。 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. **[InformixdeleteRow]** ブレードで、**[概要]** の **[すべての実行]** 一覧の先頭に表示されている項目 (最新の実行) を選択します。
12. **[ロジック アプリの実行]** ブレードで、**[実行の詳細]** を選択します。 **[アクション]** 一覧で、**[Get_rows]** を選択します。 **[状態]** の値が **[成功]** になっていることを確認します。 **[入力のリンク]** を選択して入力を表示します。 **[出力のリンク]** を選択し、出力を表示します。出力には、削除された行が含まれています。
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="technical-details"></a>技術的な詳細
## <a name="actions"></a>アクション
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 Informix データベース コネクタには、次のアクションがあります。 

| アクション | Description |
| --- | --- |
| [GetRow](connectors-create-api-informix.md#get-row) |Informix テーブルから&1; つの行を取得します |
| [GetRows](connectors-create-api-informix.md#get-rows) |Informix テーブルから複数の行を取得します |
| [InsertRow](connectors-create-api-informix.md#insert-row) |Informix テーブルに新しい行を挿入します |
| [DeleteRow](connectors-create-api-informix.md#delete-row) |Informix テーブルから行を削除します |
| [GetTables](connectors-create-api-informix.md#get-tables) |Informix データベースからテーブルを取得します |
| [UpdateRow](connectors-create-api-informix.md#update-row) |Informix テーブルの既存の行を更新します |

### <a name="action-details"></a>アクションの詳細
このセクションでは、必須または任意の入力プロパティ、コネクタに関連付けられた対応する出力など、各アクションに関する具体的な詳細について説明します。

#### <a name="get-row"></a>行を取得する
Informix テーブルから&1; つの行を取得します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table * |テーブル名 |Informix テーブルの名前 |
| id * |行 ID |取得する行の一意識別子 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

#### <a name="get-rows"></a>行を取得する
Informix テーブルから複数の行を取得します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |Informix テーブルの名前 |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |
| $filter |Filter Query (フィルター クエリ) |エントリ数を制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="insert-row"></a>行を挿入する
Informix テーブルに新しい行を挿入します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |Informix テーブルの名前 |
| item* |行 |Informix の指定されたテーブルに挿入する行 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

#### <a name="delete-row"></a>行を削除する
Informix テーブルから行を削除します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |Informix テーブルの名前 |
| id* |行 ID |削除する行の一意識別子 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
なし。

#### <a name="get-tables"></a>テーブルを取得する
Informix データベースからテーブルを取得します。  

この呼び出しには、パラメーターはありません。 

##### <a name="output-details"></a>出力の詳細
TablesList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

#### <a name="update-row"></a>行を更新する
Informix テーブルの既存の行を更新します。  

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| table* |テーブル名 |Informix テーブルの名前 |
| id* |行 ID |更新する行の一意識別子 |
| item* |行 |更新された値のある行 |

アスタリスク (*) は、そのプロパティが必須であることを意味します。

##### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="http-responses"></a>HTTP 応答
他のアクションを呼び出すとき、特定の応答を受け取る場合があります。 次の表に、これらの応答とその説明を示します。  

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました |
| default |操作に失敗しました。 |

## <a name="supported-informix-platforms-and-versions"></a>サポートされている Informix のプラットフォームとバージョン
分散型リレーショナル データベース アーキテクチャ (DRDA) クライアント接続に対応するように構成されている場合、このコネクタでは IBM Informix の次のバージョンをサポートします。

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。 [API の一覧](apis-list.md)で、Logic Apps で使用できる他のコネクタを確認してください。


