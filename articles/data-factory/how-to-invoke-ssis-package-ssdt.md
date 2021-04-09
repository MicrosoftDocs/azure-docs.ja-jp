---
title: SSDT から SSIS パッケージを実行する
description: SSDT から Azure 内で SSIS パッケージを実行する方法について説明します。
ms.service: data-factory
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019
ms.date: 09/06/2020
ms.openlocfilehash: f11a3845e8644f3f60425538b2ef32cff668d88d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384930"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT から Azure 内で SSIS パッケージを実行する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、SQL Server Data Tools (SSDT) での Azure 対応 SQL Server Integration Services (SSIS) プロジェクトの機能について説明します。 これにより、SSIS パッケージのクラウド互換性を評価したり、Azure Data Factory (ADF) の Azure-SSIS Integration Runtime (IR) で実行したりできます。 この機能を使用して、既存のパッケージを Azure にリフト アンド シフト/移行する前にテストしたり、Azure 内で実行する新しいパッケージを開発したりすることができます。

この機能では、新規作成または既存の Azure-SSIS IR を SSIS プロジェクトにアタッチして、そこでパッケージを実行できます。  プロジェクト デプロイ モデルで Azure SQL Database サーバーまたはマネージド インスタンスをホストとする SSIS カタログ (SSISDB) にデプロイされるパッケージの実行がサポートされます。 パッケージ デプロイ モデルで Azure SQL Managed Instance をホストとするファイル システム、Azure Files、SQL Server データベース (MSDB) のいずれかにデプロイされるパッケージの実行もサポートされます。 

## <a name="prerequisites"></a>前提条件

この機能を使うには、Visual Studio 用の SSIS プロジェクト拡張機能を含む最新の SSDT を[こちら](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)からダウンロードしてインストールしてください。 または、最新の SSDT をスタンドアロン インストーラーとして[こちら](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)からダウンロードしてインストールすることもできます。

## <a name="azure-enable-ssis-projects"></a>Azure 対応の SSIS プロジェクト

### <a name="creating-new-azure-enabled-ssis-projects"></a>Azure 対応の新しい SSIS プロジェクトを作成する

