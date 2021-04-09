---
title: クイック スタート:サーバーを作成する - Azure portal - Azure Database for PostgreSQL - 単一サーバー
description: このクイックスタート ガイドでは、Azure portal を使用して、Azure Database for PostgreSQL サーバーを作成して管理します。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492388"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure Database for PostgreSQL サーバーを作成する

Azure Database for PostgreSQL は、高可用性の PostgreSQL データベースをクラウドで実行、管理、スケールできるマネージド サービスです。 このクイックスタートでは、単一の Azure Database for PostgreSQL サーバーを作成して接続する方法を説明します。

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成
Azure Database for PostgreSQL の単一サーバー データベースを作成するには、[Azure portal](https://portal.azure.com/) に移動します。 *[Azure Database for PostgreSQL サーバー]* を検索して選択します。

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Azure Database for PostgreSQL を検索します。":::

1. **[追加]** を選択します。

2. Azure Database for PostgreSQL の作成ページで、 **[単一サーバー]** を選択します。

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="単一サーバーを選択します":::

3. **[基本]** フォームに次の情報を入力します。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="単一サーバーを作成する場合の [基本情報] タブを示すスクリーンショット。":::

   |設定|推奨値|説明|
   |:---|:---|:---|
   |サブスクリプション|お使いのサブスクリプション名|目的の Azure サブスクリプションを選択します。|
   |Resource group|*myresourcegroup*| 新しいリソース グループ、またはサブスクリプション内の既存のリソース グループ。|
   |サーバー名 |*mydemoserver*|Azure Database for PostgreSQL サーバーを識別する一意の名前。 指定したサーバー名にドメイン名 *postgres.database.azure.com* が追加されます。 サーバー名に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 3 から 63 文字にする必要があります。|
   |データ ソース | なし | **[なし]** を選択し、最初から新しいサーバーを作成します 既存のサーバーの geo バックアップから復元している場合にのみ、 **[バックアップ]** を選択します。|
   |管理者ユーザー名 |*myadmin*| サーバー管理者のユーザー名を入力します。 **pg_** で始めることはできません。また、次の値は使用できません: **azure_superuser**、**azure_pg_admin**、**admin**、**administrator**、**root**、**guest**、または **public**。|
   |Password |パスワード| サーバー管理者ユーザーの新しいパスワード。 これには、次のうち 3 つのカテゴリから 8 から 128 文字を含める必要があります: 英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など)。|
   |場所|目的の場所| ドロップダウン リストから場所を選択します。|
   |Version|最新のメジャー バージョン| 他の特定の要件がない場合は、最新の PostgreSQL メジャー バージョン。|
   |コンピューティングとストレージ | "*既定値を使用します*"| 既定の価格レベルは、**4 つの仮想コア** と **100 GB** のストレージを備えた **General Purpose** です。 バックアップの保有期間は、 **[7 日間]** に設定され、 **[地理冗長]** バックアップ オプションが指定されています。<br/>[価格](https://azure.microsoft.com/pricing/details/postgresql/server/)について確認し、必要に応じて既定値を更新します。|


   > [!NOTE]
   > 低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。 Basic 価格レベルで作成されたサーバーは、後から General Purpose またはメモリ最適化にスケーリングできないことに注意してください。

5. **[確認および作成]** を選択して、選択内容を確認します。 **[作成]** を選択して、サーバーをプロビジョニングします。 この操作には数分かかる場合があります。
    > [!NOTE]
    > 空のデータベース **postgres** が作成されます。 また、マネージド サービス プロセスをユーザーの操作から分離するために使用される **azure_maintenance** データベースもあります。 **azure_maintenance** データベースにアクセスすることはできません。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="成功したデプロイ。":::

[問題がある場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>ファイアウォール規則を構成する
既定では、作成したサーバーにはパブリックにアクセスできません。 自分の IP アドレスに対するアクセス許可を付与する必要があります。 Azure portal で対象のサーバー リソースに移動し、そのサーバー リソースの左側のメニューから **[接続のセキュリティ]** を選択します。 対象のリソースを見つける方法がわからない場合は、「[リソースを開く](../azure-resource-manager/management/manage-resources-portal.md#open-resources)」を参照してください。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="接続のセキュリティのためのファイアウォール規則を示すスクリーンショット。":::

**[現在のクライアント IP アドレスを追加する]** を選択し、 **[保存]** を選択します。 さらに IP アドレスを追加するか IP 範囲を指定することで、それらの IP アドレスから対象のサーバーに接続できるようになります。 詳細については、[Azure Database for PostgreSQL のファイアウォール規則](./concepts-firewall-rules.md)に関するページを参照してください。

> [!NOTE]
> 接続の問題を回避するには、ポート 5432 経由の送信トラフィックがネットワークで許可されているかどうかを確認します。 Azure Database for PostgreSQL では、このポートを使用します。

[問題がある場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>psql を使用してサーバーに接続する

一般的な PostgreSQL クライアントである [psql](http://postgresguide.com/utilities/psql.html) または [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) を使用できます。 このクイックスタートでは、Azure portal 内の [Azure Cloud Shell](../cloud-shell/overview.md) で psql を使用して接続します。

1. 対象のサーバーの **[概要]** セクションから、新しく作成されたサーバーのサーバー名、サーバー管理者のログイン名、パスワード、およびサブスクリプション ID をメモします。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="接続情報を取得します。":::


2. 左上のアイコンを選択して、ポータルで Azure Cloud Shell を開きます。

   > [!NOTE]
   > 初めて Cloud Shell を開いた場合は、リソース グループとストレージ アカウントを作成するように求めるメッセージが表示されます。 これは 1 回限りの作業であり、それ以降はすべてのセッションで自動的に接続されます。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="サーバー情報と、Azure Cloud Shell を開くためのアイコンを示すスクリーンショット。":::

3. Azure Cloud Shell ターミナルで次のコマンドを実行します。 値は実際のサーバー名と管理者ユーザーのログイン名に置き換えてください。 `<admin-username>@<servername>` 形式の管理者ユーザーで、空のデータベース **postgres** を使用します。

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Cloud Shell ターミナルでのエクスペリエンスは次のようになります。

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. 同じ Azure Cloud Shell ターミナルで、**guest** というデータベースを作成します。

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. 新しく作成した **guest** データベースへの接続を切り替えます。

   ```bash
   \c guest
   ```
6. 「`\q`」と入力し、Enter キーを押して、psql を終了します。

[問題がある場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>リソースをクリーンアップする
リソース グループに Azure Database for PostgreSQL サーバーが正常に作成されました。 これらのリソースが今後必要になると思われない場合は、リソース グループまたは PostgreSQL サーバーを削除することでこれらのリソースを削除できます。

リソース グループを削除するには:

1. Azure portal で、「**リソース グループ**」を検索して選択します。
2. リソース グループの一覧で、リソース グループの名前を選択します。
3. 対象のリソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。
4. 確認のダイアログ ボックスで対象のリリソース グループの名前を入力し、 **[削除]** を選択します。

サーバーを削除するには、対象のサーバーの **[概要]** ページで **[削除]** ボタンを選択します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="サーバーを削除するためのボタンを示すスクリーンショット。":::

[問題がある場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [データベースの設計](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[お探しの情報が見つからない場合は、お知らせください。](https://aka.ms/postgres-doc-feedback)