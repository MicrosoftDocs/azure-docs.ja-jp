---
title: Azure Automation の変更履歴とインベントリの概要
description: この記事では、環境内のソフトウェアや Microsoft サービスの変更を特定するのに役立つ、Change Tracking とインベントリの機能について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: e2371f3de8ed73250bca6639e6c749811c5559ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572609"
---
# <a name="change-tracking-and-inventory-overview"></a>変更履歴とインベントリの概要

この記事では、Azure Automation の変更履歴とインベントリを紹介します。 この機能は、Azure やオンプレミスなどのクラウド環境でホストされている仮想マシンで変更を追跡することで、配布パッケージ マネージャーによって管理されているソフトウェアの運用上の問題と環境上の問題を特定するのに役立ちます。 変更履歴とインベントリによって追跡される項目には次のものがあります。

- Windows ソフトウェア
- Linux ソフトウェア (パッケージ)
- Windows ファイルと Linux ファイル
- Windows レジストリ キー
- Microsoft サービス
- Linux デーモン

> [!NOTE]
> Azure Resource Manager のプロパティの変更を追跡するには、Azure Resource Graph の[変更履歴](../../governance/resource-graph/how-to/get-resource-changes.md)を参照してください。

変更履歴とインベントリでは、[Azure Security Center のファイルの整合性の監視 (FIM)](../../security-center/security-center-file-integrity-monitoring.md) を使用して、オペレーティング システム、アプリケーション ファイル、および Windows レジストリが調査されます。 これらのエンティティは FIM で監視されますが、変更履歴とインベントリでネイティブに追跡されます。

- ソフトウェアの変更
- Microsoft サービス
- Linux デーモン

変更履歴とインベントリに含まれる機能をすべて有効にすると、追加料金が発生する可能性があります。 続行する前に、[Automation の価格](https://azure.microsoft.com/pricing/details/automation/)と [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)を確認してください。

変更履歴とインベントリでは、Azure Monitor ログにデータが転送され、収集されたデータが Log Analytics ワークスペースに格納されます。 ファイルの整合性の監視 (FIM) 機能は、**Azure Defender for servers** が有効な場合にのみ使用できます。 詳細については、Azure Security Center の[価格](../../security-center/security-center-pricing.md)を参照してください。 FIM では、変更履歴とインベントリのデータを格納するために作成されたものと同じ Log Analytics ワークスペースにデータがアップロードされます。 正確な使用量を把握するために、リンクされた Log Analytics ワークスペースを監視することが推奨されています。 Azure Monitor ログのデータ使用量を分析する方法の詳細については、[使用量とコストの管理](../../azure-monitor/logs/manage-cost-storage.md)に関するページを参照してください。

Log Analytics ワークスペースに接続されているマシンでは、[Log Analytics エージェント](../../azure-monitor/agents/log-analytics-agent.md)を使用して、監視対象サーバーにインストールされているソフトウェア、Microsoft サービス、Windows のレジストリとファイル、および Linux デーモンの変更に関するデータが収集されます。 データが使用可能になると、処理のためにエージェントから Azure Monitor ログに送信されます。 Azure Monitor ログでは、受信したデータにロジックが適用され、記録されて分析可能になります。

> [!NOTE]
> Change Tracking とインベントリでは、Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 サポートされているリージョンの確定的な一覧については、[Azure でのワークスペースのマッピング](../how-to/region-mappings.md)に関する記事をご覧ください。 リージョン マッピングは、Automation アカウントとは別のリージョンの VM を管理する機能には影響しません。

## <a name="current-limitations"></a>現在の制限

変更履歴とインベントリでは、次の制限がサポートされていません。

- Windows レジストリ追跡用の再帰
- ネットワーク ファイル システム
- さまざまなインストール方法
- Windows に格納されている * **.exe** ファイル
- **[最大ファイル サイズ]** 列と値は現在の実装では使用されません。
- 30 分間の収集サイクルで 2500 を超えるファイルを収集しようとすると、変更履歴とインベントリのパフォーマンスが低下する可能性があります。
- ネットワーク トラフィックが高い場合は、変更レコードが表示されるまでに最大 6 時間かかることがあります。
- マシンやサーバーのシャットダウン中に構成を変更した場合は、以前の構成に対応する変更が送信される可能性があります。
- Windows Server 2016 Core RS3 マシンで修正プログラムの更新を収集する。
- Linux デーモンでは、変更が発生していなくても、変更された状態が表示される場合があります。 この問題は、Azure Monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) テーブルに `SvcRunLevels` データが書き込まれる方法が原因で発生します。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

