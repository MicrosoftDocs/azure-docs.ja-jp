---
title: 'チュートリアル: Azure Database for MariaDB を設計する - Azure portal'
description: このチュートリアルでは、Azure portal を使用して、Azure Database for MariaDB サーバーとデータベースを作成および管理する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: fe503d0e579e30b0c355d3be3e8d02b2bd7a44a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664471"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>チュートリアル: Azure portal を使用して Azure Database for MariaDB データベースを設計する

Azure Database for MariaDB は、高可用性 MySQL データベースをクラウドで実行、管理、スケーリングするためのマネージド サービスです。 Azure portal を使用して、簡単にサーバーを管理し、データベースを設計することができます。

このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Database for MariaDB を作成する
> * サーバーのファイアウォールの構成
> * mysql コマンドライン ツールを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

ブラウザーで、[Azure portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーを作成する

Azure Database for MariaDB サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](concepts-pricing-tiers.md)を使って作成します。 サーバーは、[Azure リソース グループ](../azure-resource-manager/management/overview.md)内に作成されます。

1. ポータルの左上隅にある **[リソースの作成]** ボタン (+) を選択します。

2. **[データベース]**  >  **[Azure Database for MariaDB]** の順に選択します。 検索ボックスに「**MariaDB**」と入力してサービスを検索することもできます。

   ![MySQL に移動](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. **[Azure Database for MariaDB]** タイルを選択します。 必要な情報を入力または選択します。

   ![フォームの作成](./media/tutorial-design-database-using-portal/2-create-form.png)

    設定 | 推奨値 | フィールドの説明
    ---|---|---
    サーバー名 | *一意のサーバー名* | Azure Database for MariaDB サーバーを識別する一意の名前を選択します。 たとえば **mydemoserver** を選択します。 入力したサーバー名にドメイン名 *.mariadb.database.azure.com* が追加されます。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。
    サブスクリプション | *該当するサブスクリプション* | サーバーに使用する Azure サブスクリプションを選択します。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択してください。
    Resource group | **myresourcegroup** | 新しいリソース グループ名を入力するか、既存のリソース グループを選択します。
    ソースの選択 | "**空白**" | **[空白]** を選択して新しいサーバーを作成します  (既存の Azure Database for MariaDB サーバーの geo バックアップからサーバーを作成する場合は、**[バックアップ]** を選択します)。
    サーバー管理者のログイン | **myadmin** | サーバーに接続するときに使用するサインイン アカウント。 管理者のサインイン名に **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** は使用できません。
    Password | *任意* | サーバー管理者アカウントの新しいパスワードを入力します。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。
    [パスワードの確認入力] | *任意*| 管理者アカウントのパスワードを確認します。
    場所 | *ユーザーに最も近いリージョン*| ユーザーや他の Azure アプリケーションに最も近い場所を選択します。
    Version | *最新バージョン*| 最新バージョン (別のバージョンを使用する特別な要件がある場合を除く)。
    Pricing tier | 説明を参照してください。 | 新しいサーバーのコンピューティング、ストレージ、およびバックアップ構成。 **[価格レベル]**  >  **[General Purpose]** を選択します。 次の設定は既定値のままにしてください。<br><ul><li>**[コンピューティング世代]** (Gen 5)</li><li>**[仮想コア]** (4 仮想コア)</li><li>**ストレージ** (100 GB)</li><li>**[バックアップの保有期間]** (7 日間)</li></ul><br>サーバー バックアップを geo 冗長ストレージで有効にするには、 **[バックアップ冗長オプション]** で **[地理冗長]** を選択します。 <br><br>この価格レベルの選択を保存するには、 **[OK]** を選択します。 次のスクリーンショットは、これらの選択を示しています。
    
   ![Pricing tier](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > **自動拡張** が有効になっている場合、サーバーは、割り当てられた制限に近づくとワークロードに影響を与えずにストレージを増大させます。

4. **[Review + create]\(レビュー + 作成\)** をクリックします。 ツール バーの **[通知]** ボタンをクリックすると、デプロイ プロセスを監視できます。 デプロイには、最大 20 分かかる場合があります。

## <a name="configure-the-firewall"></a>ファイアウォールを構成する

Azure Database for MariaDB は、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続はすべて拒否されます。 初めて Azure Database for MariaDB に接続する前に、ファイアウォールを構成し、クライアント コンピューターのパブリック ネットワーク IP アドレス (またはその範囲) を追加します。

1. 新しく作成したサーバーを選択し、**[接続のセキュリティ]** を選択します。

   ![接続のセキュリティ](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. **[自分の IP を追加]** を選択するか、またはファイアウォール規則を構成できます。 規則を作成したら、忘れずに **[保存]** を選択してください。

これで、mysql コマンド ライン ツールまたは MySQL Workbench を使用してサーバーに接続することができます。

> [!TIP]
> Azure Database for MariaDB サーバーは、ポート 3306 経由で通信します。 企業ネットワーク内から接続しようとしても、ポート 3306 でのアウトバウンド トラフィックがネットワークのファイアウォールで許可されていないことがあります。 その場合、Azure Database for MariaDB サーバーに接続するためには、IT 部門がポート 3306 を開放する必要があります。

## <a name="get-connection-information"></a>接続情報の取得

Azure portal から、Azure Database for MariaDB サーバーの **[サーバー名]** (完全修飾) と **[サーバー管理者ログイン名]** の値を取得します。 この完全修飾サーバー名は、mysql コマンド ライン ツールでサーバーに接続する際に使用します。

1. [Azure portal](https://portal.azure.com/) の左側のメニューで、**[すべてのリソース]** を選択します。 サーバー名を入力し、Azure Database for MariaDB サーバーを検索します。 サーバー名を選択すると、その詳細が表示されます。

2. **[概要]** ページで、**[サーバー名]** と **[サーバー管理者ログイン名]** の値を書き留めます。 各フィールドの横にある **[コピー]** ボタンを選択して、クリップボードに値をコピーすることもできます。

   ![サーバー プロパティ](./media/tutorial-design-database-using-portal/2-server-properties.png)

この例では、サーバー名は **mydemoserver.mariadb.database.azure.com**、サーバー管理者ログイン名は **myadmin\@mydemoserver** です。

## <a name="connect-to-the-server-by-using-mysql"></a>mysql を使用してサーバーに接続する

[mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)を使用して、Azure Database for MariaDB サーバーへの接続を確立します。 mysql コマンドライン ツールは、ブラウザーで Azure Cloud Shell から実行するか、ローカルにインストールされている mysql ツールを使用して自分のコンピューターから実行できます。 Azure Cloud Shell を開くには、この記事のコード ブロックにある **[使ってみる]** ボタンを選択するか、Azure portal にアクセスして右上のツール バーにある **[>_]** アイコンをクリックします。

接続するためのコマンドを入力します。

```bash
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
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

Azure Database for MariaDB データベースに接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を格納するテーブルを作成しましょう。

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>テーブルにデータを読み込む

テーブルを作成したので、次はテーブルにデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行していくつかのデータ行を挿入します。

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する

次のクエリを実行して、データベース テーブルから情報を取得します。

```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得すると、データ行が更新されます。

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する

大切なデータベース テーブルを不注意で削除し、簡単にはデータを復元できなくなった状況を想像してみてください。 Azure Database for MariaDB では、データベースのコピーを新しいサーバーに作成することによって、サーバーを特定時点まで復元することができます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル サーバーを復元します。

1. Azure portal で、ご利用の Azure Database for MariaDB を探します。 **[概要]** ページで **[復元]** を選択します。

   ![データベースを復元する](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. **[復元]** ページで次の情報を入力または選択します。

   ![[復元] フォーム](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **[復元ポイント]**: 一覧表示された期間から、どの時点までさかのぼって復元するかを選択します。 ローカル タイム ゾーンは必ず UTC に変換してください。
   - **[新しいサーバーに復元]**: 復元先となる新しいサーバーの名前を入力します。
   - **[場所]**: リージョンはソース サーバーと同じ場所にします。変更することはできません。
   - **[価格レベル]**: ソース サーバーと同じ価格レベルにします。変更することはできません。
   
3. **[OK]** を選択して、テーブルが削除される前の [時点にサーバーを復元](./howto-restore-server-portal.md)します。 サーバーを復元すると、選択した時点のサーバーのコピーが新たに作成されます。 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明しました。

> [!div class="checklist"]
> * Azure Database for MariaDB を作成する
> * サーバーのファイアウォールの構成
> * mysql コマンド ライン ツールを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

> [!div class="nextstepaction"]
> [Azure Database for MariaDB にアプリケーションを接続する方法](howto-connection-string.md)
