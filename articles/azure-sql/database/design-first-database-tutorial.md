---
title: チュートリアル:SSMS を使用して最初のリレーショナル データベースを設計する
description: SQL Server Management Studio を使用して、Azure SQL Database で最初のリレーショナル データベースを設計する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: ae7baeac6cee2a692928642e3e38ce0adad17d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674887"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>チュートリアル:SSMS を使用して Azure SQL Database でリレーショナル データベースを設計する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL Database は、Microsoft Cloud (Azure) のリレーショナルなサービスとしてのデータベース (DBaaS) です。 このチュートリアルでは、Azure Portal および [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用して以下の操作を行う方法を学習します。

> [!div class="checklist"]
>
> - Azure portal を使用してデータベースを作成する*
> - Azure portal を使用してサーバーレベルの IP ファイアウォール規則を設定する
> - SSMS を使用してデータベースに接続する
> - SSMS を使用してテーブルを作成する
> - BCP を使用してデータを一括で読み込む
> - SSMS を使用してデータを照会する

*Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

> [!TIP]
> 次の Microsoft Learn モジュールは、単純なデータベースの作成など、[Azure SQL Database に対してクエリを行う ASP.NET アプリケーションを開発および構成する](/learn/modules/develop-app-that-queries-azure-sql/)方法を無料で学習するのに役立ちます。
> [!NOTE]
> このチュートリアルでは、Azure SQL Database を使用しています。 エラスティック プールでプールされたデータベース、または SQL Managed Instance を使用することもできます。 SQL Managed Instance への接続については、以下の SQL Managed Instance のクイックスタートを参照してください。「[クイック スタート: Azure SQL Managed Instance に接続するように Azure VM を構成する](../managed-instance/connect-vm-instance-configure.md)」および「[クイックスタート: オンプレミスから Azure SQL Managed Instance へのポイント対サイト接続を構成する](../managed-instance/point-to-site-p2s-configure.md)」。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下がインストールされていることを確認してください。

- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (最新バージョン)
- [BCP と SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (最新バージョン)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-blank-database-in-azure-sql-database"></a>Azure SQL Database に空のデータベースを作成する

Azure SQL Database のデータベースは、定義済みの一連のコンピューティング リソースとストレージ リソースを使用して作成されます。 データベースは [Azure リソース グループ](../../active-directory-b2c/overview.md)内に作成され、[論理 SQL サーバー](logical-servers.md)を使用して管理されます。

次の手順に従って、空のデータベースを作成します。

1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。
2. **[新規]** ページで、[Azure Marketplace] セクションで **[データベース]** を、 **[おすすめ]** セクションで **[SQL Database]** をクリックします。

   ![空のデータベースを作成](./media/design-first-database-tutorial/create-empty-database.png)

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

    ![データベース サーバーの作成](./media/design-first-database-tutorial/create-database-server.png)

5. **[選択]** をクリックします。
6. **[価格レベル]** をクリックして、サービス レベル、DTU または仮想コア数、およびストレージの容量を指定します。 DTU または仮想コア数とストレージに関して、サービス レベルごとに利用できるオプションを確認してください。

    サービス レベル、DTU または仮想コアの数、およびストレージの容量を選択したら、 **[適用]** をクリックします。

7. 空のデータベースの **照合順序** を入力します (このチュートリアルでは既定値を使用)。 照合順序の詳細については、「[Collations (照合順序)](/sql/t-sql/statements/collations)」を参照してください。

8. これで **SQL Database** フォームの入力が完了したので、 **[作成]** をクリックして、データベースをプロビジョニングします。 この手順には数分かかることがあります。

9. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

   ![デプロイ中の [通知] メニューのスクリーンショット。](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>サーバーレベルの IP ファイアウォール規則を作成する

Azure SQL Database では、サーバーレベルで IP ファイアウォールが作成されます。 このファイアウォールにより、外部のアプリケーションやツールは、ファイアウォール規則でその IP がファイアウォールの通過を許可されていない限り、サーバーおよびサーバー上のすべてのデータベースに接続できなくなります。 データベースに外部から接続できるようにするには、まず、IP アドレス (または IP アドレス範囲) に対する IP ファイアウォール規則を追加する必要があります。 以下の手順に従って、[サーバーレベルの IP ファイアウォール規則](firewall-configure.md)を作成します。

> [!IMPORTANT]
> Azure SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内からこのサービスに接続しようとしても、ポート 1433 でのアウトバウンド トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、管理者がポート 1433 を開かない限り、データベースに接続することはできません。

1. デプロイが完了したら、Azure portal メニューから **[SQL データベース]** を選択するか、または任意のページから *[SQL データベース]* を検索して選択します。  

1. **[SQL データベース]** ページで *[yourDatabase]* を選択します。 データベースの概要ページが開き、完全修飾 **[サーバー名]** (`contosodatabaseserver01.database.windows.net` など) が表示され、それ以上の構成のためのオプションが提供されます。

   ![サーバー名](./media/design-first-database-tutorial/server-name.png)

1. この完全修飾サーバー名をコピーします。これは、SQL Server Management Studio からお客様のサーバーとデータベースに接続するために使用します。

1. ツール バーで **[Set server firewall]\(サーバー ファイアウォールの設定\)** をクリックします。 サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーレベルの IP ファイアウォール規則](./media/design-first-database-tutorial/server-firewall-rule.png)

1. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスを新しい IP ファイアウォール規則に追加します。 IP ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

1. **[保存]** をクリックします。 サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベル IP のファイアウォール規則が作成されます。

1. **[OK]** をクリックし、 **[ファイアウォール設定]** ページを閉じます。

これで IP アドレスが IP ファイアウォールを通過できるようになりました。 SQL Server Management Studio やその他の任意のツールを使用して、データベースに接続できます。 必ず、お客様が先ほど作成したサーバー管理者アカウントを使用してください。

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL Database IP ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** をクリックします。

## <a name="connect-to-the-database"></a>データベースに接続する

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) を使用して、データベースへの接続を確立します。

