---
title: クイック スタート:サーバーを作成する - Azure portal - Azure Database for PostgreSQL - フレキシブル サーバー
description: Azure portal ユーザー インターフェイスを使用して Azure Database for PostgreSQL - フレキシブル サーバーを作成および管理するためのクイック スタート ガイド。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 978fcb8c8cf9d2ffd929da5b76d7fd2ff109f420
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92535860"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>クイック スタート:Azure portal 内で Azure Database for PostgreSQL - フレキシブル サーバーを作成する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Azure Database for PostgreSQL は、高可用性の PostgreSQL データベースをクラウドで実行、管理、スケールできるマネージド サービスです。 このクイック スタートでは、Azure portal を使用して Azure Database for PostgreSQL - フレキシブル サーバーを作成する方法について説明します。所要時間は約 5 分です。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Web ブラウザーを開き、[ポータル](https://portal.azure.com/)に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

Azure Database for PostgreSQL サーバーは、構成済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-storage.md)を使って作成されます。 サーバーは、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)内に作成されます。

Azure Database for PostgreSQL サーバーを作成するには、次の手順に従います。

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。

2. **[データベース]**  >  **[Azure Database for PostgreSQL]** の順に選択します。

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="メニューの [Azure Database for PostgreSQL]":::

3. **[フレキシブル サーバー]** デプロイ オプションを選択します。

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Azure Database for PostgreSQL - フレキシブル サーバー デプロイ オプションを選択します":::

4. **[基本]** フォームに以下の情報を入力します。

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="サーバーの作成":::

    設定|推奨値|説明
    ---|---|---
    サブスクリプション|お使いのサブスクリプション名|サーバーに使用する Azure サブスクリプション。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択します。
    Resource group|*myresourcegroup*| 新しいリソース グループ名、またはサブスクリプションの既存のリソース グループ名。
    サーバー名 |*mydemoserver*|Azure Database for PostgreSQL サーバーを識別する一意の名前。 指定したサーバー名にドメイン名 *postgres.database.azure.com* が追加されます。 サーバー名に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 3 文字以上 63 文字以内にする必要があります。
    管理者ユーザー名 |*myadmin*| サーバーに接続するときに使用する独自のログイン アカウント。 管理者のログイン名に **azure_superuser**、**azure_pg_admin**、**admin**、**administrator**、**root**、**guest**、または **public** は使用できません。 **pg_** で始めることはできません。
    Password |お使いのパスワード| サーバー管理者アカウントの新しいパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。
    場所|ユーザーに最も近いリージョン| ユーザーに最も近い場所。
    Version|最新のメジャー バージョン| 他の特定の要件がない場合は、最新の PostgreSQL メジャー バージョン。
    コンピューティングとストレージ | **General Purpose**、**4 仮想コア**、**512 GB**、**7 日** | 新しいサーバーのコンピューティング、ストレージ、およびバックアップ構成。 **[サーバーの構成]** を選択します。 *General Purpose*、"*4 仮想コア*"、*512 GB*、および "*7 日*" は、それぞれ **[コンピューティング レベル]** 、 **[仮想コア]** 、 **[ストレージ]** 、および **[バックアップの保有期間]** の既定値です。 これらのスライダーはそのままにすることも、調整することもできます。 この価格レベルの選択を保存するには、 **[OK]** を選択します。 次のスクリーンショットは、これらの選択を示しています。

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="[価格レベル] ペイン":::
    
5. ネットワーク オプションの構成

    [ネットワーク] タブでは、どのようにしてサーバーに到達できるようにするかを選択できます。 Azure Database for PostgreSQL は、サーバー レベルでファイアウォールを作成します。 これにより、外部のアプリケーションやツールからの、サーバーとサーバー上のすべてのデータベースへの接続が禁止されます。接続を許可するには、特定の IP アドレスに対してファイアウォールを開放する規則を作成します。 サーバーにパブリックにアクセスできるようにすることをお勧めします。

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="[ネットワーク] ペイン":::

    次に、それを独自のクライアント IP アドレスに制限します。

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="[現在のクライアント IP アドレスを追加する] を選択する":::

6. **[確認および作成]** を選択して、選択内容を確認します。 **[作成]** を選択して、サーバーをプロビジョニングします。 この操作には数分かかることがあります。

7. ツール バーの **[通知]** アイコン (ベル) を選択して、デプロイ プロセスを監視します。 デプロイが完了したら、 **[ダッシュボードにピン留めする]** を選択できます。これにより、このサーバーのタイルが、サーバーの **[概要]** ページへのショートカットとして、Azure Portal ダッシュボードに作成されます。 **[リソースに移動]** を選択すると、サーバーの **[概要]** ページが開きます。

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="[通知] ペイン":::

   既定では、**postgres** データベースがサーバーに作成されます。 [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) は既定のデータベースで、ユーザー、ユーティリティ、サード パーティ製のアプリケーションが使用するためのものです。 (その他の既定のデータベースは **azure_maintenance** です。 その機能は、管理されたサービス プロセスをユーザーのアクションから分離することです。 このデータベースにはアクセスできません。)

    > [!NOTE]
    > Azure Database for PostgreSQL サーバーとの接続では、ポート 5432 が通信に使用されます。 企業ネットワーク内から接続しようとしたときに、ポート 5432 での送信トラフィックがネットワークのファイアウォールで許可されていないことがあります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、サーバーに接続することはできません。
    >

