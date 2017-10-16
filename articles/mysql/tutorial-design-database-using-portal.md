---
title: "最初の Azure Database for MySQL データベースを設計する - Azure Portal | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal を使用して、Azure Database for MySQL サーバーとデータベースを作成および管理する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql
ms.topic: tutorial
ms.date: 06/06/2017
ms.custom: mvc
ms.openlocfilehash: 92ae809646cb956b0b7cb71cf2a21610bb2d610f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="design-your-first-azure-database-for-mysql-database"></a>最初の Azure Database for MySQL データベースを設計する
Azure Database for MySQL は、高可用性 MySQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 Azure Portal を使用して、簡単にサーバーを管理し、データベースを設計することができます。

このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * mysql コマンド ライン ツールを使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal にサインインします。
Web ブラウザーを開いて [Microsoft Azure Portal](https://portal.azure.com/) にアクセスします。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
Azure Database for MySQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使って作成されます。 サーバーは、[Azure リソース グループ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)内に作成されます。

1. **[データベース]** > **[Azure Database for MySQL]** の順に移動します。 **[データベース]** カテゴリ内に MySQL Server が見つからない場合は、**[すべて表示]** をクリックして、使用可能なすべてのデータベース サービスを表示します。 検索ボックスに「**Azure Database for MySQL**」と入力すれば、サービスをすばやく探せます。
![2-1 MySQL への移動](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2. **[Azure Database for MySQL]** タイルをクリックして **[作成]** をクリックします。

この例では、Azure Database for MySQL フォームに次の情報を入力します。

| **設定** | **推奨値** | **フィールドの説明** |
|---|---|---|
| *[サーバー名]* | myserver4demo  | サーバー名はグローバルに一意である必要があります。 |
| *サブスクリプション* | mysubscription | ドロップダウン リストからサブスクリプションを選択します。 |
| *[リソース グループ]* | myresourcegroup | リソース グループを作成するか、既存のものを使用します。 |
| *[サーバー管理者ログイン]* | myadmin | 管理者アカウントの名前を設定します。 |
| *パスワード* |  | 強力な管理者アカウントのパスワードを設定します。 |
| *パスワードの確認* |  | 管理者アカウントのパスワードを確認します。 |
| *場所* |  | 使用可能なリージョンを選択します。 |
| *バージョン* | 5.7 | 最新バージョンを選択します。 |
| *[パフォーマンスの構成]* | Basic、50 コンピューティング ユニット、50 GB  | **価格レベル**、**コンピューティング ユニット**、**ストレージ (GB)** を選択して **[OK]** をクリックします。 |
| *ダッシュボードにピン留めする* | ○ | サーバーを後で簡単に見つけることができるので、このチェック ボックスをオンすることをお勧めします。 |
**[作成]**をクリックします。 1 ～ 2 分で、新しい Azure Database for MySQL サーバーがクラウドで実行されます。 ツール バーの **[通知]** ボタンをクリックすると、デプロイ プロセスを監視できます。

## <a name="configure-firewall"></a>ファイアウォールの構成
Azure Databases for MySQL は、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続はすべて拒否されます。 初めて Azure Database for MySQL に接続する前に、ファイアウォールを構成し、クライアント マシンのパブリック ネットワーク IP アドレス (またはその範囲) を追加します。

1. 新しく作成したサーバーをクリックし、**[接続のセキュリティ]** をクリックします。
   ![3-1 接続のセキュリティ](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)
2. **自分の IP を追加**するか、またはファイアウォール規則を構成できます。 規則を作成したら、忘れずに **[保存]** をクリックしてください。
これで、mysql コマンド ライン ツールまたは MySQL Workbench GUI ツールを使用してサーバーに接続することができます。

> [!TIP]
> Azure Database for MySQL サーバーは、ポート 3306 経由で通信します。 企業ネットワーク内から接続しようとしても、ポート 3306 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、Azure MySQL サーバーに接続することはできません。

## <a name="get-connection-information"></a>接続情報の取得
Azure Portal から、Azure Database for MySQL サーバーの完全修飾**サーバー名**と**サーバー管理者ログイン名**を取得します。 この完全修飾サーバー名は、mysql コマンド ライン ツールでサーバーに接続する際に使用します。 

1. [Azure Portal](https://portal.azure.com/) の左側のメニューにある **[すべてのリソース]** をクリックして名前を入力し、Azure Database for MySQL サーバーを探します。 サーバー名を選択すると、詳細が表示されます。

2. [設定] という見出しの **[プロパティ]** をクリックします。 **[サーバー名]** と **[サーバー管理者ログイン名]** の値をメモしておきます。 各フィールドの横にあるコピー ボタンをクリックすると、クリップボードにコピーできます。
   ![4-2 サーバーのプロパティ](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

この例では、サーバー名は *myserver4demo.mysql.database.azure.com*、サーバー管理者ログインは *myadmin@myserver4demo* です。

## <a name="connect-to-the-server-using-mysql"></a>mysql を使用してサーバーに接続する
[mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)を使用して、Azure Database for MySQL サーバーへの接続を確立します。 mysql コマンドライン ツールは、ブラウザーで Azure Cloud Shell から実行するか、ローカルにインストールされている mysql ツールを使用して自分のマシンから起動できます。 Azure Cloud Shell を起動するには、この記事のコード ブロックにある `Try It` ボタンをクリックするか、Azure Portal にアクセスして右上のツール バーにある `>_` アイコンをクリックします。 

接続するためのコマンドを入力します。
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

## <a name="create-a-blank-database"></a>空のデータベースの作成
サーバーに接続したら、使用する空のデータベースを作成します。
```sql
CREATE DATABASE mysampledb;
```

プロンプトで次のコマンドを実行し、新しく作成したデータベースに接続を切り替えます。
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for MySQL データベースに接続する方法を説明したので、次は基本的なタスクを行う方法をいくつか説明します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を格納するテーブルを作成しましょう。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、データベース テーブルから情報を取得します。
```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得するとき、それに応じてデータ行が更新されます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する
大切なデータベース テーブルを不注意で削除し、簡単にはデータを復元できなくなった状況を想像してみてください。 Azure Database for MySQL では、サーバーを特定時点まで復元し、データベースのコピーを新しいサーバーに作成することができます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル データベースを復元します。

1. Azure Portal で、ご利用の Azure Database for MySQL を探します。 **[概要]** ページのツール バーで **[復元]** をクリックします。 [復元] ページが表示されます。

   ![10-1 データベースの復元](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2. **[復元]** フォームに必要な情報を入力します。
   
   ![10-2 [復元] フォーム](./media/tutorial-design-database-using-portal/10_2-restore-form.png)
   
   - **[復元ポイント]**: 一覧表示された期間から、どの時点までさかのぼって復元するかを選択します。 ローカル タイム ゾーンは必ず UTC に変換してください。
   - **[新しいサーバーに復元]**: 復元先の新しいサーバー名を指定します。
   - **[場所]**: リージョンはソース サーバーと同じ場所にします。変更することはできません。
   - **[価格レベル]**: ソース サーバーと同じ価格レベルにします。変更することはできません。
   
3. **[OK]** をクリックして、[テーブルが削除される前の状態にサーバーを復元](./howto-restore-server-portal.md)します。 サーバーを復元すると、指定した時点のサーバーのコピーが新たに作成されます。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明しました。

> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * mysql コマンド ライン ツールを使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

> [!div class="nextstepaction"]
> [Azure Database for MySQL にアプリケーションを接続する方法](./howto-connection-string.md)
