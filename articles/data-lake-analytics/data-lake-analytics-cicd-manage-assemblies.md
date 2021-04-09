---
title: CI/CD パイプラインでの U-SQL アセンブリの管理 - Azure Data Lake
description: Azure DevOps を使用して CI/CD パイプラインで U-SQL C# アセンブリを管理するためのベスト プラクティスについて学習します。
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015283"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>CI/CD パイプラインで U-SQL アセンブリを管理するためのベスト プラクティス

この記事では、新しく導入された U-SQL データベース プロジェクトを使用して、U-SQL アセンブリのソース コードを管理する方法について説明します。 また、Azure DevOps を使用して、アセンブリ登録のための継続的インテグレーションおよびデプロイ (CI/CD) パイプラインを設定する方法について説明します。

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>U-SQL データベース プロジェクトを使用してアセンブリのソース コードを管理する

[U-SQL データベース プロジェクト](data-lake-analytics-data-lake-tools-develop-usql-database.md)は、開発者が高速かつ簡単に U-SQL データベースの開発、管理、および配置を行えるようにする Visual Studio のプロジェクトの一種です。 すべての U-SQL データベース オブジェクト (資格情報を除く) は、U-SQL データベース プロジェクトで管理できます。

C# アセンブリ ソース コードとアセンブリ登録 DDL U-SQL スクリプトを管理するには、以下を使用します。

- U-SQL データベース プロジェクト。アセンブリ登録 U-SQL スクリプトを管理します。
- クラス ライブラリ (U-SQL アプリケーション用)。C# のソースコードおよびユーザー定義の演算子、関数、およびアグリゲーター (UDO、UDF、UDAG) の依存関係を管理します。
- U-SQL データベース プロジェクト。クラス ライブラリ プロジェクトを参照します。

U-SQL データベース プロジェクトは、クラス ライブラリ (U-SQL アプリケーション用の) プロジェクトを参照できます。 U-SQL データベースに登録されているアセンブリは、参照される C# ソース コードを使用して、このクラス ライブラリ (U-SQL アプリケーション用の) プロジェクトから作成できます。

次の手順に従ってプロジェクトを作成し、参照を追加します。

1. **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Project]\(プロジェクト\)** の順に選択して、[Class Library (For U-SQL Application)] プロジェクトを作成します。 プロジェクトは **[Azure Data Lake] > [U-SQL]** ノードにあります。

   ![Data Lake Tools for Visual Studio -- C# クラス ライブラリ プロジェクトの作成](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. ユーザー定義の C# コードを [Class Library (For U-SQL Application)] プロジェクトに追加します。

1. **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Project]\(プロジェクト\)** の順に選択して、U-SQL プロジェクトを作成します。 プロジェクトは **[Azure Data Lake]**  >  **[U-SQL]** ノードにあります。

   ![Data Lake Tools for Visual Studio -- U-SQL データベース プロジェクトの作成](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. U-SQL データベース プロジェクトのために、C# クラス ライブラリ プロジェクトの参照を追加します。

   ![Data Lake Tools for Visual Studio - 参照の追加](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio - U-SQL データベース プロジェクト参照の追加](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. プロジェクトを右クリックし、 **[新しい項目の追加]** を選択して、U-SQL データベース プロジェクトにアセンブリ スクリプトを作成します。

   ![Data Lake Tools for Visual Studio -- アセンブリ スクリプトの追加](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. アセンブリ デザイン ビューでアセンブリ スクリプトを開きます。 **[参照からアセンブリを作成]** ドロップダウン メニューから参照先アセンブリを選択します。

   ![Data Lake Tools for Visual Studio -- 参照からのアセンブリの作成](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. **[マネージドの依存関係]** と **[追加ファイル]** が存在する場合は追加します。 追加ファイルを追加した場合、ローカル コンピューターと後のビルド コンピューターで確実にアセンブリを検索できるようにするため、ツールでは相対パスが使用されます。

下部にあるエディター ウィンドウの **\@_DeployTempDirectory** は、ビルド出力フォルダーを指すツールの定義済み変数です。 ビルド出力フォルダーには、アセンブリ名を名前とするサブフォルダーがすべてのアセンブリにあり、 すべての DLL および追加ファイルがそのサブフォルダーに格納されます。

## <a name="build-a-u-sql-database-project"></a>U-SQL データベース プロジェクトの作成

U-SQL データベース プロジェクトのビルド出力は、U-SQL データベース配置パッケージです。 名前に `.usqldbpack` というサフィックスが付いています。 `.usqldbpack` パッケージは、DDL フォルダー内の単一の U-SQL スクリプトにすべての DDL ステートメントを含む .zip ファイルです。 アセンブリのすべてのビルド済み .dll ファイルや追加ファイルは Temp フォルダー内にあります。

## <a name="deploy-a-u-sql-database"></a>U-SQL データベースの配置

`.usqldbpack` パッケージは、ローカル アカウントまたは Azure Data Lake Analytics アカウントに配置できます。 Visual Studio または配置 SDK を使用します。

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Visual Studio での U-SQL データベースの配置

Visual Studio で、U-SQL データベース プロジェクトまたは `.usqldbpack` パッケージを使用して U-SQL データベースを配置できます。

#### <a name="deploy-by-using-a-u-sql-database-project"></a>U-SQL データベース プロジェクトを使用した配置

1. U-SQL データベース プロジェクトを右クリックし、 **[展開]** を選択します。

1. **U-SQL データベースの配置** ウィザードで、データベースを配置する **[ADLA アカウント]** を選択します。 ローカル アカウントと ADLA アカウントの両方がサポートされています。

1. **[データベース ソース]** が自動的に入力されます。 これは、プロジェクトのビルド出力フォルダー内の .usqldbpack パッケージを指します。

1. データベースを作成するには、 **[データベース名]** に名前を入力します。 ターゲットの Azure Data Lake Analytics アカウントに同じ名前のデータベースが存在する場合、データベースは再作成されず、データベース プロジェクトに定義されているすべてのオブジェクトが作成されます。

1. U-SQL データベースを配置するには、 **[送信]** を選択します。 アセンブリや追加ファイルなどのすべてのリソースがアップロードされます。 すべての DDL ステートメントを含む U-SQL ジョブが送信されます。

   ![Data Lake Tools for Visual Studio -- U-SQL データベース プロジェクトの配置](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools for Visual Studio -- U-SQL データベース プロジェクトの配置ウィザード](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Azure DevOps での U-SQL データベースの配置

`PackageDeploymentTool.exe` は、U-SQL データベースを配置する際に役立つプログラミングとコマンド ライン インターフェイスを提供します。 この SDK は [U-SQL SDK Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)に含まれており、`build/runtime/PackageDeploymentTool.exe` の場所にあります。

Azure DevOps では、コマンド ライン タスクとこの SDK を使用して、U-SQL データベース更新のためのオートメーション パイプラインを設定できます。 [SDK と、U-SQL データベースを配置する CI/CD パイプラインの設定方法の詳細については、こちらを参照してください](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines)。

## <a name="next-steps"></a>次のステップ

- [Azure Data Lake Analytics の CI/CD パイプラインをセットアップする](data-lake-analytics-cicd-overview.md)
- [Azure Data Lake Analytics コードのテスト](data-lake-analytics-cicd-test.md)
- [ローカル コンピューターで U-SQL スクリプトを実行する](data-lake-analytics-data-lake-tools-local-run.md)
