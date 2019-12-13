---
title: Orchestrator から Azure Automation への移行
description: System Center Orchestrator の Runbook と統合パックを Azure Automation に移行する方法を説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b34554798130d9741318e0f518c32a41f82a17e3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849668"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Orchestrator から Azure Automation (ベータ版) へ移行する
[System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) の Runbook は特に Orchestrator 用として作成された統合パックの活動に基づいているのに対し、Azure Automation の Runbook は Windows PowerShell に基づいています。  [グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) の外観は Orchestrator Runbook に似ており、アクティビティは PowerShell コマンドレット、子 Runbook、およびアセットで表されます。

[System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) には、Runbook を Orchestrator から Azure Automation に変換するために役立つツールが含まれています。  Runbook 自体を変換するだけでなく、Runbook で使用される活動を含む統合パックを Windows PowerShell コマンドレットを含む統合モジュールに変換する必要もあります。  

Orchestrator Runbook を Azure Automation に変換するための基本的なプロセスを次に示します。  これらの各手順の詳細については、以降のセクションで詳しく説明します。

1. この記事で説明されているツールとモジュールが含まれる [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) をダウンロードします。
2. [標準活動モジュール](#standard-activities-module) を Azure Automation にインポートします。  このモジュールには、変換後の Runbook が使用する可能性がある、変換されたバージョンの Orchestrator 標準活動が含まれます。
3. System Center にアクセスする Runbook で使用する統合パック用に、 [System Center Orchestrator 統合モジュール](#system-center-orchestrator-integration-modules) を Azure Automation にインポートします。
4. [統合パック コンバーター](#integration-pack-converter) を使用してカスタムとサード パーティ製の統合パックを変換し、Azure Automation にインポートします。
5. [Runbook コンバーター](#runbook-converter) を使用して Orchestrator Runbook を変換し、Azure Automation にインストールします。
6. Azure Automation で必要な Orchestrator のアセットを手動で作成します。Runbook コンバーターではこれらのリソースが変換されません。
7. ローカル データ センターで、ローカル リソースにアクセスする変換後の Runbook を実行するように [Hybrid Runbook Worker](#hybrid-runbook-worker) を構成します。

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) は Orchestrator などのローカル データセンターに Runbook を格納して実行します。使用する統合モジュールは Azure Automation と同じです。 [Runbook コンバーター](#runbook-converter)は、SMA ではサポートされていないグラフィカル Runbook に Orchestrator Runbook を変換します。  SMA に[標準活動モジュール](#standard-activities-module)と [System Center Orchestrator 統合モジュール](#system-center-orchestrator-integration-modules)をインストールできますが、手動で [Runbook を再作成](https://technet.microsoft.com/library/dn469262.aspx)する必要があります。

## <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker
Orchestrator の Runbook はデータベース サーバー上に格納され、Runbook サーバーで実行されます。どちらのサーバーもローカル データセンター上にあります。  Azure Automation の Runbook は Azure クラウドに格納され、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) を使用してローカル データセンターで実行できます。  これが、Orchestrator から変換された Runbook を実行する通常の方法です。これらの Runbook はローカル サーバー上で実行されるように設計されているためです。

## <a name="integration-pack-converter"></a>統合パック コンバーター
統合パック コンバーターは、[Orchestrator Integration Toolkit](https://technet.microsoft.com/library/hh855853.aspx) (OIT) を使用して作成された統合パックを、Windows PowerShell に基づく統合モジュールに変換します。この統合モジュールは、Azure Automation または Service Management Automation にインポートできます。  

統合パック コンバーターを実行すると、ウィザードが表示され、統合パック (.oip) ファイルを選択できます。  次に、ウィザードに、その統合パックに含まれる活動が一覧表示され、移行する活動を選択できます。  ウィザードを完了すると、元の統合パックの各活動に対応するコマンドレットを含む統合モジュールが作成されます。

### <a name="parameters"></a>parameters
統合パック内の活動のプロパティは、その統合モジュールで対応するコマンドレットのパラメーターに変換されます。  Windows PowerShell コマンドレットには、すべてのコマンドレットで使用できる一連の [共通パラメーター](https://technet.microsoft.com/library/hh847884.aspx) が含まれています。  たとえば、-Verbose パラメーターを使用すると、対象操作についての詳細情報を出力するコマンドレットが実行されます。  コマンドレットに、共通パラメーターと同じ名前のパラメーターを含めることはできません。  活動に共通パラメーターと同じ名前を持つプロパティが含まれる場合、対象パラメーターに別の名前を指定するように求めるプロンプトがウィザードによって表示されます。

### <a name="monitor-activities"></a>モニター活動
Orchestrator のモニター Runbook では、まず [モニター活動](https://technet.microsoft.com/library/hh403827.aspx) から開始され、特定のイベントが呼び出されるのを待機しながら継続的に実行されます。  Azure Automation ではモニター Runbook がサポートされていないため、統合パックのモニター活動は変換されません。  代わりに、プレース ホルダー コマンドレットが、モニター活動用に統合モジュールに作成されます。  このコマンドレットには機能がありませんが、それを使用する変換後 Runbook がインストール可能になります。  この Runbook は、Azure Automation で実行することはできませんが、インストールが可能なので変更できます。

### <a name="integration-packs-that-cannot-be-converted"></a>変換できない統合パック
OIT を使用して作成されていない統合パックは、統合パック コンバーターを使用して変換できません。 現在このツールを使用して変換できない、Microsoft が提供する統合パックもいくつかあります。  このような統合パックを変換したバージョンは、 [こちらからダウンロード](#system-center-orchestrator-integration-modules) できるため、Azure Automation または Service Management Automation でインストールできます。

## <a name="standard-activities-module"></a>標準活動モジュール
Orchestrator には、統合パックには含まれていないものの多数の Runbook で使用される、一連の [標準活動](https://technet.microsoft.com/library/hh403832.aspx) が含まれています。  標準活動モジュールは、これらの各活動に対応するコマンドレットが含まれている統合モジュールです。  標準活動を使用する変換された Runbook をインポートする前に、Azure Automation にこの統合モジュールをインストールする必要があります。

標準活動モジュールのコマンドレットは、変換された Runbook をサポートするだけでなく、Orchestrator に精通しているユーザーが Azure Automation で新しい Runbook をビルドするために使用できます。  標準活動すべての機能はコマンドレットで実行できますが、動作方法が異なる場合があります。  変換後の標準活動モジュール内のコマンドレットは、対応する活動と同じように動作し、同じパラメーターを使用します。  これによって、既存の Orchestrator Runbook の作成者は Azure Automation の Runbook に移行するのが容易になります。

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator 統合モジュール
Microsoft は、System Center のコンポーネントと他の製品を自動化する Runbook をビルドするための [統合パック](https://technet.microsoft.com/library/hh295851.aspx) を提供しています。  現在、これらの統合パックの一部は OIT に基づいていますが、既知の問題により、統合モジュールに変換できません。  [System Center Orchestrator 統合モジュール](https://www.microsoft.com/download/details.aspx?id=49555) には、変換されたバージョンの統合パックが含まれ、Azure Automation と Service Management Automation にインポートできるようになっています。  

このツールの RTM バージョンにより、統合パック コンバーターを使用して変換できる、OIT に基づく統合パックの更新版が公開されます。  OIT に基づいていない統合パックの活動を使用する Runbook を変換する場合に役立つガイダンスも提供されます。

## <a name="runbook-converter"></a>Runbook コンバーター
Runbook コンバーターは、Orchestrator Runbook を、Azure Automation にインポートできる[グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) に変換します。  

Runbook コンバーターは、変換を実行する **ConvertFrom-SCORunbook** と呼ばれるコマンドレットを含む PowerShell モジュールとして実装されます。  ツールをインストールすると、このコマンドレットを読み込む PowerShell セッションへのショートカットが作成されます。   

Orchestrator Runbook を変換して Azure Automation にインポートするための基本的なプロセスを次に示します。  以降のセクションでは、ツールの使用と変換後の Runbook の操作についてさらに詳しく説明します。

1. Orchestrator から 1 つ以上の Runbook をエクスポートします。
2. Runbook のすべての活動の統合モジュールを取得します。
3. エクスポートされたファイルで Orchestrator Runbook を変換します。
4. ログの情報を見て、変換を確認して、必要な手動のタスクを判断します。
5. 変換後の Runbook を Azure Automation にインポートします。
6. Azure Automation で必要なアセットを作成します。
7. Azure Automation の Runbook を編集して必要な活動を変更します。

### <a name="using-runbook-converter"></a>Runbook コンバーターの使用
**ConvertFrom-SCORunbook** の構文は次のとおりです。

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

### <a name="log-files"></a>ログ ファイル
Runbook コンバーターでは、変換後の Runbook と同じ場所に次のログ ファイルが作成されます。  ファイルが既に存在する場合は、最後に行われた変換の情報で上書きされます。

| ファイル | 目次 |
|:--- |:--- |
| Runbook Converter - Progress.log |変換が正常に完了した各活動の情報や、変換されなかった各活動の警告など、変換の詳細な手順。 |
| Runbook Converter - Summary.log |警告や、実行が必要なフォローアップ タスク (例: 変換後の Runbook に必要な変数の作成) など、最後に行われた変換の概要。 |

### <a name="exporting-runbooks-from-orchestrator"></a>Orchestrator からの Runbook のエクスポート
Runbook コンバーターは、1 つ以上の Runbook が含まれる、Orchestrator からのエクスポート ファイルを扱います。  これにより、各 Orchestrator Runbook に対応する Azure Automation Runbook がエクスポート ファイルに作成されます。  

Orchestrator から Runbook をエクスポートするには、Runbook Designer で Runbook の名前を右クリックして **[エクスポート]** を選択します。  フォルダー内のすべての Runbook をエクスポートするには、フォルダーの名前を右クリックして **[エクスポート]** をクリックします。

### <a name="runbook-activities"></a>Runbook アクティビティ
Runbook コンバーターは、Orchestrator Runbook の各活動を Azure Automation の対応する活動に変換します。  変換できない活動の場合は、プレースホルダー活動が警告テキストと共に Runbook に作成されます。  変換された Runbook を Azure Automation にインポートした後、これらの活動のいずれかを、必要な機能を実行する有効な活動に置き換える必要があります。

[Standard Activities モジュール](#standard-activities-module) の Orchestrator 活動は変換されます。  ただし、Orchestrator 標準活動には、このモジュールに含まれていないため変換されないものもあります。  たとえば、 **プラットフォーム イベントの送信** は、このイベントが Orchestrator に固有であるため、Azure Automation に対応する活動はありません。

[モニター活動](https://technet.microsoft.com/library/hh403827.aspx) は、Azure Automation に相当する活動がないため、変換されません。  [変換後の統合パック](#integration-pack-converter) のモニター活動は例外で、プレースホルダー活動に変換されます。

[modules](#integration-pack-converter) パラメーターを使用して統合モジュールへのパスを指定すると、 **変換後の統合パック** の活動が変換されます。  System Center 統合パックの場合、 [System Center Orchestrator 統合モジュール](#system-center-orchestrator-integration-modules)を使用できます。

### <a name="orchestrator-resources"></a>Orchestrator のリソース
Runbook コンバーターで変換されるのは Runbook のみで、カウンター、変数、接続など、その他の Orchestrator リソースは変換されません。  Azure Automation では、カウンターがサポートされていません。  変数と接続はサポートされていますが、手動で作成する必要があります。  ログ ファイルでは、Runbook にこのようなリソースが必要かどうかが示され、変換後の Runbook が正常に動作するために Azure Automation に作成する必要がある対応リソースが指定されます。

たとえば、Runbook では、変数を使用して、活動の特定の値を設定することがあります。  変換後の Runbook は、活動を変換し、Orchestrator の変数と同じ名前の変数アセットを Azure Automation で指定します。  これは、変換後に作成される **Runbook Converter - Summary.log** ファイルに記録されます。  Runbook を使用する前に、この変数アセットを Azure Automation で手動で作成する必要があります。

### <a name="input-parameters"></a>入力パラメーター
Orchestrator の Runbook は、 **データの初期化** 活動で入力パラメーターを受け取ります。  変換対象の Runbook にこのアクティビティが含まれている場合、Azure Automation Runbook の [入力パラメーター](automation-graphical-authoring-intro.md#runbook-input-and-output) は、アクティビティのパラメーターごとに作成されます。  変換後の Runbook では、各パラメーターを取得して返す [ワークフロー スクリプト コントロール](automation-graphical-authoring-intro.md#activities) 活動が作成されます。  入力パラメーターを使用する、Runbook のすべての活動は、この活動からの出力を参照します。

この方法が使用される理由は、Orchestrator Runbook の機能を最もうまく反映するためです。  新しいグラフィカル Runbook の活動は、Runbook の入力データ ソースを使用して入力パラメーターを直接参照します。

### <a name="invoke-runbook-activity"></a>Runbook 活動の呼び出し
Orchestrator の Runbook は、 **Runbook の呼び出し** 活動を使用してその他の Runbook を開始します。 変換対象となる Runbook にこの活動が含まれていて、 **[実行が完了するまで待つ]** オプションが設定されている場合は、変換後の Runbook にこの活動用の Runbook 活動が作成されます。  **[実行が完了するまで待つ]** オプションが設定されていない場合は、**Start-AzureAutomationRunbook** を使用して Runbook を開始するワークフロー スクリプト活動が作成されます。  変換後の Runbook を Azure Automation にインポートした後、活動で指定された情報を使用して、この活動を変更する必要があります。

## <a name="related-articles"></a>関連記事
* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Orchestrator 標準活動](https://technet.microsoft.com/library/hh403832.aspx)
* [System Center Orchestrator Migration Toolkit のダウンロード](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

