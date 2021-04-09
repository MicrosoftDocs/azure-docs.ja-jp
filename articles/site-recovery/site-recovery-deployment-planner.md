---
title: VMware ディザスター リカバリーのための Azure Site Recovery Deployment Planner
description: Azure Site Recovery Deployment Planner による VMware VM から Azure へのディザスター リカバリーについて説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mayg
ms.openlocfilehash: 4291e8438f70e2e7190cd4dc6c890b5b325f2324
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100360909"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>VMware から Azure 用の Azure Site Recovery Deployment Planner について
この記事は、VMware から Azure へのレプリケーションを行う運用環境のデプロイに関する Azure Site Recovery Deployment Planner のユーザー ガイドです。

## <a name="overview"></a>概要

Azure Site Recovery を使用して VMware 仮想マシン (VM) を保護する前に、必要な RPO (回復ポイントの目標) を達成するうえで十分な帯域幅を日次データ変化率に基づいて割り当てます。 適切な台数の構成サーバーとプロセス サーバーをオンプレミスにデプロイしてください。

また、レプリケーション先となる Azure Storage アカウントの種類と数を見極めて作成する必要があります。 レプリケーション元となる運用サーバーの使用量が時間経過に伴って増えることを考慮し、その増加率を見越して、Standard ストレージ アカウントまたは Premium ストレージ アカウントを作成することになります。 ストレージの種類は、読み取り/書き込みの [IOPS] (I/O Operations Per Second) やデータの変更頻度といったワークロード特性と Site Recovery の制限に基づいて VM ごとに選択します。

 Site Recovery Deployment Planner は、Hyper-V から Azure へのディザスター リカバリー シナリオと VMware から Azure へのディザスター リカバリー シナリオの両方で使用できるコマンドライン ツールです。 このツールを使用すると、(運用環境には一切影響を与えることなく) リモートから VMware VM をプロファイリングして、レプリケーションとテスト フェールオーバーに必要な帯域幅要件とストレージ要件を把握することができます。 このツールは、Site Recovery のコンポーネントを一切オンプレミスにインストールせずに実行することができます。 達成スループットの実績値を正確に把握するために、本番デプロイの最初のステップの 1 つとして、最終的にデプロイすることになる Site Recovery 構成サーバーの最低限の要件を満たした Windows Server で Planner を実行してください。

このツールで把握できる情報は次のとおりです。

**適合性の評価**

* ディスク数、ディスク サイズ、IOPS、変更頻度、ブートの種類 (EFI/BIOS)、OS バージョンに基づく VM の適格性評価

**ネットワーク帯域幅ニーズと RPO の評価**

* 差分レプリケーションに必要な推定ネットワーク帯域幅
* オンプレミスから Azure までの間で Site Recovery が得られるスループット
* 束ねる VM の数 (初回レプリケーションを所定の時間内に完了するための推定帯域幅に基づく)
* 特定の帯域幅で達成できる RPO
* プロビジョニングされた帯域幅が低かった場合に、必要な RPO に生じる影響

**Azure インフラストラクチャの要件**

* VM ごとに必要なストレージの種類 (Standard ストレージまたは Premium ストレージ)
* レプリケーション用に設定する Standard ストレージ アカウントと Premium ストレージ アカウントの総数 (キャッシュ ストレージ アカウントを含む)
* ストレージ アカウントの命名に関する推奨事項 (Storage ガイダンスに基づく)
* サブスクリプションのテスト フェールオーバー (またはフェールオーバー) の前にセットアップする Azure コアの数
* Azure VM の推奨サイズ (オンプレミスの VM ごと)

**オンプレミス インフラストラクチャの要件**
* オンプレミスにデプロイする必要のある構成サーバーとプロセス サーバーの台数

**Azure へのディザスター リカバリーの推定コスト**
* Azure へのディザスター リカバリーの推定合計コスト: コンピューティング、ストレージ、ネットワーク、Site Recovery ライセンスのコスト
* VM あたりの詳細なコスト分析


