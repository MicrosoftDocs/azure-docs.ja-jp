---
title: SSIS パッケージの実行アクティビティを使用した SSIS パッケージの実行
description: この記事では、SSIS パッケージの実行アクティビティを使用して、SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: 6027c2d94535ca2ef5c41e7027fe070c6ccb21a0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926489"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory の SSIS パッケージの実行アクティビティを使用して SSIS パッケージを実行する
この記事では、SSIS パッケージの実行アクティビティを使用して、SQL Server Integration Services (SSIS) パッケージを Azure Data Factory パイプラインで実行する方法を説明します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS 統合ランタイム (IR) がない場合は、[「チュートリアル: Azure-SSIS 統合ランタイムのプロビジョニング](tutorial-create-azure-ssis-runtime-portal.md)」の手順に従って作成します。

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal でパッケージを実行する
このセクションでは、データ ファクトリのユーザー インターフェイス (UI) またはアプリを使用して、SSIS パッケージを実行する SSIS パッケージの実行アクティビティを含む Data Factory パイプラインを作成します。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する
この手順では、Data Factory の UI またはアプリを使用してパイプラインを作成します。 パイプラインに SSIS パッケージの実行アクティビティを追加し、SSIS パッケージを実行するように構成します。 

1. Azure portal の Data Factory の概要ページまたはホームページで、 **[作成と監視]** タイルを選択して Data Factory の UI またはアプリを別のタブで開始します。 

   ![Data Factory ホーム ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **[Let's get started]\(始めましょう\)** ページで **[Create pipeline]\(パイプラインの作成\)** を選択します。 

   ![開始ページ](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. **[アクティビティ]** ツールボックスで **[全般]** を展開します。 次に、**SSIS パッケージの実行**アクティビティをパイプライン デザイナー画面にドラッグします。 

   ![SSIS パッケージの実行アクティビティをデザイナー画面にドラッグする](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. SSIS パッケージの実行アクティビティの **[全般]** タブで、アクティビティの名前と説明を入力します。 オプションの **[タイムアウト]** と **[再試行]** の値を設定します。

   ![[一般] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. SSIS パッケージの実行アクティビティの **[設定]** タブで、パッケージを実行する Azure-SSIS IR を選択します。 パッケージでデータ ストア (SQL サーバー、オンプレミスのファイル共有、Azure Files など) へのアクセスに Windows 認証が使用されている場合は、 **[Windows 認証]** チェック ボックスをオンにします。 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、パッケージ実行資格情報の値を入力します。 

    または、お使いの Azure キー コンテナーに格納されているシークレットを値として使用することもできます。 それを行うには、関連する資格情報の横にある **[Azure Key Vault]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、資格情報の値に対するシークレットの名前またはバージョンを選択します。

    キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<Key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。 パッケージで 32 ビット ランタイムを実行する必要がある場合は、 **[32 ビット ランタイム]** チェック ボックスをオンにします。

   **[パッケージの場所]** では、 **[SSISDB]** 、 **[ファイル システム (パッケージ)]** 、 **[ファイル システム (プロジェクト)]** 、または **[Embedded package]\(埋め込みパッケージ\)** を選択します。 パッケージの場所として **[SSISDB]** を選択する場合 (Azure-SSIS IR が Azure SQL Database サーバーまたはマネージド インスタンスによってホストされている SSIS カタログ (SSISDB) を使用してプロビジョニングされている場合は自動的に選択されます)、SSISDB にデプロイされている実行対象パッケージを指定します。 

    Azure-SSIS IR の実行中に **[Manual entries]\(手動入力\)** チェック ボックスがオフの場合は、既存のフォルダー、プロジェクト、/パッケージ、または環境を SSISDB から参照します。 **[最新の情報に更新]** を選択して、SSISDB から新しく追加したフォルダー、プロジェクト、パッケージ、または環境を取り込み、それらを参照して選択できるようにします。 パッケージを実行するための環境を参照または選択するには、プロジェクトを事前に構成して、SSISDB の同じフォルダーからの参照としてこれらの環境を追加する必要があります。 詳細については、[SSIS 環境の作成とマップ](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)に関するページを参照してください。

   **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 

   ![[設定] タブでプロパティを設定する - 自動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Azure-SSIS IR が実行されていない場合、または **[Manual entries]\(手動入力\)** チェック ボックスがオンになっている場合は、`<folder name>/<project name>/<package name>.dtsx` および `<folder name>/<environment name>` の形式で SSISDB からパッケージと環境のパスを直接入力します。

   ![[設定] タブでプロパティを設定する - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   パッケージの場所として **[ファイル システム (パッケージ)]** を選択する場合は (Azure-SSIS IR が SSISDB なしでプロビジョニングされた場合は自動的に選択されます)、 **[パッケージのパス]** ボックスにパッケージ ファイル (`.dtsx`) の汎用名前付け規則 (UNC) のパスを指定して、実行するパッケージを指定します。 たとえば、Azure Files にパッケージを保存する場合、そのパッケージのパスは `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` です。 
   
   別のファイルでパッケージを構成する場合は、 **[構成パス]** ボックスに構成ファイルへの UNC パス (`.dtsConfig`) も指定する必要があります。 たとえば、構成を Azure Files に保存する場合、その構成のパスは `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` です。

   ![[設定] タブでプロパティを設定する - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   パッケージの場所として **[ファイル システム (プロジェクト)]** を選択する場合は、 **[プロジェクトのパス]** ボックスにプロジェクト ファイル (`.ispac`) の UNC パスを、 **[パッケージ名]** ボックスにプロジェクトのパッケージ ファイル (`.dtsx`) を指定して、実行するパッケージを指定します。 たとえば、Azure Files にプロジェクトを保存する場合、そのプロジェクトのパスは `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` です。

   ![[設定] タブでプロパティを設定する - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   次に、プロジェクト、パッケージ、または構成ファイルにアクセスするための資格情報を指定します。 以前にパッケージ実行の資格情報値を入力した場合は (前述を参照)、 **[Same as package execution credentials]\(パッケージ実行の資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、パッケージ アクセス資格情報の値を入力します。 たとえば、プロジェクト、パッケージ、または構成を Azure Files に保存する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 

   または、お使いのキー コンテナーに格納されているシークレットを値として使用することもできます。 これらの資格情報は、パッケージ実行タスク (いずれも固有のパスまたは同じプロジェクト、パッケージに指定されたものを含む構成) でパッケージおよび子パッケージへのアクセスに使用されます。 

   パッケージの場所として **[Embedded package]\(埋め込みパッケージ\)** を選択した場合は、パッケージをドラッグ アンド ドロップして実行するか、ファイル フォルダーから指定されたボックスに**アップロード**します。 パッケージは自動的に圧縮され、アクティビティ ペイロードに埋め込まれます。 埋め込みが完了すると、パッケージは後で編集用に**ダウンロード**できます。 また、埋め込みパッケージを複数のアクティビティで使用できるパイプライン パラメーターに割り当てることによって**パラメーター化する**ことで、パイプライン ペイロードのサイズを最適化することもできます。 埋め込みパッケージがすべて暗号化されておらず、その内部でパッケージ実行タスクの使用が検出された場合は、 **[パッケージ実行タスク]** チェックボックスが自動的に選択され、ファイルシステム参照を含む関連する子パッケージが自動的に追加され、それらも埋め込まれます。 パッケージ実行タスクの使用を検出できない場合は、 **[パッケージ実行タスク]** チェックボックスを手動で選択し、ファイルシステム参照を含む関連する子パッケージを 1 つずつ追加して、それらも埋め込むようにする必要があります。 子パッケージが SQL Server 参照を使用する場合は、SQL Server が Azure-SSIS IR によってアクセス可能であることを確認してください。  子パッケージのためのプロジェクト参照の使用は現在サポートされていません。
   
   ![[設定] タブでプロパティを設定する - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   SQL Server Data Tools を使用してパッケージを作成するときに **EncryptAllWithPassword** または **EncryptSensitiveWithPassword** 保護レベルを使用した場合は、 **[暗号化用パスワード]** ボックスにパスワードの値を入力します。 または、キー コンテナーに格納されているシークレットを値として使用することもできます (前述を参照)。 **EncryptSensitiveWithUserKey** 保護レベルを使用した場合は、構成ファイルか、 **[SSIS パラメータ]** 、 **[接続マネージャー]** 、または **[プロパティのオーバーライド]** タブに、機密性の高い値を再入力します (後述を参照)。 

   **EncryptAllWithUserKey** 保護レベルを使用した場合は、サポートされません。 SQL Server Data Tools または `dtutil` コマンドライン ユーティリティを使用して、別の保護レベルを使用するようにパッケージを再構成する必要があります。 
   
   **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 パッケージに指定できる標準のログ プロバイダーを使用する以外に、パッケージの実行をログに記録する場合は、 **[ログ パス]** ボックスで UNC パスを指定してログ フォルダーを指定します。 たとえば、ログを Azure Files に保存する場合、ログ記録のパスは `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` です。 サブフォルダーは、個々のパッケージ実行ごとにこのパス内に作成され、SSIS パッケージの実行アクティビティの実行 ID に基づいて名前が付けられます。このログ ファイルは 5 分ごとに生成されます。 
   
   最後に、ログ フォルダーにアクセスするための資格情報を指定します。 以前にパッケージ アクセスの資格情報値を入力した場合は (前述を参照)、 **[Same as package access credentials]\(パッケージ アクセスの資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、ログイン アクセス資格情報の値を入力します。 たとえば、Azure Files にログを格納する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 

    または、お使いのキー コンテナーに格納されているシークレットを値として使用することもできます。 これらの資格情報はログの格納に使用されます。 
   
   前述のすべての UNC パスでは、完全修飾ファイル名を 260 文字未満にする必要があります。 ディレクトリ名は 248 文字未満でなければなりません。

1. SSIS パッケージの実行アクティビティの **[SSIS パラメーター]** タブで、Azure-SSIS IR の実行中に パッケージの場所として **[SSISDB]** が選択され、 **[設定]** タブの **[Manual entries]\(手動入力\)** チェック ボックスがオフの場合は、値を割り当てるパラメーターとして、SSISDB から選択したプロジェクト/パッケージ内の既存の SSIS パラメーターが表示されます。 それ以外の場合は、値を 1 つずつ入力して手動で値を割り当てることができます。 パッケージの実行が成功するためには、それらが存在し、正しく入力されていることを確認します。 
   
   SQL Server Data Tools でパッケージを作成するときに **EncryptSensitiveWithUserKey** 保護レベルを使用し、パッケージの場所として **[ファイル システム (パッケージ)]** または **[ファイル システム (プロジェクト)]** を選択する場合は、構成ファイルまたはこのタブで機密性の高いパラメーターを再入力して値を割り当てる必要もあります。 
   
   パラメーターに値を割り当てるときに、式、関数、Data Factory システム変数、および Data Factory パイプライン パラメーターまたは変数を使用して動的コンテンツを追加できます。 または、お使いのキー コンテナーに格納されているシークレットを値として使用することもできます。

   ![[SSIS パラメーター] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. SSIS パッケージの実行アクティビティの **[接続マネージャー]** タブで、Azure-SSIS IR の実行中にパッケージの場所として **[SSISDB]** が選択され、 **[設定]** タブの **[Manual entries]\(手動入力\)** チェック ボックスがオフの場合は、プロパティに値を割り当てる接続マネージャーとして、SSISDB から選択したプロジェクト/パッケージ内の既存の接続マネージャーが表示されます。 それ以外の場合は、値を 1 つずつ入力して手動でプロパティに値を割り当てることができます。 パッケージの実行が成功するためには、それらが存在し、正しく入力されていることを確認します。 
   
   SQL Server Data Tools でパッケージを作成するときに **EncryptSensitiveWithUserKey** 保護レベルを使用し、パッケージの場所として **[ファイル システム (パッケージ)]** または **[ファイル システム (プロジェクト)]** 選択されている場合は、構成ファイルまたはこのタブで機密性の高い接続マネージャーのプロパティを再入力して値を割り当てる必要があります。 
   
   接続マネージャーのプロパティに値を割り当てるときに、式、関数、Data Factory システム変数、および Data Factory パイプライン パラメーターまたは変数を使用して動的コンテンツを追加できます。 または、お使いのキー コンテナーに格納されているシークレットを値として使用することもできます。

   ![[接続マネージャー] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. SSIS パッケージの実行アクティビティの **[プロパティのオーバーライド]** タブで、選択したパッケージの既存のプロパティのパスを 1 つずつ入力して、手動でそれらに値を割り当てます。 パッケージの実行が成功するためには、それらが存在し、正しく入力されていることを確認します。 たとえば、ユーザー変数の値をオーバーライドするには、`\Package.Variables[User::<variable name>].Value` の形式でパスを入力します。 
   
   SQL Server Data Tools でパッケージを作成するときに **EncryptSensitiveWithUserKey** 保護レベルを使用し、パッケージの場所として **[ファイル システム (パッケージ)]** または **[ファイル システム (プロジェクト)]** を選択する場合は、構成ファイルまたはこのタブで機密性の高いプロパティを再入力して値を割り当てる必要もあります。 
   
   プロパティに値を割り当てるときに、式、関数、Data Factory システム変数、および Data Factory パイプライン パラメーターまたは変数を使用して動的コンテンツを追加できます。

   ![[プロパティのオーバーライド] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   構成ファイルおよび **[SSIS パラメーター]** タブで割り当てられた値は、 **[接続マネージャー]** タブまたは **[プロパティ オーバーライド]** タブを使用してオーバーライドできます。 **[接続マネージャー]** タブで割り当てられた値は、 **[プロパティ オーバーライド]** タブでオーバーライドすることもできます。

1. パイプラインの構成を検証するために、ツール バーの **[検証]** を選択します。 **[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じるには、 **[>>]** を選択します。

1. Data Factory にパイプラインを発行するには、 **[Publish All]\(すべて発行\)** を選択します。 

### <a name="run-the-pipeline"></a>パイプラインを実行する
この手順では、パイプラインの実行をトリガーします。 

1. パイプラインの実行をトリガーするには、ツール バーの **[トリガー]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 

   ![[Trigger Now]\(今すぐトリガー\)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。 

### <a name="monitor-the-pipeline"></a>パイプラインの監視

1. 左側で **[監視]** タブに切り替えます。 パイプラインの実行とその状態が、その他の情報 (**実行開始**時刻など) と共に表示されます。 表示を更新するには、 **[最新の情報に更新]** を選択します。

   ![パイプライン実行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプラインに 1 つしかアクティビティがないので、アクティビティの実行が 1 つだけ表示されます。 SSIS パッケージの実行アクティビティです。

   ![アクティビティの実行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. パッケージが実行されたことを確認するには、次のクエリを SQL サーバーの SSISDB データベースに対して実行します。 

   ```sql
   select * from catalog.executions
   ```

   ![パッケージの実行を確認する](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. パイプラインのアクティビティ実行の出力から SSISDB 実行 ID を取得し、その ID を使用して SQL Server Management Studio 内でより包括的な実行ログとエラー メッセージを確認することもできます。

   ![実行 ID を取得します。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する

パイプラインがスケジュールに従って実行されるように (時間単位、日単位など)、パイプラインに対してスケジュールされたトリガーを作成することもできます。 例については、「[Create a data factory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」 (データ ファクトリの作成 - データ ファクトリ UI ) リンクをご確認ください。

## <a name="run-a-package-with-powershell"></a>PowerShell を使用してパッケージを実行する
このセクションでは、Azure PowerShell を使用して、SSIS パッケージを実行する SSIS パッケージの実行アクティビティを含む Data Factory パイプラインを作成します。 

[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-az-ps)に関するページの手順に従って、最新の Azure PowerShell モジュールをインストールしてください。

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Azure-SSIS IR でデータ ファクトリを作成する
既に Azure-SSIS IR がプロビジョニングされている既存のデータ ファクトリを使用することも、Azure-SSIS IR を使用して新しいデータ ファクトリを作成することもできます。 「[チュートリアル: PowerShell を使用した Azure への SSIS パッケージのデプロイに関するチュートリアル](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)の手順に従って、Azure-SSIS IR を使用して新しい ADF を作成します。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する 
この手順では、SSIS パッケージの実行アクティビティでパイプラインを作成します。 このアクティビティは SSIS パッケージを実行します。 

1. 次の例のような内容を記述した *RunSSISPackagePipeline.json* という名前の JSON ファイルを *C:\ADF\RunSSISPackage* フォルダーに作成します。

   > [!IMPORTANT]
   > ファイルを保存する前に、オブジェクトの名前、説明、パス、プロパティ値、パラメーター値、パスワード、およびその他の変数値を置き換えます。 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   ファイル システム、ファイル共有、または Azure Files に格納されているパッケージを実行するには、次のようにパッケージおよびログの場所のプロパティの値を入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   ファイル システム、ファイル共有、または Azure Files に格納されているプロジェクト内のパッケージを実行するには、次のようにパッケージの場所のプロパティの値を入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   埋め込みパッケージを実行するには、パッケージの場所プロパティの値を次のように入力します。

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. Azure PowerShell で、*C:\ADF\RunSSISPackage* フォルダーに切り替えます。

3. パイプライン **RunSSISPackagePipeline** を作成するには、**Set-AzDataFactoryV2Pipeline** コマンドレットを実行します。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   出力例を次に示します。

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>パイプラインを実行する
**Invoke-AzDataFactoryV2Pipeline** コマンドレットを使ってパイプラインを実行します。 コマンドレットは、将来の監視のために、パイプラインの実行 ID を返します。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>パイプラインの監視

次の PowerShell スクリプトを実行し、データのコピーが完了するまで、パイプラインの実行の状態を継続的にチェックします。 次のスクリプトを PowerShell ウィンドウにコピーするか貼り付けて、Enter キーを押します。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Azure portal を使用してパイプラインを監視することもできます。 具体的な手順については、「[パイプラインの監視](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)」を参照してください。

### <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する
前の手順ではオンデマンドでパイプラインを実行しました。 スケジュール トリガーを作成して、スケジュール (毎時、毎日など) によってパイプラインをトリガーすることもできます。

1. 以下の内容を記述した *MyTrigger.json* という名前の JSON ファイルを *C:\ADF\RunSSISPackage* フォルダー内に作成します。 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Azure PowerShell で、*C:\ADF\RunSSISPackage* フォルダーに切り替えます。
1. **Set-AzDataFactoryV2Trigger** コマンドレットを実行してトリガーを作成します。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 既定ではトリガーは停止状態になっています。 **Start-AzDataFactoryV2Trigger** コマンドレットを使用してトリガーを起動します。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. **Start-AzDataFactoryV2Trigger** コマンドレットを実行して、トリガーが起動されていることを確認します。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. 時刻の時間部分が次の時間になってから、次のコマンドを実行します。 たとえば、現在時刻が午後 3 時 25 分 (UTC) であれば、午後 4 時 (UTC) にコマンドを実行します。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   パッケージが実行されたことを確認するには、次のクエリを SQL サーバーの SSISDB データベースに対して実行します。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>次の手順
次のブログ記事を参照してください。
- [Azure Data Factory パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
