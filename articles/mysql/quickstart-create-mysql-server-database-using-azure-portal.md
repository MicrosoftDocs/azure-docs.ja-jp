---
title: クイック スタート:サーバーを作成する - Azure portal - Azure Database for MySQL
description: この記事では、Azure Portal を使用して、5 分程度ですばやくサンプルの Azure Database for MySQL サーバーを作成する手順を説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: 840e7769e8a242314b10562684c942a2468ea5ad
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705148"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>クイック スタート:Azure portal で Azure Database for MySQL サーバーを作成する

Azure Database for MySQL は、高可用性 MySQL データベースをクラウドで実行、管理、スケーリングするための管理されたサービスです。 このクイック スタートでは、Azure Portal を使用して 5 分で Azure Database for MySQL サーバーを作成する方法を説明します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする
Web ブラウザーを開き、[Azure Portal](https://portal.azure.com/) に移動します。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
Azure Database for MySQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使って作成します。 サーバーは、[Azure リソース グループ](../azure-resource-manager/management/overview.md)内に作成します。

Azure Database for MySQL サーバーを作成するには、次の手順に従います。

1. ポータルの左上隅にある **[リソースの作成]** (+) を選択します。

2. **[データベース]**  >  **[Azure Database for MySQL]** の順に選択します。 検索ボックスに「**MySQL**」と入力してサービスを検索することもできます。

  
>[!div class="mx-imgBorder"]
> ![Azure Database for MySQL オプション](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 新しいサーバーの詳細フォームには次の情報を入力してください。
    
>[!div class="mx-imgBorder"]
> ![サーバー フォームを作成する](./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png)

**設定** | **推奨値** | **フィールドの説明** 
---|---|---
サブスクリプション | 該当するサブスクリプション | サーバーに使用する Azure サブスクリプションを選択します。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択してください。
Resource group | *myresourcegroup* | 新規または既存のリソース グループの名前を入力します。 リソース グループを使用すると、1 つのプロジェクトに属する依存関係を整理できます。
サーバー名 | 一意のサーバー名 | Azure Database for MySQL サーバーを識別する一意の名前を入力します。 たとえば、"mysqldbserver" などとします。サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
データ ソース |*なし* | *[なし]* を選択し、最初から新しいサーバーを作成します (既存の Azure Database for MySQL サーバーの geo バックアップからサーバーを作成している場合は、 *[バックアップ]* を選択します)。
サーバー管理者のログイン | myadmin | サーバー管理者のユーザー名を入力します。 管理者のユーザー名として、**azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** を使用することはできません。
Password | *<任意>* | サーバー管理者アカウントの新しいパスワードを入力します。 パスワードは、英大文字または小文字、数字、英数字以外の文字 (!、$、#、% など) を組み合わせて、8 から 128 文字までの長さにする必要があります。
[パスワードの確認入力] | *<任意>*| 管理者アカウントのパスワードを確認します。
場所 | *ユーザーに最も近いリージョン*| ユーザーや他の Azure アプリケーションに最も近い場所を選択します。
Version | *最新のメジャー バージョン*| 最新メジャー バージョン (別のバージョンを指定する特定の要件がある場合を除く)。
コンピューティングとストレージ | **汎用**、**Gen 5**、**2 仮想コア**、**5 GB**、**7 日**、**地理冗長** |新しいサーバーのコンピューティング、ストレージ、およびバックアップ構成。 **[サーバーの構成]** を選択します。 次に、適切な価格レベルを選択します。詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/mysql/)を参照してください。 サーバー バックアップを geo 冗長ストレージで有効にするには、 **[バックアップ冗長オプション]** から **[地理冗長]** を選択します。 **[OK]** を選択します。

   > [!NOTE]
   > 低負荷なコンピューティングと I/O がワークロードに適している場合は、Basic 価格レベルの使用を検討してください。 Basic 価格レベルで作成されたサーバーは後で General Purpose またはメモリ最適化にスケーリングできないことに注意してください。 

4. **[確認および作成]** を選択して、サーバーをプロビジョニングします。 プロビジョニングには、最大 20 分かかる場合があります。
   
5. デプロイ プロセスを監視するには、ツール バーの **[通知]** (ベル アイコン) を選択します。
   
既定では、データベース **information_schema**、**mysql**、**performance_schema**、および **sys** は、ご利用のサーバーに作成されます。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成
既定では、作成されたサーバーはファイアウォールで保護され、パブリックにアクセスすることはできません。 ご使用の IP にアクセス権を付与するには、Azure portal で対象のサーバー リソースに移動し、そのサーバー リソースの左側のメニューから **[接続のセキュリティ]** を選択します。 対象のリソースを見つける方法がわからない場合は、[リソースを開く方法](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)に関するセクションを参照してください。

>[!div class="mx-imgBorder"]
> ![[接続のセキュリティ] - [ファイアウォール規則]](./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png)
   
次に、 **[現在のクライアント IP アドレスを追加]** を選択し、 **[保存]** を選択します。 IP を追加するか、IP 範囲を指定することで、それらの IP から対象のサーバーに接続できるようになります。 詳細については、[Azure Database for MySQL サーバーのファイアウォール規則を管理する方法](./concepts-firewall-rules.md)に関するページを参照してください

> [!NOTE]
> 接続の問題が発生しないように、Azure Database for MySQL で使用されるポート 3306 経由のアウトバウンド トラフィックがネットワークで許可されているかどうかを確認します。  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>mysql コマンドライン クライアントを使用して Azure Database for MySQL サーバーに接続する
ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) を選択できます。 このクイックスタートでは、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) で **mysql.exe** を実行して、サーバーに接続します。

