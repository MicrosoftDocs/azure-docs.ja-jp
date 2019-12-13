---
title: Azure Automation で変更を追跡する
description: Change Tracking ソリューションは、ユーザーの環境で起こるソフトウェアと Windows サービスの変更を特定するために役立ちます。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fd800062c4a8362919b1818550b2fca9fa3eb88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850552"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する

この記事では、Change Tracking ソリューションを使用して、ユーザーの環境内の変更箇所を簡単に識別する方法を説明します。 このソリューションは、Windows および Linux ソフトウェア、Windows および Linux ファイル、Windows レジストリ キー、Windows サービス、および Linux デーモンに対する変更を追跡します。 構成の変更を識別することで、運用上の問題を特定できるようになります。

監視対象サーバーにインストールされているソフトウェア、Windows サービス、Windows レジストリとファイル、および Linux デーモンの変更が、クラウドの Azure Monitor サービスに送信され、処理されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。 [変更の追跡] ダッシュボードの情報を使用して、サーバー インフラストラクチャで行われた変更を簡単に確認できます。

> [!NOTE]
> Azure Automation Change Tracking では、仮想マシンでの変更が追跡されます。 Azure Resource Manager のプロパティの変更を追跡するには、Azure Resource Graph の[変更履歴](../governance/resource-graph/how-to/get-resource-changes.md)を参照してください。

## <a name="supported-windows-operating-systems"></a>サポートされている Windows オペレーティング システム

Windows エージェントでは、次のバージョンの Windows オペレーティング システムが正式にサポートされています。

* Windows Server 2008 R2 以降

## <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム

次の Linux ディストリビューションは公式にサポートされています。 ただし、Linux エージェントは、ここに記載されていないディストリビューションでも動作する可能性があります。 記載されている各メジャー バージョンのマイナー リリースは、特に記載がない限りすべてサポートされます。

### <a name="64-bit"></a>64 ビット

* CentOS 6 および 7
* Amazon Linux 2017.09
* Oracle Linux 6 および 7
* Red Hat Enterprise Linux Server 6 および 7
* Debian GNU/Linux 8 および 9
* Ubuntu Linux 14.04 LTS、16.04 LTS、および 18.04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 ビット

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 および 9
* Ubuntu Linux 14.04 LTS および 16.04 LTS

## <a name="onboard"></a>Change Tracking とインベントリの有効化

変更の追跡を開始するには、Change Tracking および Inventory ソリューションを有効にする必要があります。 Change Tracking と Inventory にマシンをオンボーディングする方法は多数あります。 以下に、推奨およびサポートされている、ソリューションにオンボードする方法を示します。

