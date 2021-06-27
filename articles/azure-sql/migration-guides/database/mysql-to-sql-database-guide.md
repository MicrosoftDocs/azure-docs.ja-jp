---
title: 'MySQL から Azure SQL Database: 移行ガイド'
description: このガイドでは、SQL Server Migration Assistant for MySQL (SSMA for MySQL) を使用して、MySQL データベースを Azure SQL データベースに移行する方法について説明します。
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.date: 03/19/2021
ms.openlocfilehash: 32c56df5bafa9439fc559edf137c1080920cfb32
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284376"
---
# <a name="migration-guide-mysql-to-azure-sql-database"></a>移行ガイド: MySQL から Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

このガイドでは、[SQL Server Migration](https://azure.microsoft.com/en-us/migration/sql-server/) Assistant for MySQL (SSMA for MySQL) を使用して、MySQL データベースを Azure SQL Database に[移行する方法](https://azure.microsoft.com/migration/migration-journey)について説明します。 

その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://docs.microsoft.com/data-migration)」を参照してください。 

## <a name="prerequisites"></a>前提条件

MySQL データベースから SQL データベースへの移行を始める前に、次のことを行います。

- ソース環境がサポートされていることを確認する。 現時点では、MySQL 5.6 および 5.7 がサポートされています。 
- [SQL Server Migration Assistant for MySQL](https://www.microsoft.com/download/details.aspx?id=54257) をダウンロードしてインストールします。
- ソースとターゲットの両方にアクセスするための接続と十分なアクセス許可があることを確認します。

## <a name="pre-migration"></a>移行前 

前提条件を満たしたら、環境のトポロジを検出し、[Azure Cloud 移行](https://azure.microsoft.com/migration)の実現可能性を評価する準備が整いました。

### <a name="assess"></a>アクセス 

SQL Server Migration Assistant (SSMA) for MySQL を使用すると、データベース オブジェクトとデータを確認し、データベースの移行を評価できます。 

評価を作成するには、以下を実行します。

1. [SSMA for MySQL](https://www.microsoft.com/download/details.aspx?id=54257) を開きます。 
1. **[ファイル]** を選択してから、 **[新しいプロジェクト]** を選択します。 
1. **[新しいプロジェクト]** ペインで、プロジェクトの名前と場所を入力した後、 **[移行先]** ドロップダウン リストで **[Azure SQL Database]** を選択します。 
1. **[OK]** を選択します。

   ![移行プロジェクトの名前、場所、ターゲットを入力するための [新しいプロジェクト] ペインのスクリーンショット。](./media/mysql-to-sql-database-guide/new-project.png)

1. **[MySQL への接続]** タブを選択し、MySQL サーバーの接続に関する詳細を指定します。 

   ![ソースへの接続を指定するための [MySQL への接続] ペインのスクリーンショット。](./media/mysql-to-sql-database-guide/connect-to-mysql.png)

1. **[MySQL Metadata Explorer]\(MySQL メタデータ エクスプローラー\)** ペインで、MySQL スキーマを右クリックして、 **[Create Report]\(レポートの作成\)** を選択します。 または、右上にある **[レポートの作成]** タブを選択することもできます。

   ![SSMA for MySQL での [Create Report]\(レポートの作成\) リンクのスクリーンショット。](./media/mysql-to-sql-database-guide/create-report.png)

1. HTML レポートをレビューして、変換の統計情報、エラー、警告を把握します。 これを分析して、変換の問題点と解決策を把握します。 
   また、そのレポートを Excel で開いて、MySQL オブジェクトのインベントリを取得し、スキーマの変換を実行するために必要な作業を確認することもできます。 レポートの既定の場所は、SSMAProjects 内のレポート フォルダーです。 次に例を示します。 
   
   `drive:\Users\<username>\Documents\SSMAProjects\MySQLMigration\report\report_2016_11_12T02_47_55\`
 
   ![SSMA の変換レポートの例のスクリーンショット。](./media/mysql-to-sql-database-guide/conversion-report.png)

### <a name="validate-the-data-types"></a>データ型を検証する

既定のデータ型マッピングを検証し、必要に応じて要件に基づいて変更します。 そのためには次を行います。 

1. **[ツール]** を選択し、 **[プロジェクトの設定]** を選択します。  
1. **[Type Mappings]\(型のマッピング\)** タブを選択します。 

   ![SSMA for MySQL の[Type Mapping]\(型のマッピング\) ペインのスクリーンショット。](./media/mysql-to-sql-database-guide/type-mappings.png)

1. **[MySQL Metadata Explorer]\(MySQL メタデータ エクスプローラー\)** ペインでテーブル名を選択すると、各テーブルの型のマッピングを変更できます。 

### <a name="convert-the-schema"></a>スキーマの変換 

スキーマを変換するには、次の手順を実行します。 

1. (省略可能) 動的または特殊なクエリを変換するには、ノードを右クリックし、 **[ステートメントの追加]** を選択します。 

1. **[Connect to Azure SQL Database]\(Azure SQL Database への接続\)** タブを選択し、次の操作を行います。

   a. SQL データベースへの接続に関する詳細を入力します。  
   b. ドロップダウンの一覧で、ターゲットの SQL データベースを選択します。 または、新しい名前を指定することもできます。その場合、データベースはターゲット サーバー上に作成されます。  
   c. 認証の詳細を指定します。  
   d. **[接続]** を選択します。

   ![SSMA for MySQL の [Connect to Azure SQL Database]\(Azure SQL Database への接続\) ペインのスクリーンショット。](./media/mysql-to-sql-database-guide/connect-to-sqldb.png)
 
1. 処理するスキーマを右クリックして、 **[スキーマの変換]** を選択します。 または、右上にある **[Convert schema]\(スキーマの変換\)** タブを選択することもできます。

   ![[MySQL Metadata Explorer]\(MySQL メタデータ エクスプローラー\) ペインの [Convert Schema]\(スキーマの変換\) コマンドのスクリーンショット。](./media/mysql-to-sql-database-guide/convert-schema.png)

1. 変換が完了したら、変換されたオブジェクトを確認して元のオブジェクトを比較し、可能性のある問題を特定して、推奨事項に基づいてそれらに対処します。 

   ![変換されたオブジェクトと元のオブジェクトの比較を示すスクリーンショット。](./media/mysql-to-sql-database-guide/table-comparison.png)

   変換された Transact-SQL テキストを元のコードと比較し、推奨事項を確認します。

   ![変換されたクエリとソース コードとの比較を示すスクリーンショット。](./media/mysql-to-sql-database-guide/procedure-comparison.png)

1. **[出力]** ペインで **[結果の確認]** を選択し、 **[エラー一覧]** ペインでエラーを確認します。 
1. オフライン スキーマ修復の演習のために、プロジェクトをローカルに保存します。 それを行うには、 **[ファイル]**  >  **[プロジェクトの保存]** の順に選択します。 これにより、スキーマを SQL データベースに発行する前にソースとターゲットのスキーマをオフラインで評価し、修復を実行する機会が与えられます。

   次に示すように、変換されたプロシージャを元のプロシージャと比較します。 

   ![変換されたプロシージャと元のプロシージャの比較を示すスクリーンショット。](./media/mysql-to-sql-database-guide/procedure-comparison.png)


## <a name="migrate-the-databases"></a>データベースを移行する 

データベースを評価して不整合に対処した後は、移行プロセスを実行できます。 移行には、スキーマの発行とデータの移行という 2 つのステップが含まれます。 

スキーマを発行し、データを移行するには、次の手順を実行します。 

1. スキーマを発行します。 **[Azure SQL Database メタデータ エクスプローラー]** ペインで、データベースを右クリックして **[データベースと同期する]** を選択します。 この操作により、MySQL のスキーマがお使いの SQL データベースに発行されます。

   ![データベースのマッピングを確認するための [Synchronize with Database]\(データベースと同期する\) ペインのスクリーンショット。](./media/mysql-to-sql-database-guide/synchronize-database-review.png)

1. データを移行します。 **[MySQL Metadata Explorer]\(MySQL メタデータ エクスプローラー\)** ペインで、移行する MySQL のスキーマを右クリックして、 **[Migrate Data]\(データの移行\)** を選択します。 または、右上にある **[データの移行]** タブを選択することもできます。

   データベース全体のデータを移行するには、データベース名の横にあるチェック ボックスをオンにします。 個々のテーブルからデータを移行するには、データベースを展開し、 **[テーブル]** を展開して、テーブルの横にあるチェック ボックスをオンにします。 個々のテーブルのデータを除外するには、このチェック ボックスをオフにします。

   ![[MySQL Metadata Explorer]\(MySQL メタデータ エクスプローラー\) ペインの [Migrate Data]\(データの移行\) コマンドのスクリーンショット。](./media/mysql-to-sql-database-guide/migrate-data.png)

1. 移行が完了したら、**データ移行レポート** を表示します。
   
   ![データ移行レポートのスクリーンショット。](./media/mysql-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) を使用してお使いの SQL データベースに接続し、データとスキーマを確認して移行を検証します。

   ![SQL Server Management Studio のスクリーンショット。](./media/mysql-to-sql-database-guide/validate-in-ssms.png)

## <a name="post-migration"></a>移行後 

*移行* 段階が正常に完了したら、移行後の一連のタスクを完了し、すべてが可能な限り円滑かつ効率的に機能していることを確認する必要があります。

### <a name="remediate-applications"></a>アプリケーションを修復する

データがターゲット環境に移行された後、以前にソースを使用していたすべてのアプリケーションは、ターゲットの使用を開始する必要があります。 これを実現するには、場合によってはアプリケーションの変更が必要になります。

### <a name="perform-tests"></a>テストを実行する

データベース移行に対するテスト アプローチは、次のアクティビティで構成されています。

1. **検証テストを作成する**: データベースの移行をテストするには、SQL クエリを使用する必要があります。 ソースとターゲットの両方のデータベースに対して実行する検証クエリを作成する必要があります。 検証クエリには、定義したスコープが含まれている必要があります。

1. **テスト環境を設定する**: このテスト環境には、ソース データベースとターゲット データベースのコピーを含める必要があります。 必ずテスト環境を分離してください。

1. **検証テストを実行する**: ソースとターゲットに対して検証テストを実行してから、結果を分析します。

1. **パフォーマンス テストを実行する**: ソースとターゲットに対してパフォーマンス テストを実行し、結果を分析して比較します。

### <a name="optimize"></a>最適化

移行後フェーズは、データの精度の問題の調整、完全性の確認、およびワークロードのパフォーマンスの問題への対処のために非常に重要です。

これらの問題と、それらを軽減するための具体的な手順の詳細については、「[移行後の検証および最適化ガイド](/sql/relational-databases/post-migration-validation-and-optimization-guide)」を参照してください。

## <a name="migration-assets"></a>移行資産

この移行シナリオの詳細については、次のリソースを参照してください。 これは、実世界の移行プロジェクトの取り組みをサポートするために開発されたものです。

| タイトル | 説明 |
| --- | --- |
| [データ ワークロード評価モデルとツール](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | 特定のワークロードに対して、推奨される "最適な" ターゲット プラットフォーム、クラウドの準備状況、アプリケーションとデータベースの修復レベルを提供します。 シンプルなワンクリックの計算とレポート生成機能があり、自動化された均一なターゲット プラットフォームの決定プロセスが用意されているので、大規模な資産評価の促進に役立ちます。 |

データ SQL エンジニアリング チームが、これらのリソースを開発しました。 このチームの主要な作業は、Microsoft の Azure データ プラットフォームへのデータ プラットフォーム移行プロジェクトの複雑な近代化を容易にし、迅速に進めることです。

## <a name="next-steps"></a>次のステップ 

- ワークロードを Azure に移行することで実現できるコスト削減額を見積もるには、「[Azure 総保有コスト (TCO) 計算ツール](https://aka.ms/azure-tco)」を参照してください。

- さまざまなデータベースおよびデータ移行シナリオや特殊なタスクを支援するために使用できる Microsoft とサードパーティのサービスとツールのマトリックスについては、[データ移行のためのサービスとツール](../../../dms/dms-tools-matrix.md)に関するページを参照してください。

- その他の移行ガイドについては、「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」を参照してください。 

- 移行に関する動画については、[移行工程の概要と、移行と評価のための推奨されるツールとサービス](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)に関する記事を参照してください。

- [クラウド移行リソース](https://azure.microsoft.com/migration/resources/)の詳細については、「[クラウド移行ソリューション](https://azure.microsoft.com/migration)」を参照してください。