SSDT では、**Integration Services プロジェクト (Azure 対応)** テンプレートを使用して、Azure 対応の新しい SSIS プロジェクトを作成できます。

   ![Azure 対応の新しい SSIS プロジェクト](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Azure 対応プロジェクトが作成されると、Azure Data Factory の SSIS に接続するように求められます。

   ![Azure-SSIS IR プロンプトに接続する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Azure-SSIS IR にすぐに接続する場合、詳細については「[Azure-SSIS IR への接続](#connectssisir)」を参照してください。 SSDT の [ソリューション エクスプローラー] ウィンドウでプロジェクト ノードを右クリックして、メニューをポップアップ表示することによって、後で接続することもできます。 次に、 **[Azure Data Factory の SSIS]** サブメニューの **[Azure Data Factory の SSIS に接続する]** 項目を選択します。

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a>既存の SSIS プロジェクトを Azure 対応にする

既存の SSIS プロジェクトの場合は、次の手順に従って Azure 対応にすることができます。

1. SSDT の [ソリューション エクスプローラー] ウィンドウでプロジェクト ノードを右クリックして、メニューをポップアップ表示します。 次に、 **[Azure Data Factory の SSIS]** サブメニューで **[Azure 対応プロジェクト]** 項目を選択し、**Azure 対応プロジェクト ウィザード** を起動します。

   ![既存の SSIS プロジェクトを Azure 対応にする](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. **[Visual Studio の構成を選択]** ページで、Azure でパッケージの実行設定を適用する既存の VS の構成を選択します。 VS 構成を作成していない場合は、新しい VS 構成を作成することもできます ([新しい VS 構成の作成](/visualstudio/ide/how-to-create-and-edit-configurations)に関する記事を参照してください)。 ローカル環境とクラウド環境でパッケージを実行する場合は、少なくとも 2 つの異なる VS 構成を使用することをお勧めします。これにより、クラウド構成に対してプロジェクトを Azure 対応にすることができます。 このようにして、プロジェクトまたはパッケージをパラメーター化した場合は、さまざまな実行環境 (ローカル コンピューターまたは Azure のいずれか) に基づいて、実行時にプロジェクトまたはパッケージのパラメーターに異なる値を割り当てることができます。 例については、「[パッケージ実行環境の切り替え](#switchenvironment)」を参照してください。

   ![Visual Studio の構成を選択](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. 既存の SSIS プロジェクトを Azure 対応にするには、ターゲット サーバーのバージョンを Azure-SSIS IR でサポートされている最新のバージョンに設定する必要があります。 Azure-SSIS IR は現在、**SQL Server 2017** に基づいています。 SQL Server 2017 でサポートされない追加のコンポーネントがパッケージに含まれていないことを確認してください。 また、互換性のあるすべての追加コンポーネントがカスタム セットアップを使用して Azure-SSIS IR にインストールされていることも確認してください ([Azure-SSIS IR のカスタマイズ](./how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事を参照してください)。 **[次へ]** ボタンをクリックして続行します。

   ![ターゲット サーバーのバージョンの切り替え](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. Azure-SSIS IR へのプロジェクトの接続を完了するには、[Azure-SSIS IR への接続](#connectssisir)に関するページを参照してください。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a>Azure 対応プロジェクトを Azure Data Factory の SSIS に接続する

Azure 対応プロジェクトを ADF の SSIS に接続することにより、パッケージを Azure Files にアップロードして、Azure-SSIS IR 上で実行できます。 これを行うには、次の手順を実行します。

1. **[ADF での SSIS の概要]** ページで、概要を確認し、 **[次へ]** ボタンをクリックして続行します。

   ![ADF での SSIS の概要](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. **[ADF で SSIS IR を選択]** ページで、パッケージを実行する既存の ADF と Azure-SSIS IR を選択します。 また、それらがない場合は新規に作成することもできます。
   - 既存の Azure-SSIS IR を選択するには、まず、関連する Azure サブスクリプションと ADF を選択します。
   - Azure-SSIS IR がない既存の ADF を選択した場合は、 **[SSIS IR の作成]** ボタンを選択して、ADF ポータルに新しく作成します。 作成したら、このページに戻って、新しい Azure-SSIS IR を選択できます。
   - ADF がない既存の Azure サブスクリプションを選択した場合は、 **[SSIS IR の作成]** ボタンを選択して、**統合ランタイム作成ウィザード** を起動します。 このウィザードでは、指定された場所とプレフィックスを入力して、新しい Azure リソース グループ、Data Factory、SSIS IR を自動的に作成することができます。これには、次のパターンの名前が付けられます。**YourPrefix-RG/DF/IR-YourCreationTime**. 作成したら、このページに戻って、新しい ADF と Azure-SSIS IR を選択できます。

   ![ADF で SSIS IR を選択する](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. **[Azure Storage の選択]** ページで、既存の Azure ストレージ アカウントを選択してパッケージを Azure Files にアップロードします。 また、アカウントがない場合は新規に作成することもできます。
   - 既存の Azure ストレージ アカウントを選択するには、まず、関連する Azure サブスクリプションを選択します。
   - Azure Storage アカウントがない Azure-SSIS IR と同じ Azure サブスクリプションを選択した場合は **[Azure Storage の作成]** ボタンを選択します。 Azure-SSIS IR と同じ場所に新しいアカウントが自動的に作成され、Azure-SSIS IR 名のプレフィックスと作成日を組み合わせた名前が付けられます。 作成したら、このページに戻って、新しい Azure Storage アカウントを選択できます。
   - Azure Storage アカウントがない別の Azure サブスクリプションを選択した場合は、 **[Azure Storage の作成]** ボタンを選択して、Azure portal で新しく作成します。 作成したら、このページに戻って、新しい Azure Storage アカウントを選択できます。

   ![Azure Storage を選択する](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. Azure-SSIS IR へのプロジェクトの接続を完了するには、 **[接続]** ボタンを選択します。 SSDT のソリューション エクスプローラー ウィンドウの **[リンクされた Azure リソース]** ノードに、選択した Azure-SSIS IR と Azure Storage アカウントが表示されます。 また、Azure-SSIS IR の状態が定期的に更新されて表示されます。 Azure-SSIS IR を管理するには、ノードを右クリックしてメニューをポップアップ表示し、 **[開始\停止\管理]** 項目を選択して ADF ポータルに移動します。

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Azure で実行する SSIS プロジェクトまたはパッケージを評価する

### <a name="assessing-single-or-multiple-packages"></a>1 つまたは複数のパッケージを評価する

Azure でパッケージを実行する前に、それらを評価して潜在的なクラウドの互換性の問題を顕在化させることができます。 これには、移行ブロッカーと、注意する必要がある追加情報が含まれます。 
-  1 つのパッケージを 1 つずつ評価するオプションとプロジェクト内のすべてのパッケージを同時に評価するオプションがあります。

   ![パッケージの評価](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![プロジェクトの評価](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  SSDT の **[評価レポート]** ウィンドウでは、顕在化されたすべての潜在的なクラウドの互換性の問題と、それぞれの詳細な説明およびレコメンデーションを確認できます。 また、評価レポートを CSV ファイルにエクスポートすることもできます。このファイルは、これらの問題を軽減する必要があるすべてのユーザーと共有できます。 

   ![評価レポート](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>評価ルールを抑制する

クラウドの互換性に関する潜在的な問題が、対象のパッケージに該当しないか、適切に軽減されていることを確認したら、それを顕在化させた関連する評価ルールを抑制することができます。 これにより、後続の評価レポートでノイズが軽減されます。
-  SSDT の **[評価レポート]** ウィンドウにある **[評価ルールの抑制の構成]** リンクを選択して、 **[評価ルール抑制の設定]** ウィンドウをポップアップ表示し、抑制する評価ルールを選択します。

   ![評価ルール抑制の設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  または、SSDT の [ソリューション エクスプローラー] ウィンドウでプロジェクト ノードを右クリックして、メニューをポップアップ表示します。 **[Azure Data Factory の SSIS]** サブメニューの **[Azure 対応の設定]** 項目を選択して、プロジェクトのプロパティ ページを含むウィンドウをポップアップ表示します。 **[Azure 対応の設定]** セクションで、 **[抑制された評価ルール ID]** プロパティを選択します。 最後に、その省略記号 ( **...** ) ボタンを選択して、 **[評価ルール抑制の設定]** ウィンドウをポップアップ表示します。このウィンドウでは、抑制する評価ルールを選択できます。

   ![Azure 対応の設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![[Azure 対応の設定] を使用した評価ルール抑制の設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Azure で SSIS パッケージを実行する

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Azure 対応の設定を構成する

Azure でパッケージを実行する前に、Azure 対応の設定を構成できます。 たとえば、Azure-SSIS IR でオンプレミス/クラウドのデータストアにアクセスするための Windows 認証を有効にするには、次の手順を実行します。

1. SSDT の [ソリューション エクスプローラー] ウィンドウでプロジェクト ノードを右クリックして、メニューをポップアップ表示します。 次に、 **[Azure Data Factory の SSIS]** サブメニューの **[Azure 対応の設定]** 項目を選択して、プロジェクトのプロパティ ページを含むウィンドウをポップアップ表示します。

   ![Azure 対応の設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. **[Azure 対応の設定]** セクションで **[Windows 認証を有効にする]** プロパティを選択し、ドロップダウン メニューで **[True]** を選択します。 次に、 **[Windows 認証資格情報]** プロパティを選択し、省略記号 ( **...** ) ボタンを選択して、 **[Windows 認証資格情報]** ウィンドウをポップアップ表示します。

   ![Windows 認証を有効化します。](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Windows 認証資格情報を入力します。 たとえば、Azure Files にアクセスするには、 **[ドメイン]** 、 **[ユーザー名]** 、および **[パスワード]** にそれぞれ、`Azure`、`YourStorageAccountName`、および `YourStorageAccountKey` を入力します。

   ![Windows 認証資格情報](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>パッケージの実行を開始する

Azure 対応プロジェクトを ADF で SSIS に接続し、クラウドの互換性を評価して、潜在的な問題を軽減したら、Azure-SSIS IR でパッケージを実行/テストできます。
-  SSDT ツール バーの **[開始]** ボタンを選択して、メニューをドロップダウン表示します。 次に、 **[Azure で実行]** 項目を選択します。

   ![Azure で実行する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  または、SSDT の [ソリューション エクスプローラー] ウィンドウでパッケージ ノードを右クリックして、メニューをポップアップ表示します。 次に、 **[Azure でパッケージを実行する]** 項目を選択します。

   ![Azure でパッケージを実行する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Azure でパッケージを実行するには、Azure-SSIS IR が実行されている必要があるため、Azure-SSIS IR が停止している場合は、ダイアログ ウィンドウが表示されて、開始することができます。 カスタム セットアップ時間を除けば、このプロセスは 5 分以内に完了しますが、Azure SSIS IR が仮想ネットワークに参加するのに約 20 から 30 分かかる場合があります。 Azure でパッケージを実行した後は、SSDT のソリューション エクスプローラー ウィンドウでノードを右クリックしてメニューをポップアップ表示し、 **[開始\停止\管理]** 項目を選択して ADF ポータルに移動し、Azure-SSIS IR を停止して実行コストを管理できます。

### <a name="using-execute-package-task"></a>パッケージ実行タスクを使用する

パッケージに、ローカル ファイル システムに格納されている子パッケージを参照するパッケージ実行タスクが含まれている場合は、次の追加の手順を実行します。

1. プロジェクトに接続されているのと同じ Azure Storage アカウントで子パッケージを Azure Files にアップロードし、新しい汎用名前付け規則 (UNC) パス (例: `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`) を取得します

2. パッケージ実行タスクのファイル接続マネージャーで、それらの子パッケージのファイル パスを新しい UNC パスに置き換えます
   - SSDT が実行されているローカル コンピューターが新しい UNC パスにアクセスできない場合は、ファイル接続マネージャーの [プロパティ] パネルで入力できます
   - または、ファイル パスに変数を使用して、実行時に適切な値を割り当てることもできます

パッケージに、同じプロジェクト内の子パッケージを参照するパッケージ実行タスクが含まれている場合は、追加の手順は必要ありません。

### <a name="switching-package-protection-level"></a>パッケージの保護レベルを切り替える

Azure での SSIS パッケージの実行では、**EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 保護レベルはサポートされません。 このため、それらを使用するようにパッケージが構成されている場合は、それぞれ **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 保護レベルを使用して、一時的に変換されます。 また、Azure-SSIS IR で実行するためにパッケージを Azure Files にアップロードするときに、暗号化パスワードがランダムに生成されます。

> [!NOTE]
> **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 保護レベルを使用するように構成された子パッケージを参照するパッケージ実行タスクがパッケージに含まれている場合は、パッケージを実行する前に、それぞれ **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 保護レベルを使用するようにこれらの子パッケージを手動で再構成する必要があります。

パッケージが **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 保護レベルを使用するように既に構成されている場合は、変更されません。 Azure-SSIS IR で実行するためにパッケージを Azure Files にアップロードするときに、暗号化パスワードがランダムに生成されます。

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> パッケージ実行環境を切り替える

プロジェクト デプロイ モデルのプロジェクトまたはパッケージをパラメーター化する場合は、複数の VS 構成を作成して、パッケージの実行環境を切り替えることができます。 このようにして、実行時に環境固有の値をプロジェクトまたはパッケージのパラメーターに割り当てることができます。 ローカル環境とクラウド環境でパッケージを実行する場合は、少なくとも 2 つの異なる VS 構成を使用することをお勧めします。これにより、クラウド構成に対してプロジェクトを Azure 対応にすることができます。 ローカル コンピューターと Azure の間でパッケージ実行環境を切り替える手順の例を次に示します。

1. たとえば、ファイルの属性を設定するファイル システム タスクがパッケージに含まれているとします。 ローカル コンピューターで実行すると、ローカル ファイル システムに格納されているファイルの属性が設定されます。 Azure-SSIS IR で実行する場合は、Azure Files に格納されているファイルの属性を設定できます。 最初に、string 型のパッケージ パラメーターを作成して、**FilePath** という名前を付け、ターゲット ファイル パスの値を保持するようにします。

   ![パッケージ パラメーターを作成する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. 次に、 **[ファイル システム タスク エディター]** ウィンドウの **[全般]** ページで、 **[ソース接続]** セクションの **SourceVariable** プロパティを **FilePath** パッケージ パラメーターでパラメーター化します。 

   ![ソース接続をパラメーター化する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. 既定では、**Development** という名前のローカル環境にパッケージ実行用の既存の VS 構成があります。 **Azure** というクラウド環境にパッケージ実行のための新しい VS 構成を作成します。まだ作成していない場合は、[新しい VS 構成の作成](/visualstudio/ide/how-to-create-and-edit-configurations)に関する記事を参照してください。

4. パッケージのパラメーターを表示する場合は、 **[構成にパラメーターを追加]** ボタンを選択して、パッケージの **[パラメーター値の管理]** ウィンドウを開きます。 次に、**Development** および **Azure** 構成の **FilePath** パッケージ パラメーターに、異なる値のターゲット ファイル パスを割り当てます。

   ![パラメーター値の割り当て](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. クラウド構成に対してプロジェクトを Azure 対応にします。まだ行っていない場合は、「[既存の SSIS プロジェクトを Azure 対応にする](#azureenableproject)」を参照してください。 次に、Azure 対応の設定を構成して、Azure-SSIS IR に対して Azure Files にアクセスするための Windows 認証を有効にします。まだ行っていない場合は、「[Azure 対応の設定を構成する」](#azureenabledsettings)を参照してください。

6. パッケージを Azure で実行します。 **Development** 構成を選択すると、パッケージ実行環境をローカル コンピューターに切り替えることができます。

   ![Visual Studio 構成を切り替える](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>パッケージ構成ファイルを使用する

パッケージ デプロイ モデルでパッケージ構成ファイルを使用する場合は、実行時に環境固有の値をパッケージのプロパティに割り当てることができます。 これらのファイルは、Azure-SSIS IR で実行するために、パッケージと共に Azure Files に自動的にアップロードされます。

### <a name="checking-package-execution-logs"></a>パッケージの実行ログをチェックする

パッケージの実行を開始すると、SSDT の **[進行状況]** ウィンドウにログが書式設定されて表示されます。 長時間実行されるパッケージの場合、ログは分単位で定期的に更新されます。 SSDT ツール バーの **[停止]** ボタンを選択することで、パッケージの実行をすぐに停止できます。 また、UNC パス `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` でログの生データを一時的に検索することもできますが、1 日後にクリーンアップされます。

## <a name="current-limitations"></a>現在の制限

-  Azure 対応の SSDT では、商用/グローバルのクラウド リージョンのみがサポートされており、現時点では政府/ナショナルのクラウド リージョンはサポートされていません。

## <a name="next-steps"></a>次の手順

SSDT から Azure でのパッケージの実行に問題がなければ、ADF パイプラインに SSIS パッケージ実行アクティビティとしてそれらをデプロイし、実行できます。[ADF パイプラインの SSIS パッケージ実行アクティビティとしての SSIS パッケージの実行](./how-to-invoke-ssis-package-ssis-activity.md)に関する記事を参照してください。