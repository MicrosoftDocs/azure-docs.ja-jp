---
title: Orchestrator から Azure Automation (ベータ版) に移行する
description: この記事では、Orchestrator の Runbook と統合パックを Azure Automation に移行する方法を説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6ee4a09df0f95cb809db0e5c0e63d195ee5cfdff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896937"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Orchestrator から Azure Automation (ベータ版) に移行する

[System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) の Runbook は特に Orchestrator 用として作成された統合パックの活動に基づいているのに対し、Azure Automation の Runbook は Windows PowerShell に基づいています。 Azure Automation の[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) の外観は Orchestrator Runbook に似ており、アクティビティは PowerShell コマンドレット、子 Runbook、およびアセットで表されます。 Runbook 自体を変換するだけでなく、Runbook で使用される活動を含む統合パックを Windows PowerShell コマンドレットを含む統合モジュールに変換する必要もあります。 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) は Orchestrator などのローカル データセンターに Runbook を格納して実行します。使用する統合モジュールは Azure Automation と同じです。 Runbook コンバーターは、SMA ではサポートされていないグラフィカル Runbook に Orchestrator Runbook を変換します。 SMA に標準活動モジュールと System Center Orchestrator 統合モジュールをインストールできますが、手動で [Runbook を再作成](/system-center/sma/authoring-automation-runbooks)する必要があります。

## <a name="download-the-orchestrator-migration-toolkit"></a>Orchestrator 移行ツールキットのダウンロード

移行の最初の手順として、[System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323)をダウンロードします。 このツールキットには、Runbook を Orchestrator から Azure Automation に変換するために役立つツールが含まれています。  

## <a name="import-the-standard-activities-module"></a>標準活動モジュールのインポート

[標準活動モジュール](/system-center/orchestrator/standard-activities)を Azure Automation にインポートします。 このモジュールには、変換後のグラフィカル Runbook で使用できる、変換されたバージョンの Orchestrator 標準活動が含まれます。

## <a name="import-orchestrator-integration-modules"></a>Orchestrator 統合モジュールのインポート

