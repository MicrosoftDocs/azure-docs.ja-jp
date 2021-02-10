---
title: Azure Automation での Runbook の実行
description: この記事では、Azure Automation における Runbook の処理の概要を示します。
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 71273c456b14fa4ea289e2a48d441de99ce8a4b1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053909"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation での Runbook の実行

Azure Automation でのプロセス自動化によって、PowerShell、PowerShell ワークフロー、グラフィカル Runbook を作成し、管理することができます。 詳細については、[Azure Automation Runbook](automation-runbook-types.md) に関するページを参照してください。 

Runbook は、Automation によって、その内部で定義されているロジックに基づいて実行されます。 Runbook は、中断された場合、先頭から再開されます。 この動作では、一時的な問題が発生した場合の再起動をサポートするように Runbook を作成する必要があります。

Azure Automation で Runbook を開始すると、ジョブが作成されます。ジョブは Runbook の単一の実行インスタンスです。 ジョブごとに、Azure サブスクリプションに接続することによって Azure リソースにアクセスします。 ジョブでデータ センター内のリソースにアクセスできるのは、それらのリソースがパブリック クラウドからアクセスできる場合に限られます。

Azure Automation では、Runbook の実行中に各ジョブを実行するための worker が割り当てられます。 ワーカーは多数の Azure アカウントで共有されますが、異なる Automation アカウントからのジョブは互いに分離されます。 どの worker がジョブの要求を処理するかを制御することはできません。

Azure portal で Runbook の一覧を表示すると、各 Runbook に対して開始された各ジョブの状態が示されます。 Azure Automation には、ジョブのログが最大 30 日間保存されます。

