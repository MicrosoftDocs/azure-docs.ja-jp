---
title: Azure-SSIS 統合ランタイムの設定のカスタマイズ
description: この記事では、Azure SSIS 統合ランタイムのカスタム セットアップ インターフェイスを使用して、追加のコンポーネントをインストールしたり、設定を変更したりする方法について説明します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 1/25/2019
ms.openlocfilehash: d80ff102648deebf63cc0752b2980274cb90aeb9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922892"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムの設定のカスタマイズ

Azure SSIS 統合ランタイムのカスタム セットアップ インターフェイスは、お使いの Azure SSIS IR のプロビジョニングや再構成の間に、独自のセットアップ手順を追加するためのインターフェイスを提供します。 カスタム セットアップでは、既定の動作の構成または環境を変更したり (追加の Windows サービスを開始する、ファイル共有のアクセス資格情報を永続化するなど)、Azure SSIS IR の各ノードに追加のコンポーネントをインストールしたり (アセンブリ、ドライバー、または拡張機能など) することが可能です。

スクリプトとその関連ファイルを準備して、それらを Azure Storage アカウントの BLOB コンテナーにアップロードすることで、カスタム セットアップを構成します。 お使いの Azure SSIS IR をプロビジョニングまたは再構成するとき、コンテナーに対して Shared Access Signature (SAS) の Uniform Resource Identifier (URI) を提供します。 Azure SSIS IR の各ノードは、その後、スクリプトとその関連ファイルをコンテナーからダウンロードし、昇格された特権を使用してカスタム セットアップを実行します。 カスタム セットアップが完了すると、各ノードは、実行の標準出力およびその他のログをコンテナーにアップロードします。

無料 (ライセンスなし) コンポーネントおよび有料 (ライセンスあり) コンポーネントの両方をインストールできます。 ISV の場合は、[Azure SSIS IR の有料 (ライセンスあり) コンポーネントを開発する方法](how-to-develop-azure-ssis-ir-licensed-components.md)に関するページをご覧ください。

> [!IMPORTANT]
> Azure-SSIS IR の v2 シリーズ ノードはカスタムセットアップには適していないため、代わりに v3 シリーズ ノードを使用してください。  既に v2 シリーズ ノードを使用している場合は、できるだけ早く v3 シリーズ ノードを使用するように切り替えてください。

## <a name="current-limitations"></a>現時点での制限事項

-   `gacutil.exe` を使用してアセンブリをグローバル アセンブリ キャッシュ (GAC) にインストールする場合は、カスタム セットアップの一部として `gacutil.exe` を指定するか、パブリック プレビュー コンテナー内に提供されているコピーを使用する必要があります。

-   スクリプト内でサブフォルダーを参照する場合、`msiexec.exe` はルート フォルダーを参照するための `.\` 表記をサポートしていません。 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` の代わりに `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` のようなコマンドを使います。

-   カスタム セットアップを備えた Azure SSIS IR を仮想ネットワークに参加させる必要がある場合は、Azure Resource Manager 仮想ネットワークのみがサポートされます。 クラシック仮想ネットワークはサポートされません。

-   Azure-SSIS IR では現在のところ、管理共有をご利用いただけません。

-   Azure-SSIS IR では、IBM iSeries Access ODBC ドライバーはサポートされません。 カスタム セットアップ中にインストール エラーが表示される場合があります。 サポートについては、IBM のサポートにお問い合わせください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure SSIS IR をカスタマイズするには、以下のものが必要です。