>[!IMPORTANT]
>
>使用率は時間経過に伴って増加する可能性が高いので、このツールにおける上記の計算はいずれも、ワークロード特性の増加率を 30% と仮定して実行されます。 また、プロファイリングのメトリック (読み取り/書き込みの IOPS、変更頻度など) はすべて 95 パーセンタイル値が採用されます。 増加率とパーセンタイル計算は、どちらも設定により変更することができます。 詳細については、「増加率に関する考慮事項」セクションを参照してください。 パーセンタイル値の詳細については、「計算に使用されるパーセンタイル値」セクションを参照してください。
>

## <a name="support-matrix"></a>サポート マトリックス

| **カテゴリ** | **VMware から Azure** |**Hyper-V から Azure**|**Azure から Azure**|**Hyper-V からセカンダリ サイト**|**VMware からセカンダリ サイト**
--|--|--|--|--|--
サポートされるシナリオ |はい|はい|いいえ|はい*|いいえ
サポートされているバージョン | vCenter 7.0、6.7、6.5、6.0、または 5.5| Windows Server 2016、Windows Server 2012 R2 | NA |Windows Server 2016、Windows Server 2012 R2|NA
サポートされている構成|vCenter、ESXi| Hyper-V クラスター、Hyper-V ホスト|NA|Hyper-V クラスター、Hyper-V ホスト|NA|
Site Recovery Deployment Planner の実行中のインスタンスごとにプロファイルできるサーバーの数 |単一 (単一の vCenter Server または ESXi サーバーに含まれている VM を同時にプロファイル可能)|複数 (複数のホストまたはホスト クラスターにまたがった VM を同時にプロファイル可能)| NA |複数 (複数のホストまたはホスト クラスターにまたがった VM を同時にプロファイル可能)| NA

*このツールは本来、Hyper-V から Azure へのディザスター リカバリー シナリオ用です。 Hyper-V からセカンダリ サイトへのディザスター リカバリーについては、必要なネットワーク帯域幅、各ソース Hyper-V サーバーの必要な空き記憶域、初期レプリケーションのバッチ数とバッチ定義など、ソース側の推奨事項の確認にのみ使用できます。 レポートの Azure の推奨事項とコストは無視してください。 さらに、スループットの取得操作は、Hyper-V からセカンダリ サイトへのディザスター リカバリー シナリオには適用できません。

## <a name="prerequisites"></a>前提条件
このツールの処理は、プロファイリングとレポート生成という大きく 2 つの段階に分けられます。 加えて第 3 の選択肢として、スループットの計算のみを行うこともできます。 以下の表に示したのは、プロファイリングとスループット測定に使用するサーバーの要件です。