次の図では、[PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks)、[グラフィック Runbook](automation-runbook-types.md#graphical-runbooks)での Runbook ジョブのライフサイクルを示します。

![ジョブの状態 - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Runbook の実行環境

Azure Automation の Runbook は、Azure サンドボックスまたは [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) のいずれかで実行できます。 

Runbook は、Azure のリソースに対して認証および実行されるように設計されている場合、複数のジョブで使用できる共有環境である Azure サンドボックスで実行されます。 同じサンドボックスを使用するジョブは、サンドボックスのリソース制限に縛られます。 Azure サンドボックス環境では、対話型操作はサポートされていません。 そのため、すべてのプロセス外 COM サーバーにアクセスできません。 また、Win32 呼び出しを行う Runbook には、ローカルの MOF ファイルを使用する必要があります。

また、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) を使用すると、そのロールがホストされているコンピューターで、環境内のローカル リソースに対して Runbook を直接実行することができます。 Azure Automation によって Runbook が格納および管理された後、1 つ以上の割り当てられたコンピューターに配信されます。

>[!NOTE]
>Linux Hybrid Runbook Worker 上で実行するには、スクリプトが署名されていること、またワーカーが適切に構成されていることが必要です。 または、[署名の検証をオフにする必要があります](automation-linux-hrw-install.md#turn-off-signature-validation)。

次の表では、いくつかの Runbook 実行タスクと、それぞれに対して推奨される実行環境を示します。

|タスク|推奨|Notes|
|---|---|---|
|Azure リソースと統合する|Azure サンドボックス|Azure でホストされ、認証がより簡単です。 Azure VM で Hybrid Runbook Worker を使用している場合は、[マネージド ID による Runbook 認証](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)を使用できます。|
|Azure リソースを管理するための最適なパフォーマンスを得る|Azure サンドボックス|スクリプトは同じ環境内で実行され、待ち時間が短くなります。|
|運用コストを最小限に抑える|Azure サンドボックス|コンピューティングのオーバーヘッドがなく、VM の必要がありません。|
|実行時間の長いスクリプトを実行する|Hybrid Runbook Worker|Azure サンドボックスには[リソースの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)があります。|
|ローカル サービスと対話する|Hybrid Runbook Worker|ホスト コンピューター、他のクラウド環境内のリソース、またはオンプレミス環境内のリソースに直接アクセスします。 |
|サードパーティのソフトウェアと実行可能ファイルが必要である|Hybrid Runbook Worker|自分でオペレーティング システムを管理し、ソフトウェアをインストールできます。|
|Runbook でファイルまたはフォルダーを監視する|Hybrid Runbook Worker|Hybrid Runbook Worker で [Watcher タスク](automation-watchers-tutorial.md)を使用します。|
|リソースを大量に消費するスクリプトを実行する|Hybrid Runbook Worker| Azure サンドボックスには[リソースの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)があります。|
|特定の要件を持つモジュールを使用する| Hybrid Runbook Worker|いくつかの例を次に示します。</br> WinSCP - winscp.exe への依存関係 </br> IIS administration - IIS の有効化または管理に対する依存関係|
|インストーラでモジュールをインストールする|Hybrid Runbook Worker|サンドボックス用のモジュールでは、コピーがサポートされている必要があります。|
|4\.7.2 以外のバージョンの .NET Framework が必要な Runbook またはモジュールを使用する|Hybrid Runbook Worker|Azure サンドボックスでは .NET Framework 4.7.2 がサポートされており、別のバージョンへのアップグレードはサポートされていません。|
|昇格が必要なスクリプトを実行する|Hybrid Runbook Worker|サンドボックスでは、昇格は許可されません。 Hybrid Runbook Worker を使用すると、UAC をオフにして、昇格が必要なコマンドを実行するときに [Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) を使用できます。|
|Windows Management Instrumentation (WMI) へのアクセスを必要とするスクリプトを実行する|Hybrid Runbook Worker|クラウドのサンドボックスで実行されているジョブでは、WMI プロバイダーにアクセスできません。 |

## <a name="temporary-storage-in-a-sandbox"></a>サンドボックスでの一時ストレージ

Runbook ロジックの一部として一時ファイルを作成する必要がある場合は、Azure で実行されている Runbook に対して、Azure サンドボックスの Temp フォルダー (つまり `$env:TEMP`) を使用できます。 唯一の制限は、各サンドボックスのクォータである 1 GB を超えるディスク領域を使用できないことです。 PowerShell ワークフローを使用する場合、PowerShell ワークフローによってチェックポイントが使用され、スクリプトが別のサンドボックスで再試行される可能性があるため、このシナリオでは問題が発生する可能性があります。

ハイブリッド サンドボックスでは、Hybrid Runbook Worker でのストレージの可用性に基づいて `C:\temp` を使用できます。 ただし、Azure VM に関する推奨事項によって、永続化させる必要があるデータについては、Windows または Linux の[一時ディスク](../virtual-machines/managed-disks-overview.md#temporary-disk)を使用しないでください。

## <a name="resources"></a>リソース

Runbook には必ず、[リソース](/rest/api/resources/resources) (VM、ネットワーク、ネットワーク上のリソースなど) を扱うロジックが含まれています。 リソースは Azure サブスクリプションに関連付けられており、どのリソースにアクセスする場合でも Runbook に適切な資格情報が必要となります。 Runbook におけるリソースの処理の例については、「[リソースの処理](manage-runbooks.md#handle-resources)」を参照してください。

## <a name="security"></a>セキュリティ

Azure Automation は、[Azure Security Center (ASC)](../security-center/security-center-introduction.md) を使用してリソースのセキュリティを確保し、Linux システムにおけるセキュリティ侵害を検出します。 リソースが Azure 内にあるかどうかにかかわらず、セキュリティはワークロード全体で確保されます。 [Azure Automation での認証の概要](automation-security-overview.md)に関するページを参照してください。

VM 上でスクリプトを実行できるユーザーには、ASC によって制約が課されます。スクリプトは、署名されている場合と署名されていない場合とがあります。 VM へのルート アクセスを持つユーザーは、明示的にデジタル署名を使用してマシンを構成するか、デジタル署名をオフにする必要があります。 それ以外のユーザーが、Automation アカウントを作成し、該当する機能を有効にした後で実行できるのは、オペレーティング システムの更新プログラムを適用するスクリプトだけです。

## <a name="subscriptions"></a>サブスクリプション

Azure [サブスクリプション](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings)は、課金の対象となる 1 つまたは複数のクラウドベース サービスをご利用いただくために Microsoft との間で交わされる契約です。 Azure Automation では、各サブスクリプションが 1 つの Azure Automation アカウントに関連付けられます。また、そのアカウントには、[複数のサブスクリプションを作成](manage-runbooks.md#work-with-multiple-subscriptions)することができます。

## <a name="credentials"></a>資格情報

Azure のリソースであれ、サードパーティ システムのリソースであれ、Runbook がリソースにアクセスするためには、Runbook に適切な[資格情報](shared-resources/credentials.md)が必要となります。 これらの資格情報は、Azure Automation や Key Vault などに格納されます。  

## <a name="azure-monitor"></a>Azure Monitor

Azure Automation では、そのマシンの操作を監視するために [Azure Monitor](../azure-monitor/overview.md) が使用されます。 これらの操作には、Log Analytics ワークスペースと [Log Analytics エージェント](../azure-monitor/platform/log-analytics-agent.md)が必要です。

### <a name="log-analytics-agent-for-windows"></a>Windows 用の Log Analytics エージェント

[Windows 用の Log Analytics エージェント](../azure-monitor/platform/agent-windows.md)は、Azure Monitor と連携して、Windows VM と物理コンピューターを管理します。 マシンは、Azure 内で実行されていても、Azure 以外の環境 (ローカル データセンターなど) で実行されていてもかまいません。

>[!NOTE]
>Windows 用の Log Analytics エージェントは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていました。

### <a name="log-analytics-agent-for-linux"></a>Linux 用 Log Analytics エージェント

[Linux 用 Log Analytics エージェント](../azure-monitor/platform/agent-linux.md)は、Windows 用のエージェントと同様に機能しますが、Linux コンピューターを Azure Monitor に接続します。 エージェントをインストールすると、Hybrid Runbook Worker などでルート アクセス許可が要求されるコマンドを実行できる、**nxautomation** というユーザー アカウントもインストールされます。 **nxautomation** アカウントは、パスワードを必要としないシステム アカウントです。

[Linux Hybrid Runbook Worker のインストール](automation-linux-hrw-install.md)中は、対応する sudo アクセス許可を持った **nxautomation** アカウントが存在していなければなりません。 そのアカウントが存在しない、または適切なアクセス許可がアカウントにない状態でワーカーをインストールしようとすると、インストールは失敗します。

`sudoers.d` フォルダーまたはその所有権のアクセス許可は変更しないでください。 **nxautomation** アカウントには sudo アクセス許可が必要です。このアクセス許可は削除しないでください。 これを特定のフォルダーまたはコマンドに制限すると、破壊的変更が発生する可能性があります。

Log Analytics エージェントと **nxautomation** アカウント向けに提供されるログは次のとおりです。

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics エージェントのログ
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - Automation ワーカーのログ

>[!NOTE]
>Update Management の一環として有効にされた **nxautomation** ユーザーによって実行されるのは、署名済みの Runbook のみです。

## <a name="runbook-permissions"></a>Runbook のアクセス許可

Runbook には、資格情報を通じて Azure に対する認証を行うためのアクセス許可が必要です。 [Azure Automation の認証の概要](automation-security-overview.md)に関するページを参照してください。

## <a name="modules"></a>モジュール

Azure Automation は、いくつかの AzureRM モジュール (AzureRM.Automation)、いくつかの内部コマンドレットを含むモジュールなど、多くの既定のモジュールをサポートしています。 また、AzureRM モジュールに優先して現在使用されている Az モジュール (Az.Automation) など、インストール可能なモジュールもサポートされています。 Runbook と DSC 構成で使用できるモジュールの詳細については、「[Azure Automation でモジュールを管理する](shared-resources/modules.md)」を参照してください。

## <a name="certificates"></a>証明書

Azure Automation では、Azure に対する認証に[証明書](shared-resources/certificates.md)が使用されるほか、Azure またはサードパーティのリソースに証明書が追加されます。 証明書は、Runbook や DSC の構成からアクセスできるよう安全に保存されます。

Runbook では、証明機関 (CA) による署名のない自己署名証明書を使用できます。 「[新しい証明書の作成](shared-resources/certificates.md#create-a-new-certificate)」を参照してください。

## <a name="jobs"></a>ジョブ

Azure Automation では、同じ Automation アカウントから複数のジョブを実行するための環境がサポートされています。 1 つの Runbook で、多数のジョブを同時に実行することができます。 同時に実行するジョブが多いほど、同じサンドボックスにディスパッチする頻度が上がります。 

同じサンドボックス プロセスで実行するジョブは、お互いに影響し合う可能性があります。 1 つの例は、[Disconnect-AzAccount](/powershell/module/az.accounts/disconnect-azaccount) コマンドレットの実行です。 このコマンドレットを実行すると、共有サンドボックス プロセス内の各 Runbook ジョブが切断されます。 このシナリオに取り組む例については、「[コンカレント ジョブの防止](manage-runbooks.md#prevent-concurrent-jobs)」を参照してください。

>[!NOTE]
>Azure サンドボックスで実行されている Runbook から開始された PowerShell ジョブは、完全な [PowerShell 言語モード](/powershell/module/microsoft.powershell.core/about/about_language_modes)で実行されないことがあります。

### <a name="job-statuses"></a>ジョブの状態

次の表では、ジョブで可能性のある状態について説明します。 Azure portal で、すべての Runbook ジョブの状態の概要を表示したり、特定の Runbook ジョブの詳細にドリルダウンしたりできます。 Log Analytics ワークスペースとの統合を構成し、Runbook のジョブの状態やジョブ ストリームを転送することも可能です。 Azure Monitor ログとの統合の詳細については、「[Automation から Azure Monitor ログにジョブの状態とジョブ ストリームを転送する](automation-manage-send-joblogs-log-analytics.md)」を参照してください。 Runbook で状態を扱う例については、「[ジョブの状態を取得する](manage-runbooks.md#obtain-job-statuses)」も参照してください。

| Status | 説明 |
|:--- |:--- |
| アクティブ化中 |ジョブをアクティブ化しています。 |
| 完了 |ジョブは正常に完了しました。 |
| 失敗 |グラフィック Runbook または PowerShell ワークフロー Runbook のコンパイルが失敗しました。 PowerShell Runbook を開始できなかったか、ジョブで例外が発生しました。 「[Azure Automation の Runbook の種類](automation-runbook-types.md)」を参照してください。|
| 失敗、リソースを待機中 |ジョブは [fair share](#fair-share) の限界に 3 回到達し、毎回、同じチェックポイントから、または Runbook の先頭から起動したために、失敗しました。 |
| キューに登録済み |ジョブは、開始できるように、Automation ワーカー上のリソースが使用可能になるのを待機しています。 |
| 再開中 |システムは、ジョブが停止された後、そのジョブを再開しています。 |
| 実行中 |ジョブは実行中です。 |
| 実行中、リソースを待機中 |ジョブはフェア シェア制限に達したためにアンロードされました。 ジョブは最後のチェックポイントからすぐに再開します。 |
| 開始中 |ジョブがワーカーに割り当てられており、システムがジョブを起動しています。 |
| 停止済み |ジョブは完了した後、ユーザーによって停止されました。 |
| 停止中 |システムは、ジョブを停止しています。 |
| Suspended |[グラフィック Runbook と PowerShell ワークフロー Runbook](automation-runbook-types.md) のみに適用されます。 ユーザーか、システムか、または Runbook 内のコマンドによってジョブは中断されました。 Runbook にチェックポイントがない場合は、先頭から開始されます。 チェックポイントがある場合は、最後のチェックポイントからもう一度再開できます。 システムは、例外が発生した場合にのみ Runbook を中断します。 既定で、`ErrorActionPreference` 変数は、エラーでもジョブの実行を継続することを示す [Continue] に設定されます。 このユーザー設定変数を [Stop] に設定すると、エラーが発生したときにジョブは中断されます。  |
| 中断中 |[グラフィック Runbook と PowerShell ワークフロー Runbook](automation-runbook-types.md) のみに適用されます。 ユーザーの要求を受けてシステムはジョブを中断しようとしています。 Runbook は、次のチェックポイントに到達してからでないと、中断できません。 最後のチェックポイントに既に到達している場合は、完了してからでないと中断できません。 |

## <a name="activity-logging"></a>アクティビティ ログ

Azure Automation で Runbook を実行すると、その Automation アカウントのアクティビティ ログに詳細が書き込まれます。 ログの使用の詳細については、「[アクティビティ ログから詳細を取得する](manage-runbooks.md#retrieve-details-from-activity-log)」を参照してください。

## <a name="exceptions"></a>例外

ここでは、Runbook で例外または断続的な問題を処理するいくつかの方法について説明します。 たとえば WebSocket の例外があります。 正しい例外処理を使用すると、一時的なネットワーク エラーによって Runbook が失敗することを防ぐことができます。

### <a name="erroractionpreference"></a>ErrorActionPreference

[ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 変数により、終了しないエラーに対して PowerShell が対応する方法が決まります。 終了するエラーは常に終了し、`ErrorActionPreference` の影響を受けません。

Runbook で `ErrorActionPreference` を使用すると、[Get-ChildItem](/powershell/module/microsoft.powershell.management/get-childitem) コマンドレットの `PathNotFound` などの通常は終了しないエラーによって、Runbook の完了が停止します。 次の例は、`ErrorActionPreference` の使用方法を示しています。 スクリプトが停止するため、最後の [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) コマンドは実行されません。

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) は、終了するエラーを処理するために PowerShell スクリプトで使用されます。 スクリプトでは、このメカニズムを使用して、特定の例外または一般的な例外をキャッチできます。 エラーを追跡または処理するには、`catch` ステートメントを使用する必要があります。 次の例は、存在しないファイルをダウンロードしようとしています。 これは、`System.Net.WebException` 例外をキャッチして、その他の例外の最後の値を返します。

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) を使用すると、終了するエラーを生成できます。 このメカニズムは、Runbook で独自のロジックを定義するときに役立ちます。 スクリプトが停止の必要な条件を満たしている場合は、`throw` ステートメントを使用して停止できます。 次の例では、このステートメントを使用して、必要な関数パラメーターを示します。

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>エラー

Runbook はエラーを処理する必要があります。 Azure Automation では、終了するエラーと終了しないエラーの 2 種類の PowerShell エラーがサポートされます。 

終了するエラーが発生すると、Runbook の実行は停止されます。 Runbook は、[失敗] のジョブ状態で停止します。

終了しないエラーの場合は、エラー発生後もスクリプトを継続できます。 終了しないエラーの例は、Runbook で、存在しないパスを指定して `Get-ChildItem` コマンドレットを使用したときに発生するものです。 PowerShell では、パスが存在しないことを確認して、エラーをスローし、次のフォルダーへと処理を継続します。 この場合のエラーでは、Runbook のジョブ状態は [失敗] に設定されず、ジョブが完了する可能性もあります。 終了しないエラー時に Runbook を強制的に停止するには、コマンドレットで `ErrorAction Stop` を使用できます。

## <a name="calling-processes"></a>プロセスの呼び出し

Azure サンドボックスで実行される Runbook では、実行可能ファイル ( **.exe** ファイル) やサブプロセスなどのプロセスの呼び出しはサポートされていません。 その理由は、Azure サンドボックスは、基になるすべての API にアクセスできるとは限らないコンテナーで実行される共有プロセスであるためです。 サード パーティ製ソフトウェアや、サブプロセスの呼び出しを必要とするシナリオの場合は、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) で Runbook を実行する必要があります。

## <a name="device-and-application-characteristics"></a>デバイスとアプリケーションの特性

Azure サンドボックス内の Runbook ジョブは、デバイスやアプリケーションの特性にアクセスできません。 Windows でパフォーマンス メトリック (一般的なものはメモリや CPU の使用率など) のクエリを実行するために使用される最も一般的な API は WMI です。 ただし、クラウドで実行されているジョブでは Web-Based Enterprise Management (WBEM) の Microsoft による実装にアクセスできないため、どの API が使用されるかは問題ではありません。 このプラットフォームは、デバイスとアプリケーションの特性を定義するための業界標準を提供する Common Information Model (CIM) に基づいて構築されています。

## <a name="webhooks"></a>Webhooks

Azure DevOps Services や GitHub などの外部サービスを使用すると、Azure Automation で Runbook を開始できます。 この種類の開始を行うために、サービスでは 1 つの HTTP 要求を介して [Webhook](automation-webhooks.md) を使用します。 Webhook を使用すると、完全な Azure Automation 機能を実装せずに Runbook を開始できます。

## <a name="shared-resources"></a><a name="fair-share"></a>共有リソース

クラウド内のすべての Runbook 間でリソースを共有するために、Azure では、"フェア シェア" と呼ばれる概念が使用されています。 3 時間以上実行されているジョブがあると、フェア シェアの下、Azure はそれらのジョブを一時的にアンロードまたは停止します。 [PowerShell の Runbook](automation-runbook-types.md#powershell-runbooks) と [Python の Runbook](automation-runbook-types.md#python-runbooks) のジョブは停止されて再起動されず、ジョブの状態は [停止済み] になります。

長時間実行される Azure Automation タスクの場合は、Hybrid Runbook Worker の使用をお勧めします。 Hybrid Runbook Worker はフェア シェアによって制限されず、Runbook が実行できる時間に制限がありません。 その他のジョブの[制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)は、Azure サンドボックスと Hybrid Runbook Worker の両方に適用されます。 Hybrid Runbook Worker は 3 時間のフェア シェア制限を受けませんが、予期しないローカル インフラストラクチャの問題からの再起動がサポートされる worker で実行されるように Runbook を開発する必要があります。

もう 1 つのオプションは、子 Runbook を使用して Runbook を最適化することです。 たとえば、複数のデータベースに対するデータベース操作など、複数のリソースに対する同じ機能によって Runbook がループする可能性があります。 このような機能は、[子 Runbook](automation-child-runbooks.md) に移動し、[Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) を使用して親 Runbook でそれを呼び出すことができます。 子 Runbook は別々のプロセスで並列に実行されます。

子 Runbook を使用すると、親 Runbook の完了までにかかる合計時間が短縮されます。 Runbook で子の完了後にまだ実行する操作がある場合、[Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) コマンドレットを使用して、子 Runbook のジョブ状態を確認できます。

## <a name="next-steps"></a>次のステップ

* PowerShell Runbook の使用を開始するには、「[チュートリアル:PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)」を参照してください。
* Runbook を操作するには、「[Azure Automation で Runbook を管理する](manage-runbooks.md)」を参照してください。
* PowerShell の詳細については、[PowerShell のドキュメント](/powershell/scripting/overview)を参照してください。
* PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation#automation)」をご覧ください。
