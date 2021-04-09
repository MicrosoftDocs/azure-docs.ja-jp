---
title: C# での初めてのリレーショナル データベースを設計する
description: ADO.NET と C# を使用して、Azure SQL Database で最初のリレーショナル データベースを設計する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 4b3235f457f1c6475c18045886c49d3dd2ca2242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92671176"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>チュートリアル:C&#x23; と ADO.NET を使用して Azure SQL Database 内でリレーショナル データベースを設計する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database は、Microsoft Cloud (Azure) のリレーショナルなサービスとしてのデータベース (DBaaS) です。 このチュートリアルでは、Azure Portal、ADO.NET、および Visual Studio を使用して以下の手順を実行する方法を説明します。

> [!div class="checklist"]
>
> * Azure portal を使用してデータベースを作成する
> * Azure portal を使用してサーバーレベルの IP ファイアウォール規則を設定する
> * ADO.NET と Visual Studio を使用してデータベースに接続する
> * ADO.NET を使用してテーブルを作成する
> * ADO.NET を使用してデータを挿入、更新、削除する
> * ADO.NET を使用してデータのクエリを実行する

*Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

> [!TIP]
> 次の Microsoft Learn モジュールは、単純なデータベースの作成など、[Azure SQL Database に対してクエリを行う ASP.NET アプリケーションを開発および構成する](/learn/modules/develop-app-that-queries-azure-sql/)方法を無料で学習するのに役立ちます。

## <a name="prerequisites"></a>前提条件

[Visual Studio 2019](https://www.visualstudio.com/downloads/) 以降のインストール。

## <a name="create-a-blank-database-in-azure-sql-database"></a>Azure SQL Database に空のデータベースを作成する

Azure SQL Database のデータベースは、定義済みの一連のコンピューティング リソースとストレージ リソースを使用して作成されます。 データベースは [Azure リソース グループ](../../active-directory-b2c/overview.md)内に作成され、[論理 SQL サーバー](logical-servers.md)を使用して管理されます。

次の手順に従って、空のデータベースを作成します。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[新規]** ページで、[Azure Marketplace] セクションで **[データベース]** を、 **[おすすめ]** セクションで **[SQL Database]** をクリックします。

   ![空のデータベースを作成](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. 前の画像で示されているように、 **[SQL Database]** のフォームに次の情報を入力します。

    | 設定       | 推奨値 | 説明 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **データベース名** | *yourDatabase* | 有効なデータベース名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
    | **サブスクリプション** | *yourSubscription*  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
    | **リソース グループ** | *yourResourceGroup* | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 |
    | **ソースの選択** | 空のデータベース | 空のデータベースを作成するように指定します。 |

4. **[サーバー]** をクリックして、既存のサーバーを使用するか、新しいサーバーを作成して構成します。 既存のサーバーを選択するか、 **[新しいサーバーの作成]** をクリックして **[新しいサーバー]** フォームに次の情報を入力します。

    | 設定       | 推奨値 | 説明 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 |
    | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
    | **パスワード** | 有効なパスワード | パスワードには 8 文字以上が含まれていること、また、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が使用されていることが必要です。 |
    | **場所** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

    ![データベース サーバーの作成](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. **[選択]** をクリックします。
6. **[価格レベル]** をクリックして、サービス レベル、DTU または仮想コア数、およびストレージの容量を指定します。 DTU または仮想コア数とストレージに関して、サービス レベルごとに利用できるオプションを確認してください。

    サービス レベル、DTU または仮想コアの数、およびストレージの容量を選択したら、 **[適用]** をクリックします。

7. 空のデータベースの **照合順序** を入力します (このチュートリアルでは既定値を使用)。 照合順序の詳細については、「[Collations (照合順序)](/sql/t-sql/statements/collations)」を参照してください。

8. これで **SQL Database** フォームの入力が完了したので、 **[作成]** をクリックして、データベースをプロビジョニングします。 この手順には数分かかることがあります。

9. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

   ![スクリーンショットは、Azure portal の、"デプロイは進行中です" と表示された [通知] を示しています。](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>サーバーレベルの IP ファイアウォール規則を作成する

SQL Database では、サーバーレベルで IP ファイアウォールが作成されます。 このファイアウォールにより、外部のアプリケーションやツールは、ファイアウォール規則でその IP がファイアウォールの通過を許可されていない限り、サーバーおよびサーバー上のすべてのデータベースに接続できなくなります。 データベースに外部から接続できるようにするには、まず、IP アドレス (または IP アドレス範囲) に対する IP ファイアウォール規則を追加する必要があります。 以下の手順に従って、[サーバーレベルの IP ファイアウォール規則](firewall-configure.md)を作成します。

> [!IMPORTANT]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内からこのサービスに接続しようとしても、ポート 1433 でのアウトバウンド トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、管理者がポート 1433 を開かない限り、データベースに接続することはできません。

1. デプロイが完了したら、左側のメニューで **[SQL データベース]** をクリックし、**SQL データベース** ページで、*yourDatabase* をクリックします。 お客様のデータベースの概要ページが開くと、完全修飾 **サーバー名** (*yourserver.database.windows.net* など) や追加の構成オプションが表示されます。

2. この完全修飾サーバー名をコピーします。これは、SQL Server Management Studio からお客様のサーバーとデータベースに接続するために使用します。

   ![サーバー名](./media/design-first-database-csharp-tutorial/server-name.png)

3. ツール バーで **[Set server firewall]\(サーバー ファイアウォールの設定\)** をクリックします。 サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーレベルの IP ファイアウォール規則](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスを新しい IP ファイアウォール規則に追加します。 IP ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

5. **[保存]** をクリックします。 サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベル IP のファイアウォール規則が作成されます。

6. **[OK]** をクリックし、 **[ファイアウォール設定]** ページを閉じます。

これで IP アドレスが IP ファイアウォールを通過できるようになりました。 SQL Server Management Studio やその他の任意のツールを使用して、データベースに接続できます。 必ず、お客様が先ほど作成したサーバー管理者アカウントを使用してください。

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database IP ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対するアクセスを無効にするには、このページの **[オフ]** をクリックします。

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、データベースとテーブルの作成、データベースへの接続、データの読み込み、クエリの実行など、基本的なデータベース タスクについて学習しました。 以下の方法を学習しました。

> [!div class="checklist"]
>
> * Azure portal を使用してデータベースを作成する
> * Azure portal を使用してサーバーレベルの IP ファイアウォール規則を設定する
> * ADO.NET と Visual Studio を使用してデータベースに接続する
> * ADO.NET を使用してテーブルを作成する
> * ADO.NET を使用してデータを挿入、更新、削除する
> * ADO.NET を使用してデータのクエリを実行する

次のチュートリアルに進み、データ移行について確認してください。

> [!div class="nextstepaction"]
> [DMS を使用して SQL Server を Azure SQL Database にオフラインで移行する](../../dms/tutorial-sql-server-to-azure-sql.md)