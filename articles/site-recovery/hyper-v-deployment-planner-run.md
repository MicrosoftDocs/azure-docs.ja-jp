---
title: Azure Site Recovery での Hyper-V の Deployment Planner の実行
description: この記事では、Hyper-V の Azure へのディザスター リカバリーのために Azure Site Recovery Deployment Planner を実行する方法について説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mayg
ms.openlocfilehash: ba1979c940d4a92b3d1a7a52a4f356b2896ece55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74082620"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Hyper-V の Azure へのディザスター リカバリーのために Azure Site Recovery Deployment Planner を実行する

Site Recovery Deployment Planner のコマンドライン ツール (ASRDeploymentPlanner.exe) は、次の 4 つのモードのいずれかで実行できます。 
-   仮想マシン (VM) の一覧の取得
-   [プロファイル](#profile-hyper-v-vms)
-   レポートの生成
-   [スループットの取得](#get-throughput)

まずツールを実行して、1 つまたは複数の Hyper-V ホストから VM のリストを取得します。 次にプロファイリング モードでツールを実行し、VM のデータ変更頻度と IOPS を取得します。 そのうえでツールを実行してレポートを生成し、ネットワーク帯域幅やストレージの要件を把握してください。

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Hyper-V VM のプロファイリング対象リストの取得
まず、プロファイリングの対象となる VM のリストを用意する必要があります。 Deployment Planner ツールの GetVMList モードを使用して、複数の Hyper-V ホストに存在する VM のリストを 1 回のコマンドで生成します。 完全なリストを生成したら、プロファイルする必要のない VM は出力ファイルから削除してかまいません。 その出力ファイルを、その他すべての操作 (プロファイリング、レポート生成、スループット取得) に使用することになります。

ツールの実行対象として Hyper-V クラスター、スタンドアロンの Hyper-V ホスト、またはその両方を指定して、VM リストを生成することができます。

### <a name="command-line-parameters"></a>コマンドライン パラメーター
次の表は、GetVMList モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| パラメーター名 | 説明 |
|---|---|
| -Operation | GetVMList |
| -User | Hyper-V ホストまたは Hyper-V クラスターに接続するためのユーザー名。 ユーザーには管理アクセス権が必要です。|
| -ServerListFile | プロファイリングの対象 VM を含むサーバーのリストが記述されたファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルの各行には、次のいずれかの情報が記述されている必要があります。<ul><li>Hyper-V ホストの名前または IP アドレス</li><li>Hyper-V クラスターの名前または IP アドレス</li></ul><br>**例:** ServerList.txt に次のサーバーを記述します。<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(省略可) この操作の実行中に生成されたデータの格納先となる UNC (汎用名前付け規則) パスまたはローカル ディレクトリ パス。 名前を指定しなかった場合、現在のパス下の ProfiledData という名前のディレクトリが既定のディレクトリとして使用されます。|
|-OutputFile| (省略可) Hyper-V サーバーからフェッチされた VM のリストが保存されるファイル。 名前を指定しなかった場合は、VMList.txt に詳細が保存されます。  このファイルを使用してプロファイリングを開始します。プロファイリングする必要のない VM は、あらかじめファイルから削除してください。|
|-Password|(省略可) Hyper-V ホストに接続するためのパスワード。 パラメーターとして指定しない場合は、コマンドの実行時に指定を求めるメッセージが表示されます。|

### <a name="getvmlist-discovery"></a>GetVMList の検出

- **Hyper-V クラスター**: サーバー リスト ファイルに Hyper-V クラスターの名前を指定した場合、該当するクラスターからすべての Hyper-V ノードが検出され、その各 Hyper-V ホストに存在する VM が取得されます。
**Hyper-V ホスト**: Hyper-V ホストの名前を指定した場合は、まずそれがクラスターに属しているかどうかがチェックされます。 属している場合は、クラスターに属しているノードがフェッチされます。 その後、各 Hyper-V ホストから VM が取得されます。 

プロファイリングの対象となる VM のフレンドリ名または IP アドレスを手動でファイルに列挙してもかまいません。

出力ファイルをメモ帳で開き、プロファイリングするすべての VM の名前を別のファイル (ProfileVMList.txt など) にコピーします。 VM の名前は 1 行につき 1 つです。 このファイルは、他のあらゆる操作 (プロファイリング、レポート生成、スループット取得) で、ツールの -VMListFile パラメーターへの入力として使用されます。

### <a name="examples"></a>例

#### <a name="store-the-list-of-vms-in-a-file"></a>VM のリストをファイルに格納する
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>VM のリストを既定の場所 (-Directory パス) に保存する
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Hyper-V VM のプロファイリング
プロファイリング モードでは、Deployment Planner ツールが各 Hyper-V ホストに接続して VM に関するパフォーマンス データを収集します。 

プロファイリングが運用環境の VM のパフォーマンスに影響を及ぼすことはありません (運用環境の VM に対して直接接続が確立されることはありません)。 Hyper-V ホストから、あらゆるパフォーマンス データが収集されます。

このツールは、Hyper-V ホストを 15 秒おきに照会することでプロファイリングの精度を確保します。 毎分のパフォーマンス カウンター データの平均値を保存します。

クラスターのノード間における VM の移行とホスト内のストレージの移行がシームレスに処理されます。

### <a name="getting-the-vm-list-to-profile"></a>プロファイリング対象 VM リストの取得
プロファイリングする VM のリストを作成する方法については、GetVMList 操作を参照してください。

プロファイリングの対象となる VM のリストを作成したら、プロファイリング モードでツールを実行できます。 

### <a name="command-line-parameters"></a>コマンドライン パラメーター
次の表は、プロファイリング モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。 ツールは、VMware から Azure への移動のシナリオと Hyper-V から Azure への移動のシナリオで共通です。 Hyper-V では、次のパラメーターを適用できます。 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| パラメーター名 | 説明 |
|---|---|
| -Operation | StartProfiling |
| -User | Hyper-V ホストまたは Hyper-V クラスターに接続するためのユーザー名。 ユーザーには管理アクセス権が必要です。|
| -VMListFile | プロファイリングの対象となる VM のリストを含むファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 Hyper-V の場合、このファイルが GetVMList 操作の出力ファイルとして得られます。 このファイルを手動で作成する場合は、サーバー名または IP アドレスの後に VM 名を記述する必要があります (\ で区切り、1 行に 1 つずつ)。 このファイルに指定する VM 名は、Hyper-V ホスト上の VM 名と同じであることが必要です。<br><br>**例:** VMList.txt ファイルに、次のように VM を記述します。<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|プロファイリングを実行する時間 (分)。 最小値は 30 分です。|
|-NoOfHoursToProfile|プロファイリングを実行する時間 (時)。|
|-NoOfDaysToProfile |プロファイリングを実行する時間 (日)。 プロファイリングの実行期間は 7 日間より長くすることをお勧めします。 それだけの期間があれば、実際の環境のワークロード パターンを十分に観察し、正確な推奨を提供することができます。|
|-Virtualization|仮想化の種類 (VMware または Hyper-V)。|
|-Directory|(省略可) プロファイリング中に生成されたプロファイリング データの格納先となる UNC パスまたはローカル ディレクトリ パス。 名前を指定しなかった場合、現在のパスの下の ProfiledData という名前のディレクトリが既定のディレクトリとして使用されます。|
|-Password|(省略可) Hyper-V ホストに接続するためのパスワード。 パラメーターとして指定しない場合は、コマンドの実行時に指定を求めるメッセージが表示されます。|
|-StorageAccountName|(省略可) オンプレミスから Azure へのデータのレプリケーションに関して達成可能なスループットの調査対象となるストレージ アカウントの名前。 このストレージ アカウントにテスト データがアップロードされてスループットが計算されます。 ストレージ アカウントは、汎用 v1 (GPv1) 型にする必要があります。|
|-StorageAccountKey|(省略可) ストレージ アカウントにアクセスするためのキー。 Azure Portal の **[ストレージ アカウント]**  > [<*ストレージ アカウント名*>] >  **[設定]**  >  **[アクセス キー]**  >  **[Key1]** (クラシック ストレージ アカウントの場合はプライマリ アクセス キー) の順に移動します。|
|-Environment|(省略可) Azure ストレージ アカウントのレプリケーション先となる環境。 3 つの値 (AzureCloud、AzureUSGovernment、AzureChinaCloud) のうち、いずれかの値を指定できます。 既定値は AzureCloud です。 このパラメーターは、レプリケーション先のリージョンが Azure 米国政府機関または Azure China 21Vianet であるときに使用します。|

VM のプロファイリング期間は 7 日間より長くすることをお勧めします。 変更頻度のパターンが 1 か月間で変動する場合は、変更頻度が最大となる週をプロファイル期間とすることをお勧めします。 よりよい推奨を得るための理想的なプロファイル期間は 31 日です。 

プロファイリング期間中は ASRDeploymentPlanner.exe が実行状態となります。 ツールには、プロファイリングの時間を日数で入力します。 ツールの簡単なテストや概念実証が目的であれば、プロファイル期間は数時間や数分でもかまいません。 プロファイリング期間として許容される最短時間は 30 分です。 

Hyper-V サーバーから Azure へのレプリケーション時に Azure Site Recovery で得られるスループットの調査対象として、プロファイリング中、必要に応じてストレージ アカウントの名前とキーを指定することができます。 プロファイリング時にストレージ アカウントの名前とキーが渡されないと、達成可能なスループットは計算されません。

複数の VM グループを対象として、Deployment Planner ツールのインスタンスを複数実行することができます。 その場合、プロファイリングの対象となるグループ内やグループ間で、同じ VM 名が重複しないようにしてください。 たとえば、10 台の VM (VM1 から VM10 まで) をプロファイリングしたとします。 数日後、別の 5 台の VM (VM11 から VM15 まで) をプロファイリングします。 VM の 2 番目のセット (VM11 から VM15 まで) に対して、別のコマンド ライン コンソールからツールを実行できます。 

1 つ目のプロファイリング インスタンスとなっていた VM を 2 つ目の VM のセットに含めることは避けてください。または、2 回目の実行時に 1 回目とは異なる出力ディレクトリを使用してください。 Deployment Planner ツールの 2 つのインスタンスで、同じ出力ディレクトリに対して同じ VM をプロファイリングした場合、生成されるレポートは正確ではなくなります。 

既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 上限を変更するには、ASRDeploymentPlanner.exe.config ファイルの MaxVMsSupported キー値を変更します。
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
既定の設定では、たとえば 1,500 台の VM をプロファイリングする場合、2 つの VMList.txt ファイルを作成します。 一方に 1,000 台の VM を含め、もう一方に 500 台の VM を含めます。 Azure Site Recovery Deployment Planner の 2 つのインスタンスを実行して、一方に VMList1.txt を使用し、もう一方に VMList2.txt を使用します。 両方の VMList VM のプロファイリング データを格納するために、同じディレクトリ パスを使用できます。 

レポートを生成するためにツールが実行されるサーバーのハードウェア構成 (特に RAM サイズ) によっては、メモリ不足で操作が失敗する可能性があります。 優れたハードウェアを使用している場合、MaxVMsSupported をより高い値に変更できます。  

VM の構成は、プロファイリング処理の開始時に 1 回だけ捕捉されて、VMDetailList.xml というファイルに保存されます。 この情報は、レポートの生成時に使用されます。 プロファイリングの開始から終了までの間に生じた VM 構成の変化 (コア数、ディスク数、NIC 数が増えるなど) は捕捉されません。 プロファイリングの途中で、その対象となる VM の構成が変わった場合は、以下の回避策によって、レポートの生成時に最新の VM 情報を取得することができます。

* "VMdetailList.xml" をバックアップし、現在の場所からこのファイルを削除します。
* レポートの生成時に -User 引数と -Password 引数を指定します。

プロファイリング コマンドによって、いくつかのファイルがプロファイリング ディレクトリに生成されます。 これらのファイルは一切削除しないでください。削除すると、レポートの生成に影響が生じます。

### <a name="examples"></a>例

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>VM を 30 日間プロファイリングし、オンプレミスから Azure へのレプリケーションのスループットを調査する
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>VM を 15 日間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>ツールの簡単なテストを行うために VM を 60 分間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>概念実証のために VM を 2 時間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>プロファイリングに関する考慮事項

ツールを実行しているサーバーが再起動されるかクラッシュした場合、または Ctrl + C キーでツールを終了した場合、プロファイリング データは保持されます。 ただし直近 15 分間のプロファイリング データは失われる可能性があります。 そのような場合は、サーバーの再起動後に、プロファイリング モードでもう一度ツールを実行する必要があります。

ストレージ アカウントの名前とキーを渡したときは、プロファイリングの最後の段階でスループットが測定されます。 プロファイリングが完了する前にツールを終了した場合、スループットは計算されません。 レポートが生成される前にスループットを調べるために、コマンド ライン コンソールから GetThroughput 操作を実行してください。 そのようにしないと、生成されるレポートにはスループットの情報が出力されません。

Azure Site Recovery では、iSCSI ディスクやパススルー ディスクを含んだ VM がサポートされません。 VM に接続された iSCSI ディスクやパススルー ディスクをこのツールで検出してプロファイリングすることはできません。

## <a name="generate-a-report"></a>レポートの生成
Deployment Planner ツールでは、マクロ有効 Microsoft Excel ファイル (XLSM ファイル) がレポートの出力結果として生成されます。 これには、デプロイの推奨情報がすべてまとめられています。 このレポートは、指定されたディレクトリに DeploymentPlannerReport_<*一意の数値識別子*>.xlsm という名前で格納されます。

プロファイリングが完了したら、レポート生成モードでツールを実行できます。 

### <a name="command-line-parameters"></a>コマンドライン パラメーター
次の表は、レポート生成モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。 ツールは、VMware から Azure への移動と Hyper-V から Azure への移動で共通です。 Hyper-V では、次のパラメーターを適用できます。
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| パラメーター名 | 説明 |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | レポートを生成するプロファイリング対象 VM のリストが記述されたファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 Hyper-V の場合、このファイルが GetVMList 操作の出力ファイルとして得られます。 このファイルを手動で作成する場合は、サーバー名または IP アドレスの後に VM 名を記述する必要があります (\ で区切り、1 行に 1 つずつ)。 このファイルに指定する VM 名は、Hyper-V ホスト上の VM 名と同じであることが必要です。<br><br>**例:** VMList.txt ファイルに、次のように VM を記述します。<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|仮想化の種類 (VMware または Hyper-V)。|
|-Directory|(省略可) プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる UNC パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 名前を指定しなかった場合、現在のパスの下の ProfiledData という名前のディレクトリが既定のディレクトリとして使用されます。|
| -User | (省略可) Hyper-V ホストまたは Hyper-V クラスターに接続するためのユーザー名。 ユーザーには管理アクセス権が必要です。 レポートに使用する VM の最新の構成情報 (ディスク数、コア数、NIC 数など) を取得するには、ユーザーとパスワードを指定します。 この値を指定しなかった場合は、プロファイリング時に収集された構成情報が使用されます。|
|-Password|(省略可) Hyper-V ホストに接続するためのパスワード。 パラメーターとして指定しない場合は、コマンドの実行時に指定を求めるメッセージが表示されます。|
| -DesiredRPO | (省略可) 必要な RPO (回復ポイントの目標) を分単位で指定します。 既定では 15 分です。|
| -Bandwidth | (省略可能) 帯域幅 (メガビット/秒)。 このパラメーターを使用すると、指定した帯域幅で達成できる RPO が計算されます。 |
| -StartDate | (省略可) 開始日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 StartDate は EndDate と一緒に指定する必要があります。 StartDate を指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| -EndDate | (省略可) 終了日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 EndDate は StartDate と一緒に指定する必要があります。 EndDate を指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| -GrowthFactor | (省略可) 増加率 (%)。 既定値は 30% です。 |
| -UseManagedDisks | (省略可) UseManagedDisks: Yes/No。 既定値は Yes です。 1 つのストレージ アカウントに配置できる仮想マシンの数は、仮想マシンのフェールオーバー/テスト フェールオーバーが、非管理対象ディスクではなく、マネージド ディスクに対して実行されるかどうかに基づいて計算されます。 |
|-SubscriptionId |(省略可) サブスクリプションの GUID。 サブスクリプション、それに関連付けられているオファー、ターゲット Azure リージョンに基づき、指定した通貨で最新の料金に関するコスト見積もりレポートを生成する場合に、このパラメーターを使用します。|
|-TargetRegion|(省略可) レプリケーション先となる Azure リージョン。 Azure のコストはリージョンによって異なるので、特定のターゲット Azure リージョンでレポートを生成するために、このパラメーターを使用します。 既定値は、WestUS2 または最近使用したターゲット リージョンです。 「[サポートされるターゲット リージョン](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)」の一覧を参照してください。|
|-OfferId|(省略可) サブスクリプションに関連付けられているオファー。 既定値は MS-AZR-0003P (従量課金制) です。|
|-Currency|(省略可) 生成されたレポートでコストの表示に使用する通貨。 既定値は、米ドル ($) または最近使用した通貨です。 「[サポートされる通貨](hyper-v-deployment-planner-cost-estimation.md#supported-currencies)」の一覧を参照してください。|

既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 上限を変更するには、ASRDeploymentPlanner.exe.config ファイルの MaxVMsSupported キー値を変更します。
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>例
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>既定値でレポートを生成する (プロファイリング データがローカル ドライブにある場合)
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>レポートを生成する (プロファイリング データがリモート サーバーにある場合)
ユーザーには、リモート ディレクトリに対する読み取り/書き込みアクセス権が必要です。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>レプリケーション用にプロビジョニングする帯域幅を指定してレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>増加率を既定値の 30% から 5% に変更してレポートを生成する 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>一部のプロファイリング データでレポートを生成する
たとえば、30 日間分のプロファイリング データがあるときに 20 日間のみを対象としてレポートを生成します。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>RPO を 5 分としてレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>インド ルピーと特定のプラン ID でインド南部 Azure リージョンのレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>計算に使用されるパーセンタイル値
ツールがレポートを生成するとき、読み取り/書き込み IOPS、書き込み IOPS、およびデータ変更頻度の既定のパーセンタイル値は 95 です。 これらの値は、すべての VM のプロファイリング中に収集されます。 そうすることで、レプリケーション先のストレージ アカウントとレプリケーション元の帯域幅の要件を決定する際に、VM の一時的な事象に起因する瞬間的な 100 パーセンタイルを判断から除外することができます。 そのような一時的な事象としては、1 日 1 回実行されるバックアップ ジョブや定期的なデータベース インデックス作成、分析レポート生成アクティビティなど、持続性のない瞬時性イベントが考えられます。

95 パーセンタイル値を使用することで、実際のワークロード特性の真の姿を捉え、それらのワークロードが Azure で実行されている状態で最高のパフォーマンスを得ることができます。 通常、この値を変更する必要はありません。 あえて値を変更する場合 (たとえば 90 パーセンタイルに下げたい場合) は、既定のフォルダーにある構成ファイル ASRDeploymentPlanner.exe.config を編集、保存すると、既にあるプロファイリング データで新しいレポートを生成することができます。
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>増加率に関する考慮事項
時間の経過と共に使用率が増える可能性を想定し、ワークロード特性の増大を考慮することはきわめて重要です。 いったん保護された状態を確保した後でワークロードの特性が変化した場合、保護に使用するストレージ アカウントを切り替えるためには、一度保護を無効にしてから再度有効にする必要があります。

たとえば現在、VM のレプリケーションが Standard ストレージ アカウントで問題なく行われているとします。 3 か月後には、おそらく次のような変化が生じています。

1. たとえば、VM で実行されているアプリケーションのユーザー数が増加します。
2. そうなれば VM の変更頻度が増えます。その増大に Azure Site Recovery のレプリケーションを追い付かせるためには、Premium Storage への移行が必要になるでしょう。
3. Premium Storage アカウントの保護を無効にし、再度有効にする必要があります。

そのため、デプロイ計画の段階で増加を考慮することが強く推奨されています。 既定値は 30% となっていますが、アプリケーションの使用パターンや成長予測について一番よく知っているのは皆さん自身です。 この値はレポートの生成時に適宜変更してください。 さらに、同じプロファイリング データとさまざまな成長要素で、複数のレポートを生成することができます。 そうすることで、最も効果的なレプリケーション先のストレージとレプリケーション元の帯域幅を特定できます。 

生成された Microsoft Excel レポートには、次の情報が含まれています。

* [On-premises summary (オンプレミス サマリー)](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Recommendations (推奨事項)](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM-storage placement (VM-ストレージの配置)](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Compatible VMs (適合 VM)](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (不適合 VM)](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [On-premises storage requirement (オンプレミス ストレージ要件)](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR batching (IR バッチ分割)](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Cost estimation (コスト見積もり)](hyper-v-deployment-planner-cost-estimation.md)

![Deployment Planner レポート](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>スループットの取得
オンプレミスから Azure へのレプリケーション時に Azure Site Recovery が達成できるスループットを見積もるには、GetThroughput モードで Deployment Planner ツールを実行します。 このツールでは、それが実行されているサーバーを起点としてスループットが計算されます。 このサーバーは、保護対象の VM が存在する Hyper-V サーバーであることが理想です。 

### <a name="command-line-parameters"></a>コマンドライン パラメーター 
コマンド ライン コンソールを開いて、Azure Site Recovery のデプロイ プランニング ツールのフォルダーに移動します。 以下のパラメーターを指定して ASRDeploymentPlanner.exe を実行します。
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 パラメーター名 | 説明 |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|仮想化の種類 (VMware または Hyper-V)。|
|-Directory|(省略可) プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる UNC パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 名前を指定しなかった場合、現在のパスの下の ProfiledData という名前のディレクトリが既定のディレクトリとして使用されます。|
| -StorageAccountName | オンプレミスから Azure へのデータのレプリケーションに関して、使用帯域幅の調査に使うストレージ アカウントの名前。 このストレージ アカウントにテスト データがアップロードされて使用帯域幅が計算されます。 ストレージ アカウントは、汎用 v1 (GPv1) 型にする必要があります。|
| -StorageAccountKey | ストレージ アカウントにアクセスするためのストレージ アカウント キー。 Azure Portal の **[ストレージ アカウント]**  > [<*ストレージ アカウント名*>] >  **[設定]**  >  **[アクセス キー]**  >  **[Key1]** の順に移動します。|
| -VMListFile | 使用帯域幅の計算に関して、プロファイリングの対象となる VM のリストを含んだファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 Hyper-V の場合、このファイルが GetVMList 操作の出力ファイルとして得られます。 このファイルを手動で作成する場合は、サーバー名または IP アドレスの後に VM 名を記述する必要があります (\ で区切り、1 行に 1 つずつ)。 このファイルに指定する VM 名は、Hyper-V ホスト上の VM 名と同じであることが必要です。<br><br>**例:** VMList.txt ファイルに、次のように VM を記述します。<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(省略可) Azure ストレージ アカウントのレプリケーション先となる環境。 3 つの値 (AzureCloud、AzureUSGovernment、AzureChinaCloud) のうち、いずれかの値を指定できます。 既定値は AzureCloud です。 このパラメーターは、レプリケーション先の Azure リージョンが Azure 米国政府機関または Azure China 21Vianet であるときに使用します。|

### <a name="example"></a>例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>スループットに関する考慮事項

ツールは、指定されたディレクトリにいくつかの 64 MB の asrvhdfile<*番号*>.vhd ファイル (<*番号*> はファイルの番号) を作成します。 これらのファイルをストレージ アカウントにアップロードすることによってスループットが調査されます。 これらのファイルはすべて、スループットの測定後にストレージ アカウントとローカル サーバーから削除されます。 スループットの計算中に何かの理由でツールが終了した場合、ストレージ アカウントとローカル サーバーからファイルが削除されません。 この場合は、ファイルを手動で削除する必要があります。

スループットは、指定された時点で測定されます。 これは、他のすべての条件が変わらない場合に Azure Site Recovery がレプリケーション中に達成できる最大スループットです。 たとえば、いずれかのアプリケーションが同じネットワーク上で、それまでよりも多くの帯域幅を使い始めた場合、レプリケーション時の実際のスループットは変化します。 保護されている VM のデータ変更頻度が高いときに GetThroughput 操作を実行した場合、スループットの測定結果に差異が生じます。 

さまざまな状況で得られるスループット レベルを把握するために、プロファイリング中はいろいろなタイミングでツールを実行することをお勧めします。 レポートには、最後に測定されたスループットが表示されます。

> [!NOTE]
> ストレージと CPU の特性が Hyper-V サーバーと同じサーバーでツールを実行してください。

レプリケーションに関しては、RPO を 100% の時間満たすうえで推奨される帯域幅を設定してください。 適切な帯域幅を設定したにもかかわらず、ツールから報告される達成スループットに改善が見られない場合は、次の作業を行ってください。

1. Azure Site Recovery のスループットを制限するネットワーク QoS (サービス品質) の問題が存在しているかどうかを確認します。
2. 物理的にサポートされる最も近い Microsoft Azure リージョンに Azure Site Recovery コンテナーが存在し、ネットワーク待ち時間が最小限で済んでいるかどうかを確認します。
3. ローカル ストレージの特性を確認し、ハードウェアの強化 (例: HDD から SSD など) が可能であるかどうかを調べます。

    
## <a name="next-steps"></a>次のステップ
* [生成されたレポートの分析](hyper-v-deployment-planner-analyze-report.md)
