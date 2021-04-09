---
title: Azure-SSIS 統合ランタイムのセットアップのカスタマイズ
description: この記事では、Azure SSIS 統合ランタイムのカスタム セットアップ インターフェイスを使用して、追加のコンポーネントをインストールしたり、設定を変更したりする方法について説明します
ms.service: data-factory
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: b1b3a34ac495936ed92e29353a41efb8c462768f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387735"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムのセットアップのカスタマイズ

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) でカスタム セットアップを使用して Azure SQL Server Integration Services (SSIS) 統合ランタイム (IR) をカスタマイズすることができます。 お使いの Azure SSIS IR のプロビジョニングまたは再構成の間に、独自の手順を追加することができます。 

カスタム セットアップを使用すると、Azure-SSIS IR の既定の運用構成または環境を変更できます。 たとえば、追加の Windows サービスを開始するには、ファイル共有のアクセス資格情報を保持するか、強力な暗号化/よりセキュリティで保護されたネットワーク プロトコル (TLS 1.2) を使用します。 また、ご利用の Azure-SSIS IR の各ノード上にアセンブリ、ドライバー、拡張機能などの追加のコンポーネントをインストールすることもできます。 これらには、カスタム作成、オープンソース、またはサードパーティのコンポーネントを使用できます。 組み込みコンポーネントとプレインストール コンポーネントの詳細については、[Azure-SSIS IR の組み込みコンポーネントとプレインストール コンポーネント](./built-in-preinstalled-components-ssis-integration-runtime.md)に関するページを参照してください。

ご利用の Azure-SSIS IR では、次の 2 つの方法のいずれかでカスタム セットアップを行うことができます。 
* **スクリプトを使用した標準カスタム セットアップ**: スクリプトとその関連ファイルを準備し、それらをすべてまとめてご利用の Azure ストレージ アカウントの BLOB コンテナーにアップロードします。 その後、Azure SSIS IR を設定または再構成するときに、ご利用のコンテナーに対して Shared Access Signature (SAS) の Uniform Resource Identifier (URI) を指定します。 ご利用の Azure SSIS IR の各ノードでは、その後、スクリプトとその関連ファイルがご利用のコンテナーからダウンロードされ、昇格されたアクセス許可を使用してご自分のカスタム セットアップが実行されます。 そのカスタム セットアップが完了すると、各ノードによって、実行の標準出力およびその他のログがご利用にコンテナーにアップロードされます。
* **スクリプトを使用しない高速カスタム セットアップ**: いくつかの一般的なシステム構成および Windows コマンドを実行したり、スクリプトを使用せずにいくつかの一般的なまたは推奨される追加コンポーネントをインストールしたりします。

標準および高速のカスタム セットアップでは、無料 (ライセンスなし) および有料 (ライセンスあり) のコンポーネントの両方をインストールできます。 お客様が独立ソフトウェア ベンダー (ISV) である場合は、[Azure SSIS IR の有料またはライセンスありのコンポーネントの開発](how-to-develop-azure-ssis-ir-licensed-components.md)に関するドキュメントをご覧ください。

> [!IMPORTANT]
> 将来の機能強化の恩恵を受けるには、カスタム セットアップのある Azure-SSIS IR に v3 以降の一連のノードを使用することをお勧めします。

## <a name="current-limitations"></a>現在の制限

次の制限事項は、標準カスタム セットアップにのみ適用されます。

- ご自分のスクリプトで *gacutil.exe* を使用してアセンブリをグローバル アセンブリ キャッシュ (GAC) にインストールする場合は、ご自分のカスタム セットアップの一部として *gacutil.exe* を指定する必要があります。 または、*パブリック プレビュー* コンテナーの *Sample* フォルダーで提供されているコピーを使用することもできます。下の「**標準カスタム セットアップのサンプル**」のセクションを参照してください。

- ご自分のスクリプト内でサブフォルダーを参照する場合、*msiexec.exe* ではルート フォルダーを参照する `.\` 表記はサポートされていません。 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` ではなく、`msiexec /i "MySubfolder\MyInstallerx64.msi" ...` などのコマンドを使用します。