変更履歴とインベントリは、Log Analytics エージェントの要件を満たすすべてのオペレーティング システムでサポートされます。 Log Analytics エージェントで現在サポートされている Windows および Linux オペレーティング システムのバージョンの一覧については、「[サポートされるオペレーティング システム](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)」を参照してください。

TLS 1.2 のクライアント要件を理解するには、「[Azure Automation に対する TLS 1.2 の強制](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)」を参照してください。

### <a name="python-requirement"></a>Python の要件

変更履歴とインベントリは、Python 2 でのみサポートされています。 既定で Python 2 を含まないディストリビューションがマシンで使用されている場合は、Python 2 をインストールする必要があります。 次のサンプル コマンドでは、異なるディストリビューションに Python 2 がインストールされます。

- Red Hat、CentOS、Oracle: `yum install -y python2`
- Ubuntu、Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

python2 実行可能ファイルに *python* という別名を付ける必要があります。

## <a name="network-requirements"></a>ネットワークの要件

変更履歴とインベントリに必要なポート、URL、およびその他のネットワークの詳細については、[Azure Automation のネットワーク構成](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory)を確認してください。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

変更履歴とインベントリは、次の方法で有効にすることができます。

- 1 台以上の Azure マシンと Azure 以外のマシンに対しては、お使いの [Automation アカウント](enable-from-automation-account.md)から。