## <a name="get-the-connection-information"></a>接続情報の取得

Azure Database for PostgreSQL サーバーを作成したときに、**postgres** という名前の既定のデータベースも作成されています。 データベース サーバーに接続するには、サーバーの完全な名前と管理者ログイン資格情報が必要です。 これらの値は、クイック スタートの前の手順でメモしてあるはずです。 メモしていなかった場合は、ポータルのサーバーの **[概要]** ページから、サーバー名とログイン情報を簡単に見つけることができます。

サーバーの **[概要]** ページを開きます。 **[サーバー名]** と **[サーバー管理者ログイン名]** をメモしておきます。 各フィールドの上にカーソルを合わせると、テキストの右側にコピー シンボルが表示されます。 必要に応じてコピー シンボルを選択して値をコピーします。

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="サーバーの [概要] ページ":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>psql を使用した PostgreSQL データベースへの接続

Azure Database for PostgreSQL サーバーに接続するために使用できる、多くのアプリケーションがあります。 クライアント コンピューターに PostgreSQL がインストールされている場合は、[psql](https://www.postgresql.org/docs/current/static/app-psql.html) のローカル インスタンスを使用して Azure PostgreSQL サーバーに接続できます。 ここでは psql コマンド ライン ユーティリティを使用して、Azure PostgreSQL サーバーに接続しましょう。

1. 次の psql コマンドを実行して Azure Database for PostgreSQL に接続します。

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   たとえば次のコマンドは、アクセス資格情報を使用して、PostgreSQL サーバー **mydemoserver.postgres.database.azure.com** にある既定のデータベース **postgres** に接続します。 パスワードの入力を求められたら、選択した `<server_admin_password>` を入力します。
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   接続されると、sql コマンドの入力を求める postgres プロンプトが psql ユーティリティによって表示されます。 初回接続時の出力には、使用している psql のバージョンが Azure Database for PostgreSQL サーバー側のバージョンと異なることが原因で、警告が表示されることがあります。

   psql の出力例: 

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > クライアントの IP アドレスを許可するようにファイアウォールが構成されていない場合、次のエラーが発生します。
   >
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. ("psql: 致命的:  ホスト <IP アドレス>、ユーザー "myadmin"、データベース "postgres" のエントリが pg_hba.conf にありません。SSL オン 致命的: SSL 接続が必要です。) Specify SSL options and retry. (SSL のオプションを指定して再試行してください。)
   >
   > クライアントの IP が上記のファイアウォール規則の手順で許可されていることを確認します。

2. プロンプトで次のコマンドを入力して、"mypgsqldb" という空のデータベースを作成します。

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。

    ```bash
    \c mypgsqldb
    ```

4. 「`\q`」を入力し、Enter キーを押して、psql を終了します。

psql を介して Azure Database for PostgreSQL サーバーに接続し、空のユーザー データベースを作成しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成したリソースは、2 つある方法のうちいずれかでクリーンアップすることができます。 Azure リソース グループを削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、サーバー リソースだけを削除してください。

> [!TIP]
> このコレクションの他のクイック スタートは、このクイック スタートに基づいています。 引き続きクイックスタートの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したリソースをポータルで削除してください。

新しく作成したサーバーを含むリソース グループ全体を削除する手順は次のとおりです。

1. ポータルで目的のリソース グループを探します。 左側のメニューで **[リソース グループ]** を選択します。 次に、リソース グループ名 (例に示した **myresourcegroup** など) を選択します。

2. リソース グループ ページで **[削除]** を選択します。 リソース グループ名 (例に示した **myresourcegroup** など) をテキスト ボックスに入力して、削除の確認を行います。 **[削除]** を選択します。

新しく作成したサーバーを削除するには、次の手順に従います。

1. ポータルで目的のサーバーを探します (サーバーをまだ開いていない場合)。 左側のメニューで **[すべてのリソース]** を選択します。 次に、作成したサーバーを検索します。

2. **[概要]** ページで **[削除]** を選択します。

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="[削除] ボタン":::

3. 削除するサーバーの名前を確認し、影響を受けるデータベースをその下に表示します。 テキスト ボックスにサーバー名 (例に示した **mydemoserver** など) を入力します。 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [App Service と PostgreSQL を使用して Django アプリをデプロイする](tutorial-django-app-service-postgres.md)