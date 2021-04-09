---
title: U-SQL データベース プロジェクトを開発する - Azure Data Lake
description: Azure Data Lake Tools for Visual Studio を使用して、U-SQL データベースを開発する方法について説明します。
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015249"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>U-SQL データベース プロジェクトを使った Azure Data Lake 用 U-SQL データベースの開発

U-SQL データベースは、非構造化データに対する構造化ビューと、テーブル内で管理された構造化データに対する構造化ビューを提供します。 また、構造化データとカスタム コードを整理するための一般的なメタデータ カタログ システムを提供します。 データベースは、これらの関連オブジェクトをグループ化する概念です。

詳細については、[U-SQL データベースおよびデータ定義言語 (DDL)](/u-sql/data-definition-language-ddl-statements) を参照してください。

U-SQL データベース プロジェクトは、開発者が高速かつ簡単に U-SQL データベースの開発、管理、および配置を行えるようにする Visual Studio のプロジェクトの一種です。

## <a name="create-a-u-sql-database-project"></a>U-SQL データベース プロジェクトの作成

バージョン 2.3.3000.0 以降の Azure Data Lake Tools for Visual Studio には、U-SQL データベース プロジェクトと呼ばれる新しいプロジェクト テンプレートが追加されています。 U-SQL プロジェクトを作成するには、 **[File]\(ファイル\) > [New]\(新規\) > [Project]\(プロジェクト\)** の順に選択します。 U-SQL データベース プロジェクトは、 **[Azure Data Lake] > [U-SQL] ノード** にあります。