- Azure 以外のマシン ([Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)に登録されているマシンまたはサーバーを含む) に対しては、手動で。 ハイブリッド マシンの場合は、最初にマシンを [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)に接続し、次に Azure Policy を使用して [*Linux* または *Windows* Azure Arc マシンに Log Analytics エージェントをデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)組み込みポリシーを割り当てることにより、Windows 用の Log Analytics エージェントをインストールすることをお勧めします。 Azure Monitor for VMs を使用してマシンの監視も行う場合は、代わりに [Azure Monitor for VMs を有効にする](../../governance/policy/samples/built-in-initiatives.md#monitoring)イニシアティブを使用します。

- 単一の Azure VM に対しては、Azure portal の [[仮想マシン] ページ](enable-from-vm.md)から。 このシナリオは、Linux VM 用と Windows VM 用があります。

- [複数の Azure VM](enable-from-portal.md) に対しては、Azure portal の [仮想マシン] ページからそれらを選択することで。

## <a name="tracking-file-changes"></a>ファイルの変更を追跡する

Windows と Linux の両方でファイルの変更を追跡する場合、変更履歴とインベントリでは、ファイルの MD5 ハッシュが使用されます。 この機能では、ハッシュを使用して、前回のインベントリ以降に変更が加えられたかどうかが検出されます。

## <a name="tracking-file-content-changes"></a>ファイル コンテンツの変更を追跡する

Change Tracking とインベントリを使用すると、Windows または Linux のファイルのコンテンツを表示できます。 ファイルを変更するたびに、変更履歴とインベントリによって、[Azure Storage アカウント](../../storage/common/storage-account-create.md)にファイルのコンテンツが格納されます。 ファイルを追跡しているときに、変更前後のそのコンテンツを表示できます。 ファイルのコンテンツは、インラインで、または並べて表示できます。

![ファイル内の変更を表示する](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>レジストリ キーの追跡

Change Tracking とインベントリを使用すると、Windows レジストリ キーへの変更を監視できます。 監視により、サード パーティのコードやマルウェアでアクティブ化できる拡張性のポイントを正確に特定できます。 次の表に、事前に構成された (有効ではない) レジストリ キーの一覧を示します。 これらのキーを追跡するには、それぞれを有効にする必要があります。

> [!div class="mx-tdBreakAll"]
> |レジストリ キー | 目的 |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | スタートアップ時に実行されるスクリプトを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | シャットアップ時に実行されるスクリプトを監視します。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | ユーザーが Windows アカウントにサインインする前に読み込まれるキーを監視します。 このキーは、64 ビット コンピューターで実行される 32 ビット アプリケーションに使用されます。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | アプリケーションの設定の変更を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Windows エクスプローラーに直接フックされ、通常は **explorer.exe** でインプロセスで実行されるコンテキスト メニュー ハンドラーを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Windows エクスプローラーに直接フックされ、通常は **explorer.exe** でインプロセスで実行されるコピー フック ハンドラーを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | アイコン オーバーレイ ハンドラーの登録を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 64 ビット コンピューターで実行される 32 ビット アプリケーションのアイコン オーバーレイ ハンドラーの登録を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer の新しいブラウザー ヘルパー オブジェクト プラグインを監視します。 現在のページのドキュメント オブジェクト モデル (DOM) にアクセスし、ナビゲーションを制御するときに使用されます。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer の新しいブラウザー ヘルパー オブジェクト プラグインを監視します。 現在のページのドキュメント オブジェクト モデル (DOM) にアクセスし、64 ビットコンピューターで実行される 32 ビット アプリケーションのナビゲーションを制御するときに使用されます。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | カスタム ツール メニューやカスタム ツール バー ボタンなどの新しい Internet Explorer の拡張機能を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 64 ビットコンピューターで実行される 32 ビット アプリケーションのカスタム ツールのメニューやカスタム ツール バー ボタンなどの新しい Internet Explorer の拡張機能を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | wavemapper、wave1、wave2、msacm.imaadpcm、.msadpcm、.msgsm610、および vidc に関連付けられている 32 ビット ドライバーを監視します。 **system.ini** ファイルの [drivers] セクションに似ています。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 64 ビットコンピューターで実行される 32 ビット アプリケーションの wavemapper、wave1、wave2、msacm.imaadpcm、.msadpcm、.msgsm610、および vidc に関連付けられている 32 ビット ドライバーを監視します。 **system.ini** ファイルの [drivers] セクションに似ています。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 既知のまたは一般的に使用されるシステムの DLL の一覧を監視します。 監視では、システム DLL のトロイの木馬バージョンを削除することで、弱いアプリケーション ディレクトリのアクセス許可が悪用されることを防止します。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Windows の対話型ログオン サポート モデルである **winlogon.exe** からイベント通知を受信できるパッケージの一覧を監視します。

## <a name="recursion-support"></a>再帰のサポート

変更履歴とインベントリでは再帰がサポートされているので、ワイルドカードを指定して、ディレクトリ全体の追跡を簡略化できます。 また、再帰では環境変数も提供されています。これにより、複数のドライブ名またはダイナミック ドライブ名を持つ環境間でファイルを追跡できます。 次の一覧に、再帰を構成するときに知っておくべき基本的な情報を示します。

- 複数のファイルを追跡するにはワイルドカードが必要です。

- ワイルドカードを使用できるのは、**c:\folder\\file** _ や _ */etc/* .conf** など、ファイル パスの最後のセグメントでのみです。

- 環境変数に無効なパスが存在する場合、検証は成功しますが、実行時にそのパスはエラーになります。

- パスを設定するときは、漠然としたパス名は避けてください。そのような設定により、走査対象のフォルダー数が膨大になる可能性があるためです。

## <a name="change-tracking-and-inventory-data-collection"></a>変更履歴とインベントリのデータ収集

次の表は、変更履歴とインベントリでサポートされている変更の種類に対するデータ収集の頻度を示しています。 すべての種類について、現在の状態のデータ スナップショットも、少なくとも 24 時間ごとに更新されます。

| **[変更の種類]** | **頻度** |
| --- | --- |
| Windows レジストリ | 50 分 |
| Windows ファイル | 30 分 |
| Linux ファイル | 約 15 分 |
| Microsoft サービス | 10 秒から 30 分</br> 既定値は30 分 |
| Linux デーモン | 5 分 |
| Windows ソフトウェア | 30 分 |
| Linux ソフトウェア | 5 分 |

次の表は、変更履歴とインベントリでのマシンごとの追跡項目制限を示しています。

| **リソース** | **制限** |
|---|---|---|
|ファイル|500|
|レジストリ|250|
|Windows ソフトウェア (修正プログラムを含まない) |250|
|Linux パッケージ|1250|
|サービス|250|
|デーモン|250|

変更履歴とインベントリを使用しているマシンでの Log Analytics の平均データ使用量は、1 か月あたり約 40 MB です (環境によって異なります)。 Log Analytics ワークスペースの使用量と推定コスト機能を使用して、Change Tracking とインベントリによって取り込まれたデータを使用状況グラフに表示できます。 このデータ ビューを使用して、データの使用量を評価し、それが請求にどのように影響しているかを判断します。 「[ご自分の使用量を理解してコストを見積もる](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs)」を参照してください。

### <a name="microsoft-service-data"></a>Microsoft サービス データ

Microsoft サービスに対する既定の収集の頻度は 30 分です。 **[設定の編集]** にある **[Microsoft サービス]** タブのスライダーを使用して、頻度を構成できます。

![Microsoft サービス スライダー](./media/overview/windowservices.png)

パフォーマンスを最適化するために、Log Analytics エージェントでは変更の追跡のみが行われます。 大きいしきい値を設定すると、サービスがその元の状態に戻った場合に変更が検出されない可能性があります。 頻度を小さな値に設定すると、そうしないと検出されなかった可能性がある変更をキャッチすることができます。

> [!NOTE]
> エージェントは変更を 10 秒の間隔まで追跡できますが、データが Azure portal に表示されるにはまだ数分かかります。 ポータルに表示される期間中に行われた変更も引き続き追跡され、ログに記録されます。

## <a name="support-for-alerts-on-configuration-state"></a>構成状態のアラートのサポート

変更履歴とインベントリの主な機能は、ハイブリッド環境の構成状態への変更に関するアラートを生成することです。 アラートへの応答時に役立つ多くのアクションをトリガーできます。 たとえば、Azure 機能、Automation Runbook、Webhook などのアクションがあります。 マシンの **c:\windows\system32\drivers\etc\hosts** ファイルへの変更に関するアラートは、Change Tracking とインベントリのデータに関するアラートを適切に適用した一例です。 次の表で定義されているクエリのシナリオなど、警告のシナリオは他にも多数あります。

|クエリ  |説明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|システムの重要なファイルに対する変更を追跡するのに役立ちます。|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|キー構成ファイルに対する変更を追跡するのに役立ちます。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|システムに不可欠なサービスに対する変更を追跡するのに役立ちます。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState!= "Running"|システムに不可欠なサービスに対する変更を追跡するのに役立ちます。|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|ロックダウンされたソフトウェア構成が必要な環境で役立ちます。|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion!= "8.0.11081.0"|古いソフトウェア バージョンや非準拠のソフトウェア バージョンがインストールされているマシンを確認するのに役立ちます。 このクエリでは、変更は報告されず、最後に報告された構成の状態が報告されます。|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 重要なウイルス対策キーに対する変更を追跡するのに役立ちます。|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| ファイアウォール設定に対する変更を追跡するのに役立ちます。|

## <a name="next-steps"></a>次のステップ

- Automation アカウントで有効にするには、「[Automation アカウントで変更履歴とインベントリを有効にする](enable-from-automation-account.md)」を参照してください。

- Azure portal で有効にするには、「[Azure portal で変更履歴とインベントリを有効にする](enable-from-portal.md)」を参照してください。

- Runbook で有効にするには、「[Runbook で変更履歴とインベントリを有効にする](enable-from-runbook.md)」を参照してください。

- Azure VM で有効にするには、「[Azure VM で変更履歴とインベントリを有効にする](enable-from-vm.md)」を参照してください。
