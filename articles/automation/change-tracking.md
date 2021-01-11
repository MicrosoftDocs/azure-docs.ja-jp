---
title: Azure Automation の変更履歴とインベントリの概要
description: この記事では、環境内のソフトウェアや Microsoft サービスの変更を特定するのに役立つ、Change Tracking とインベントリの機能について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2fe92942e263cf53b9827ccbcb13a2d7bafc367c
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511050"
---
# <a name="change-tracking-and-inventory-overview"></a>変更履歴とインベントリの概要

この記事では、Azure Automation の変更履歴とインベントリを紹介します。 この機能は、仮想マシンとサーバー インフラストラクチャの変更を追跡することで、配布パッケージ マネージャーによって管理されているソフトウェアの運用上の問題と環境上の問題を特定するのに役立ちます。 変更履歴とインベントリによって追跡される項目には次のものがあります。 

- Windows ソフトウェア
- Linux ソフトウェア (パッケージ)
- Windows ファイルと Linux ファイル
- Windows レジストリ キー
- Microsoft サービス
- Linux デーモン

> [!NOTE]
> Azure Resource Manager のプロパティの変更を追跡するには、Azure Resource Graph の[変更履歴](../governance/resource-graph/how-to/get-resource-changes.md)を参照してください。

変更履歴とインベントリでは、Azure Monitor からデータが取得されます。 Log Analytics ワークスペースに接続されている仮想マシンでは、Log Analytics エージェントを使用して、監視対象サーバーにインストールされているソフトウェア、Microsoft サービス、Windows のレジストリとファイル、および Linux デーモンの変更に関するデータが収集されます。 データが使用可能になると、処理のためにエージェントによって Azure Monitor に送信されます。 Azure Monitor では、受信したデータにロジックが適用され、それが記録されて使用可能になります。

> [!NOTE]
> Change Tracking とインベントリでは、Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 サポートされているリージョンの確定的な一覧については、[Azure でのワークスペースのマッピング](./how-to/region-mappings.md)に関する記事をご覧ください。 リージョン マッピングは、Automation アカウントとは別のリージョンの VM を管理する機能には影響しません。

現在、変更履歴とインベントリでは、次の項目がサポートされていません。

- Windows レジストリ追跡用の再帰
- ネットワーク ファイル システム
- さまざまなインストール方法
- *Windows 用の **.exe** ファイル

その他の制限事項:

- **[最大ファイル サイズ]** 列と値は現在の実装では使用されません。
- 30 分間の収集サイクルで収集するファイル数が 2,500 を超えると、Change Tracking とインベントリのパフォーマンスが低下する可能性があります。
- ネットワーク トラフィックが高いとき、変更レコードが表示されるまでに最大 6 時間かかることがあります。
- コンピューターのシャットダウン中に構成を変更した場合、そのコンピューターから前の構成に対応する変更が送信される可能性があります。

現在、変更履歴とインベントリでは、次の問題が発生しています。

- Windows Server 2016 Core RS3 マシンについては、修正プログラムの更新は収集されません。

- Linux デーモンでは、変更が発生していなくても、変更された状態が表示される場合があります。 この問題は、Azure Monitor [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) ログの `SvcRunLevels` データがキャプチャされる方法が原因で発生します。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

変更履歴とインベントリは、Log Analytics エージェントの要件を満たすすべてのオペレーティング システムでサポートされます。 オペレーティング システムの正式なバージョンは、Windows Server 2008 SP1 以降と Windows 7 SP1 以降です。 この機能は、多くの Linux オペレーティング システムでもサポートされています。 サポートされているオペレーティング システムの一覧については、「[Log Analytics エージェントの概要](../azure-monitor/platform/log-analytics-agent.md)」を参照してください。

