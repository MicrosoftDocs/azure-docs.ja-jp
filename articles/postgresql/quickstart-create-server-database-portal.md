---
title: "Azure Portal: Azure Database for PostgreSQL サーバーを作成する | Microsoft Docs"
description: "Azure Portal ユーザー インターフェイスを使用して Azure Database for PostgreSQL サーバーを作成して管理するためのクイック スタート ガイド。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/10/2017
ms.openlocfilehash: f806a8c1f06a58d0c8440d54c91ecfa41988d8ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Azure Portal で Azure Database for PostgreSQL サーバーを作成する

Azure Database for PostgreSQL は、高可用性の PostgreSQL データベースをクラウドで実行、管理、スケールできるマネージ サービスです。 このクイック スタートでは、Azure Portal を使用して Azure Database for PostgreSQL サーバーを作成する方法について説明します。所要時間は約 5 分です。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。
Web ブラウザーを開き、[ポータル](https://portal.azure.com/)に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

Azure Database for PostgreSQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使って作成されます。 サーバーは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内に作成されます。

Azure Database for PostgreSQL サーバーを作成するには、次の手順に従います。
1. ポータルの左上隅にある **[新規]** ボタン (+) を選択します。

2. **[データベース]** > **[Azure Database for PostgreSQL]** の順に選択します。

    ![[Azure Database for PostgreSQL] オプション](./media/quickstart-create-database-portal/1-create-database.png)

3. 前の画像で示されているように、新規サーバーの詳細フォームに次の情報を入力します。

    設定|推奨値|Description
    ---|---|---
    サーバー名 |*mypgserver-20170401*|Azure Database for PostgreSQL サーバーを識別する一意の名前。 指定したサーバー名にドメイン名 *postgres.database.azure.com* が追加されます。 サーバー名に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 3 文字以上 63 文字以内にする必要があります。
    [サブスクリプション]|該当するサブスクリプション|サーバーに使用する Azure サブスクリプション。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択してください。
    リソース グループ|*myresourcegroup*| 新しいリソース グループ名、またはサブスクリプションの既存のリソース グループ名。
    サーバー管理者のログイン |*mylogin*| サーバーに接続するときに使用する独自のログイン アカウント。 管理者のログイン名に **azure_superuser**、**azure_pg_admin**、**admin**、**administrator**、**root**、**guest**、または **public** は使用できません。 **pg_** で始めることはできません。
    パスワード |<任意> | サーバー管理者アカウントの新しいパスワード。 8 文字以上 128 文字以内にする必要があります。 パスワードには、英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、$、#、% など) のうち、3 種類の文字が含まれている必要があります。
    場所|ユーザーに最も近いリージョン| ユーザーに最も近い場所。
    PostgreSQL のバージョン|最新バージョン| 特別な要件がない限り、最新バージョン。
    [価格レベル]  | **Basic**、**50 コンピューティング ユニット**、**50 GB** | 新しいデータベースのサービス レベルとパフォーマンス レベル。 **[価格レベル]** を選択します。 次に、**[Basic]\(基本\)** タブを選択します。**[コンピューティング ユニット]** スライダーの左端を選択して、このクイック スタートで使用可能な最小量に値を調整します。 価格レベルの選択を保存するには、**[OK]** を選択します。 詳細については、次のスクリーンショットをご覧ください。 
    [ダッシュボードにピン留めする] | ○ | ポータルのフロント ダッシュボード ページでサーバーを追跡しやすくなります。

    > [!IMPORTANT]
    > ここで指定するサーバー管理者のログイン名とパスワードは、このクイック スタートの後半でサーバーとそのデータベースにサインインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。

    ![[価格レベル] ウィンドウ](./media/quickstart-create-database-portal/2-service-tier.png)

4. **[作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには、最大 20 分かかる場合があります。

5. ツール バーの **[通知]** シンボルを選択して、デプロイ プロセスを監視します。

    ![[通知] ウィンドウ](./media/quickstart-create-database-portal/3-notifications.png)
   
  既定では、**postgres** データベースがサーバーに作成されます。 [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) は既定のデータベースで、ユーザー、ユーティリティ、サード パーティ製のアプリケーションが使用するためのものです。 

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for PostgreSQL は、サーバー レベルでファイアウォールを作成します。 これにより、外部のアプリケーションやツールからの、サーバーとサーバー上のすべてのデータベースへの接続が禁止されます。接続を許可するには、特定の IP アドレスに対してファイアウォールを開放する規則を作成します。 

1. デプロイが完了したら、目的のサーバーを探します。 必要に応じて、検索することができます。 たとえば、左側のメニューで **[すべてのリソース]** を選択します。 サーバー名 (例に示した **mypgserver-20170401** など) を入力して、新しく作成されたサーバーを検索します。 検索結果の一覧からご利用のサーバー名を選択します。 サーバーの **[概要]** ページが開き、さらに多くの構成オプションが表示されます。
 
    ![サーバー名の検索](./media/quickstart-create-database-portal/4-locate.png)

2. サーバーのページで、**[接続のセキュリティ]** を選択します。

    ![[接続のセキュリティ] 設定](./media/quickstart-create-database-portal/5-firewall-2.png)

3. **[ファイアウォール規則]** という見出しの下で、**[規則名]** 列の空白のテキスト ボックス内を選択し、ファイアウォール規則の作成を開始します。 

    このクイック スタートでは、サーバーに対してすべての IP アドレスを許可してみましょう。 各列のテキスト ボックスに次の値を入力します。

    規則の名前 | 開始 IP | 終了 IP 
    ---|---|---
    AllowAllIps | 0.0.0.0 | 255.255.255.255

4. **[接続のセキュリティ]** ページの上部のツール バーで、**[保存]** を選択します。 接続のセキュリティの更新が正常に完了したことを示す通知が表示されるまで待ってから、次に進んでください。

    > [!NOTE]
    > Azure Database for PostgreSQL サーバーとの接続では、ポート 5432 が通信に使用されます。 企業ネットワーク内から接続しようとしても、ポート 5432 での送信トラフィックがネットワークのファイアウォールで許可されていないことがあります。 その場合、会社の IT 部門によってポート 5432 が開放されない限り、サーバーに接続することはできません。
    >

## <a name="get-the-connection-information"></a>接続情報の取得

Azure Database for PostgreSQL サーバーを作成したときに、**postgres** という名前の既定のデータベースも作成されています。 データベース サーバーに接続するには、サーバーの完全な名前と管理者ログイン資格情報が必要です。 これらの値は、クイック スタートの前の手順でメモしてあるはずです。 メモしていなかった場合は、ポータルのサーバーの **[概要]** ページから、サーバー名とログイン情報を簡単に見つけることができます。

サーバーの **[概要]** ページを開きます。 **[サーバー名]** と **[サーバー管理者ログイン名]** をメモしておきます。 各フィールドの上にカーソルを合わせると、テキストの右側にコピー シンボルが表示されます。 必要に応じてコピー シンボルを選択して値をコピーします。

 ![サーバーの [概要] ページ](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-the-postgresql-database-by-using-psql-in-cloud-shell"></a>Cloud Shell で psql を使用して PostgreSQL データベースに接続する

Azure Database for PostgreSQL サーバーに接続するために使用できる、多くのアプリケーションがあります。 まず、サーバーに接続する方法を示すために、psql コマンド ライン ユーティリティを使用してみましょう。 追加のソフトウェアをインストールしなくても、ここで説明するように、Web ブラウザーと Azure Cloud Shell を使用できます。 自分のマシンに psql ユーティリティがローカルにインストールしてある場合は、そこからも接続できます。

1. 上部のナビゲーション ウィンドウで、Cloud Shell を開くターミナル シンボルを選択します。

   ![Azure Cloud Shell のターミナル シンボル](./media/quickstart-create-database-portal/7-cloud-console.png)

2. ブラウザーで Cloud Shell を開き、Bash シェル コマンドを入力できます。

   ![Cloud Shell の Bash プロンプト](./media/quickstart-create-database-portal/8-bash.png)

3. Cloud Shell プロンプトで psql コマンド ラインを入力して、Azure Database for PostgreSQL サーバーのデータベースに接続します。

    [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) ユーティリティで Azure Database for PostgreSQL サーバーに接続するには、次の形式を使用します。
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    たとえば、次のコマンドは、サンプル サーバーに接続します。

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    psql パラメーター |推奨値|Description
    ---|---|---
    --host | サーバー名 | 前の手順で Azure Database for PostgreSQL サーバーを作成したときに使用したサーバー名の値。 例に示したサーバーは、**mypgserver-20170401.postgres.database.azure.com** です。例で示されているように、完全修飾ドメイン名 (**\*.postgres.database.azure.com**) を使用します。 サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得してください。 
    --port | 5432 | Azure Database for PostgreSQL サーバーに接続するときに使用するポート。 
    --username | サーバー管理者ログイン名 |前の手順で Azure Database for PostgreSQL サーバーを作成したときに指定したサーバー管理者ログイン ユーザー名。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得してください。 形式は *username@servername* です。
    --dbname | *postgres* | 初回接続時にシステムによって生成される既定のデータベース名。 後で独自のデータベースを作成します。

    独自のパラメーター値で psql コマンドを実行した後、サーバー管理者パスワードの入力を求めるメッセージが表示されます。 これは、サーバーの作成時に指定したのと同じパスワードです。 

    psql パラメーター |推奨値|Description
    ---|---|---
    パスワード | 管理者パスワード | 入力したパスワードの文字は bash プロンプトには表示されません。 認証して接続するには、すべての文字を入力してから **Enter** キーを押します。

    接続されると、sql コマンドの入力を求める postgres プロンプトが psql ユーティリティによって表示されます。 初回接続時の出力には、Cloud Shell の psql のバージョンが Azure Database for PostgreSQL サーバー側のバージョンと異なることが原因で、警告が表示されることがあります。 
    
    psql の出力例: 
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Cloud Shell の IP アドレスを許可するようにファイアウォールが構成されていない場合、次のエラーが発生します。
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL: SSL connection is required.Specify SSL options and retry. ("psql: 致命的:  ホスト "138.91.195.82"、ユーザー "mylogin"、データベース "postgres" のエントリが pg_hba.conf にありません。SSL オン 致命的:  SSL 接続が必要です。SSL オプションを指定して再試行してください。)
    > 
    > エラーを解決するには、サーバーの構成がこの記事の「サーバーレベルのファイアウォール規則の構成」セクションの手順と一致していることを確認してください。

4. プロンプトに次のコマンドを入力して空のデータベースを作成します。
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    このコマンドが完了するまで数分かかることがあります。 

5. プロンプトで次のコマンドを実行し、新しく作成したデータベース **mypgsqldb** に接続を切り替えます。
    ```bash
    \c mypgsqldb
    ```

6. 「`\q`」を入力してから、**Enter** キーを押して psql を終了します。 完了したら Cloud Shell を閉じることができます。

これで、Azure Database for PostgreSQL サーバーに接続し、空のユーザー データベースを作成することができました。 他の一般的なツールである pgAdmin を使用して接続するには、次のセクションに進みます。

## <a name="connect-to-the-postgresql-database-by-using-pgadmin"></a>pgAdmin を使用して PostgreSQL データベースに接続する

GUI ツール pgAdmin を使用して Azure PostgreSQL サーバーに接続する手順は次のとおりです。
1. クライアント コンピューターで pgAdmin アプリケーションを開きます。 [pgAdmin の Web サイト](http://www.pgadmin.org/)からインストールできます。

2. ダッシュボード ページで、**[クイックリンク]** セクションにある **[新しいサーバーの追加]** シンボルを選択します。

3. **[Create - Server]\(作成 - サーバー\)** ダイアログ ボックスの **[General]\(全般\)** タブに、サーバーの一意のフレンドリ名を入力します (「**Azure PostgreSQL Server**」など)。

    ![[General]\(全般\) タブ](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)

4. **[Create - Server]\(作成 - サーバー\)** ダイアログ ボックスの **[接続]** タブで、指定された設定を使用し、**[保存]** をクリックします。

   ![[接続] タブ](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    pgAdmin パラメーター |推奨値|Description
    ---|---|---
    Host Name/Address (ホスト名/アドレス) | サーバー名 | 前の手順で Azure Database for PostgreSQL サーバーを作成したときに使用したサーバー名の値。 例に示したサーバーは、**mypgserver-20170401.postgres.database.azure.com.** です。例で示されているように、完全修飾ドメイン名 (**\*.postgres.database.azure.com**) を使用します。 サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得してください。 
    Port | 5432 | Azure Database for PostgreSQL サーバーに接続するときに使用するポート。 
    メンテナンス データベース | *postgres* | システムによって生成される既定のデータベース名。
    ユーザー名 | サーバー管理者ログイン名 | 前の手順で Azure Database for PostgreSQL サーバーを作成したときに指定したサーバー管理者ログイン ユーザー名。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得してください。 形式は *username@servername* です。
    パスワード | 管理者パスワード | このクイック スタートでサーバーを作成したときに選択したパスワードです。
    役割 | 空白 | この時点でロール名を指定する必要はありません。 このフィールドは空白にしてください。
    SSL モード | 必須 | 既定では、すべての Azure PostgreSQL サーバーは SSL 適用がオンの状態で作成されます。 SSL 適用をオフにする方法については、[SSL の適用](./concepts-ssl-connection-security.md)に関する記事をご覧ください。
    
5. [ **保存**] を選択します。

6. 左側の **[ブラウザー]** ウィンドウで **[サーバー]** ノードを展開します。 **Azure PostgreSQL Server** など、ご利用のサーバーを選択します。 クリックして接続します。

7. サーバー ノードを展開し、その下の **[Databases]\(データベース\)** を展開します。 既存の *postgres* データベースと、新しく作成されたユーザー データベース (前のセクションで作成した **mypgsqldb** など) が一覧表示されます。 Azure Database for PostgreSQL では、サーバーごとに複数のデータベースを作成できることに注意してください。

8. **[データベース]** を右クリックし、**[作成]** メニューを選択して **[データベース]** を選択します。

9. **[データベース]** フィールドに任意のデータベース名 (例に示した **mypgsqldb** など) を入力します。

10. データベースの**所有者**をリスト ボックスから選択します。 サーバー管理者ログイン名 (例に示した **mylogin** など) を選択します。

11. **[保存]** を選択して、新しい空のデータベースを作成します。

12. **[ブラウザー]** ウィンドウで、該当するサーバー名の [データベース] 一覧で、作成したデータベースを確認します。

    ![[ブラウザー] ウィンドウ](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
このクイック スタートで作成したリソースは、2 つある方法のうちいずれかでクリーンアップすることができます。 [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除した場合、そのリソース グループに含まれるすべてのリソースが削除対象となります。 他のリソースをそのまま維持する場合は、1 つのサーバー リソースだけを削除してください。

> [!TIP]
> このコレクションの他のクイック スタートは、このクイック スタートに基づいています。 引き続きクイック スタートの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したリソースをポータルで削除してください。

新しく作成したサーバーを含むリソース グループ全体を削除する手順は次のとおりです。
1. ポータルで目的のリソース グループを探します。 左側のメニューで **[リソース グループ]** を選択します。 次に、リソース グループ名 (例に示した **myresourcegroup** など) を選択します。

2. リソース グループ ページで **[削除]** を選択します。 リソース グループ名 (例に示した **myresourcegroup** など) をテキスト ボックスに入力して、削除の確認を行います。 **[削除]**を選択します。

新しく作成したサーバーを削除するには、次の手順に従います。
1. ポータルで目的のサーバーを探します (サーバーをまだ開いていない場合)。 左側のメニューで **[すべてのリソース]** を選択します。 次に、作成したサーバーを検索します。

2. **[概要]** ページで **[削除]** を選択します。

    ![[削除] ボタン](./media/quickstart-create-database-portal/12-delete.png)

3. 削除するサーバーの名前を確認し、影響を受けるデータベースをその下に表示します。 テキスト ボックスにサーバー名 (例に示した **mypgserver-20170401** など) を入力します。 **[削除]**を選択します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)