Microsoft は、System Center のコンポーネントと他の製品を自動化する Runbook をビルドするための [統合パック](/previous-versions/system-center/packs/hh295851(v=technet.10)) を提供しています。 現在、これらの統合パックの一部は OIT に基づいていますが、既知の問題により、統合モジュールに変換できません。 System Center にアクセスする Runbook で使用する統合パック用に、 [System Center Orchestrator 統合モジュール](https://www.microsoft.com/download/details.aspx?id=49555)を Azure Automation にインポートします。 このパッケージには、変換されたバージョンの統合パックが含まれ、Azure Automation と Service Management Automation にインポートできるようになっています。  

## <a name="convert-integration-packs"></a>統合パックの変換

[Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) を使用して作成された統合パックを、[統合パック コンバーター](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard)を使用して PowerShell ベースの統合モジュールに変換します。この統合モジュールは、Azure Automation または Service Management Automation にインポートできます。 統合パック コンバーターを実行すると、ウィザードが表示され、統合パック (.oip) ファイルを選択できます。 次に、ウィザードに、その統合パックに含まれる活動が一覧表示され、移行する活動を選択できます。 ウィザードを完了すると、元の統合パックの各活動に対応するコマンドレットを含む統合モジュールが作成されます。

> [!NOTE]
> OIT を使用して作成されていない統合パックは、統合パック コンバーターを使用して変換できません。 現在このツールを使用して変換できない、Microsoft が提供する統合パックもいくつかあります。 このような統合パックを変換したバージョンは、 ダウンロード用に提供されているため、Azure Automation または Service Management Automation でインストールできます。

### <a name="parameters"></a>パラメーター

統合パック内の活動のプロパティは、その統合モジュールで対応するコマンドレットのパラメーターに変換されます。  Windows PowerShell コマンドレットには、すべてのコマンドレットで使用できる一連の [共通パラメーター](/powershell/module/microsoft.powershell.core/about/about_commonparameters) が含まれています。 たとえば、-Verbose パラメーターを使用すると、対象操作についての詳細情報を出力するコマンドレットが実行されます。  コマンドレットに、共通パラメーターと同じ名前のパラメーターを含めることはできません。 活動に共通パラメーターと同じ名前を持つプロパティが含まれる場合、対象パラメーターに別の名前を指定するように求めるプロンプトがウィザードによって表示されます。

### <a name="monitor-activities"></a>モニター活動

Orchestrator のモニター Runbook では、まず [モニター活動](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) から開始され、特定のイベントが呼び出されるのを待機しながら継続的に実行されます。 Azure Automation ではモニター Runbook がサポートされていないため、統合パックのモニター活動は変換されません。 代わりに、プレース ホルダー コマンドレットが、モニター活動用に統合モジュールに作成されます。  このコマンドレットには機能がありませんが、それを使用する変換後 Runbook がインストール可能になります。 この Runbook は、Azure Automation で実行することはできませんが、インストールが可能なので変更できます。

Orchestrator には、統合パックには含まれていないものの多数の Runbook で使用される、一連の [標準活動](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) が含まれています。  標準活動モジュールは、これらの各活動に対応するコマンドレットが含まれている統合モジュールです。 標準活動を使用する変換された Runbook をインポートする前に、Azure Automation にこの統合モジュールをインストールする必要があります。

標準活動モジュールのコマンドレットは、変換された Runbook をサポートするだけでなく、Orchestrator に精通しているユーザーが Azure Automation で新しい Runbook をビルドするために使用できます。 標準活動すべての機能はコマンドレットで実行できますが、動作方法が異なる場合があります。 変換後の標準活動モジュール内のコマンドレットは、対応する活動と同じように動作し、同じパラメーターを使用します。 これは Azure Automation Runbook に移行する際に役立ちます。

## <a name="convert-orchestrator-runbooks"></a>Orchestrator Runbook の変換

Orchestrator Runbook コンバーターは、Orchestrator Runbook を、Azure Automation にインポートできる[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) に変換します。 Runbook コンバーターは、変換を実行するコマンドレット `ConvertFrom-SCORunbook` を含む PowerShell モジュールとして実装されます。 コンバーターをインストールすると、このコマンドレットを読み込む PowerShell セッションへのショートカットが作成されます。   

Runbook を変換して Azure Automation にインポートする基本的な手順を次に示します。 コマンドレットの使用方法の詳細については、このセクションの後半で説明します。

1. Orchestrator から 1 つ以上の Runbook をエクスポートします。
2. Runbook のすべての活動の統合モジュールを取得します。
3. エクスポートされたファイルで Orchestrator Runbook を変換します。
4. ログの情報を見て、変換を確認して、必要な手動のタスクを判断します。
5. 変換後の Runbook を Azure Automation にインポートします。
6. Azure Automation で必要なアセットを作成します。
7. Azure Automation の Runbook を編集して必要な活動を変更します。

`ConvertFrom-SCORunbook` の構文は次のとおりです。

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - 変換する Runbook を含むエクスポート ファイルへのパス。
* Module - Runbook の活動を含む統合モジュールのコンマ区切りリスト。
* OutputFolder - 変換後のグラフィカル Runbook を作成するフォルダーへのパス。

次の例のコマンドは、**MyRunbooks.ois_export** というエクスポート ファイルの Runbook を変換します。  これらの Runbook では、Active Directory と Data Protection Manager の統合パックが使用されています。

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Runbook コンバーターのログファイルの使用

Runbook コンバーターでは、変換後の Runbook と同じ場所に次のログ ファイルが作成されます。  ファイルが既に存在する場合は、最後に行われた変換の情報で上書きされます。

| ファイル | 内容 |
|:--- |:--- |
| Runbook Converter - Progress.log |変換が正常に完了した各活動の情報や、変換されなかった各活動の警告など、変換の詳細な手順。 |
| Runbook Converter - Summary.log |警告や、実行が必要なフォローアップ タスク (例: 変換後の Runbook に必要な変数の作成) など、最後に行われた変換の概要。 |

### <a name="export-runbooks-from-orchestrator"></a>Orchestrator からの Runbook のエクスポート

Runbook コンバーターは、1 つ以上の Runbook が含まれる、Orchestrator からのエクスポート ファイルを扱います。  これにより、各 Orchestrator Runbook に対応する Azure Automation Runbook がエクスポート ファイルに作成されます。  

Orchestrator から Runbook をエクスポートするには、Runbook Designer で Runbook の名前を右クリックして **[エクスポート]** を選択します。  フォルダー内のすべての Runbook をエクスポートするには、フォルダーの名前を右クリックして **[エクスポート]** をクリックします。

### <a name="convert-runbook-activities"></a>Runbook 活動の変換

Runbook コンバーターは、Orchestrator Runbook の各活動を Azure Automation の対応する活動に変換します。  変換できない活動の場合は、プレースホルダー活動が警告テキストと共に Runbook に作成されます。  変換された Runbook を Azure Automation にインポートした後、これらの活動のいずれかを、必要な機能を実行する有効な活動に置き換える必要があります。

標準活動モジュールの Orchestrator 活動は変換されます。 ただし、Orchestrator 標準活動には、このモジュールに含まれていないため変換されないものもあります。 たとえば、`Send Platform Event` は、このイベントが Orchestrator に固有であるため、Azure Automation に対応する活動はありません。

[モニター活動](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) は、Azure Automation に相当する活動がないため、変換されません。 変換後の統合パック のモニター活動は例外で、プレースホルダー活動に変換されます。

`modules` パラメーターを使用して統合モジュールへのパスを指定すると、変換後の統合パックの活動が変換されます。 System Center 統合パックの場合、 System Center Orchestrator 統合モジュールを使用できます。

### <a name="manage-orchestrator-resources"></a>Orchestrator のリソースの管理

Runbook コンバーターで変換されるのは Runbook のみで、カウンター、変数、接続など、その他の Orchestrator リソースは変換されません。  Azure Automation では、カウンターがサポートされていません。  変数と接続はサポートされていますが、手動で作成する必要があります。 ログ ファイルでは、Runbook にこのようなリソースが必要かどうかが示され、変換後の Runbook が正常に動作するために Azure Automation に作成する必要がある対応リソースが指定されます。

たとえば、Runbook では、変数を使用して、活動の特定の値を設定することがあります。  変換後の Runbook は、活動を変換し、Orchestrator の変数と同じ名前の変数アセットを Azure Automation で指定します。 このアクションは、変換後に作成される **Runbook Converter - Summary.log** ファイルに記録されます。 Runbook を使用する前に、この変数アセットを Azure Automation で手動で作成する必要があります。

### <a name="work-with-orchestrator-input-parameters"></a>Orchestrator 入力パラメーターの操作

Orchestrator の Runbook は、`Initialize Data` 活動で入力パラメーターを受け取ります。  変換対象の Runbook にこのアクティビティが含まれている場合、Azure Automation Runbook の [入力パラメーター](automation-graphical-authoring-intro.md#handle-runbook-input) は、アクティビティのパラメーターごとに作成されます。  変換後の Runbook では、各パラメーターを取得して返す [ワークフロー スクリプト コントロール](automation-graphical-authoring-intro.md#use-activities) 活動が作成されます。 入力パラメーターを使用する、Runbook のすべての活動は、この活動からの出力を参照します。

この方法が使用される理由は、Orchestrator Runbook の機能を最もうまく反映するためです。  新しいグラフィカル Runbook の活動は、Runbook の入力データ ソースを使用して入力パラメーターを直接参照します。

### <a name="invoke-runbook-activity"></a>Runbook 活動の呼び出し

Orchestrator の Runbook は、`Invoke Runbook` 活動を使用してその他の Runbook を開始します。 変換対象となる Runbook にこの活動が含まれていて、`Wait for completion` オプションが設定されている場合は、変換後の Runbook にこの活動用の Runbook 活動が作成されます。  `Wait for completion` オプションが設定されていない場合は、[Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) を使用して Runbook を開始するワークフロー スクリプト活動が作成されます。 変換後の Runbook を Azure Automation にインポートした後、活動で指定された情報を使用して、この活動を変更する必要があります。

## <a name="create-orchestrator-assets"></a>Orchestrator アセットの作成

Runbook コンバーターでは、Orchestrator アセットは変換されません。 Azure Automation で、必要な Orchestrator アセットを手動で作成する必要があります。

## <a name="configure-hybrid-runbook-worker"></a>Hybrid Runbook Worker の構成

Orchestrator では、Runbook はデータベース サーバー上に格納され、Runbook サーバーで実行されます。どちらのサーバーもローカル データセンター上にあります。 Azure Automation の Runbook は Azure クラウドに格納され、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) を使用してローカル データセンターで実行できます。 Orchestrator から変換された Runbook を実行するようにワーカーを構成してください。これらの Runbook はローカル サーバー上で実行され、ローカル リソースにアクセスするように設計されているためです。

## <a name="related-articles"></a>関連記事

* Orchestrator の詳細については、[System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) に関するページを参照してください。
* サービスの管理を自動化する方法の詳細については、「[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12))」を参照してください。
* Orchestrator 活動の詳細については、[Orchestrator の標準活動](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))に関するページを参照してください。
* Orchestrator 移行ツールキットを入手するには、[System Center Orchestrator Migration Toolkit のダウンロード](https://www.microsoft.com/download/details.aspx?id=47323)に関するページを参照してください。
* Azure Automation の Hybrid Runbook Worker の概要については、「[Hybrid Runbook Worker の概要](automation-hybrid-runbook-worker.md)」を参照してください。
