---
title: 最初のリレーショナル データベースを設計する - C# - Azure SQL Database | Microsoft Docs
description: ADO.NET と C# を使用して、Azure SQL Database の単一データベースで最初のリレーショナル データベースを設計する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 02/08/2019
ms.openlocfilehash: f6f0160e06ac10a6f1e4e51c93bfa133818117c8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568966"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>チュートリアル:C&#x23; と ADO.NET を使用して Azure SQL Database 内の単一データベースでリレーショナル データベースを設計する

Azure SQL Database は、Microsoft Cloud (Azure) のリレーショナルなサービスとしてのデータベース (DBaaS) です。 このチュートリアルでは、Azure Portal、ADO.NET、および Visual Studio を使用して以下の手順を実行する方法を説明します。

> [!div class="checklist"]
> * Azure portal を使用して単一データベースを作成する*
> * Azure portal を使用してサーバーレベルの IP ファイアウォール規則を設定する
> * ADO.NET と Visual Studio を使用してデータベースに接続する
> * ADO.NET を使用してテーブルを作成する
> * ADO.NET を使用してデータを挿入、更新、削除する
> * ADO.NET を使用してデータのクエリを実行する

*Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

[Visual Studio 2019](https://www.visualstudio.com/downloads/) 以降のインストール。

## <a name="create-a-blank-single-database"></a>空の単一データベースを作成する

Azure SQL Database の単一データベースは、定義済みの一連のコンピューティング リソースとストレージ リソースを使用して作成されます。 データベースは [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内に作成され、[データベース サーバー](sql-database-servers.md)を使用して管理されます。

空の単一データベースを作成するには、次の手順に従います。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
2. **[新規]** ページで、[Azure Marketplace] セクションで **[データベース]** を、 **[おすすめ]** セクションで **[SQL Database]** をクリックします。

   ![空のデータベースを作成](./media/sql-database-design-first-database/create-empty-database.png)

3. 前の画像で示されているように、 **[SQL Database]** のフォームに次の情報を入力します。

    | Setting       | 推奨値 | 説明 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **データベース名** | *yourDatabase* | 有効なデータベース名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
    | **サブスクリプション** | *yourSubscription*  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
    | **リソース グループ** | *yourResourceGroup* | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
    | **[ソースの選択]** | 空のデータベース | 空のデータベースを作成するように指定します。 |

4. **[サーバー]** をクリックして既存のデータベース サーバーを使用するか、新しいデータベース サーバーを作成して構成します。 既存のサーバーを選択するか、 **[新しいサーバーの作成]** をクリックして **[新しいサーバー]** フォームに次の情報を入力します。

    | Setting       | 推奨値 | 説明 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
    | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
    | **パスワード** | 有効なパスワード | パスワードには 8 文字以上が含まれていること、また、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が使用されていることが必要です。 |
    | **Location** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

    ![データベース サーバーの作成](./media/sql-database-design-first-database/create-database-server.png)

5. **[選択]** をクリックします。
6. **[価格レベル]** をクリックして、サービス レベル、DTU または仮想コア数、およびストレージの容量を指定します。 DTU または仮想コア数とストレージに関して、サービス レベルごとに利用できるオプションを確認してください。

    サービス レベル、DTU または仮想コアの数、およびストレージの容量を選択したら、 **[適用]** をクリックします。

7. 空のデータベースの**照合順序**を入力します (このチュートリアルでは既定値を使用)。 照合順序の詳細については、「[Collations (照合順序)](/sql/t-sql/statements/collations)」を参照してください。

8. これで **SQL Database** フォームの入力が完了したので、 **[作成]** をクリックして、単一データベースをプロビジョニングします。 この手順には数分かかることがあります。

9. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

   ![通知](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>サーバーレベルの IP ファイアウォール規則を作成する

SQL Database サービスでは、サーバーレベルで IP ファイアウォールが作成されます。 このファイアウォールにより、外部のアプリケーションやツールは、ファイアウォール規則でその IP がファイアウォールの通過を許可されていない限り、サーバーおよびサーバー上のすべてのデータベースに接続できなくなります。 単一データベースに外部から接続できるようにするには、まず、IP アドレス (または IP アドレス範囲) に対する IP ファイアウォール規則を追加する必要があります。 以下の手順に従って、[SQL Database のサーバーレベルの IP ファイアウォール規則](sql-database-firewall-configure.md)を作成します。

> [!IMPORTANT]
> SQL Database サービスの通信は、ポート 1433 上で行われます。 企業ネットワーク内からこのサービスに接続しようとしても、ポート 1433 でのアウトバウンド トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、管理者がポート 1433 を開かない限り、単一データベースに接続することはできません。

1. デプロイが完了したら、左側のメニューから **[SQL データベース]** をクリックし、**SQL データベース** ページで、*yourDatabase* をクリックします。 お客様のデータベースの概要ページが開くと、完全修飾**サーバー名** (*yourserver.database.windows.net* など) や追加の構成オプションが表示されます。

2. この完全修飾サーバー名をコピーします。これは、SQL Server Management Studio からお客様のサーバーとデータベースに接続するために使用します。

   ![サーバー名](./media/sql-database-design-first-database/server-name.png)

3. ツール バーで **[Set server firewall]\(サーバー ファイアウォールの設定\)** をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーレベルの IP ファイアウォール規則](./media/sql-database-design-first-database/server-firewall-rule.png)

4. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスを新しい IP ファイアウォール規則に追加します。 IP ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

5. **[Save]** をクリックします。 SQL Database サーバー上のポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルの IP ファイアウォール規則が作成されます。

6. **[OK]** をクリックし、 **[ファイアウォール設定]** ページを閉じます。

これで IP アドレスが IP ファイアウォールを通過できるようになりました。 SQL Server Management Studio やその他の任意のツールを使用して、単一データベースに接続できます。 必ず、お客様が先ほど作成したサーバー管理者アカウントを使用してください。

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database IP ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** をクリックします。

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、データベースとテーブルの作成、データベースへの接続、データの読み込み、クエリの実行など、基本的なデータベース タスクについて学習しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * データベースを作成する
> * ファイアウォール規則の設定
> * [Visual Studio と C#](sql-database-connect-query-dotnet-visual-studio.md) を使用して SQL Database に接続する
> * テーブルの作成
> * データの挿入、更新、削除、照会

次のチュートリアルに進み、データ移行について確認してください。

> [!div class="nextstepaction"]
> [DMS を使用して SQL Server を Azure SQL Database にオフラインで移行する](../dms/tutorial-sql-server-to-azure-sql.md)