* [仮想マシンから](automation-onboard-solutions-from-vm.md)
* [複数のマシンを参照することから](automation-onboard-solutions-from-browse.md)
* [お使いの Automation アカウントから](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook によって](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Change Tracking と Inventory の構成

ソリューションにコンピューターをオンボードする方法については、[Automation ソリューションのオンボード](automation-onboard-solutions-from-automation-account.md)に関するページを参照してください。 Change Tracking と Inventory ソリューションでのマシンのオンボードが完了したら、追跡する項目を構成できます。新しいファイルまたはレジストリ キーの追跡を有効にすると、Change Tracking と Inventory の両方に対して有効になります。

Windows と Linux の両方でファイルの変更を追跡する場合、ファイルの MD5 ハッシュが使用されます。 これらのハッシュは、前回のインベントリから変更が加えられたかどうかを検出するために使用されます。

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure Security Center のファイルの整合性の監視

Azure Security Center は、Azure Change Tracking 上に構築されたファイルの整合性の監視 (FIM) を追加しました。 FIM はファイルとレジストリのみを監視しますが、完全な Change Tracking ソリューションには次のものも含まれます。

- ソフトウェアの変更
- Windows サービス
- Linux デーモン

FIM が既に有効になっていて、完全な Change Tracking ソリューションを試したい場合は、次の手順を実行する必要があります。 お使いの設定はこの処理によって削除されません。

> [!NOTE]
> 完全な Change Tracking ソリューションを有効にすると、追加料金が発生することがあります。詳細については、「[Automation の価格](https://azure.microsoft.com/pricing/details/automation/)」を参照してください。

1. ワークスペースに移動し、[インストールされている監視ソリューションの一覧](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)から監視ソリューションを見つけて削除します。
2. 「[監視ソリューションを削除する](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)」で説明されているように、ソリューション名をクリックして [概要] ページを開き、 [削除] をクリックします。
3. Automation アカウントに移動し、 **[構成管理]** のリソース メニューから **[Change Tracking]\(変更の追跡\)** を選択して、ソリューションを再度有効にします。
4. ワークスペースの設定の詳細を確認し、 **[Enable]\(有効にする\)** をクリックします。

### <a name="configure-linux-files-to-track"></a>追跡する Linux ファイルを構成する

次の手順を使用して、Linux コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 **[設定の編集]** (歯車アイコン) をクリックします。
2. **[変更の追跡]** ページで、 **[Linux ファイル]** を選択し、 **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
3. **[変更履歴用の Linux ファイルを追加する]** で、追跡するファイルまたはディレクトリの情報を入力し、 **[保存]** をクリックします。

|プロパティ  |説明  |
|---------|---------|
|有効     | 設定が適用されるかどうかを決定します。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス。 例: "/etc/*.conf"       |
|パスの種類     | 追跡する項目の種類。"ファイル" または "ディレクトリ" を指定できます。        |
|再帰     | 追跡する項目を検索するときに、再帰を使用するかどうかを決定します。        |
|sudo の使用     | この設定により、項目を確認するときに、sudo を使用するかどうかが決まります。         |
|リンク     | この設定により、ディレクトリを走査するときの、シンボリック リンクの処理方法が決まります。<br> **無視** - シンボリック リンクを無視し、参照先のファイル/ディレクトリを含めません。<br>**フォロー** - 再帰中、シンボリック リンクに従います。参照先のファイル/ディレクトリも含めます。<br>**管理** - シンボリック リンクに従います。また、返却された内容の変更を許可します。     |
|すべての設定のファイル コンテンツをアップロードする| 追跡した変更についてファイル コンテンツのアップロードをオンまたはオフにします。 使用できるオプションは **True** または **False** です。|

> [!NOTE]
> "管理" リンク オプションはお勧めしません。 ファイルのコンテンツの取得はサポートされていません。

### <a name="configure-windows-files-to-track"></a>追跡する Windows ファイルの構成

次の手順を使用して、Windows コンピューターでのファイル追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 **[設定の編集]** (歯車アイコン) をクリックします。
2. **[Change Tracking]\(変更の追跡\)** ページで、 **[Windows ファイル]** を選択し、 **[+ 追加]** をクリックして、追跡する新しいファイルを追加します。
3. **[変更履歴用の Windows ファイルを追加する]** で、追跡するファイルの情報を入力し、 **[保存]** をクリックします。

|プロパティ  |説明  |
|---------|---------|
|有効     | 設定が適用されるかどうかを決定します。        |
|Item Name     | 追跡するファイルのフレンドリ名。        |
|グループ     | ファイルを論理的にグループ化するためのグループ名。        |
|パスの入力     | ファイル確認のためのパス (例: "c:\temp\\\*.txt")。<br>"%winDir%\System32\\\*.*" などの環境変数も使用できます。       |
|再帰     | 追跡する項目を検索するときに、再帰を使用するかどうかを決定します。        |
|すべての設定のファイル コンテンツをアップロードする| 追跡した変更についてファイル コンテンツのアップロードをオンまたはオフにします。 使用できるオプションは **True** または **False** です。|

## <a name="wildcard-recursion-and-environment-settings"></a>ワイルドカード、再帰、環境設定

再帰を使用すると、ワイルドカードを指定することで、複数のディレクトリを対象とした追跡を簡単に行うことができます。また、環境変数を指定すれば、複数のドライブ名や動的なドライブ名を使って、複数の環境を対象にファイルを追跡できます。 次の一覧に、再帰を構成するときに知っておくべき基本的な情報を示します。

* 複数のファイルを追跡するにはワイルドカードが必要です。
* ワイルドカードは、パスの最後のセグメントでしか使用できません (`c:\folder\*file*`や `/etc/*.conf` など)。
* 環境変数に無効なパスが存在する場合、検証は成功しますが、インベントリの実行時にそのパスはエラーになります。
* パスを設定するとき、漠然としたパス (`c:\*.*` など) は避けてください。走査の対象になるフォルダーが膨大な数に上ります。

## <a name="configure-file-content-tracking"></a>ファイル コンテンツの追跡を構成する

ファイル コンテンツの変更の追跡を使用して、ファイル変更の前後のコンテンツを表示できます。 これは、Windows および Linux ファイルに対して使用できます。ファイルが変更されるたびに、ファイルのコンテンツがストレージ アカウントに格納され、変更の前後のファイルがインラインで、または横に並べて表示されます。 詳細については、[追跡されたファイルのコンテンツの表示](change-tracking-file-contents.md)に関するページを参照してください。

![ファイル内の変更を表示する](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>追跡する Windows レジストリ キーを構成する

次の手順を使用して、Windows コンピューターでのレジストリ キー追跡を構成します。

1. Automation アカウントで、 **[構成管理]** の **[Change Tracking]\(変更の追跡\)** を選択します。 **[設定の編集]** (歯車アイコン) をクリックします。
2. **[Change Tracking]\(変更の追跡\)** ページで、 **[Windows レジストリ]** を選択し、 **[+ 追加]** をクリックして、追跡する新しいレジストリ キーを追加します。
3. **[変更履歴用の Windows レジストリを追加する]** で、追跡するキーの情報を入力し、 **[保存]** をクリックします。

|プロパティ  |説明  |
|---------|---------|
|有効     | 設定が適用されるかどうかを決定します。        |
|Item Name     | 追跡するレジストリ キーのフレンドリ名。        |
|Group     | レジストリ キーを論理的にグループ化するためのグループ名。        |
|Windows レジストリ キー   | レジストリ キーを確認するためのパス。 例: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>制限事項

現在、Change Tracking ソリューションでは以下の項目に対応していません。

* Windows レジストリ追跡用の再帰
* ネットワーク ファイル システム

その他の制限事項:

* **[最大ファイル サイズ]** 列と値は現在の実装では使用されません。
* 30 分間の収集サイクルで収集するファイル数が 2500 を超えると、ソリューションのパフォーマンスが低下する可能性があります。
* ネットワーク トラフィックが高いとき、変更レコードが表示されるまでに最大 6 時間かかることがあります。
* コンピューターのシャットダウン中に構成を変更した場合、そのコンピューターから前の構成に対応する変更が送信される可能性があります。

## <a name="known-issues"></a>既知の問題

現在、Change Tracking ソリューションでは、以下の問題が発生しています。

* Windows Server 2016 Core RS3 マシンについては、修正プログラムの更新は収集されていません。
* Linux デーモンは、変更がなかった場合でも、変更された状態を表示する可能性があります。 これは、`SvcRunLevels` フィールドのキャプチャ方法が原因です。

## <a name="change-tracking-data-collection-details"></a>変更の追跡データ収集の詳細

次の表は、変更の種類ごとにデータ収集の頻度を示したものです。 すべての種類について、現在の状態のデータ スナップショットも、少なくとも 24 時間ごとに更新されます。

| **変更の種類** | **頻度** |
| --- | --- |
| Windows レジストリ | 50 分 |
| Windows ファイル | 30 分 |
| Linux ファイル | 約 15 分 |
| Windows サービス | 10 秒から 30 分</br> 既定値は30 分 |
| Linux デーモン | 5 分 |
| Windows ソフトウェア | 30 分 |
| Linux ソフトウェア | 5 分 |

次の表は、Change Tracking でのマシンごとの追跡項目制限を示します。

| **リソース** | **制限**| **メモ** |
|---|---|---|
|ファイル|500||
|レジストリ|250||
|Windows ソフトウェア|250|ソフトウェア修正プログラムは含まれません|
|Linux パッケージ|1250||
|サービス|250||
|デーモン|250||

Change Tracking と Inventory を使用しているマシンでの Log Analytics の平均データ使用量は、1 か月あたり約 40 MB です。 この値は概数にすぎず、環境によって異なる可能性があります。 お使いの環境を監視し、実際に必要な正確な使用量を確認することをお勧めします。

### <a name="windows-service-tracking"></a>Windows サービスの追跡

Windows サービスに対する既定の収集の頻度は 30 分です。 この頻度を構成するには、 **[変更の追跡]** に移動します。 **[Windows サービス]** タブの **[設定の編集]** には、Windows サービスに対する収集の頻度を 10 秒から 30 分まで変更できるスライダーがあります。 このスライダーを必要な頻度に移動すると、その頻度が自動的に保存されます。

![Windows サービスのスライダー](./media/change-tracking/windowservices.png)

エージェントは変更のみを追跡します。これにより、エージェントのパフォーマンスが最適化されます。 大きいしきい値を設定すると、サービスがその元の状態に戻した場合に変更が検出されない可能性があります。 頻度を小さな値に設定すると、そうしないと検出されなかった可能性がある変更を捕まえることができます。

> [!NOTE]
> エージェントは変更を 10 秒の間隔まで追跡できますが、データがポータルに表示されるにはまだ数分かかります。 ポータルに表示される期間中の変更も引き続き追跡され、ログに記録されます。

### <a name="registry-key-change-tracking"></a>レジストリ キーの変更追跡

レジストリ キーへの変更を監視する目的は、サード パーティのコードやマルウェアがアクティブ化できる拡張性のポイントを正確に特定することです。 次の一覧は、事前構成されたレジストリ キーを示しています。 これらのキーは構成されていますが、有効にはなっていません。 これらのレジストリ キーを追跡するには、それぞれを有効にする必要があります。

> [!div class="mx-tdBreakAll"]
> |レジストリ キー | 目的 |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Windows Explorer に直接フックされ、通常は Explorer.exe でインプロセスで実行される共通自動開始エントリを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | スタートアップ時に実行されるスクリプトを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | シャットアップ時に実行されるスクリプトを監視します。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | ユーザーが Windows アカウントにサインインする前に読み込まれるキーを監視します。 このキーは、64 ビット コンピューターで実行される 32 ビット プログラムに使用されます。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | アプリケーションの設定の変更を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Windows Explorer に直接フックされ、通常は Explorer.exe でインプロセスで実行される共通自動開始エントリを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Windows Explorer に直接フックされ、通常は Explorer.exe でインプロセスで実行される共通自動開始エントリを監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | アイコン オーバーレイ ハンドラーの登録を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 64 ビット コンピューターで実行される 32 ビット プログラムのアイコン オーバーレイ ハンドラーの登録を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer の新しいブラウザー ヘルパー オブジェクト プラグインを監視します。 現在のページのドキュメント オブジェクト モデル (DOM) にアクセスし、ナビゲーションを制御するときに使用されます。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer の新しいブラウザー ヘルパー オブジェクト プラグインを監視します。 現在のページのドキュメント オブジェクト モデル (DOM) にアクセスし、64 ビットコンピューターで実行される 32 ビット プログラムのナビゲーションを制御するときに使用されます。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | カスタム ツール メニューやカスタム ツール バー ボタンなどの新しい Internet Explorer の拡張機能を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 64 ビットコンピューターで実行される 32 ビット プログラムのカスタム ツールのメニューや 64 ビット コンピューター上で実行する 32 ビット プログラムのカスタム ツール バー ボタンなどの新しい Internet Explorer の拡張機能を監視します。
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | wavemapper、wave1、wave2、msacm.imaadpcm、.msadpcm、.msgsm610、および vidc に関連付けられている 32 ビット ドライバーを監視します。 SYSTEM.INI ファイルの [drivers] セクションに似ています。
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | 64 ビットコンピューターで実行される 32 ビット プログラムの wavemapper、wave1、wave2、msacm.imaadpcm、.msadpcm、.msgsm610、および vidc に関連付けられている 32 ビット ドライバーを監視します。 SYSTEM.INI ファイルの [drivers] セクションに似ています。
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | 既知のまたは一般的に使用されるシステムの DLL の一覧を監視します。このシステムは、システム DLL のトロイの木馬バージョンを削除することで、弱いアプリケーション ディレクトリのアクセス許可が悪用されることを防止します。
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Windows オペレーティング システムの対話型ログオン サポート モデルである Winlogon からイベント通知を受信できるパッケージの一覧を監視します。

## <a name="network-requirements"></a>ネットワークの要件

Change Tracking には次のアドレスが明示的に必要です。 このアドレスへの通信は、ポート 443 を使用して行われます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>変更の追跡を使用する

ソリューションを有効にした後、監視対象コンピューターの変更の概要を表示するには、Automation アカウントの **[構成管理]** で **[Change Tracking]\(変更の追跡\)** を選択します。

コンピューターに対する変更を表示し、各イベントの詳細を確認することができます。 グラフ上部にあるドロップ ダウンを使用すると、変更の種類および時間の範囲に基づいて、グラフと詳細情報を制限できます。 グラフ上をクリックしてドラッグすることで、カスタムの時間の範囲を選択することもできます。 **変更の種類**は、**Events**、**Daemons**、**Files**、**Registry**、**Software**、**Windows Services** のいずれかの値になります。 カテゴリは変更の種類を示し、**Added**、**Modified**、または **Removed** になります。

![[変更の追跡] ダッシュボードの画像](./media/change-tracking/change-tracking-dash01.png)

変更またはイベントをクリックすると、その変更に関する詳細情報が表示されます。 例に示すように、サービスの起動の種類が手動から自動に変更されました。

![変更追跡の詳細の画像](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>検索ログ

ポータルで提供されている詳細のほか、ログに対して検索を実行できます。 **[Change Tracking]\(変更の追跡\)** ページを開いた状態で、 **[Log Analytics]** をクリックします。これにより、 **[ログ]** ページが開きます。

### <a name="sample-queries"></a>サンプル クエリ

次の表は、このソリューションによって収集された変更レコードを探すログ検索の例です。

|クエリ  |説明  |
|---------|---------|
|ConfigurationData<br>&#124; where   ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"<br>&#124; where SvcState == "Stopped"<br>&#124; summarize arg_max(TimeGenerated, *) by SoftwareName, Computer         | Windows サービスの最新のインベントリ レコードで、自動に設定されたが、停止中として報告されたものを表示します<br>結果はその SoftwareName と Computer の最新のレコードに限定されます      |
|ConfigurationChange<br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Removed"<br>&#124; order by TimeGenerated desc|削除されたソフトウェアの変更レコードを表示します|

## <a name="alert-on-changes"></a>変更に関するアラート

Change Tracking と Inventory の重要な機能は、構成の状態と、ハイブリッド環境の構成の状態に対する変更のアラートを生成する機能です。

次の例では、スクリーンショットで、マシン上でファイル `C:\windows\system32\drivers\etc\hosts` が変更されていることを示しています。 Hosts ファイルは Windows でホスト名を IP アドレスに解決して DNS にも優先するようにするために使用され、それによって接続の問題や、悪質な Web サイトや危険な Web サイトへのトラフィックのリダイレクトが生じる可能性があるため、このファイルは重要です。

![hosts ファイルの変更を示すグラフ](./media/change-tracking/changes.png)

この変更をさらに分析するには、 **[Log Analytics]** をクリックしてログ検索に移動します。 ログ検索で、クエリ `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` を使って Hosts ファイルに対するコンテンツの変更を検索します。 このクエリは、完全修飾パスに "hosts" という単語が含まれているファイルのうち、ファイル コンテンツの変更が含まれている変更を検索します。 パスの部分を完全修飾された形式 (`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` など) に変更することで、特定のファイルを確認することもできます。

クエリが目的の結果を返したら、ログ検索エクスペリエンスで **[新しいアラート ルール]** ボタンをクリックしてアラート作成ページを開きます。 このエクスペリエンスには Azure portal の **Azure Monitor** から移動することもできます。 アラート作成エクスペリエンスで、クエリをもう一度確認し、アラート ロジックを変更します。 この場合は、環境内のすべてのマシンで 1 つでも変更が検出されたら、アラートがトリガーされるようにします。

![hosts ファイルに対する変更を追跡するための変更クエリを示すイメージ](./media/change-tracking/change-query.png)

条件ロジックを設定した後、トリガーされるアラートに対応するアクションを実行するアクション グループを割り当てます。 この場合は、電子メールの送信と ITSM チケットの作成を設定しています。  Azure 関数、Automation Runbook、Webhook、ロジック アプリのトリガーなど、役に立つその他の多くのアクションも実行できます。

![変更に関するアラートに対するアクション グループの構成のイメージ](./media/change-tracking/action-groups.png)

すべてのパラメーターとロジックを設定した後、アラートを環境に適用できます。

### <a name="alert-suggestions"></a>アラートに関する推奨事項

Hosts ファイルへの変更に関するアラートは、Change Tracking や Inventory のデータに関するアラートの 1 つの適切な利用ですが、以下のセクションでクエリの例と共に定義されているケースを含み、アラートにはその他多くのシナリオがあります。

|クエリ  |説明  |
|---------|---------|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Files" and FileSystemPath contains " c:\\windows\\system32\\drivers\\"|システムの重要なファイルに対する変更を追跡するのに役立ちます|
|ConfigurationChange <br>&#124; where FieldsChanged contains "FileContentChecksum" and FileSystemPath == "c:\\windows\\system32\\drivers\\etc\\hosts"|キー構成ファイルに対する変更を追跡するのに役立ちます|
|ConfigurationChange <br>&#124; where ConfigChangeType == "WindowsServices" and SvcName contains "w3svc" and SvcState == "Stopped"|システムの重要なサービスに対する変更を追跡するのに役立ちます|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|システムの重要なサービスに対する変更を追跡するのに役立ちます|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Software" and ChangeCategory == "Added"|ロックダウンされたソフトウェア構成が必要な環境で役立ちます|
|ConfigurationData <br>&#124; where SoftwareName contains "Monitoring Agent" and CurrentVersion != "8.0.11081.0"|古いソフトウェア バージョンや非準拠のソフトウェア バージョンがインストールされているマシンを確認するのに役立ちます。 変更ではなく、最後に報告された構成の状態を報告します。|
|ConfigurationChange <br>&#124; where RegistryKey == @"HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| 重要なウイルス対策キーに対する変更を追跡するのに役立ちます|
|ConfigurationChange <br>&#124; where RegistryKey contains @"HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| ファイアウォール設定に対する変更を追跡するのに役立ちます|

## <a name="next-steps"></a>次の手順

ソリューションの使用方法の詳細については、Change Tracking のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [環境の変更に関する問題を解決する](automation-tutorial-troubleshoot-changes.md)

* [Azure Monitor ログのログ検索](../log-analytics/log-analytics-log-searches.md)を使用して、詳細な変更追跡データを確認します。