| サーバーの要件 | 説明|
|---|---|
|プロファイリングとスループット測定| <ul><li>オペレーティング システム:Windows Server 2016 または Windows Server 2012 R2<br>(少なくとも[構成サーバーのサイズの推奨事項](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server)を満たしていることが望ましい)</li><li>コンピューターの構成:8 vCPU、16 GB RAM、300 GB HDD</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Visual Studio 2012 の Visual C++ 再頒布可能パッケージ](https://aka.ms/vcplusplus-redistributable)</li><li>このサーバーから Azure (*.blob.core.windows.net) へのインターネット アクセス、ポート 443<br>[これは省略可能です。 レポート生成時に使用可能な帯域幅を手動で指定することを選択できます。]</li><li>Azure ストレージ アカウント</li><li>サーバー上の管理者アクセス権</li><li>100 GB 以上の空きディスク領域 (平均 3 台のディスクがある仮想マシン 1,000 台をそれぞれ 30 日間プロファイリングすることを想定)</li><li>VMware vCenter の統計レベルは 1 以上に設定できます</li><li>vCenter ポートの許可 (既定値は 443):Site Recovery Deployment Planner は、このポートを使用して vCenter サーバー/ESXi ホストに接続します</ul></ul>|
| レポートの生成 | Excel 2013 以降がインストールされた Windows PC または Windows Server。<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual Studio 2012 の Visual C++ 再頒布可能パッケージ](https://aka.ms/vcplusplus-redistributable)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) が必要なのは、VM の最新の VM 構成情報をフェッチするためにレポート生成コマンドで -User オプションを渡す場合のみです。 Deployment Planner は vCenter サーバーに接続します。 vCenter ポート (既定値は 443) に vCenter Server への接続を許可します。</li>|
| ユーザーのアクセス許可 | 読み取り専用アクセス許可 (プロファイリング中、VMware vCenter サーバー/VMware vSphere ESXi ホストにアクセスするユーザー アカウントに適用) |

> [!NOTE]
>
>このツールでプロファイリングできるのは、VMDK ディスクと RDM ディスクを使用した VM のみです。 iSCSI ディスクまたは NFS ディスクを使用した VM をプロファイリングすることはできません。 Site Recovery は、VMware サーバーの iSCSI ディスクと NFS ディスクをサポートしていますが、 Deployment Planner はゲスト内に存在せず、プロファイリングは vCenter のパフォーマンス カウンターのみを使用して行われるため、これらの種類のディスクをこのツールで十分把握することはできません。
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Deployment Planner ツールのダウンロードと抽出
1. 最新バージョンの [Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) をダウンロードします。
ツールは .zip フォルダーにパッケージされています。 VMware から Azure へのレプリケーション シナリオに対応しているのは、このツールの最新バージョンのみです。

2. ツールの実行場所となる Windows Server に .zip フォルダーをコピーします。
このツールは、プロファイリングの対象となる VM のホスト (vCenter サーバー/vSphere ESXi ホスト) にネットワークで接続されている Windows Server 2012 R2 から実行することができます。 ただしこのツールは、[構成サーバーのサイズ ガイドライン](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server)に準拠したハードウェア構成のサーバーで実行することをお勧めします。 Site Recovery のコンポーネントをオンプレミスにデプロイ済みである場合は、その構成サーバーからツールを実行してください。

    ツールの実行起点となるサーバーには、(プロセス サーバーを一体化した) 構成サーバーと同じハードウェア構成を使用することをお勧めします。 そのような構成によって、Site Recovery がレプリケーションを実行している間の実際のスループットを正確に反映したレポートを得ることができます。 スループットの計算は、サーバーで利用できるネットワーク帯域幅とそのサーバーのハードウェア構成 (CPU、記憶域など) に左右されます。 その他のサーバーからツールを実行した場合、計算されるのは、そのサーバーから Azure へのスループットです。 しかもそのサーバーには、構成サーバーとは異なるハードウェア構成が使用されていることが考えられます。そうなると、達成スループットとしてツールから報告される値の精度が低下してしまいます。

3. zip フォルダーを展開します。
このフォルダーには、複数のファイルとサブフォルダーが格納されています。 実行可能ファイルは、親フォルダーにある ASRDeploymentPlanner.exe です。

    例:.zip ファイルを E:\ ドライブにコピーして展開します。
    E:\ASR Deployment Planner_v2.3.zip

    E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>最新バージョンの Deployment Planner への更新

最新の更新については、Deployment Planner の[バージョン履歴](site-recovery-deployment-planner-history.md)にまとめられています。

以前のバージョンの Deployment Planner をお持ちの場合、次のいずれかを行ってください。
 * 最新バージョンにプロファイリングの修正プログラムが含まれていない場合で、かつ現在ご利用のバージョンの Deployment Planner でプロファイリングが既に進行中である場合は、プロファイリングを続行してください。
 * 最新バージョンにプロファイリングの修正プログラムが含まれている場合は、現在ご利用のバージョンでのプロファイリングを中止し、新しいバージョンでプロファイリングを再開することをお勧めします。


 >[!NOTE]
 >
 >新しいバージョンでプロファイリングを開始するときは、既存のファイルにプロファイル データが追記されるよう、同じ出力ディレクトリ パスを指定してください。 そうすることで、すべてのプロファイリング データを使ってレポートが生成されます。 異なる出力ディレクトリを指定した場合は、新しいファイルが作成され、古いプロファイリング データはレポートの生成に使用されません。
 >
 >Deployment Planner のバージョンアップに使われる .zip ファイルには、その都度、累積的な更新が格納されています。 以前のフォルダーに最新のファイルをコピーする必要はありません。 新しいフォルダーを作成して使用できます。


## <a name="version-history"></a>バージョン履歴
Site Recovery Deployment Planner ツールの最新バージョンは 2.5 です。
各更新で追加された修正については、「[Site Recovery Deployment Planner version history (Site Recovery Deployment Planner のバージョン履歴)](./site-recovery-deployment-planner-history.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
[Site Recovery Deployment Planner の実行](site-recovery-vmware-deployment-planner-run.md)