![Data Lake Tools for Visual Studio における U-SQL データベース プロジェクトの作成](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>データベース プロジェクトを使用した U-SQL データベース オブジェクトの開発

U-SQL データベース プロジェクトを右クリックします。 **[追加] > [新しい項目]** の順に選択します。 サポートされているすべての種類のオブジェクトが **[新しい項目の追加]** ウィザードに表示されます。

テーブル値関数などの非アセンブリ オブジェクトの場合、新しい項目の追加後に新しい U-SQL スクリプトが作成されます。 エディターで、そのオブジェクトの DDL ステートメントの開発を始めることができます。

アセンブリ オブジェクトの場合、アセンブリの登録と DLL ファイルなどの追加ファイルの配置に役立つ、わかりやすい UI エディターがツールによって提供されます。 次の手順は、アセンブリ オブジェクトの定義を U-SQL データベース プロジェクトに追加する方法を示したものです。

1. UDO/UDAG/UDF を含む C# プロジェクトへの参照を U-SQL データベース プロジェクトに追加します。

   ![Data Lake Tools for Visual Studio - 参照の追加](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio - U-SQL データベース プロジェクト参照の追加](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. アセンブリ デザイン ビューで、 **[Create assembly from reference]\(参照からアセンブリを作成\)** ボックスの一覧から参照先アセンブリを選択します。

   ![Data Lake Tools for Visual Studio -- 参照からのアセンブリの作成](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. **[マネージドの依存関係]** と **[追加ファイル]** が存在する場合は追加します。 追加ファイルを追加する場合、ローカル コンピューターと後のビルド コンピューターの両方で確実にアセンブリを検索できるようにするため、ツールは相対パスを使用します。

@_DeployTempDirectory は、ビルド出力フォルダーを指すツールの定義済み変数です。 ビルド出力フォルダーには、アセンブリ名を名前とするサブフォルダーがすべてのアセンブリにあり、 すべての DLL および追加ファイルがそのサブフォルダーに格納されます。

## <a name="build-a-u-sql-database-project"></a>U-SQL データベース プロジェクトの作成

U-SQL データベース プロジェクトのビルド出力は、名前に `.usqldbpack` というサフィックスのついた U-SQL データベース展開パッケージです。 `.usqldbpack` パッケージは .zip ファイルで、**DDL** フォルダー内の単一の U-SQL スクリプトにはすべての DDL ステートメントが、**Temp** フォルダーにはアセンブリのすべての DLL と追加ファイルが含まれています。

詳細については、[MSBuild コマンド ラインと Azure DevOps Services ビルド タスクを使用して U-SQL データベース プロジェクトをビルドする方法](data-lake-analytics-cicd-overview.md)に関するページをご覧ください。

## <a name="deploy-a-u-sql-database"></a>U-SQL データベースの配置

.usqldbpack パッケージは、ローカル アカウントと Azure Data Lake Analytics アカウントのどちらにでも、Visual Studio またはデプロイ SDK を使用して配置できます。

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Visual Studio での U-SQL データベースの配置

Visual Studio で、U-SQL データベース プロジェクトまたは .usqldbpack パッケージを使用して U-SQL データベースを配置できます。

#### <a name="deploy-through-a-u-sql-database-project"></a>U-SQL データベース プロジェクトによる配置

1. U-SQL データベース プロジェクトを右クリックし、 **[展開]** を選択します。

1. **U-SQL データベースの配置** ウィザードで、データベースを配置する **[ADLA account]\(ADLA アカウント\)** を選択します。 ローカル アカウントと ADLA アカウントの両方がサポートされています。

1. **[Database Source]\(データベース ソース\)** は、プロジェクトのビルド出力フォルダー内の .usqldbpack パッケージを指すように自動的に設定されます。

1. データベースを作成するには、 **[データベース名]** に名前を入力します。 ターゲットの Azure Data Lake Analytics アカウントに同じ名前のデータベースが存在する場合、データベースは再作成されず、データベース プロジェクトに定義されているすべてのオブジェクトが作成されます。

1. U-SQL データベースを配置するには、 **[送信]** を選択します。 すべてのリソース (アセンブリおよび追加ファイル) がアップロードされ、すべての DDL ステートメントを含む U-SQL ジョブが送信されます。

   ![Data Lake Tools for Visual Studio -- U-SQL データベース プロジェクトの配置](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools for Visual Studio -- U-SQL データベース プロジェクトの配置ウィザード](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>U-SQL データベース展開パッケージによる配置

1. **[サーバー エクスプローラー]** を開きます。 データベースの配置先となる **Azure Data Lake Analytics アカウント** を展開します。

1. **U-SQL データベース** を右クリックし、 **[データベースの配置]** を選択します。

1. **[Database Source] (データベース ソース)** に U-SQL データベース展開パッケージ (.usqldbpack ファイル) のパスを設定します。

1. データベースを作成するには、 **[データベース名]** に名前を入力します。 ターゲットの Azure Data Lake Analytics アカウントに同じ名前のデータベースが存在する場合、データベースは再作成されず、データベース プロジェクトに定義されているすべてのオブジェクトが作成されます。

   ![Data Lake Tools for Visual Studio -- U-SQL データベース パッケージの配置](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![Data Lake Tools for Visual Studio -- U-SQL データベース パッケージの配置ウィザード](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>SDK を使用した U-SQL データベースの配置

`PackageDeploymentTool.exe` は、U-SQL データベースを配置する際に役立つプログラミングとコマンド ライン インターフェイスを提供します。 この SDK は [U-SQL SDK Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)に含まれており、`build/runtime/PackageDeploymentTool.exe` の場所にあります。

[SDK と、U-SQL データベースを配置する CI/CD パイプラインの設定方法の詳細については、こちらを参照してください](data-lake-analytics-cicd-overview.md)。

## <a name="reference-a-u-sql-database-project"></a>U-SQL データベース プロジェクトの参照

U-SQL プロジェクトから、U-SQL データベース プロジェクトを参照できます。 この参照は、2 つのワークロードに影響します。

- *プロジェクトのビルド*: U-SQL スクリプトをビルドする前に、参照先のデータベース環境が設定されます。
- *(local-project) アカウントに対するローカル実行*: U-SQL スクリプトを実行する前に、参照先のデータベース環境が (local-project) アカウントに配置されます。 [ローカル実行と、(local-machine) アカウントと (local-project) アカウントの違いについては、こちらを参照してください](data-lake-analytics-data-lake-tools-local-run.md)。

### <a name="how-to-add-a-u-sql-database-reference"></a>U-SQL データベース参照の追加方法

1. **ソリューション エクスプローラー** で U-SQL プロジェクトを右クリックし、 **[U-SQL データベース参照の追加]** を選択します。

    ![Data Lake Tools for Visual Studio -- データベース プロジェクト参照の追加](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. 現在のソリューションの U-SQL データベース プロジェクトまたは U-SQL データベース パッケージ ファイルからのデータベース参照を構成します。

3. データベースの名前を指定します。

    ![Data Lake Tools for Visual Studio におけるデータベース プロジェクト参照の追加ウィザード](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>次のステップ

- [Azure Data Lake Analytics の CI/CD パイプラインをセットアップする方法](data-lake-analytics-cicd-overview.md)
- [Azure Data Lake Analytics コードをテストする方法](data-lake-analytics-cicd-test.md)
- [ローカル コンピューターで U-SQL スクリプトを実行する](data-lake-analytics-data-lake-tools-local-run.md)
