---
title: クイック スタート:サーバーを作成する - Azure portal - Azure Database for MySQL
description: この記事では、Azure Portal を使用して、5 分程度でサンプルの Azure Database for MySQL サーバーを作成する手順を説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: b8f3a8e06bb27051417205c4dc3141948960bb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98567799"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>クイックスタート: Azure Portal を使用した Azure Database for MySQL サーバーの作成

Azure Database for MySQL は、高可用な MySQL データベースをクラウドで実行し、管理し、スケーリングするために使用されるマネージド サービスです。 このクイックスタートでは、Azure portal を使用して Azure Database for MySQL 単一サーバーを作成する方法を説明します。 また、サーバーに接続する方法も説明します。

## <a name="prerequisites"></a>前提条件
Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="create-an-azure-database-for-mysql-single-server"></a>Azure Database for MySQL 単一サーバーを作成する
1. MySQL 単一サーバー データベースを作成するには、[Azure portal](https://portal.azure.com/) に移動します。 **[Azure Database for MySQL]** を検索して選択します。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL を見つける":::

1. **[追加]** を選択します。

2. **[MySQL デプロイ オプションの Azure データベースの選択]** ページで、 **[単一サーバー]** を選択します。
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="[単一サーバー] オプションを示すスクリーンショット。":::

3. 新しい単一サーバーの基本設定を入力します。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="[MySQL サーバーの作成] ページを示すスクリーンショット。":::

   **設定** | **推奨値** | **説明**
   ---|---|---
   サブスクリプション | 該当するサブスクリプション | 目的の Azure サブスクリプションを選択します。
   Resource group | **myresourcegroup** | 新しいリソース グループ、またはサブスクリプションの既存のものを入力します。
   サーバー名 | **mydemoserver** | 一意の名前を入力します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 から 63 文字にする必要があります。
   データ ソース |**なし** | **[なし]** を選択し、最初から新しいサーバーを作成します 既存のサーバーの geo バックアップから復元している場合にのみ、 **[バックアップ]** を選択します。
   場所 |目的の場所 | 一覧から場所を選択します。
   Version | 最新のメジャー バージョン| 最新のメジャー バージョンを使用します。 [サポートされているすべてのバージョン](concepts-supported-versions.md)に関するページを参照してください。
   コンピューティングとストレージ | 既定値を使用します| 既定の価格レベルは、**4 つの仮想コア** と **100 GB** のストレージを備えた **General Purpose** です。 バックアップの保有期間は、 **[7 日間]** に設定され、 **[地理冗長]** バックアップ オプションが指定されています。<br/>[価格](https://azure.microsoft.com/pricing/details/mysql/)に関するページを確認し、必要に応じて既定値を更新します。
   管理者ユーザー名 | **mydemoadmin** | サーバー管理者のユーザー名を入力します。 管理者のユーザー名として、**azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** を使用することはできません。
   Password | パスワード | サーバー管理者ユーザーの新しいパスワード。 パスワードは、英大文字または小文字、数字、英数字以外の文字 (!、$、#、% など) の組み合わせを含む、8 から 128 文字までの長さにする必要があります。
  

   > [!NOTE]
   > 低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。 Basic 価格レベルで作成されたサーバーは、後から General Purpose またはメモリ最適化にスケーリングできないことに注意してください。

4. **[確認および作成]** を選択して、サーバーをプロビジョニングします。

5. ポータル ページに "**デプロイが完了しました**" と表示されるまで待ちます。 **[リソースに移動]** を選択して、新しく作成されたサーバー ページにアクセスします。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="&quot;デプロイが完了しました&quot; メッセージを示すクリーンショット。":::

[問題が発生していますか。お知らせください。](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

既定では、新しいサーバーはファイアウォールで保護されています。 接続するには、次の手順に従って、自分の IP でアクセスできるようにする必要があります。

1. サーバー リソースの左側のペインで、 **[接続のセキュリティ]** に移動します。 対象のリソースを見つける方法がわからない場合は、[リソースを開く方法](../azure-resource-manager/management/manage-resources-portal.md#open-resources)に関するセクションを参照してください。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="[接続のセキュリティ] > [ファイアウォール規則] ページ示すスクリーンショット。":::

2. **[現在のクライアント IP アドレスを追加する]** を選択し、 **[保存]** を選択します。

   > [!NOTE]
   > 接続の問題が発生しないように、Azure Database for MySQL で使用されるポート 3306 経由のアウトバウンド トラフィックがネットワークで許可されているかどうかを確認します。 

さらに IP を追加するか、IP 範囲を指定することで、それらの IP から対象のサーバーに接続できるようになります。 詳細については、[Azure Database for MySQL サーバーのファイアウォール規則を管理する方法](./concepts-firewall-rules.md)に関するページを参照してください。


[問題が発生していますか。お知らせください](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>mysql.exe を使用してサーバーに接続する
ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) のどちらかを使用できます。 このクイックスタートでは、[Azure Cloud Shell](../cloud-shell/overview.md) で mysql.exe を使用して、サーバーに接続します。


1. 次のスクリーンショットに示すように、ツール バーの最初のボタンを選択して、ポータルで Azure Cloud Shell を開きます。 スクリーンショットに示されているように、 **[概要]** セクションで、新しいサーバーのサーバー名、サーバー管理者名、サブスクリプションを記録します。

    > [!NOTE]
    > 初めて Cloud Shell を開いた場合は、リソース グループとストレージ アカウントを作成するように求めるメッセージが表示されます。 これは 1 回限りのステップです。 すべてのセッションに自動的にアタッチされます。

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Azure portal での Cloud Shell を示すスクリーンショット。":::
2. Azure Cloud Shell ターミナルで次のコマンドを実行します。 ここで示す値は、実際のサーバー名と管理者ユーザー名に置き換えてください。 Azure Database for MySQL の場合、次に示すように、管理者ユーザー名に `@\<servername>` を追加する必要があります。 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Cloud Shell ターミナルは次のようになります。

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. 同じ Azure Cloud Shell ターミナルで、`guest` という名前のデータベースを作成します。
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. `guest` データベースに切り替えます。
      ```
      mysql> USE guest;
      Database changed
      ```
5. 「`quit`」と入力して **Enter** キーを押し、mysql を終了します。

[問題が発生していますか。お知らせください。](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>リソースをクリーンアップする
これでリソース グループに Azure Database for MySQL サーバーが作成されました。  これらのリソースが今後不要である思われる場合は、リソース グループを削除してリソースを削除することも、単にこの MySQL サーバーを削除することもできます。 リソース グループを削除するには、次の手順のようにします。
1. Azure portal で、「**リソース グループ**」を検索して選択します。
2. リソース グループの一覧で、リソース グループの名前を選択します。
3. 対象のリソース グループの **[概要]** ページで、 **[リソース グループの削除]** を選択します。
4. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

サーバーを削除するには、次に示すように、サーバーの **[概要]** ページで **[削除]** を選択します。
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="サーバーの [概要] ページの [削除] ボタンを示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
>[MySQL を使用して Windows で PHP アプリを構築する](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[MySQL を使用して Linux で PHP アプリを構築する](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[お探しのものが見つかりませんか。お知らせください。](https://aka.ms/mysql-doc-feedback)
