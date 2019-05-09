---
title: SSIS を使用した Azure Data Lake Analytics U-SQL ジョブのスケジュール設定
description: SQL Server Integration Services を使用して U-SQL ジョブのスケジュールを設定する方法について説明します。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 6894486118f69e682353142be04821e1d28440e5
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523173"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>SQL Server Integration Services (SSIS) を使用した U-SQL ジョブのスケジュール設定

このドキュメントでは、SQL Server Integration Service (SSIS) を使用した U-SQL ジョブの調整および作成方法について説明します。 

## <a name="prerequisites"></a>前提条件

[Integration Services 用の Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) では、[Azure Data Lake Analytics タスク](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)と、Azure Data Lake Analytics サービスへの接続に役立つ[ Azure Data Lake Analytics 接続マネージャー](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)が提供されています。 このタスクを使用するには、以下がインストールされていることを確認します。

- [Visual Studio の SQL Server Data Tools (SSDT) をダウンロードし、インストールする](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Integration Services (SSIS) 用の Azure Feature Pack のインストール](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics タスク

Azure Data Lake Analytics タスクにより、ユーザーは Azure Data Lake Analytics アカウントに U-SQL ジョブを送信することができます。 

Azure Data Lake Analytics タスクの構成方法については、[こちら](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)を参照してください。

![SSIS での Azure Data Lake Analytics タスク](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

U-SQL スクリプトは、SSIS の組み込み関数とタスクを使用して、さまざまな場所から取得できます。次のシナリオでは、さまざまなユーザーのケースに対して、U-SQL スクリプトを構成する方法を示します。

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>シナリオ 1: インライン スクリプト呼び出し tvf とストアド プロシージャを使用する

Azure Data Lake Analytics タスク エディターで、**SourceType** を **DirectInput** として構成し、U-SQL ステートメントを **USQLStatement** に配置します。

容易なメンテナンスとコード管理のため、たとえば、短い U-SQL スクリプトのみをインライン スクリプトとして含めると、U-SQL データベース内で既存のテーブル値関数とストアド プロシージャを呼び出すことができます。 

![SSIS タスクのインライン U-SQL スクリプトを編集する](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

関連記事:[ストアド プロシージャにパラメーターを渡す方法](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>シナリオ 2: Azure Data Lake Store 内の U-SQL ファイルを使用する

Azure Feature Pack の **Azure Data Lake Store ファイル システム タスク**を使用すると、Azure Data Lake Store 内の U-SQL ファイルも使用できます。 このアプローチを使用すると、クラウドに格納されているスクリプトを使用することができます。

次の手順に従って、Azure Data Lake Store ファイル システム タスクと Azure Data Lake Analytics タスク間の接続を設定します。

### <a name="set-task-control-flow"></a>タスク制御フローを設定する

SSIS パッケージのデザイン ビューで、**Azure Data Lake Store ファイル システム タスク**、**Foreach ループ コンテナー**、およびその Foreach ループ コンテナー内の **Azure Data Lake Analytics タスク**を追加します。 Azure Data Lake Store ファイル システム タスクは、お使いの ADLS アカウントで U-SQL ファイルを一時フォルダーにダウンロードするのに役立ちます。 Foreach ループ コンテナーと Azure Data Lake Analytics タスクは、一時フォルダー配下のすべての U-SQL ファイルを U-SQL ジョブとして Azure Data Lake Analytics アカウントに送信するのに役立ちます。

![Azure Data Lake Store 内の U-SQL ファイルを使用する](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Azure Data Lake Store ファイル システム タスクを構成する

1. **[操作]** を **[CopyFromADLS]** に設定します。
2. **[AzureDataLakeConnection]** を設定します。Azure Data Lake Store 接続マネージャーの詳細については、[こちら](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017)を参照してください。
3. **[AzureDataLakeDirectory]** を設定します。 U-SQL スクリプトが格納されているフォルダーをポイントします。 Azure Data Lake Store アカウントのルート フォルダーからの相対パスを使用します。
4. **[展開先]** を、ダウンロードした U-SQL スクリプトをキャッシュするフォルダーに設定します。 このフォルダー パスは、U-SQL ジョブを送信するために Foreach ループ コンテナーで使用されます。 

![Azure Data Lake Store ファイル システム タスクを構成する](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

Azure Data Lake Store ファイル システム タスクの詳細については、[こちら](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017)を参照してください。

### <a name="configure-foreach-loop-container"></a>Foreach ループ コンテナーを構成する

1. **[コレクション]** ページで、**[列挙子]** を **[Foreach File 列挙子]** に設定します。

2. **[列挙子の構成]** グループの下の **[フォルダー]** を、ダウンロードした U-SQL スクリプトを含む一時フォルダーに設定します。

3. **[列挙子の構成]** グループの下の **[ファイル]** を `*.usql` に設定して、末尾が `.usql` のファイルのみをループ コンテナーがキャッチできるようします。

    ![Foreach ループ コンテナーを構成する](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. **[変数のマッピング]** ページで、ユーザー定義変数を追加して、各 U-SQL ファイルのファイル名を取得します。 ファイル名を取得するには、**[インデックス]** を 0 に設定します。 この例では、`User::FileName` という名前の変数を定義します。 この変数は、U-SQL スクリプト ファイルの接続を動的に取得し、Azure Data Lake Analytics タスクの U-SQL ジョブ名を設定するために使用されます。

    ![ファイル名を取得するように Foreach ループ コンテナーを構成する](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Azure Data Lake Analytics タスクを構成する 

1. **[SourceType]** を **[FileConnection]** に設定します。

2. **[FileConnection]** を Foreach ループ コンテナーから返されるファイル オブジェクトをポイントするファイル接続に設定します。
    
    このファイル接続を作成するには:

   1. [FileConnection] の設定で **[\<新しい接続...>]** を選択します。
   2. **[使用法の種類]** を **[既存のファイル]** に設定し、**[ファイル]** を既存の任意のファイルのファイル パスに設定します。

       ![Foreach ループ コンテナーを構成する](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. **接続マネージャー** ビューで、今作成したファイル接続を右クリックし、**[プロパティ]** を選択します。

   4. **[プロパティ]** ウィンドウで、**[式]** を展開し、**[ConnectionString]** を Foreach ループ コンテナーで定義されている変数 (`@[User::FileName]` など) に設定します。

       ![Foreach ループ コンテナーを構成する](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. **[AzureDataLakeAnalyticsConnection]** を、ジョブの送信先となる Azure Data Lake Analytics アカウントに設定します。 Azure Data Lake Analytics 接続マネージャーの詳細については、[こちら](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017)を参照してください。

4. その他のジョブ構成を設定します。 [詳細情報](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017)

5. **[式]** を使用して、U-SQL ジョブの名前を動的に設定します。

    1. **[式]** ページで、**[JobName]** に新しい式のキーと値のペアを追加します。
    2. JobName の値を Foreach ループ コンテナーで定義されている変数 (`@[User::FileName]` など) に設定します。
    
        ![U-SQL ジョブ名に SSIS 式を構成する](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>シナリオ 3: Azure Blob Storage 内の U-SQL ファイルを使用する

Azure Feature Pack の **Azure BLOB のダウンロード タスク**を使用すると、Azure Blob Storage 内の U-SQL ファイルを使用することができます。 このアプローチを使用すると、クラウドでスクリプトを使用することができます。

手順は「[シナリオ 2: Azure Data Lake Store 内の U-SQL ファイルを使用する](#scenario-2-use-u-sql-files-in-azure-data-lake-store)」と同じです。 Azure Data Lake Store ファイル システム タスクを Azure BLOB のダウンロード タスクに変更します。 Azure BLOB のダウンロード タスクの詳細については、[こちら](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017)を参照してください。

制御フローは次のようになります。

![Azure Data Lake Store 内の U-SQL ファイルを使用する](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>シナリオ 4: ローカル コンピューター上の U-SQL ファイルを使用する

クラウドに格納されている U-SQL ファイルを使用する以外に、自分のローカル コンピューター上のファイルまたは SSIS パッケージでデプロイされたファイルも使用することができます。

1. SSIS プロジェクトで **[接続マネージャー]** を右クリックして、**[新しい接続マネージャー]** を選択します。

2. **[ファイル]** の種類を選択し、**[追加]** をクリックします。

3. **[使用法の種類]** を **[既存のファイル]** に設定し、**[ファイル]** をローカル コンピューター上のファイルに設定します。

    ![ローカル ファイルへのファイル接続を追加する](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. **Azure Data Lake Analytics** タスクを追加して、以下を行います。
    1. **[SourceType]** を **[FileConnection]** に設定します。
    2. **[FileConnection]** を今作成したファイル接続に設定します。

5. Azure Data Lake Analytics タスクのその他の構成を完了します。

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>シナリオ 5: SSIS 変数で U-SQL ステートメントを使用する

U-SQL ステートメントを動的に生成する必要がある場合があります。 **SSIS 変数**と **SSIS 式**、およびスクリプト タスクのようなその他の SSIS タスクを使用すると、U-SQL ステートメントを動的に生成することができます。

1. 最上位のメニュー項目 **[SSIS] > [変数]** から、変数ツール ウィンドウを開きます。

2. SSIS 変数を追加して値を直接設定するか、**式**を使用して値を生成します。

3. **Azure Data Lake Analytics タスク**を追加して、以下を行います。
    1. **[SourceType]** を**変数**に設定します。
    2. **[SourceVariable]** を今作成した SSIS 変数に設定します。

4. Azure Data Lake Analytics タスクのその他の構成を完了します。

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>シナリオ 6: U-SQL スクリプトにパラメーターを渡す

U-SQL スクリプト内の U-SQL 変数の値を動的に設定する必要がある場合があります。 このシナリオでは、Azure Data Lake Analytics タスクの**パラメーター マッピング**機能が役立ちます。 通常は、2 つの一般的なユーザー ケースがあります。

- 現在の日時に基づいて入力と出力のファイル パス変数を動的に設定します。
- ストアド プロシージャのパラメーターを設定します。

U-SQL スクリプトのパラメーターの設定方法については、[こちら](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration)を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure での SSIS パッケージの実行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Integration Services (SSIS) 用の Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Azure Data Factory を使用した U-SQL ジョブのスケジュール設定](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

