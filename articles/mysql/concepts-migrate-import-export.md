---
title: Azure Database for MySQL でのインポートとエクスポート
description: この記事では、MySQL Workbench などのツールを使用して、Azure Database for MySQL でデータベースをインポートおよびエクスポートする一般的な方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545520"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>インポートとエクスポートを使用した MySQL データベースの移行
この記事では、MySQL Workbench を使用して、Azure Database for MySQL サーバーにデータをインポートおよびエクスポートする 2 つの一般的な方法について説明します。 

## <a name="before-you-begin"></a>開始する前に
このハウツー ガイドの手順を実行するには、以下が必要です。
- 「[Azure Portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)」の手順に従って作成された Azure Database for MySQL サーバー
- MySQL Workbench ([ダウンロード](https://dev.mysql.com/downloads/workbench/))、または別の MySQL インポート/エクスポート ツール

## <a name="use-common-tools"></a>一般的なツールの使用
MySQL Workbench、Toad、Navicat などの一般的なツールを使用して、Azure Database for MySQL にリモートで接続し、データをインポートまたはエクスポートします。 

インターネットに接続されたクライアント コンピューターでこのようなツールを使用して、Azure Database for MySQL に接続します。 [Azure Database for MySQL での SSL 接続の構成](concepts-ssl-connection-security.md)に関する記事で説明するように、セキュリティのベスト プラクティスとして、SSL で暗号化された接続を使用します。

Azure Database for MySQL に移行するとき、インポート ファイルおよびエクスポート ファイルをクラウドの特別な場所に移動する必要はありません。 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーでのデータベースの作成
データを移行する Azure Database for MySQL サーバーに空のデータベースを作成します。 データベースは、MySQL Workbench、Toad、Navicat などのツールを使用して作成します。 データベースは、ダンプされたデータが含まれるデータベースと同じ名前にすることも、別の名前でデータベースを作成することもできます。

接続するために、Azure Database for MySQL の **[概要]** で接続情報を見つけます。

![Azure Portal で接続情報を見つける](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

接続情報を MySQL Workbench に追加します。

![MySQL Workbench の接続文字列](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>ダンプと復元の代わりにインポート/エクスポート手法を使用する状況の判断
次のシナリオでは、MySQL ツールを使用して、データベースを Azure MySQL Database にインポートおよびエクスポートします。 他のシナリオでは、代わりに[ダンプと復元](concepts-migrate-dump-restore.md)の方法を使用することでメリットが得られます。 

- 既存の MySQL データベースから少数のテーブルを選択して、Azure MySQL Database にインポートする必要がある場合は、インポート/エクスポート手法を使用するのが適しています。  これにより、不要なテーブルを移行から除外して、時間とリソースを節約できます。 たとえば、[mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) では `--include-tables` スイッチまたは `--exclude-tables` スイッチを使用し、[mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables) では `--tables` スイッチを使用します。
- テーブル以外のデータベース オブジェクトを移行する場合は、それらのオブジェクトを明示的に作成します。 制約 (主キー、外部キー、インデックス)、ビュー、関数、プロシージャ、トリガーなど、移行するデータベース オブジェクトを含めます。
- MySQL データベース以外の外部データ ソースからデータを移行する場合は、フラット ファイルを作成し、[mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html) を使用してそれらのファイルをインポートします。

Azure Database for MySQL にデータを読み込むときは、データベースのすべてのテーブルで InnoDB ストレージ エンジンが使用されていることを確認します。 Azure Database for MySQL でサポートされるのは InnoDB ストレージ エンジンだけであるため、代替のストレージ エンジンはサポートされません。 テーブルで別のストレージ エンジンが必要な場合は、Azure Database for MySQL に移行する前に、それらのテーブルを変換して InnoDB エンジン形式を使用する必要があります。 

たとえば、MyISAM エンジンを使用する WordPress または Web アプリがある場合は、まず、データを InnoDB テーブルに移行してテーブルを変換します。 その後、Azure Database for MySQL に復元します。 テーブルを作成するときに、`ENGINE=INNODB` 句を使用してエンジンを設定し、移行前にデータを互換性のあるテーブルに転送します。 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>インポートおよびエクスポートのパフォーマンスに関する推奨事項
-   データを読み込む前に、クラスター化インデックスと主キーを作成します。 主キー順にデータを読み込みます。 
-   データの読み込みが完了するまでセカンダリ インデックスの作成を遅らせます。 読み込み後に、すべてのセカンダリ インデックスを作成します。 
-   読み込み前に外部キー制約を無効にします。 外部キーのチェックを無効にすると、パフォーマンスが大幅に向上します。 読み込み後に制約を有効にし、データを検証して参照整合性を確認します。
-   データを並列で読み込みます。 リソースの上限に達するような過剰な並列処理を避け、Azure Portal で使用可能なメトリックを使用してリソースを監視します。 
-   パーティション テーブルを適宜使用します。

## <a name="import-and-export-by-using-mysql-workbench"></a>MySQL Workbench を使用したインポートとエクスポート
MySQL Workbench では、2 とおりの方法でデータをインポートおよびエクスポートできます。 各方法は目的が異なります。 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>オブジェクト ブラウザーのコンテキスト メニューを使用したテーブル データのエクスポート/インポート ウィザード
![オブジェクト ブラウザーのコンテキスト メニューの MySQL Workbench ウィザード](./media/concepts-migrate-import-export/p1.png)

テーブル データのウィザードでは、CSV ファイルと JSON ファイルを使用してインポート/エクスポート操作をサポートします。 区切り記号、列の選択、エンコードの選択など、複数の構成オプションがあります。 各ウィザードは、ローカルの MySQL サーバーまたはリモート接続された MySQL サーバーに対して実行できます。 インポート アクションには、テーブル、列、型マッピングが含まれます。 

これらのウィザードには、テーブルを右クリックすると表示されるオブジェクト ブラウザーのコンテキスト メニューからアクセスできます。 **[Table Data Export Wizard]\(テーブル データのエクスポート ウィザード\)** または **[Table Data Import Wizard]\(テーブル データのインポート ウィザード\)** を選択します。 

#### <a name="table-data-export-wizard"></a>テーブル データのエクスポート ウィザード
次の例では、テーブルを CSV ファイルにエクスポートします。 
1. エクスポートするデータベースのテーブルを右クリックします。 
2. **[Table Data Export Wizard (テーブル データのエクスポート ウィザード)]** を選択します。 エクスポートする列、行のオフセット (該当する場合)、数 (該当する場合) を選択します。 
3. **[エクスポートするデータの選択]** ページで、**[次へ]** をクリックします。 ファイル パスとファイルの種類 (CSV または JSON) を選択します。 また、行区切り記号、文字列を囲む方法、フィールド区切り記号も選択します。 
4. **[Select output file location]\(出力ファイルの場所の選択\)** ページで、**[次へ]** をクリックします。 
5. **[データのエクスポート]** ページで、**[次へ]** をクリックします。

#### <a name="table-data-import-wizard"></a>テーブル データのインポート ウィザード
次の例では、CSV ファイルからテーブルをインポートします。
1. インポートするデータベースのテーブルを右クリックします。 
2. インポートする CSV ファイルを参照して選択し、**[次へ]** をクリックします。 
3. インポート先テーブル (新規または既存) を選択し、**[Truncate table before import]\(インポート前にテーブルを切り詰め処理を行う\)** チェック ボックスをオンまたはオフにします。 **[次へ]** をクリックします。
4. エンコードとインポートする列を選択し、**[次へ]** をクリックします。 
5. **[データのインポート]** ページで、**[次へ]** をクリックします。 データが適宜インポートされます。

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>ナビゲーター ウィンドウの SQL データのエクスポート/インポート ウィザード
ウィザードを使用して、MySQL Workbench または mysqldump コマンドで生成された SQL をエクスポートまたはインポートできます。 これらのウィザードには、**ナビゲーター** ウィンドウからアクセスするか、メイン メニューの **[サーバー]** を選択してアクセスします。 次に、**[データのエクスポート]** または **[データのインポート]** を選択します。 

#### <a name="data-export"></a>データのエクスポート
![ナビゲーター ウィンドウを使用した MySQL Workbench データのエクスポート](./media/concepts-migrate-import-export/p2.png)

**[データのエクスポート]** タブを使用して、MySQL データをエクスポートできます。 
1. エクスポートする各スキーマを選択し、必要に応じて各スキーマから特定のスキーマ オブジェクト/テーブルを選択して、エクスポートを生成します。 構成オプションには、プロジェクト フォルダーまたは自己完結型 SQL ファイルへのエクスポート、格納されているルーチンとイベントのダンプ、テーブル データのスキップなどがあります。 
 
   また、**[Export a Result Set]\(結果セットのエクスポート\)** を使用して、SQL エディターの特定の結果セットを、CSV、JSON、HTML、XML などの別の形式にエクスポートすることもできます。 
3. エクスポートするデータベース オブジェクトを選択し、関連するオプションを構成します。
4. **[更新]** をクリックして、現在のオブジェクトを読み込みます。
5. 必要に応じて、**[詳細オプション]** タブを開き、エクスポート操作を調整します。 たとえば、テーブル ロックの追加、insert ステートメントではなく replace ステートメントの使用、バッククォート文字を含む識別子の引用などを行います。
6. **[エクスポートの開始]** をクリックして、エクスポート処理を開始します。


#### <a name="data-import"></a>データのインポート
![管理ナビゲーターを使用した MySQL Workbench のデータ インポート](./media/concepts-migrate-import-export/p3.png)

**[データのインポート]** タブを使用して、データ エクスポート操作または mysqldump コマンドによってエクスポートされたデータをインポートまたは復元できます。 
1. プロジェクト フォルダーまたは自己完結型 SQL ファイルを選択するか、インポート先スキーマを選択するか、**[新規]** を選択して新しいスキーマを定義します。 
2. **[インポートの開始]** をクリックしてインポート処理を開始します。

## <a name="next-steps"></a>次の手順
- 別の移行方法として、[Azure Database for MySQL でのダンプと復元を使用した MySQL データベースの移行](concepts-migrate-dump-restore.md)に関する記事を読みます。
- Azure Database for MySQL へのデータベースの移行については、「[データベース移行ガイド](https://aka.ms/datamigration)」をご覧ください。 