- Windows によって自動的に作成される管理用共有、または非表示のネットワーク共有は、現在、Azure-SSIS IR ではサポートされていません。

- Azure-SSIS IR では、IBM iSeries Access ODBC ドライバーはサポートされません。 カスタム セットアップ中にインストール エラーが表示される場合があります。 その場合は、IBM のサポートにお問い合わせください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

ご利用の Azure SSIS IR をカスタマイズするには、次のアイテムが必要です。

- [Azure サブスクリプション](https://azure.microsoft.com/)

- [お使いの Azure SSIS IR のプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)

- [Azure ストレージ アカウント](https://azure.microsoft.com/services/storage/)。 高速カスタム セットアップには必要ありません。 標準カスタム セットアップの場合は、カスタム セットアップ スクリプトとその関連ファイルをアップロードして BLOB コンテナーに格納します。 また、カスタム セットアップ処理では、同じ BLOB コンテナーに実行ログもアップロードします。

## <a name="instructions"></a>Instructions

ADF UI でカスタム セットアップを使用して Azure-SSIS IR をプロビジョニングまたは再構成できます。 PowerShell を使用して同じ操作を実行する場合は、[Azure PowerShell](/powershell/azure/install-az-ps) をダウンロードしてインストールします。

### <a name="standard-custom-setup"></a>標準カスタム セットアップ

ADF UI で標準カスタム セットアップを使用して Azure-SSIS IR をプロビジョニングまたは再構成するには、次の手順を実行します。

1. カスタム セットアップ スクリプトとその関連ファイル (たとえば、.bat、.cmd、.exe、.dll、.msi、または .ps1 ファイル) を準備します。

   * カスタム セットアップのエントリ ポイントである *main.cmd* という名前のスクリプト ファイルを用意しておく必要があります。  
   * スクリプトを確実にサイレント実行できるようにするには、まずご利用のローカル コンピューターでテストする必要があります。  
   * 他のツール (*msiexec* など) によって生成された追加のログが、ご利用のコンテナーにアップロードされるようにする場合は、定義済みの環境変数 `CUSTOM_SETUP_SCRIPT_LOG_DIR` をご自分のスクリプトのログ フォルダーとして指定します (たとえば、*msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*)。

1. [Azure Storage Explorer](https://storageexplorer.com/) をダウンロードし、インストールして、開きます。

   a. **[(Local and Attached)]\((ローカルで接続済み)\)** で、 **[ストレージ アカウント]** を右クリックして、 **[Azure Storage へ接続]** を選択します。

      ![Azure Storage への接続](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用する\)** を選択して、 **[次へ]** を選択します。

      ![ストレージ アカウントの名前とキーを使用する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. ご利用の Azure ストレージ アカウント名とキーを入力し、 **[次へ]** を選択してから **[接続]** を選択します。

      ![ストレージ アカウントの名前とキーを指定する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 接続された Azure ストレージ アカウントで、 **[BLOB コンテナー]** を右クリックし、 **[BLOB コンテナーの作成]** を選択して、新しいコンテナーに名前を付けます。

      ![BLOB コンテナーを作成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 新しいコンテナーを選択し、ご自分のカスタム セットアップ スクリプトとその関連ファイルをアップロードします。 任意のフォルダーではなく、必ずご利用のコンテナーの最上位に *main.cmd* をアップロードしてください。 ご利用のコンテナーには必要なカスタム セットアップ ファイルのみを含める必要があります。これにより、ご利用の Azure-SSIS IR に後でダウンロードする場合に時間が短縮されます。 カスタム セットアップの最長期間は現在 45 分に設定されていて、これを過ぎるとタイムアウトになります。これには、ご利用のコンテナーからすべてのファイルをダウンロードし、それらを Azure-SSIS IR にインストールする時間が含まれます。 セットアップにより長い時間が必要な場合は、サポート チケットを生成してください。

      ![BLOB コンテナーにファイルをアップロードする](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. コンテナーを右クリックしてから、 **[Shared Access Signature の取得]** を選択します。

      ![コンテナーの Shared Access Signature を取得する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 十分に長い有効期間と [読み取り]、[書き込み]、または [リスト] のアクセス許可を使用して、ご利用のコンテナー用に SAS URI を作成します。 カスタム セットアップ スクリプトとその関連ファイルをダウンロードして実行するには、SAS URI が必要となります。 これは、Azure-SSIS IR の任意のノードが再イメージ化または再起動されるたびに発生します。 また、セットアップ実行ログをアップロードするには、書き込みアクセス許可も必要です。

      > [!IMPORTANT]
      > Azure-SSIS IR の作成から削除まで (この期間に Azure-SSIS IR を定期的に停止および開始する場合は特に) そのライフサイクル全体を通して、SAS URI の有効期限が切れないこと、カスタム セットアップ リソースが常に利用可能であることを保証します。

      ![コンテナーの Shared Access Signature を生成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. コンテナーの SAS URI をコピーして保存します。

      ![Shared Access Signature をコピーして保存する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. **[詳細設定]** ページの **[統合ランタイムのセットアップ]** ペインにある **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]** \(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ラインタイムをカスタマイズする\) チェック ボックスをオンにします。 次に、 **[カスタム セットアップ コンテナーの SAS URI]** テキスト ボックスに、ご利用のコンテナーの SAS URI を入力します。

   ![カスタム セットアップでの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

標準カスタム セットアップが完了し、Azure SSIS IR が開始した後、ご利用のコンテナーの *main.cmd.log* フォルダー内ですべてのカスタム セットアップ ログを見つけることができます。 これには、*main.cmd* の標準出力とその他の実行ログが含まれます。

### <a name="express-custom-setup"></a>高速カスタム セットアップ

ADF UI で高速カスタム セットアップを使用して Azure-SSIS IR をプロビジョニングまたは再構成するには、次の手順を実行します。

1. **[詳細設定]** ページの **[統合ランタイムのセットアップ]** ペインにある **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]** \(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ラインタイムをカスタマイズする\) チェック ボックスをオンにします。 

1. **[新規]** を選択して **[高速カスタム セットアップの追加]** ウィンドウを開いてから、 **[高速カスタム セットアップの種類]** ドロップダウン リストで種類を選択します。 現在、高速カスタム セットアップが提供されており、cmdkey コマンドを実行したり、環境変数を追加したり、Azure PowerShell をインストールしたり、ライセンスされたコンポーネントをインストールしたりすることができます。

#### <a name="running-cmdkey-command"></a>cmdkey コマンドを実行する

高速カスタム セットアップで種類として **[cmdkey コマンドの実行]** を選択した場合は、Azure-SSIS IR で Windows cmdkey コマンドを実行できます。 これを行うには、対象のコンピューター名またはドメイン名、ユーザー名またはアカウント名、およびパスワードまたはアカウント キーを、 **[/追加]** 、 **[/ユーザー]** 、および **[/パスワード]** の各テキスト ボックスにそれぞれ入力します。 これにより、SQL Server、ファイル共有、または Azure Files のアクセス資格情報を Azure-SSIS IR に保持できます。 たとえば、Azure Files にアクセスするには、 **[/追加]** 、 **[/ユーザー]** 、および **[/パスワード]** にそれぞれ、`YourAzureStorageAccountName.file.core.windows.net`、`azure\YourAzureStorageAccountName`、および `YourAzureStorageAccountKey` を入力します。 これは、ローカル コンピューターで Windows [cmdkey](/windows-server/administration/windows-commands/cmdkey) コマンドを実行する場合と似ています。 現在のところ、cmdkey コマンドを実行する高速カスタム セットアップ 1 つだけがサポートされます。 複数の cmdkey コマンドを実行するには、代わりに標準カスタム セットアップを使用します。

#### <a name="adding-environment-variables"></a>環境変数を追加する

高速カスタム セットアップで種類として **[環境変数の追加]** を選択した場合は、Azure-SSIS IR に Windows 環境変数を追加できます。 これを行うには、環境変数の名前と値を **[変数名]** および **[変数値]** テキスト ボックスにそれぞれ入力します。 これにより、Azure-SSIS IR で実行されるパッケージ (スクリプト コンポーネントやタスクなど) で環境変数を使用できます。 これは、ローカル コンピューターで Windows [set](/windows-server/administration/windows-commands/set_1) コマンドを実行する場合と似ています。

#### <a name="installing-azure-powershell"></a>Azure PowerShell をインストールする

高速カスタム セットアップで種類として **[Azure PowerShell のインストール]** を選択した場合は、Azure-SSIS IR に PowerShell の Az モジュールをインストールできます。 これを行うには、[サポートされているモジュールの一覧](https://www.powershellgallery.com/packages/az)から、Az モジュールのバージョン番号 (x.y.z) を入力します。 これにより、パッケージで Azure PowerShell コマンドレットまたはスクリプトを実行して、Azure リソース ([Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md) など) を管理できます。

#### <a name="installing-licensed-components"></a>ライセンスされたコンポーネントをインストールする

高速カスタム セットアップで種類として **[ライセンス付与されたコンポーネントのインストール]** を選択した場合は、 **[コンポーネント名]** ドロップダウン リストで、Microsoft の ISV パートナーからの統合コンポーネントを選択できます。

   * **[SentryOne の Task Factory]** コンポーネントを選択した場合は、Azure-SSIS IR に SentryOne の [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) という一連のコンポーネントをインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **2020.1.3** です。

   * **[oh22 の HEDDA.IO]** コンポーネントを選択した場合は、Azure-SSIS IR に oh22 の [HEDDA.IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR) データ品質またはクレンジング コンポーネントをインストールできます。 これを行うには、事前にサービスを購入する必要があります。 現在の統合バージョンは **1.0.14** です。

   * **[oh22 の SQLPhonetics.NET]** コンポーネントを選択した場合は、Azure-SSIS IR に oh22 の [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) データ品質または照合コンポーネントをインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **1.0.45** です。

   * **[KingswaySoft の SSIS Integration Toolkit]** コンポーネントを選択した場合は、お使いの Azure-SSIS IR に KingswaySoft から Microsoft Dynamics、SharePoint、Project Server、Oracle、Salesforce Marketing Cloud などの CRM、ERP、マーケティング、コラボレーション アプリ用の [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) という一連のコンポーネントをインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **2020.1** です。

   * **[KingswaySoft の SSIS Productivity Pack]** コンポーネントを選択した場合は、お使いの Azure-SSIS IR に KingswaySoft から [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) という一連のコンポーネントをインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **20.1** です。

   * **[Theobald Software の Xtract IS]** コンポーネントを選択した場合は、お使いの Azure-SSIS IR に Theobald Software から SAP システム (ERP、S/4HANA、BW) 用の [Xtract IS](https://theobald-software.com/en/xtract-is/) という一連のコンポーネントをインストールできます。 これを行うには、事前に購入した製品ライセンス ファイルを **[ライセンス ファイル]** 入力ボックスにドラッグ アンド ドロップするか、アップロードします。 現在の統合バージョンは **6.1.1.3** です。

   * **[AecorSoft の Integration Service]** コンポーネントを選択した場合は、Azure-SSIS IR に AecorSoft から SAP および Salesforce システム用の [Integration Service](https://www.aecorsoft.com/en/products/integrationservice) の一連のコネクタをインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **3.0.00** です。

   * **CData の SSIS 標準パッケージ** コンポーネントを選択する場合は、Microsoft SharePoint コネクタなど、CData からの一般的なコンポーネントの [SSIS 標準パッケージ](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) スイートを Azure-SSIS IR にインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **19.7354** です。

   * **CData の SSIS 拡張パッケージ** コンポーネントを選択する場合は、Microsoft Dynamics 365 Business Central コネクタや、**SSIS 標準パッケージ** 内の他のコンポーネントなど、CData からのすべてのコンポーネントの一連の [SSIS 拡張パッケージ](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended)を Azure-SSIS IR にインストールできます。 これを行うには、 **[ライセンス キー]** テキスト ボックスに、事前に購入した製品ライセンス キーを入力します。 現在の統合バージョンは **19.7354** です。 サイズが大きいため、インストールのタイムアウトを回避するには、ノード 1 つあたり 4 つの CPU コアが Azure-SSIS IR に存在することを確認してください。

追加された高速カスタム セットアップは、 **[詳細設定]** ページに表示されます。 これらを削除する場合は、対応するチェック ボックスをオンにしてから **[削除]** を選択します。

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してカスタム セットアップで Azure-SSIS IR をプロビジョニングまたは再構成するには、次の手順を実行します。

1. Azure-SSIS IR が既に開始または実行している場合は、最初に停止します。

1. この後、Azure-SSIS IR を開始する前に `Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行して、カスタム セットアップを追加したり削除したりできます。

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>標準カスタム セットアップのサンプル

標準カスタム セットアップのサンプルを表示して再利用するには、次の手順を実行します。

1. Azure Storage Explorer を使用して、パブリック プレビュー コンテナーに接続します。

   a. **[(Local and Attached)]\((ローカルおよび接続済み)\)** で、 **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure storage]\(Azure Storage へ接続\)** を選択し、 **[Use a connection string or a shared access signature URI]\(接続文字列または Shared Access Signature URI を使用する\)** を選択して、 **[次へ]** を選択します。

      ![Shared Access Signature を使って Azure Storage に接続する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. **[SAS URI を使用する]** を選択してから、 **[URI]** テキスト ボックスに次の SAS URI を入力します。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![コンテナーに対して Shared Access Signature を提供する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. **[次へ]** 、 **[接続]** の順に選択します。

   d. 左側のウィンドウで、接続されている **publicpreview** コンテナーを選択して、 *[CustomSetupScript]* フォルダーをダブルクリックします。 このフォルダーには、次のアイテムが含まれています。

      * *Sample* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に基本タスクをインストールするためのカスタム セットアップが格納されています。 タスクは何も行いませんが、数秒間スリープ状態になります。 このフォルダーには、*gacutil* フォルダーも含まれていて、その内容全体 (*gacutil.exe*、*gacutil.exe.config*、および *1033\gacutlrc.dll*) を、そのままご利用のコンテナーにコピーできます。

      * *UserScenarios* フォルダー。これには、実際のユーザー シナリオからのいくつかのカスタム セットアップ サンプルが格納されています。 Azure-SSIS IR に複数のサンプルをインストールする場合は、それらのカスタム セットアップ スクリプト (*main.cmd*) ファイルを 1 つにまとめて、関連付けられているすべてのファイルと共にコンテナーにアップロードすることができます。

        ![パブリック プレビュー コンテナーの内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. *[UserScenarios]* フォルダーをダブルクリックして、次の項目を検索します。

      * *.NET FRAMEWORK 3.5* フォルダー。これには、ご利用の Azure-SSIS IR の各ノードに以前のバージョンの .NET Framework をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 一部のカスタム コンポーネントでは、このバージョンが必要になる場合があります。

      * *BCP* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に、SQL Server コマンドライン ユーティリティ (*MsSqlCmdLnUtils.msi*) をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 これらのユーティリティの 1 つは、一括コピー プログラム (*bcp*) です。

      * *DNS SUFFIX* フォルダー。これには、Azure-SSIS IR からの DNS クエリで使用する前に、独自の DNS サフィックス (たとえば *test.com*) を修飾されていない単一ラベル ドメイン名に追加し、それを完全修飾ドメイン名 (FQDN) に変換するためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。

      * *EXCEL* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に一部の C# アセンブリとライブラリをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 スクリプト タスクでこれらを使用すると、Excel ファイルの読み取りと書き込みを動的に行うことができます。 
      
        まず、[*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) と [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) をダウンロードし、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。 または、標準の Excel コネクタ (Connection Manager、Source、および Destination) のみを使用する場合は、それらを含む Access の再頒布可能パッケージが既に Azure-SSIS IR にプレインストールされているため、カスタム セットアップは必要ありません。
      
      * *MYSQL ODBC* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に MySQL ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ODBC コネクタ (接続マネージャー、ソース、および変換先) を使用して、MySQL サーバーに接続できます。 
     
        まず、[最新の 64 ビットおよび 32 ビット バージョンの MySQL ODBC ドライバー インストーラーをダウンロード](https://dev.mysql.com/downloads/connector/odbc/)し (たとえば、*mysql-connector-odbc-8.0.13-winx64.msi* や *mysql-connector-odbc-8.0.13-win32.msi*)、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。

      * *ORACLE ENTERPRISE* フォルダー。これには、ご利用の Azure SSIS IR Enterprise Edition の各ノードに Oracle コネクタおよび OCI ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) とサイレント インストール構成ファイル (*client.rsp*) が格納されています。 このセットアップでは、Oracle 接続マネージャー、ソース、および変換先を使用して、Oracle サーバーに接続できます。 
      
        まず、Microsoft Connectors v5.0 for Oracle (*AttunitySSISOraAdaptersSetup.msi* および *AttunitySSISOraAdaptersSetup64.msi*) を [Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=55179)から、そして最新の Oracle クライアント (たとえば、*winx64_12102_client.zip*) を [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html) からダウンロードします。 次に、これらすべてを *main.cmd* および *client.rsp* と共にご利用のコンテナーにアップロードします。 TNS を使用して Oracle に接続する場合は、*tnsnames.ora* をダウンロードし、編集して、ご利用のコンテナーにアップロードすることも必要です。 このようにして、セットアップ中に Oracle インストール フォルダーにコピーできます。

      * *ORACLE STANDARD ADO.NET* フォルダー。これには、ご利用の Azure-SSIS IR の各ノード上に Oracle ODP.NET ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ADO.NET 接続マネージャー、ソース、および変換先を使用して、Oracle サーバーに接続できます。 
      
        まず、[最新の Oracle ODP.NET ドライバーをダウンロード](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)し (たとえば、*ODP.NET_Managed_ODAC122cR1.zip*)、次にそれを *main.cmd* と共にご利用のコンテナーにアップロードします。
       
      * *ORACLE STANDARD ODBC* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Oracle ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このスクリプトでは、データソース名 (DSN) も構成されます。 このセットアップでは、ODBC 接続マネージャー、ソース、および変換先を使用するか、または Power Query 接続マネージャーおよびソース (データ ソースの種類が ODBC である) を使用して Oracle サーバーに接続できます。 
      
        まず、最新の Oracle Instant Client (Basic Package または Basic Lite Package) と ODBC Package をダウンロードし、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。
        * [64 ビット パッケージをダウンロードします](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic Package: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*。Basic Lite Package: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*。ODBC Package: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32 ビット パッケージをダウンロードします](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic Package: *instantclient-basic-nt-18.3.0.0.0dbru.zip*。Basic Lite Package: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*。ODBC Package: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *ORACLE STANDARD OLEDB* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Oracle OLEDB ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、OLEDB 接続マネージャー、ソース、および変換先を使用して、Oracle サーバーに接続できます。 
     
        まず、[最新の Oracle OLEDB ドライバーをダウンロード](https://www.oracle.com/partners/campaign/index-090165.html)し (たとえば、*ODAC122010Xcopy_x64.zip*)、次にそれを *main.cmd* と共にご利用のコンテナーにアップロードします。

      * *POSTGRESQL ODBC* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に PostgreSQL ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ODBC 接続マネージャー、ソース、および変換先を使用して、PostgreSQL サーバーに接続できます。 
     
        まず、[最新の 64 ビットおよび 32 ビット バージョンの PostgreSQL ODBC ドライバー インストーラーをダウンロード](https://www.postgresql.org/ftp/odbc/versions/msi/)し (たとえば、*psqlodbc_x64.msi* や *psqlodbc_x86.msi*)、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。

      * *SAP BW* フォルダー。これには、ご利用の Azure SSIS IR Enterprise Edition の各ノード上に SAP .NET コネクター アセンブリ (*librfc32.dll*) をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、SAP BW 接続マネージャー、ソース、および変換先を使用して、SAP BW サーバーに接続できます。 
      
        まず、SAP インストール フォルダーからの 64 ビットまたは 32 ビット バージョンの *librfc32.dll* を、*main.cmd* と共にご利用のコンテナーにアップロードします。 次に、セットアップ中にスクリプトによって SAP アセンブリが *%windir%\SysWow64* または *%windir%\System32* フォルダーにコピーされます。

      * *STORAGE* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Azure PowerShell をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップを使用すると、[ご利用の Azure Storage を管理するための Azure PowerShell コマンドレットまたはスクリプト](../storage/blobs/storage-quickstart-blobs-powershell.md)を実行する SSIS パッケージをデプロイして実行できるようになります。 
      
        *main.cmd*、サンプルの *AzurePowerShell.msi* (または最新バージョンを使用)、および *storage.ps1* をご利用のコンテナーにコピーします。 パッケージのテンプレートとして *PowerShell.dtsx* を使用します。 パッケージ テンプレートでは、修正可能な PowerShell スクリプト (*storage.ps1*) をダウンロードする [Azure BLOB のダウンロード タスク](/sql/integration-services/control-flow/azure-blob-download-task)と、各ノード上でスクリプトを実行する [プロセス実行タスク](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)が組み合わされています。

      * *TERADATA* フォルダー。これには、カスタム セットアップ スクリプト (*main.cmd*)、その関連ファイル (*install.cmd*)、およびインストーラー パッケージ ( *.msi*) が格納されています。 これらのファイルによって、ご利用の Azure SSIS IR Enterprise Edition の各ノード上に Teradata コネクター、Teradata Parallel Transporter (TPT) API、および ODBC ドライバーがインストールされます。 このセットアップでは、Teradata 接続マネージャー、ソース、および変換先を使用して、Teradata サーバーに接続できます。 
      
        まず、[Teradata Tools and Utilities 15.x の zip ファイルをダウンロード](http://partnerintelligence.teradata.com)し (たとえば、*TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*)、次にそれを、前に説明した *.cmd* および *.msi* ファイルと共にご利用のコンテナーにアップロードします。

      * *TLS 1.2* フォルダー。Azure-SSIS IR の各ノードで強力な暗号化およびセキュリティが強化されたネットワーク プロトコル (TLS 1.2) を使用するためのカスタム セットアップ スクリプト (*main.cmd*) が含まれています。 また、このスクリプトは古い SSL/TLS バージョンを無効にします。

      * *ZULU OPENJDK* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Zulu OpenJDK をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) と PowerShell ファイル (*install_openjdk.ps1*) が格納されています。 このセットアップでは、Azure Data Lake Store または柔軟なファイル コネクタを使用して、ORC および Parquet ファイルを処理できます。 詳細については、[Integration Services 用の Azure Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#dependency-on-java) に関するページを参照してください。 
      
        まず、[最新の Zulu OpenJDK をダウンロード](https://www.azul.com/downloads/zulu/zulu-windows/)し (たとえば、*zulu8.33.0.1-jdk8.0.192-win_x64.zip*)、次にそれを、*main.cmd* および *install_openjdk.ps1* と共にご利用のコンテナーにアップロードします。

        ![ユーザー シナリオ フォルダー内のフォルダー](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. これらの標準カスタム セットアップのサンプルを再利用するには、選択したフォルダーの内容をご利用のコンテナーへコピーします。

1. ADF UI で Azure-SSIS IR をプロビジョニングまたは再構成する場合は、 **[詳細設定]** ページの **[統合ランタイムのセットアップ]** ウィンドウにある **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ラインタイムをカスタマイズする\)** チェックボックスをオンにします。 次に、 **[カスタム セットアップ コンテナーの SAS URI]** テキスト ボックスに、ご利用のコンテナーの SAS URI を入力します。
   
1. Azure PowerShell を使って Azure SSIS IR をプロビジョニングまたは再構成する場合、Azure SSIS IR が既に開始/実行している場合はこれを停止し、コンテナーの SAS URI を `SetupScriptContainerSasUri` パラメーターの値として設定して `Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行した後に、Azure SSIS IR を起動します。

1. 標準カスタム セットアップが完了し、Azure SSIS IR が開始した後、ご利用のコンテナーの *main.cmd.log* フォルダー内ですべてのカスタム セットアップ ログを見つけることができます。 これには、*main.cmd* の標準出力とその他の実行ログが含まれます。

## <a name="next-steps"></a>次のステップ

- [Azure-SSIS IR の Enterprise Edition のセットアップ](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS IR の有料またはライセンス付きコンポーネントの開発](how-to-develop-azure-ssis-ir-licensed-components.md)
