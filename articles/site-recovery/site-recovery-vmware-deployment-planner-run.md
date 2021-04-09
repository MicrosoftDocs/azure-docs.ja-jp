---
title: Azure Site Recovery を使用した VMware ディザスター リカバリーのために Deployment Planner を実行する
description: この記事では、VMware の Azure へのディザスター リカバリーのために Azure Site Recovery Deployment Planner を実行する方法について説明します。
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004723"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>VMware ディザスター リカバリーのために Deployment Planner を実行する
この記事は、VMware から Azure へのレプリケーションを行う運用環境のデプロイに関する Azure Site Recovery Deployment Planner のユーザー ガイドです。


## <a name="modes-of-running-deployment-planner"></a>Deployment Planner の実行モード
このコマンド ライン ツール (ASRDeploymentPlanner.exe) は、次の 3 とおりのモードで実行できます。

1.  [プロファイリング](#profile-vmware-vms)
2.  [レポートの生成](#generate-report)
3.  [スループットの取得](#get-throughput)

最初はプロファイリング モードでツールを実行し、VM のデータ変更頻度と IOPS を取得します。 そのうえでツールを実行してレポートを生成し、ネットワーク帯域幅やストレージの要件、DR コストを把握します。

## <a name="profile-vmware-vms"></a>VMware VM のプロファイリング
プロファイリング モードでは、Deployment Planner ツールが vCenter サーバー/vSphere ESXi ホストに接続して VM に関するパフォーマンス データを収集します。

* プロファイリングが運用環境の VM のパフォーマンスに影響を及ぼすことはありません (運用環境の VM に対して直接接続が確立されることはありません)。 すべてのパフォーマンス データは vCenter サーバー/vSphere ESXi ホストから収集されます。
* サーバーに対するプロファイリングの影響を無視できる程度にまで抑えるため、vCenter サーバー/vSphere ESXi ホストがツールによって照会される頻度は 15 分に 1 回となります。 パフォーマンス カウンター データは絶えず保存されるため、そのことでプロファイリングの精度が損なわれることはありません。

### <a name="create-a-list-of-vms-to-profile"></a>プロファイリングする VM のリストを作成する
まず、プロファイリングの対象となる VM のリストを用意する必要があります。 以降の手順に示した VMware vSphere PowerCLI コマンドを使用すれば、vCenter サーバー/vSphere ESXi ホストにあるすべての VM の名前を取得できます。 または、プロファイリングの対象となる VM のフレンドリ名または IP アドレスを手動でファイルに列挙してもかまいません。

1. VMware vSphere PowerCLI がインストールされている VM にサインインします。
2. VMware vSphere PowerCLI コンソールを開きます。
3. スクリプトの実行ポリシーが有効になっていることを確認します。 無効になっている場合は、VMware vSphere PowerCLI コンソールを管理者モードで起動し、次のコマンドを実行して有効にします。

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. Connect-VIServer がコマンドレットの名前として認識されない場合は、別途次のコマンドを実行する必要があります。

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. vCenter サーバー/vSphere ESXi ホスト上にあるすべての VM の名前を取得して、リストを .txt ファイルに格納するには、次の 2 つのコマンドを実行します。
&lsaquo;server name&rsaquo;、&lsaquo;user name&rsaquo;、&lsaquo;password&rsaquo;、&lsaquo;outputfile.txt&rsaquo; は、実際の値に置き換えてください。

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. 出力ファイルをメモ帳で開き、プロファイリングするすべての VM の名前を別のファイル (ProfileVMList.txt など) にコピーします。VM の名前は 1 行につき 1 つです。 このファイルをコマンド ライン ツールの *-VMListFile* パラメーターの入力として使用します。

    ![Deployment Planner に入力として渡す VM 名リスト
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>プロファイリングの開始
プロファイリングの対象となる VM のリストを作成したら、プロファイリング モードでツールを実行できます。 プロファイリング モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧を次に示します。

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| パラメーター名 | 説明 |
|---|---|
| -Operation | StartProfiling |
| -Server | プロファイリングの対象となる VM がある vCenter サーバー/vSphere ESXi ホストの完全修飾ドメイン名または IP アドレス。|
| -User | vCenter サーバー/vSphere ESXi ホストに接続するためのユーザー名。 ユーザーには、少なくとも読み取り専用アクセス権が必要です。|
| -VMListFile | プロファイリングの対象となる VM のリストを含むファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルには、1 行につき 1 つの VM 名または IP アドレスが記述されている必要があります。 このファイルに指定する仮想マシン名は、vCenter サーバー/vSphere ESXi ホスト上の VM 名と一致している必要があります。<br>たとえば VMList.txt ファイルに、次のように VM を記述することができます。<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
|-NoOfMinutesToProfile|プロファイリングを実行する時間 (分)。 最小値は 30 分です。|
|-NoOfHoursToProfile|プロファイリングを実行する時間 (時)。|
| -NoOfDaysToProfile | プロファイリングを実行する日数。 プロファイリングの実行期間は 7 日間より長くすることをお勧めします。それだけの期間があれば、実際の環境のワークロード パターンを十分に観察し、正確な情報を把握することができます。 |
|-Virtualization|仮想化の種類を指定します (VMware または Hyper-V)。|
| -Directory | (省略可) プロファイリング中に生成されたプロファイリング データの格納先となる UNC (汎用名前付け規則) パスまたはローカル ディレクトリ パス。 ディレクトリ名を指定しなかった場合、現在のパスの "ProfiledData" という名前のディレクトリが既定のディレクトリとして使用されます。 |
| -Password | (省略可) vCenter サーバー/vSphere ESXi ホストに接続するためのパスワード。 ここで指定しなかった場合は、コマンドの実行時に入力を求められます。|
|-Port|(省略可) vCenter/ESXi ホストに接続するためのポート番号。 既定のポートは 443 です。|
|-Protocol| (省略可) vCenter に接続するためのプロトコル ("http" または "https") を指定します。 既定のプロトコルは https です。|
| -StorageAccountName | (省略可) オンプレミスから Azure へのデータのレプリケーションに関して達成可能なスループットの調査対象となるストレージ アカウントの名前。 このストレージ アカウントにテスト データがアップロードされてスループットが計算されます。 ストレージ アカウントは、汎用 v1 (GPv1) 型にする必要があります。 |
| -StorageAccountKey | (省略可) ストレージ アカウントにアクセスするためのストレージ アカウント キー。 Azure Portal の [ストレージ アカウント]、[<*ストレージ アカウント名*>]、[設定]、[アクセス キー]、[Key1] の順に移動します。 |
| -Environment | (省略可) レプリケーション先となる Azure ストレージ アカウント環境。 AzureCloud、AzureUSGovernment、AzureChinaCloud の 3 つのうち、いずれかの値を指定できます。 既定値は AzureCloud です。 このパラメーターは、レプリケーション先の Azure リージョンが Azure 米国政府機関または Azure China 21Vianet であるときに使用します。 |


VM のプロファイリング期間は 7 日間より長くすることをお勧めします。 変更頻度のパターンが 1 か月間で変動する場合は、変更頻度が最大となる週をプロファイル期間とすることをお勧めします。 理想的なプロファイル期間は 31 日で、そうすれば、よりよいアドバイスが得られます。 プロファイリング期間中は ASRDeploymentPlanner.exe が実行状態となります。 ツールには、プロファイリングの時間を日数で入力します。 ツールの簡単なテストや概念実証が目的であれば、プロファイル期間は数時間や数分でもかまいません。 プロファイリング期間として許容される最短時間は 30 分です。

構成サーバーまたはプロセス サーバーから Azure へのレプリケーション時に Site Recovery で得られるスループットの調査対象として、プロファイリング中、必要に応じてストレージ アカウントの名前とキーを指定することができます。 プロファイリング時にストレージ アカウントの名前とキーが渡されないと、達成可能なスループットは計算されません。

複数の VM グループを対象として、Deployment Planner ツールのインスタンスを複数実行することができます。 その場合、プロファイリングの対象となるグループ内やグループ間で、同じ VM 名が重複しないようにしてください。 たとえば 10 台の VM (VM1 ～ VM10) をプロファイリングし、数日後に別の 5 台の VM (VM11 ～ VM15) をプロファイリングする必要が生じたとします。この場合、2 つ目の VM グループ (VM11 ～ VM15) について、別のコマンド ライン コンソールからツールを実行することができます。 1 つ目のプロファイリング インスタンスの対象となっていた VM 名を 2 つ目の VM グループに含めることは避けてください。または 2 回目の実行時には、1 回目とは異なる出力ディレクトリを使用してください。 Deployment Planner ツールの 2 つのインスタンスで、同じ出力ディレクトリに対して同じ VM をプロファイリングした場合、生成されるレポートは正確ではなくなります。

既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 上限を変更するには、*ASRDeploymentPlanner.exe.config* ファイルの MaxVMsSupported キー値を変更します。
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
既定の設定では、たとえば 1,500 台の VM をプロファイリングする場合に 2 つの VMList.txt ファイルを作成します。 1 つのリストには 1,000 台の VM が含まれており、もう 1 つのリストには 500 台の VM が含まれています。 Azure Site Recovery Deployment Planner の 2 つのインスタンスを実行して、一方に VMList1.txt を使用し、もう一方に VMList2.txt を使用します。 両方の VMList VM のプロファイリング データを格納するために、同じディレクトリ パスを使用できます。

ハードウェア構成 (特にレポートを生成するためにツールが実行されるサーバーの RAM サイズ) によっては、メモリ不足で操作が失敗する可能性があることが確認されています。 優れたハードウェアを使用している場合、MaxVMsSupported をより高い値に変更できます。  

vCenter サーバーが複数ある場合、プロファイリングで vCenter サーバーごとに 1 つの ASRDeploymentPlanner インスタンスを実行する必要があります。

VM の構成は、プロファイリング処理の開始時に 1 回だけ捕捉されて、VMDetailList.xml というファイルに保存されます。 この情報は、レポートの生成時に使用されます。 プロファイリングの開始から終了までの間に生じた VM 構成の変化 (コア数、ディスク数、NIC 数が増えるなど) は捕捉されません。 パブリック プレビューを使用したプロファイリングの途中で、その対象となる VM の構成が変わった場合は、以下の回避策によって、レポートの生成時に最新の VM 情報を取得することができます。

* "VMdetailList.xml" をバックアップし、現在の場所からこのファイルを削除します。
* レポートの生成時に -User 引数と -Password 引数を指定します。

プロファイリング コマンドによって、いくつかのファイルがプロファイリング ディレクトリに生成されます。 これらのファイルは一切削除しないでください。削除すると、レポートの生成に影響が生じます。

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>例 1:VM を 30 日間プロファイリングし、オンプレミスから Azure へのレプリケーションのスループットを調査する
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>例 2:VM を 15 日間プロファイリングする

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>例 3: ツールの簡単なテストを行うために VM を 60 分間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>例 4: 概念実証のために VM を 2 時間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* ツールを実行しているサーバーが再起動されるかクラッシュした場合、または Ctrl + C キーでツールを終了した場合、プロファイリング データは保持されます。 ただし直近 15 分間のプロファイリング データは失われる可能性があります。 そのような場合は、サーバーの再起動後に、プロファイリング モードでもう一度ツールを実行する必要があります。
>* ストレージ アカウントの名前とキーを渡したときは、プロファイリングの最後の段階でスループットが測定されます。 プロファイリングが完了する前にツールを終了した場合、スループットは計算されません。 レポートが生成される前にスループットを調べるために、コマンド ライン コンソールから GetThroughput 操作を実行してください。 そのようにしないと、生成されるレポートにはスループットの情報が出力されません。


## <a name="generate-report"></a>レポートの生成
Deployment Planner ツールでは、デプロイの推奨情報をすべてまとめたマクロ有効 Microsoft Excel ファイル (XLSM ファイル) がレポートの出力結果として生成されます。 このレポートは、指定されたディレクトリに `DeploymentPlannerReport_<unique numeric identifier>.xlsm` という名前で格納されます。

>[!NOTE]
>レポートの生成には、Excel 2013 以降がインストールされた Windows PC または Windows Server が必要です。 コストの見積もりを作成するには、このマシンの小数点記号を "." として構成する必要があります。 小数点の記号として "," を設定している場合は、コントロール パネルの [日付、時刻、または数値の形式の変更] に移動し、[追加の設定] に移動して小数点の記号を "." に変更します。

プロファイリングが完了したら、レポート生成モードでツールを実行できます。 次の表は、レポート生成モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|パラメーター名 | 説明 |
|-|-|
| -Operation | GenerateReport |
| -Server |  レポートを生成するプロファイリング対象の VM がある vCenter/vSphere サーバーの完全修飾ドメイン名または IP アドレス (プロファイリング時と同じ名前または IP アドレスを使用します)。 プロファイリング時に vCenter サーバーを使用した場合は、レポートの生成対象として vSphere サーバーを使用することはできません (その逆も同様)。|
| -VMListFile | レポートを生成するプロファイリング対象 VM のリストが記述されたファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルには、1 行につき 1 つの VM 名または IP アドレスが記述されている必要があります。 このファイルに指定する VM 名は、vCenter サーバー/vSphere ESXi ホスト上の VM 名と同じであること、またプロファイリング時に使用されたものと一致している必要があります。|
|-Virtualization|仮想化の種類を指定します (VMware または Hyper-V)。|
| -Directory | (省略可) プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる UNC パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 名前を指定しなかった場合は、"ProfiledData" ディレクトリが使用されます。 |
| -GoalToCompleteIR | (省略可) プロファイリング対象 VM の初回レプリケーションが完了するまでの目標所要時間。 生成されたレポートには、指定した時間内に初回レプリケーションを完了するために必要な VM の台数が記載されます。 既定値は 72 時間です。 |
| -User | (省略可) vCenter/vSphere サーバーへの接続に使用するユーザー名。 レポートに使用する VM の最新の構成情報 (ディスク数、コア数、NIC 数など) を取得するために使用されます。 名前を指定しなかった場合は、プロファイリングの開始時に収集された構成情報が使用されます。 |
| -Password | (省略可) vCenter サーバー/vSphere ESXi ホストに接続するためのパスワード。 パスワードをパラメーターとして指定しなかった場合は、後でコマンドの実行時に入力を求められます。 |
|-Port|(省略可) vCenter/ESXi ホストに接続するためのポート番号。 既定のポートは 443 です。|
|-Protocol|(省略可) vCenter に接続するためのプロトコル ("http" または "https") を指定します。 既定のプロトコルは https です。|
| -DesiredRPO | (省略可) 必要な RPO (回復ポイントの目標) を分単位で指定します。 既定では 15 分です。|
| -Bandwidth | 帯域幅 (Mbps)。 このパラメーターで指定した帯域幅で達成できる RPO が計算されます。 |
| -StartDate | (省略可) 開始日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 *StartDate* は *EndDate* と一緒に指定する必要があります。 StartDate を指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| -EndDate | (省略可) 終了日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 *EndDate* は *StartDate* と一緒に指定する必要があります。 EndDate を指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| -GrowthFactor | (省略可) 増加率 (%)。 既定値は 30% です。 |
| -UseManagedDisks | (省略可) UseManagedDisks (Yes/No)。 既定値は Yes です。 1 つのストレージ アカウントに配置できる仮想マシンの数は、仮想マシンのフェールオーバー/テスト フェールオーバーが、非管理対象ディスクではなく、マネージド ディスクに対して実行されることを想定して計算されます。 |
|-SubscriptionId |(省略可) サブスクリプションの GUID。 このパラメーターは、サブスクリプションに基づいた最新の価格、サブスクリプションに関連付けられたプラン、および **指定された通貨** の特定のターゲット Azure リージョンに関するコスト見積もりレポートを生成する必要がある場合に必要なことに注意してください。|
|-TargetRegion|(省略可) レプリケーション先となる Azure リージョン。 Azure のコストはリージョンによって異なります。そこで、特定のターゲット Azure リージョンでレポートを生成するために、このパラメーターを使用します。<br>WestUS2 または最近使用したターゲット リージョンが既定値となります。<br>「[サポートされるターゲット リージョン](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions)」の一覧を参照してください。|
|-OfferId|(省略可) 指定されたサブスクリプションに関連付けられているオファー。 既定値は MS-AZR-0003P (従量課金制) です。|
|-Currency|(省略可) 生成されたレポートでコストの表示に使用する通貨。 米ドル ($) または最近使用した通貨が既定値となります。<br>「[サポートされる通貨](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)」の一覧を参照してください。|

既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 上限を変更するには、*ASRDeploymentPlanner.exe.config* ファイルの MaxVMsSupported キー値を変更します。
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>例 1:既定値でレポートを生成する (プロファイリング データがローカル ドライブにある場合)
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>例 2:レポートを生成する (プロファイリング データがリモート サーバーにある場合)
ユーザーには、リモート ディレクトリに対する読み取り/書き込みアクセス権が必要です。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>例 3: 特定の帯域幅と初回レプリケーションの目標所要時間を指定してレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>例 4: 増加率を既定値の 30% から 5% に変更してレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>例 5:一部のプロファイリング データでレポートを生成する
たとえば、30 日間分のプロファイリング データがあるときに 20 日間のみを対象としてレポートを生成します。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>例 6:RPO を 5 分としてレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>例 7:インド ルピーと特定のプラン ID でインド南部 Azure リージョンのレポートを生成する

サブスクリプション ID は、特定の通貨のコスト レポートを生成するために必要なことに注意してください。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>計算に使用されるパーセンタイル値
**プロファイリング時に収集されたパフォーマンス メトリックに対し、レポートの生成時に使用される既定のパーセンタイル値について**

すべての VM において、プロファイリング時に収集される読み取り/書き込みの IOPS、書き込み IOPS、データの変更頻度には、既定で 95 パーセンタイル値が使用されます。 そうすることで、レプリケーション先のストレージ アカウントとレプリケーション元の帯域幅の要件を決定する際に、VM の一時的な事象に起因する瞬間的な 100 パーセンタイルを判断から除外することができます。 そのような一時的な事象としては、1 日 1 回実行されるバックアップ ジョブや定期的なデータベース インデックス作成、分析レポート生成アクティビティなど、持続性のない瞬時性イベントが考えられます。

95 パーセンタイル値を使用することで、実際のワークロード特性の真の姿を捉え、それらのワークロードが Azure で実行されている状態でのベストなパフォーマンスを把握することができます。 通常この値を変更する必要はありません。 あえて値を変更する場合 (たとえば 90 パーセンタイルに下げたい場合) は、既定のフォルダーにある構成ファイル *ASRDeploymentPlanner.exe.config* を編集、保存すれば、既にあるプロファイリング データで新しいレポートを生成することができます。
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>増加率に関する考慮事項
**デプロイ計画時に増加率を考慮すべき理由**

時間の経過と共に使用率が増える可能性を想定し、ワークロード特性の増大を考慮することはきわめて重要です。 いったん保護された状態を確保した後でワークロードの特性が変化した場合、保護に使用するストレージ アカウントを切り替えるためには、一度保護を無効にしてから再度有効にする必要があります。

たとえば現在、VM のレプリケーションが Standard ストレージ アカウントで問題なく行われているとします。 3 か月後には、おそらくさまざまな変化が生じています。

* たとえば、VM で実行されているアプリケーションのユーザー数が増加します。
* そうなれば VM の変更頻度が増えます。その増大に Site Recovery のレプリケーションを追い付かせるためには、Premium Storage への移行が必要になるでしょう。
* その場合、一度保護を無効にしてから、Premium ストレージ アカウントに切り替えて再度、保護を有効にする必要があります。

そのために、デプロイ計画の段階で増加を考慮することが強く推奨されています。既定値は 30% となっていますが、 アプリケーションの使用パターンや予想される新規ユーザーについて一番よく知っているのは皆さん自身です。この値は、レポートの生成時に適宜変更できます。 また、同じプロファイリング データで、増加率を変えながら複数のレポートを生成することができるほか、最も効果的なレプリケーション先のストレージとレプリケーション元の帯域幅を特定できます。

生成された Microsoft Excel レポートには、次の情報が含まれています。

* [On-Premises Summary (オンプレミス サマリー)](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Recommendations (推奨事項)](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM<->Storage Placement (VM<->ストレージの配置)](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Compatible VMs (適合 VM)](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (不適合 VM)](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Cost Estimation (コスト見積もり)](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>スループットの取得

オンプレミスから Azure へのレプリケーション時に Site Recovery が達成できるスループットを見積もるには、GetThroughput モードで Deployment Planner ツールを実行します。 このツールでは、それが実行されているサーバーを起点としてスループットが計算されます。 このサーバーは、構成サーバーのサイズ ガイドラインに準拠していることが理想です。 Site Recovery のインフラストラクチャ コンポーネントをオンプレミスにデプロイ済みである場合は、その構成サーバーでツールを実行してください。

コマンド ライン コンソールを開いて、Site Recovery のデプロイ プランニング ツール フォルダーに移動します。 以下のパラメーターを指定して ASRDeploymentPlanner.exe を実行してください。

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|パラメーター名 | 説明 |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|仮想化の種類を指定します (VMware または Hyper-V)。|
| -Directory | (省略可) プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる UNC パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 ディレクトリ名を指定しなかった場合は、"ProfiledData" ディレクトリが使用されます。 |
| -StorageAccountName | オンプレミスから Azure へのデータのレプリケーションに関して、使用帯域幅の調査に使うストレージ アカウントの名前。 このストレージ アカウントにテスト データがアップロードされて使用帯域幅が計算されます。 ストレージ アカウントは、汎用 v1 (GPv1) 型にする必要があります。|
| -StorageAccountKey | ストレージ アカウントにアクセスするためのストレージ アカウント キー。 Azure Portal の [ストレージ アカウント]、[<*ストレージ アカウント名*>]、[設定]、[アクセス キー]、[Key1] \(クラシック ストレージ アカウントの場合は [プライマリ アクセス キー]) の順に移動します。 |
| -VMListFile | 使用帯域幅の計算に関して、プロファイリングの対象となる VM のリストを含んだファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルには、1 行につき 1 つの VM 名または IP アドレスが記述されている必要があります。 このファイルに指定する VM 名は、vCenter サーバー/vSphere ESXi ホスト上の VM 名と一致させる必要があります。<br>たとえば VMList.txt ファイルに、次のように VM を記述することができます。<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Environment | (省略可) レプリケーション先となる Azure ストレージ アカウント環境。 AzureCloud、AzureUSGovernment、AzureChinaCloud の 3 つのうち、いずれかの値を指定できます。 既定値は AzureCloud です。 このパラメーターは、レプリケーション先の Azure リージョンが Azure 米国政府機関または Azure China 21Vianet であるときに使用します。 |

Deployment Planner ツールは、指定されたディレクトリに 64 MB の asrvhdfile<#>.vhd ファイル ("#" はファイルの番号) をいくつか作成します。 これらのファイルをストレージ アカウントにアップロードすることによってスループットが調査されます。 これらのファイルはすべて、スループットの測定後にストレージ アカウントとローカル サーバーから削除されます。 スループットの計算中に何らかの理由でツールが終了した場合、ストレージとローカル サーバーからファイルが削除されません。 この場合は、ファイルを手動で削除する必要があります。

スループットは、特定の時点で測定されるものです。そのときと同じ条件下で Site Recovery がレプリケーション時に達成できる最大スループットと考えてください。 たとえば、いずれかのアプリケーションが同じネットワーク上で、それまでよりも多くの帯域幅を使い始めた場合、レプリケーション時の実際のスループットは変化します。 GetThroughput コマンドを構成サーバーから実行する場合、保護されている VM や実行中のレプリケーションのことまでは考慮されません。 保護されている VM のデータ変更頻度が高いときに GetThroughput 操作を実行した場合、スループットの測定結果に差異が生じます。 さまざまな状況で得られるスループット レベルを把握するために、プロファイリング中はいろいろなタイミングでツールを実行することをお勧めします。 レポートには、最後に測定されたスループットが表示されます。

### <a name="example"></a>例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Deployment Planner ツールは、ストレージと CPU の特性が構成サーバーと同じサーバーで実行してください。
>
> レプリケーションに関しては、RPO を 100% の時間満たすうえで推奨される帯域幅を設定してください。 適切な帯域幅を設定したにもかかわらず、ツールから報告される達成スループットに改善が見られない場合は、次の作業を行ってください。
>
>  1. Site Recovery のスループットを制限するネットワーク QoS (サービス品質) が存在しているかどうかを確認します。
>
>  2. 物理的にサポートされる最も近い Microsoft Azure リージョンに Site Recovery コンテナーが存在し、ネットワーク待ち時間が最小限で済んでいるかどうかを確認します。
>
>  3. ローカル ストレージの特性を確認し、ハードウェアの強化 (例: HDD から SSD など) が可能であるかどうかを調べます。
>
>  4. [レプリケーションに使用されるネットワーク帯域幅を増やす](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)ために、プロセス サーバーで Site Recovery の設定を変更します。

## <a name="next-steps"></a>次のステップ
* [生成されたレポートの分析](site-recovery-vmware-deployment-planner-analyze-report.md)