1. 左上の強調表示されているアイコンをクリックして、ポータルで Azure Cloud Shell を起動します。 次の画像に示すように、 **[概要]** セクションから、新しく作成されたサーバーのサーバー名、サーバー管理者のログイン名、パスワード、およびサブスクリプションをメモします。

    >[!NOTE]
    >初めて Cloud Shell を起動する場合は、リソース グループとストレージ アカウントを作成するように求めるメッセージが表示されます。 これは 1 回限りの作業であり、それ以降はすべてのセッションで自動的に接続されます。 

   >[!div class="mx-imgBorder"]
   > ![ポータルでの Cloud Shell の完全なビュー](./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png)
2. Azure Cloud Shell ターミナルで次のコマンドを実行します。 値は実際のサーバー名と管理者ユーザーのログイン名に置き換えてください。 Azure Database for MySQL の管理者ユーザー名には、次に示すように "@\<servername>" が必要です  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Cloud Shell ターミナルでのエクスペリエンスは次のようになります
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
3. 同じ Azure Cloud Shell ターミナルで、データベース **guest** を作成します 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. データベース **guest** に変更します
  ```
  mysql> USE guest;
  Database changed 
  ```
5. 「```quit```」と入力し、Enter キーを押して、mysql を終了します。   

## <a name="clean-up-resources"></a>リソースをクリーンアップする
リソース グループに Azure Database for MySQL サーバーが正常に作成されました。  これらのリソースが今後不要である思われる場合は、リソース グループを削除してリソースを削除することも、単にこの MySQL サーバーを削除することもできます。 リソース グループを削除するには、次の手順を実行します。
1. Azure portal で、「**リソース グループ**」を検索して選択します。 
2. リソース グループの一覧で、リソース グループの名前を選択します。
3. リソース グループの [概要] ページで、 **[リソース グループの削除]** を選択します。
4. 確認のダイアログ ボックスでリソース グループの名前を入力し、 **[削除]** を選択します。

サーバーを削除するには、次に示すように、サーバーの **[概要]** ページで **[削除]** ボタンをクリックします。
> [!div class="mx-imgBorder"]
> ![リソースを削除する](media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
>[MySQL を使用する PHP アプリを Windows 上で構築する](../app-service/app-service-web-tutorial-php-mysql.md)
>[MySQL を使用する PHP アプリを Linux 上で構築する](../app-service/containers/tutorial-php-mysql-app.md)
>[MySQL を使用する Java ベースの Spring アプリを構築する](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)