1. SQL Server Management Studio を開きます。
2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

   | 設定       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **サーバーの種類** | データベース エンジン | この値は必須です。 |
   | **サーバー名** | 完全修飾サーバー名 | たとえば、*yourserver.database.windows.net* などです。 |
   | **認証** | SQL Server 認証 | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
   | **Login** | サーバー管理者アカウント | サーバーの作成時に指定したアカウントです。 |
   | **パスワード** | サーバー管理者アカウントのパスワード | お客様がサーバーを作成したときに指定したパスワードです。 |

   ![[サーバーに接続]](./media/design-first-database-tutorial/connect.png)

3. **[サーバーへの接続]** ダイアログ ボックスの **[オプション]** をクリックします。 **[データベースへの接続]** セクションに「*yourDatabase*」と入力して、このデータベースに接続します。

    ![サーバー上のデータベースに接続](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. **[Connect]** をクリックします。 SSMS で **オブジェクト エクスプローラー** ウィンドウが開きます。

5. **オブジェクト エクスプローラー** で、**Databases**、*yourDatabase* の順に展開して、サンプル データベース内のオブジェクトを表示します。

   ![データベース オブジェクト](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>データベースのテーブルを作成する

[Transact-SQL](/sql/t-sql/language-reference) を用いた大学の生徒管理システムを構成する、4 つのテーブルのデータベース スキーマを作成します。

- Person
- コース
- 生徒
- クレジット

次の図は、これらのテーブルの相互関係を示しています。 テーブルの一部は、他のテーブル内の列を参照します。 たとえば *Student* テーブルは、*Person* テーブルの *PersonId* 列を参照します。 このチュートリアルのテーブルの相互関係を把握するため、図を詳しく確認します。 効果的なデータベース テーブル作成方法の詳細は、[効果的なデータベース テーブルの作成](/previous-versions/tn-archive/cc505842(v=technet.10))を参照してください。 データ型の選択については、[データ型](/sql/t-sql/data-types/data-types-transact-sql)を参照してください。

> [!NOTE]
> [SQL Server Management Studio のテーブル デザイナー](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools)を使用して、テーブルを作成および設計することも可能です。

![テーブルのリレーションシップ](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. **オブジェクト エクスプローラー** で *yourDatabase* を右クリックし、 **[新しいクエリ]** を選択します。 データベースに接続された空のクエリ ウィンドウが開きます。

2. クエリ ウィンドウで次のクエリを実行し、データベース内の 4 つのテーブルを作成します。

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![テーブルの作成](./media/design-first-database-tutorial/create-tables.png)

3. **オブジェクト エクスプローラー** で、*yourDatabase* の **Tables** ノードを展開すると、お客様が作成したテーブルが表示されます。

   ![作成済み SSMS テーブル](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む

1. お客様の Downloads フォルダーに *sampleData* という名前のフォルダーを作成し、お客様のデータベース用のサンプル データを格納します。

2. 次のリンクを右クリックし、それらを *sampleData* フォルダーに保存します。

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. コマンド プロンプト ウィンドウを開き、*sampleData* フォルダーに移動します。

4. 次のコマンドを実行して、サンプル データをテーブルに挿入します。*server*、*database*、*user*、*password* の各値は、お客様の環境の値に置き換えてください。

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

これで、先ほど作成したテーブルにサンプル データが読み込まれました。

## <a name="query-data"></a>クエリ データ

データベース テーブルから情報を取得するには、次のクエリを実行します。 SQL クエリの記述の詳細は、[SQL クエリの記述](/previous-versions/sql/sql-server-2005/express-administrator/bb264565(v=sql.90))に関するページを参照してください。 最初のクエリでは 4 つのテーブルをすべて結合し、"Dominick Pope" の指導を受けた生徒のうち、成績が 75% を超えている生徒を検索します。 次のクエリでは 4 つのテーブルをすべて結合し、"Noe Coleman" がこれまでに登録したコースを検索します。

1. SQL Server Management Studio のクエリ ウィンドウで、次のクエリを実行します。

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. クエリ ウィンドウで次のクエリを実行します。

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、多数の基本的なデータベース タスクについて学習しました。 以下の方法を学習しました。

> [!div class="checklist"]
>
> - Azure portal を使用してデータベースを作成する*
> - Azure portal を使用してサーバーレベルの IP ファイアウォール規則を設定する
> - SSMS を使用してデータベースに接続する
> - SSMS を使用してテーブルを作成する
> - BCP を使用してデータを一括で読み込む
> - SSMS を使用してデータを照会する

次のチュートリアルでは、Visual Studio と C# を使用したデータベースの設計について学びます。

> [!div class="nextstepaction"]
> [C# と ADO.NET を使用して Azure SQL Database 内でリレーショナル データベースを設計する](design-first-database-csharp-tutorial.md)