-   [Azure サブスクリプション](https://azure.microsoft.com/)

-   [Azure SQL Database またはマネージド インスタンス サーバー](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [お使いの Azure SSIS IR のプロビジョニング](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure Storage アカウント](https://azure.microsoft.com/services/storage/)。 カスタム セットアップの場合は、カスタム セットアップ スクリプトとその関連ファイルをアップロードして BLOB コンテナーに格納します。 また、カスタム セットアップ処理では、同じ BLOB コンテナーに実行ログもアップロードします。

## <a name="instructions"></a>Instructions

1. [Azure PowerShell](/powershell/azure/install-az-ps)をダウンロードしてインストールします。

1. カスタム セットアップ スクリプトとその関連ファイル (たとえば、.bat、.cmd、.exe、.dll、.msi、または .ps1 ファイル) を準備します。

   1.  カスタム セットアップのエントリ ポイントである `main.cmd` という名前のスクリプト ファイルを保持している必要があります。

   1.  スクリプトを自動的に実行できることを確認する必要があります。まずは、ローカル コンピューター上でスクリプトをテストすることをお勧めします。

   1.  他のツール (`msiexec.exe` など) で生成された追加ログをコンテナーにアップロードする場合は、事前定義された環境変数 `CUSTOM_SETUP_SCRIPT_LOG_DIR` をお使いのスクリプトのログ フォルダー として指定します (たとえば、`msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`)。

1. [Azure Storage Explorer](https://storageexplorer.com/)をダウンロードし、インストールして、起動します。

   1. **[(Local and Attached)]\((ローカルおよび接続済み)\)** で、 **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure storage]\(Azure Storage へ接続\)** を選択します。

      ![Azure Storage へ接続](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択して、 **[次へ]** を選択します。

      ![ストレージ アカウントの名前とキーを使用する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. お使いの Azure Storage アカウント名とキーを入力し、 **[次へ]** を選択してから **[接続]** を選択します。

      ![ストレージ アカウントの名前とキーを提供する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. 接続された Azure Storage アカウントで、 **[BLOB コンテナー]** を右クリックし、 **[BLOB コンテナーの作成]** を選択して、新しいコンテナーに名前を付けます。

      ![BLOB コンテナーを作成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. 新しいコンテナーを選択し、カスタム セットアップ スクリプトとその関連ファイルをアップロードします。 任意のフォルダーではなく、必ずコンテナーの最上位に `main.cmd` をアップロードしてください。 後で Azure-SSIS IR にダウンロードする時間を短縮できるように、コンテナーには必要なカスタム セットアップ ファイルのみが含まれていることを確認してください。 カスタム セットアップの最大期間は現在 45 分でタイムアウトとなります。これには、コンテナーからすべてのファイルをダウンロードし、Azure SSIS IR のインストールにかかる時間が含まれます。 長い期間が必要な場合は、サポート チケットを発行してください。

      ![BLOB コンテナーにファイルをアップロードする](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. コンテナーを右クリックして、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

      ![コンテナーの Shared Access Signature を取得する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. 十分に長い有効期間と [読み取り]、[書き込み]、および [リスト] のアクセス許可を備えたコンテナーの SAS URI を作成します。 お使いの Azure SSIS IR の任意のノードが再イメージ化/再開されるときは常に、カスタム セットアップ スクリプトとその関連ファイルをダウンロードして実行するために SAS URI が必要になります。 セットアップ実行ログをアップロードするには、書き込みアクセス許可が必要です。

      > [!IMPORTANT]
      > Azure-SSIS IR の作成から削除までそのライフサイクル全体を通して、SAS URI の有効期限切れを防止すると共に、カスタム セットアップ リソースが常に利用可能な状態となるように徹底してください。当該期間に Azure-SSIS IR を定期的に停止/開始する場合は特に注意が必要です。

      ![コンテナーの Shared Access Signature を生成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. コンテナーの SAS URI をコピーして保存します。

      ![Shared Access Signature をコピーして保存する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Data Factory UI を使用して AZURE-SSIS IR をプロビジョニングまたは再構成する場合、Azure-SSIS IR を起動する前に、 **[詳細設定]** パネルの適切なフィールドにコンテナーの SAS URI を入力します。

      ![Shared Access Signature を入力する](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      PowerShell を使って Azure SSIS IR をプロビジョニングまたは再構成する場合、Azure SSIS IR を起動する前に、コンテナーの SAS URI を新しい `SetupScriptContainerSasUri` パラメーターの値として設定して `Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行します。 例:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. カスタム セットアップが完了してお使いの Azure SSIS IR を起動した後、ストレージ コンテナーの `main.cmd.log` フォルダー内で `main.cmd` の標準出力とその他の実行ログを見つけることができます。

1. 他のカスタム セットアップの例を確認するには、Azure Storage Explorer を使ってパブリック プレビュー コンテナーに接続します。

   a.  **[(Local and Attached)]\((ローカルおよび接続済み)\)** で、 **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure storage]\(Azure Storage へ接続\)** を選択し、 **[Use a connection string or a shared access signature URI]\(接続文字列または Shared Access Signature URI を使用する\)** を選択して、 **[次へ]** を選択します。

      ![Shared Access Signature を使って Azure Storage に接続する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  **[Use a SAS URI]\(SAS URI を使用する\)** を選択し、パブリック プレビュー コンテナーに対して以下の SAS URI を入力します。 **[次へ]** を選択して、 **[接続]** を選択します。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![コンテナーに対して Shared Access Signature を提供する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 接続されたパブリック プレビュー コンテナーを選択して、`CustomSetupScript` フォルダーをダブルクリックします。 このフォルダーには、次のアイテムが含まれています。

      1. `Sample` フォルダー。Azure SSIS IR の各ノード上に基本タスクをインストールするための 1 個のカスタム セットアップが格納されています。 タスクは何も行いませんが、数秒間スリープ状態になります。 フォルダーには、`gacutil` フォルダーも含まれています。このフォルダーのコンテンツ全体 (`gacutil.exe`、`gacutil.exe.config`、`1033\gacutlrc.dll`) をそのままご利用のコンテナーにコピーすることができます。 加えて、`main.cmd` には、ファイル共有のアクセス資格情報を永続化するためのコメントが含まれています。

      1. `UserScenarios` フォルダー。実際のユーザー シナリオに対応する複数のカスタム セットアップが格納されています。

   ![パブリック プレビュー コンテナーの内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. `UserScenarios` フォルダーをダブルクリックします。 このフォルダーには、次のアイテムが含まれています。

      1. `.NET FRAMEWORK 3.5` フォルダー。Azure-SSIS IR の各ノードのカスタム コンポーネントに必要となる可能性がある、.NET Framework の以前のバージョンをインストールするためのカスタム設定が格納されています。

      1. `BCP` フォルダー。Azure SSIS IR の各ノード上に、一括コピー プログラム (`bcp`) を含む SQL Server コマンドライン ユーティリティ (`MsSqlCmdLnUtils.msi`) をインストールするための 1 個のカスタム セットアップが格納されています。

      1. `EXCEL` フォルダー。Azure SSIS IR の各ノード上にオープンソース アセンブリ (`DocumentFormat.OpenXml.dll`、`ExcelDataReader.DataSet.dll`、および `ExcelDataReader.dll`) をインストールするための 1 個のカスタム セットアップが格納されています。

      1. `ORACLE ENTERPRISE` フォルダー。Azure SSIS IR Enterprise Edition の各ノードに Oracle コネクタおよび OCI ドライバーをインストールするための 1 個のカスタム セットアップ スクリプト (`main.cmd`) とサイレント インストール構成ファイル (`client.rsp`) が格納されています。 このセットアップによって、Oracle 接続マネージャー、接続元、および接続先を使用できるようになります。 まず、Oracle 用 Microsoft Connectors v5.0 (`AttunitySSISOraAdaptersSetup.msi` と `AttunitySSISOraAdaptersSetup64.msi`) を[Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=55179)から、および最新の Oracle クライアント (`winx64_12102_client.zip` など) を [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) からダウンロードし、次に `main.cmd` と `client.rsp` と共にそれらをすべてまとめてコンテナーにアップロードします。 TNS を使用して Oracle に接続する場合、`tnsnames.ora` をダウンロードし、編集し、コンテナーにアップロードする必要があります。そのため、セットアップ時にこのファイルを Oracle インストール フォルダーにコピーできます。

      1. `ORACLE STANDARD ADO.NET` フォルダー。Azure SSIS IR の各ノード上に Oracle ODP.NET ドライバーをインストールするための 1 個のカスタム セットアップ スクリプト (`main.cmd`) が格納されています。 このセットアップによって、ADO.NET 接続マネージャー、接続元、および接続先を使用できるようになります。 最初に、[Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) から最新の Oracle ODP.NET ドライバーを、たとえば、`ODP.NET_Managed_ODAC122cR1.zip` をダウンロードし、`main.cmd` と共にコンテナーにアップロードします。
       
      1. `ORACLE STANDARD ODBC` フォルダー。Azure-SSIS IR の各ノード上に Oracle ODBC ドライバーをインストールし、DSN を構成するためのカスタム セットアップ スクリプト (`main.cmd`) が格納されています。 このセットアップでは、ODBC Connection Manager/Source/Destination または Power Query Connection Manager/Source と ODBC データ ソースの種類を使用して Oracle サーバーに接続できます。 最初に、最新の Oracle Instant Client (Basic Package または Basic Lite Package) と ODBC Package をダウンロードします。たとえば、64 ビット パッケージを[こちら](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)から (Basic Package: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`、Basic Lite Package: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`、ODBC Package: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`)、32 ビット パッケージを[こちら](https://www.oracle.com/technetwork/topics/winsoft-085727.html)から (Basic Package: `instantclient-basic-nt-18.3.0.0.0dbru.zip`、Basic Lite Package: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`、ODBC Package: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) ダウンロードした後、`main.cmd` を使用して、コンテナーにまとめてアップロードします。

      1. `SAP BW` フォルダー。Azure SSIS IR Enterprise Edition の各ノードに SAP .NET コネクター アセンブリ (`librfc32.dll`) をインストールするための 1 個のカスタム セットアップ スクリプト (`main.cmd`) が格納されています。 このセットアップによって、SAP BW 接続マネージャー、接続元、および接続先を使用できるようになります。 最初に、64 ビットまたは 32 ビットバージョンの `librfc32.dll`を `main.cmd`と共に、 SAP インストール フォルダーからコンテナーにアップロードします。 その後、セットアップ中に、スクリプトによって SAP アセンブリが `%windir%\SysWow64` または `%windir%\System32` フォルダーにコピーされます。

      1. `STORAGE` フォルダー。Azure SSIS IR の各ノード上に Azure PowerShell をインストールするための 1 個のカスタム セットアップが格納されています。 このセットアップによって、[Azure Storage アカウントを操作するための PowerShell スクリプト](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)を実行する SSIS パッケージをデプロイして実行できるようになります。 `main.cmd`、サンプルの `AzurePowerShell.msi` (または最新バージョンをインストールする)、および `storage.ps1` をお使いのコンテナーにコピーします。 パッケージのテンプレートとして PowerShell.dtsx を使用します。 パッケージ テンプレートでは、`storage.ps1` を修正可能な PowerShell スクリプトとしてダウンロードする [Azure BLOB のダウンロード タスク](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)と、各ノード上でスクリプトを実行する[プロセス実行タスク](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)を結合しています。

      1. `TERADATA` フォルダー。カスタム セットアップ スクリプト (`main.cmd`)、その関連ファイル (`install.cmd`)、およびインストーラー パッケージ (`.msi`) が格納されています。 これらのファイルは、Azure SSIS IR Enterprise Edition の各ノード上に Teradata コネクター、TPT API、ODBC ドライバーをインストールします。 このセットアップによって、Teradata 接続マネージャー、接続元、および接続先を使用できるようになります。 最初に、Teradata Tools and Utilities (TTU) 15.x の zip ファイル (たとえば、`TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) を [Teradata](http://partnerintelligence.teradata.com) からダウンロードしてから、上述の `.cmd` および `.msi` ファイルと共にコンテナーへアップロードします。

   ![ユーザー シナリオ フォルダー内のフォルダー](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. これらのカスタム セットアップのサンプルを試行するには、選択したフォルダーからコンテナーへコンテンツをコピーして貼り付けます。 PowerShell を使って Azure SSIS IR をプロビジョニングまたは再構成する場合、コンテナーの SAS URI を新しい `SetupScriptContainerSasUri` パラメーターの値として設定して `Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行します。

## <a name="next-steps"></a>次の手順

-   [Azure SSIS 統合ランタイムの Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Azure SSIS 統合ランタイムの有料 (ライセンスあり) カスタム コンポーネントを開発する方法](how-to-develop-azure-ssis-ir-licensed-components.md)
