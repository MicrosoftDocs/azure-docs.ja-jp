---
title: 複数の Azure 仮想マシンの更新を管理する
description: この記事では、Azure 仮想マシンの更新プログラムを管理する方法について説明します。
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0a4990673479c913777a5a7c410460d3d3b31264
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478327"
---
# <a name="manage-updates-for-multiple-machines"></a>複数のマシンの更新プログラムの管理

Update Management ソリューションを使用して、Windows と Linux の仮想マシンの更新プログラムと修正プログラムを管理できます。 [Azure Automation](automation-offering-get-started.md) アカウントから、以下の操作を行うことができます。

- 仮想マシンをオンボードする
- 利用可能な更新プログラムの状態にアクセスする
- 必要な更新プログラムのインストールをスケジュールする
- 展開結果を見直して、Update Management が有効になっているすべての仮想マシンに更新プログラムが正常に適用されたことを確認する

## <a name="prerequisites"></a>前提条件

Update Management を使用するには、以下が必要です。

- サポートされているオペレーティング システムのいずれかがインストールされている仮想マシンまたはコンピューター。

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

Update Management は、次のオペレーティング システムでサポートされています。

|オペレーティング システム  |メモ  |
|---------|---------|
|Windows Server 2008、Windows Server 2008 R2 RTM    | 更新プログラムの評価のみをサポートします。         |
|Windows Server 2008 R2 SP1 以降     |Windows PowerShell 4.0 以降が必要です ([WMF 4.0 のダウンロード](https://www.microsoft.com/download/details.aspx?id=40855))。</br> より高い信頼性を確保するには Windows PowerShell 5.1 を使用することをお勧めします ([WMF 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))         |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントは、更新リポジトリへのアクセスが必要です。        |
|Ubuntu 14.04 LTS、16.04 LTS、および 18.04 LTS (x86/x64)      |Linux エージェントは、更新リポジトリへのアクセスが必要です。         |

> [!NOTE]
> Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、無人アップグレード パッケージを再構成して自動更新を無効にします。 詳細については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)を参照してください。

Linux エージェントは、更新リポジトリへのアクセスが必要です。

このソリューションでは、Log Analytics エージェント for Linux が複数の Azure Log Analytics ワークスペースにレポートする構成はサポートされていません。

## <a name="enable-update-management-for-azure-virtual-machines"></a>Update Management を Azure 仮想マシンに対して有効にする

Azure Portal で、Automation アカウントを開き、 **[Update Management]** を選択します。

**[Azure VM の追加]** を選択します。

![[Azure VM の追加] タブ](./media/manage-update-multi/update-onboard-vm.png)

利用を開始する仮想マシンを選択します。 

**［Update Management を有効にする］** で、 **［有効化］** を選択して、仮想マシンをオンボードします。

![[更新管理の有効化] ダイアログ ボックス](./media/manage-update-multi/update-enable.png)

オンボードが終了すると、仮想マシンに対して Update Management が有効になります。

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Azure 以外の仮想マシンとコンピューターに対して Update Management を有効にする

Azure 以外の Windows 仮想マシンとコンピューターに対して Update Management を有効にする方法については、[Windows コンピューターを Azure の Azure Monitor サービスに接続する](../log-analytics/log-analytics-windows-agent.md)方法に関する記事を参照してください。

Azure 以外の Linux 仮想マシンとコンピューターに対して Update Management を有効にする方法については、[Linux コンピューターを Azure Monitor ログに接続する](../log-analytics/log-analytics-agent-linux.md)方法に関する記事を参照してください。

## <a name="view-computers-attached-to-your-automation-account"></a>Automation アカウントに接続されているコンピューターを表示する

マシンに対して Update Management を有効にした後、 **[コンピューター]** を選択することで、マシンの情報を表示できます。 コンピューターの*マシン名* *対応状況*、*環境*、*OS の種類*、*インストール済みの重要なセキュリティの更新プログラム* *インストール済みのその他の更新プログラム*、および*更新エージェントの準備状況*の情報を表示できます。

  ![[コンピューター] タブの表示](./media/manage-update-multi/update-computers-tab.png)

Update Management が最近有効になったコンピューターは、まだ評価されていないことがあります。 そのようなコンピューターの対応状態は **［評価が行われていません］** になります。 対応状況に使用される値の一覧を次に示します。

- **対応**:重要な更新プログラムとセキュリティ更新プログラムがすべてインストールされているコンピューター。

- **非対応**:インストールされていない重要な更新プログラムまたはセキュリティ更新プログラムが少なくとも 1 つ存在するコンピューター。

- **評価が行われていません**:想定されている時間内にコンピューターから更新プログラムの評価データを受信していません。 Linux コンピューターでは、想定されている時間は、最後の 1 時間です。 Windows コンピューターでは、想定されている時間は、最後の 12 時間です。

エージェントの状態を表示するには、 **[Update エージェントの準備]** 列のリンクを選択します。 このオプションを選択すると、**ハイブリッド Worker** ウィンドウが開いて、ハイブリッド worker の状態が表示されます。 次の図は、長期間 Update Management に接続されていないエージェントの例を示しています。

![[コンピューター] タブの表示](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>更新の評価を表示する

Update Management が有効になると、 **[更新の管理]** ウィンドウが開きます。 **[不足している更新プログラム]** タブに、デプロイされていない更新プログラムの一覧が表示されます。

## <a name="collect-data"></a>データを収集する

仮想マシンとコンピューターにインストールされたエージェントは、更新プログラムに関するデータを収集します。 エージェントは、そのデータを Azure Update Management に送信します。

### <a name="supported-agents"></a>サポートされているエージェント

次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント |はい |Update Management は、Windows エージェントからシステムの更新プログラムに関する情報を収集した後、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |はい |Update Management は、Linux エージェントからシステムの更新プログラムに関する情報を収集した後、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |はい |Update Management は、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。 |
| Azure ストレージ アカウント |いいえ |Azure Storage には、システムの更新プログラムに関する情報は含まれません。 |

### <a name="collection-frequency"></a>収集の頻度

コンピューターが更新プログラムのコンプライアンスを確認するためにスキャンを完了した後、エージェントによって情報が Azure Monitor ログに一括転送されます。 Windows コンピューターでは、コンプライアンス スキャンは既定で 12 時間ごとに実行されます。

このスキャン スケジュールに加えて、MMA の再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムのコンプライアンスを確認するためのスキャンが開始されます。

Linux コンピューターでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 MMA エージェントを再起動した場合は、コンプライアンス スキャンは 15 分以内に開始されます。

管理対象のコンピューターの更新されたデータがダッシュボードに表示されるまでに、30 分～ 6 時間かかる場合があります。

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとサービス期間と合致する展開をスケジュールします。 デプロイに含める更新の種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

1 台以上の仮想マシンの新しい更新プログラムのデプロイをスケジュールするには、 **［更新管理］** で、 **［更新プログラムの展開のスケジュール］** を選択します。

**[新しい更新プログラムの展開]** ウィンドウで、次の情報を指定します。

- **[名前]** :更新プログラムの展開を識別する一意の名前を入力します。
- **[オペレーティング システム]** : **[Windows]** または **[Linux]** を選択します。
- **[更新するグループ (プレビュー)]** :サブスクリプション、リソース グループ、場所、およびタグの組み合わせに基づいてクエリを定義し、デプロイに含める Azure VM の動的グループを構築します。 詳細については、[動的グループ](automation-update-management.md#using-dynamic-groups)に関するページを参照してください。
- **[更新するマシン]** :保存した検索条件、インポートしたグループを選択するか、[マシン] を選択し、更新するマシンを選択します。 **[マシン]** を選択すると、マシンの準備状況が **[エージェントの更新の準備]** 列に示されます。 更新プログラムの展開をスケジュールする前にマシンの正常性状態を確認できます。 Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../azure-monitor/platform/computer-groups.md)に関するページを参照してください

  ![[新しい更新プログラムの展開] ウィンドウ](./media/manage-update-multi/update-select-computers.png)

- **[更新プログラムの分類]** :更新プログラムの展開に含めるソフトウェアの種類を選択します。 分類の種類の詳細については、「[更新プログラムの分類](automation-update-management.md#update-classifications)」をご覧ください。 分類の種類は次のとおりです。
  - 緊急更新プログラム
  - セキュリティ更新プログラム
  - 更新プログラムのロールアップ
  - Feature Pack
  - Service Pack
  - 定義の更新
  - ツール
  - 更新プログラム

- **[Updates to include/exclude]\(含める/除外する更新プログラム\)** : **[Include/Exclude]\(含める/除外する\)** ページが開きます。 含めるまたは除外する更新プログラムは別のタブに表示されます。 包含を処理する方法については、[包含の動作](automation-update-management.md#inclusion-behavior)に関するページを参照してください。

- **[スケジュール設定]** :既定の日時 (現在の時刻から 30 分後) をそのまま使用できます。 別の時刻を指定することもできます。

   展開を 1 回だけ行うか、定期的なスケジュールで行うかを指定することもできます。 定期的なスケジュールを設定するには、 **[繰り返し]** の下の **[繰り返し]** を選択します。

   ![[スケジュール設定] ダイアログ ボックス](./media/manage-update-multi/update-set-schedule.png)

- **[事前スクリプトと事後スクリプト]** :デプロイの前後に実行するスクリプトを選択します。 詳細については、[事前および事後スクリプトの管理](pre-post-scripts.md)に関するページを参照してください。
- **[メンテナンス期間 (分)]** :更新プログラムを展開する期間を指定します。 この設定により、定義したサービス期間内は変更が確実に実行されます。

- **[Reboot control]\(再起動制御\)** - この設定は、更新デプロイでの再起動の処理方法を決定します。

   |オプション|説明|
   |---|---|
   |必要に応じて再起動| **(既定値)** 必要に応じて、メンテナンス ウィンドウで許可されている場合に再起動します。|
   |常に再起動|再起動は、必要かどうかに関係なく開始されます。 |
   |再起動しない|再起動は、必要かどうかに関係なく抑制されます。|
   |Only reboot - will not install updates (再起動のみ - 更新プログラムをインストールしない)|このオプションは、更新プログラムのインストールを無視し、再起動を開始するだけです。|

スケジュールの構成が完了したら、 **[作成]** ボタンを選択して、状態ダッシュボードに戻ります。 **[スケジュール済み]** の表に、作成した展開スケジュールが表示されます。

> [!NOTE]
> Update Management では、ファースト パーティの更新プログラムの展開と、修正プログラムの事前ダウンロードをサポートしています。 このためには、修正プログラムの適用対象システムでの変更が必要になります。お使いのシステムでこれらの設定を構成する方法については、[ファースト パーティと事前ダウンロードのサポート](automation-update-management.md#firstparty-predownload)に関するセクションを参照してください。

## <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を表示する

スケジュールされた展開の開始後、 **[更新の管理]** の **[更新プログラムの展開]** タブに、展開の状態が表示されます。

展開を現在実行中の場合、状態は **[処理中]** と表示されます。 展開が正常に終了すると、状態が **[成功]** に変わります。

展開時に 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[部分的に失敗]** になります。

![更新プログラムの展開の状態](./media/manage-update-multi/update-view-results.png)

更新プログラムの展開に関するダッシュボードを表示するには、完了した展開を選択します。

**[更新プログラムを実行した結果]** ウィンドウには、仮想マシンに対する更新プログラムの合計数と展開結果が表示されます。 右側の表には、各更新プログラムとインストールの結果の詳細が示されます。 インストールの結果は、次のいずれかの値になります。

- **試行されていません**:定義されたメンテナンス期間に基づき、時間が十分ではなかったため、更新プログラムがインストールされませんでした。
- **成功**:更新できました。
- **失敗**:更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、 **[すべてのログ]** を選択します。

対象となる仮想マシンでの更新プログラムの展開を管理する Runbook のジョブ ストリームを確認するには、[出力] タイルを選択します。

展開で発生したエラーの詳細情報を確認するには、 **[エラー]** を選択します。

## <a name="next-steps"></a>次の手順

- ログ、出力、エラーを含む Update Management の詳細については、「[Azure の Update Management ソリューション](../operations-management-suite/oms-solution-update-management.md)」をご覧ください。

