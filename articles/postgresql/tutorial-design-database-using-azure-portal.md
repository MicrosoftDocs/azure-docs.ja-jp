---
title: チュートリアル:Azure Database for PostgreSQL - Single Server を設計する - Azure portal
description: このチュートリアルでは、Azure portal を使用して最初の Azure Database for PostgreSQL - 単一サーバーを設計する方法を説明します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 179d4124e28abfffa83b16cfdb418d8e860192d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93337134"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Azure Database for PostgreSQL - 単一サーバーを設計する

Azure Database for PostgreSQL は、高可用性 PostgreSQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 Azure Portal を使用して、簡単にサーバーを管理し、データベースを設計することができます。

このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明します。
> [!div class="checklist"]
> * Azure Database for PostgreSQL サーバーの作成
> * サーバーのファイアウォールの構成
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="create-an-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の作成

Azure Database for PostgreSQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-pricing-tiers.md)を使って作成されます。 サーバーは、[Azure リソース グループ](../azure-resource-manager/management/overview.md)内に作成されます。

Azure Database for PostgreSQL サーバーを作成するには、次の手順に従います。
1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[新規]** ページで **[データベース]** を選択し、 **[データベース]** ページで **[Azure Database for PostgreSQL]** を選択します。
   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL - データベースの作成":::

3. **[単一サーバー]** デプロイ オプションを選択します。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/select-deployment-option.png" alt-text="Azure Database for PostgreSQL の [単一サーバー] デプロイ オプションを選択します":::

4. **[基本]** フォームに以下の情報を入力します。

    :::image type="content" source="./media/tutorial-design-database-using-azure-portal/create-basics.png" alt-text="サーバーの作成":::

    設定|推奨値|説明
    ---|---|---
    サブスクリプション|お使いのサブスクリプション名|サーバーに使用する Azure サブスクリプション。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択してください。
    Resource group|*myresourcegroup*| 新しいリソース グループ名、またはサブスクリプションの既存のリソース グループ名。
    サーバー名 |*mydemoserver*|Azure Database for PostgreSQL サーバーを識別する一意の名前。 指定したサーバー名にドメイン名 *postgres.database.azure.com* が追加されます。 サーバー名に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 3 文字以上 63 文字以内にする必要があります。
    データ ソース | *なし* | *[なし]* を選択し、最初から新しいサーバーを作成します (既存の Azure Database for PostgreSQL サーバーの geo バックアップからサーバーを作成している場合は、 *[Backup]* を選択します)。
    管理者ユーザー名 |*myadmin*| サーバーに接続するときに使用する独自のログイン アカウント。 管理者のログイン名に **azure_superuser**、**azure_pg_admin**、**admin**、**administrator**、**root**、**guest**、または **public** は使用できません。 **pg_** で始めることはできません。
    Password |お使いのパスワード| サーバー管理者アカウントの新しいパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。
    場所|ユーザーに最も近いリージョン| ユーザーに最も近い場所。
    Version|最新のメジャー バージョン| 他の特定の要件がない場合は、最新の PostgreSQL メジャー バージョン。
    コンピューティングとストレージ | **汎用**、**Gen 5**、**2 仮想コア**、**5 GB**、**7 日**、**地理冗長** | 新しいサーバーのコンピューティング、ストレージ、およびバックアップ構成。 **[サーバーの構成]** を選択します。 次に、 **[汎用]** タブを選択します。*Gen 5*、"*4 仮想コア*"、*100 GB*、および "*7 日*" は、それぞれ **[コンピューティング世代]** 、 **[仮想コア]** 、 **[ストレージ]** 、および **[バックアップの保有期間]** の既定値です。 これらのスライダーはそのままにすることも、調整することもできます。 サーバー バックアップを geo 冗長ストレージで有効にするには、 **[バックアップ冗長オプション]** から **[地理冗長]** を選択します。 この価格レベルの選択を保存するには、 **[OK]** を選択します。 次のスクリーンショットは、これらの選択を示しています。

   > [!NOTE]
   > 低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。 Basic 価格レベルで作成されたサーバーは後で General Purpose またはメモリ最適化にスケーリングできないことに注意してください。 詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/postgresql/)を参照してください。
   > 

    :::image type="content" source="./media/quickstart-create-database-portal/2-pricing-tier.png" alt-text="[価格レベル] ペイン":::

    > [!TIP]
    > **自動拡張** が有効になっている場合、サーバーは、割り当てられた制限に近づくとワークロードに影響を与えずにストレージを増大させます。

5. **[確認および作成]** を選択して、選択内容を確認します。 **[作成]** を選択して、サーバーをプロビジョニングします。 この操作には数分かかることがあります。

6. ツール バーの **[通知]** アイコン (ベル) を選択して、デプロイ プロセスを監視します。 デプロイが完了したら、 **[ダッシュボードにピン留めする]** を選択できます。これにより、このサーバーのタイルが、サーバーの **[概要]** ページへのショートカットとして、Azure Portal ダッシュボードに作成されます。 **[リソースに移動]** を選択すると、サーバーの **[概要]** ページが開きます。

    :::image type="content" source="./media/quickstart-create-database-portal/3-notifications.png" alt-text="[通知] ペイン":::
   
   既定では、**postgres** データベースがサーバーに作成されます。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) は既定のデータベースで、ユーザー、ユーティリティ、サード パーティ製のアプリケーションが使用するためのものです。 (その他の既定のデータベースは **azure_maintenance** です。 その機能は、管理されたサービス プロセスをユーザーのアクションから分離することです。 このデータベースにはアクセスできません。)


## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for PostgreSQL サービスは、サーバーレベルでファイアウォールを使用します。 既定では、このファイアウォールにより、外部のすべてのアプリケーションやツールから、サーバーまたはサーバー上のすべてのデータベースへの接続が禁止されます。接続を許可するためには、特定の IP アドレス範囲に対して、ファイアウォールを開放するファイアウォール規則を作成する必要があります。 

1. デプロイが完了したら、左側のメニューの **[すべてのリソース]** をクリックし、サーバー名「**mydemoserver**」を入力して、ご利用の新しく作成したサーバーを検索します。 検索結果に示されたサーバー名をクリックします。 サーバーの **[概要]** ページが開き、さらに多くの構成オプションが表示されます。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure Database for PostgreSQL - サーバーの検索":::

2. サーバーのページで、 **[接続のセキュリティ]** を選択します。 

3. **[規則名]** の下のテキスト ボックス内をクリックし、接続を許可する IP の範囲を指定する新しいファイアウォール規則を追加します。 IP 範囲を入力します。 **[保存]** をクリックします。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/5-firewall-2.png" alt-text="Azure Database for PostgreSQL - ファイアウォール規則の作成":::

4. **[保存]** をクリックしてから **[X]** をクリックして、 **[接続のセキュリティ]** ページを閉じます。

   > [!NOTE]
   > Azure PostgreSQL サーバーはポート 5432 を介して通信します。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
   >

## <a name="get-the-connection-information"></a>接続情報の取得

Azure Database for PostgreSQL サーバーを作成したときに、既定の **postgres** データベースも作成されています。 データベース サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。

1. Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、作成したばかりのサーバーを検索します。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure Database for PostgreSQL - サーバーの検索":::

2. サーバー名 **[mydemoserver]** をクリックします。

3. サーバーの **[概要]** ページを選択します。 **[サーバー名]** と **[サーバー管理者ログイン名]** の値を書き留めておきます。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/6-server-name.png" alt-text="Azure Database for PostgreSQL - サーバー管理者ログイン":::


## <a name="connect-to-postgresql-database-using-psql"></a>psql を使用した PostgreSQL データベースへの接続
クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) のローカル インスタンスまたは Azure Cloud Console を使用して Azure PostgreSQL サーバーに接続できます。 ここでは psql コマンド ライン ユーティリティを使用して、Azure Database for PostgreSQL サーバーに接続しましょう。

1. 次の psql コマンドを実行して Azure Database for PostgreSQL データベースに接続します。
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある既定のデータベース **postgres** に接続します。 パスワードの入力を求められたら、選択した `<server_admin_password>` を入力します。
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Postgres への接続に URL パスを使用する場合は、`%40` を使用してユーザー名の @ 記号を URL エンコードします。 たとえば、psql の接続文字列は次のようになります。
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. サーバーに接続したら、プロンプトで空のデータベースを作成します。
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for PostgreSQL に接続する方法を説明したので、次はいくつかの基本的なタスクを実行します。

最初に、テーブルを作成してデータを読み込みます。 次の SQL コードを使用して、インベントリ情報を追跡するテーブルを作成しましょう。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

次のように入力すると、新しく作成されたテーブルが一覧に表示されます。
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したインベントリ テーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、インベントリ データベース テーブルから情報を取得します。 
```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得すると、更新された値を見ることができます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>以前の特定の時点にデータを復元する
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for PostgreSQL では、サーバーのバックアップがある任意の時点 (設定したバックアップ リテンション期間によって決まります) に遡って、新しいデータベースに過去のデータを復元できます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、インベントリ テーブルを追加する前の状態に **mydemoserver** サーバーを復元します。

1. サーバーの Azure Database for PostgreSQL の **[概要]** ページで、ツール バーの **[復元]** をクリックします。 **[復元]** ページが開きます。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png" alt-text="サーバーの Azure Database for PostgreSQL の **[概要]** ページのスクリーンショット。[復元] ボタンが強調表示されている。":::

2. **[復元]** フォームに必要な情報を入力します。

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png" alt-text="Azure portal - [復元] フォームのオプション":::

   - **復元ポイント**:サーバーが変更される前の日時を選択します。
   - **対象サーバー**:復元先の新しいサーバー名を指定します。
   - **[場所]** :リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります。
   - **価格レベル**:サーバーを復元するときは、この値を変更することはできません。 ソース サーバーと同じレベルになります。 
3. **[OK]** をクリックして、テーブルが削除される前の [状態にサーバーを復元](./howto-restore-server-portal.md)します。 異なる時点にサーバーを復元すると、[価格レベル](./concepts-pricing-tiers.md)のリテンション期間内であれば、指定した時点の元サーバーと同じサーバー内に、新しいサーバーが複製されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

前の手順では、サーバー グループ内に Azure リソースを作成しました。 これらのリソースが将来不要であると思われる場合は、サーバー グループを削除します。 サーバー グループの *[概要]* ページで、 *[削除]* ボタンを押します。 ポップアップ ページでメッセージが表示されたら、サーバー グループの名前を確認し、最後の *[削除]* ボタンをクリックします。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Portal とその他のユーティリティを使用して、次のことを行う方法を説明しました。
> [!div class="checklist"]
> * Azure Database for PostgreSQL サーバーの作成
> * サーバーのファイアウォールの構成
> * **psql** ユーティリティを使用したデータベースの作成
> * サンプル データを読み込む
> * クエリ データ
> * データの更新
> * データの復元

> [!div class="nextstepaction"]
>[Azure CLI を使用して最初の Azure Database for PostgreSQL を設計する](tutorial-design-database-using-azure-cli.md)