TLS 1.2 のクライアント要件を理解するには、「[Azure Automation に対する TLS 1.2 の強制](automation-managing-data.md#tls-12-enforcement-for-azure-automation)」を参照してください。

## <a name="network-requirements"></a>ネットワークの要件

変更履歴とインベントリには、次の表に示すネットワーク アドレスが特に必要です。 これらのアドレスへの通信には、ポート 443 が使用されます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>変更履歴とインベントリのユーザー インターフェイス

Azure portal の変更履歴とインベントリを使用して、監視対象のコンピューターの変更の概要を表示します。 この機能を使用するには、お使いの Automation アカウント用の **[構成管理]** の下の **[変更履歴]** または **[インベントリ]** で、いずれかの VM 追加オプションを選択します。  

![[変更履歴] ダッシュボード](./media/change-tracking/change-tracking-dash01.png)

ダッシュボード上部にあるドロップ ダウンを使用すると、変更の種類および時間の範囲に基づいて、変更履歴グラフと詳細情報を制限できます。 グラフ上をクリックしてドラッグすることで、カスタムの時間の範囲を選択することもできます。 

変更またはイベントをクリックすると、その詳細が表示されます。 使用できる変更の種類は次のとおりです。

- events
- デーモン
- ファイル
- レジストリ
- ソフトウェア
- Microsoft サービス

各変更を追加、変更、または削除することができます。 次の例は、サービスの起動の種類が [手動] から [自動] に変更されたことを示しています。

![Change Tracking とインベントリの詳細](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Azure Security Center での FIM のサポート

変更履歴とインベントリでは、[Azure Security Center のファイルの整合性の監視 (FIM)](../security-center/security-center-file-integrity-monitoring.md) が使用されます。 FIM ではファイルとレジストリのみが監視されますが、変更履歴とインベントリの完全な機能には次の追跡も含まれます。

- ソフトウェアの変更
- Microsoft サービス
- Linux デーモン

> [!NOTE]
> 完全な Change Tracking と Inventory 機能を有効にすると、追加料金が発生する可能性があります。 「[Automation の価格](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。 Azure portal で利用可能な[インストールされている監視ソリューションの一覧](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)から FIM を削除することができます。 「[監視ソリューションを削除する](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)」を参照してください。

## <a name="tracking-of-file-changes"></a>ファイルの変更履歴

Windows と Linux の両方でファイルの変更を追跡する場合、変更履歴とインベントリでは、ファイルの MD5 ハッシュが使用されます。 この機能では、ハッシュを使用して、前回のインベントリ以降に変更が加えられたかどうかが検出されます。

## <a name="tracking-of-file-content-changes"></a>ファイル コンテンツの変更履歴

Change Tracking とインベントリを使用すると、Windows または Linux のファイルのコンテンツを表示できます。 ファイルを変更するたびに、変更履歴とインベントリによって、[Azure Storage アカウント](../storage/common/storage-account-create.md)にファイルのコンテンツが格納されます。 ファイルを追跡しているときに、変更前後のそのコンテンツを表示できます。 ファイルのコンテンツは、インラインで、または並べて表示できます。 

![ファイル内の変更を表示する](./media/change-tracking/view-file-changes.png)

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

- ワイルドカードを使用できるのは、**c:\folder\\file*** や **/etc/*.conf** など、ファイル パスの最後のセグメントでのみです。

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

変更履歴とインベントリを使用しているマシンでの Log Analytics の平均データ使用量は、1 か月あたり約 40 MB です (環境によって異なります)。 Log Analytics ワークスペースの使用量と推定コスト機能を使用して、Change Tracking とインベントリによって取り込まれたデータを使用状況グラフに表示できます。 このデータ ビューを使用して、データの使用量を評価し、それが請求にどのように影響しているかを判断します。 「[ご自分の使用量を理解してコストを見積もる](../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs)」を参照してください。

### <a name="microsoft-service-data"></a>Microsoft サービス データ

Microsoft サービスに対する既定の収集の頻度は 30 分です。 **[設定の編集]** にある **[Microsoft サービス]** タブのスライダーを使用して、頻度を構成できます。

![Microsoft サービス スライダー](./media/change-tracking/windowservices.png)

パフォーマンスを最適化するために、Log Analytics エージェントでは変更の追跡のみが行われます。 大きいしきい値を設定すると、サービスがその元の状態に戻った場合に変更が検出されない可能性があります。 頻度を小さな値に設定すると、そうしないと検出されなかった可能性がある変更をキャッチすることができます。

> [!NOTE]
> エージェントは変更を 10 秒の間隔まで追跡できますが、データが Azure portal に表示されるにはまだ数分かかります。 ポータルに表示される期間中に行われた変更も引き続き追跡され、ログに記録されます。

## <a name="support-for-alerts-on-configuration-state"></a>構成状態のアラートのサポート

変更履歴とインベントリの主な機能は、ハイブリッド環境の構成状態への変更に関するアラートを生成することです。 アラートに応答して、多くの便利なアクションをトリガーできます。たとえば、Azure Functions、Automation Runbook、Webhook などのアクションがあります。 マシンの **c:\windows\system32\drivers\etc\hosts** ファイルへの変更に関するアラートは、Change Tracking とインベントリのデータに関するアラートを適切に適用した一例です。 次の表で定義されているクエリのシナリオなど、警告のシナリオは他にも多数あります。

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

- Automation アカウントで機能を有効にするには、「[Automation アカウントで変更履歴とインベントリを有効にする](automation-enable-changes-from-auto-acct.md)」を参照してください。

- Azure portal にブラウザーでアクセスすることによってこの機能を有効にするには、「[Azure portal での Change Tracking とインベントリの有効化](automation-enable-changes-from-browse.md)に関する記事を参照してください。

- Runbook で機能を有効にするには、「[Runbook で変更履歴とインベントリを有効にする](automation-enable-changes-from-runbook.md)」を参照してください。

- Azure VM で機能を有効にするには、「[Azure VM で変更履歴とインベントリを有効にする](automation-enable-changes-from-vm.md)」を参照してください。
