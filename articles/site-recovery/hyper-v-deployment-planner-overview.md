---
title: Azure Site Recovery を使用する Hyper-V ディザスター リカバリー用の Deployment Planner
description: Azure Site Recovery Deployment Planner による Hyper-V から Azure へのディザスター リカバリーについて説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: mayg
ms.openlocfilehash: e4f1931aab056306ac5e9f9e9ef402ca26ec2d19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86528946"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Azure Site Recovery Deployment Planner による Hyper-V から Azure へのディザスター リカバリー

この記事は、Hyper-V から Azure への運用デプロイに関する Azure Site Recovery Deployment Planner のユーザー ガイドです。

Site Recovery を使用して Hyper-V 仮想マシン (VM) を保護する前に、必要な回復ポイントの目標 (RPO) を達成するうえで十分な帯域幅を日次データ変化率に基づいて割り当てます。また、オンプレミスの Hyper-V ストレージの各ボリュームに十分な空き記憶域を割り当てます。

また、レプリケーション先となる Azure ストレージ アカウントの種類と数を見極めて作成する必要があります。 レプリケーション元となる運用サーバーの使用量が時間経過に伴って増えることを考慮し、その増加率を見越して、Standard ストレージ アカウントまたは Premium ストレージ アカウントを作成することになります。 ストレージの種類は、読み取り/書き込みの 1 秒あたりの I/O 操作数 (IOPS) やデータの変更頻度といったワークロード特性と Azure Site Recovery の制限に基づいて VM ごとに選択します。 

Azure Site Recovery Deployment Planner は、Hyper-V から Azure へのディザスター リカバリー シナリオと VMware から Azure へのディザスター リカバリー シナリオの両方で使用できるコマンドライン ツールです。 このツールを使用すると、(運用環境に一切影響を与えることなく) 複数の Hyper-V ホスト上にある Hyper-V VM をリモートでプロファイルして、レプリケーションとテスト フェールオーバー (またはフェールオーバー) に必要な帯域幅要件と Azure Storage 要件を把握することができます。 このツールは、Azure Site Recovery のコンポーネントを一切オンプレミスにインストールせずに実行できます。 ただし、達成スループットの正確な結果を得るには、プランナーを実行する Windows Server のハードウェア構成を、Azure へのディザスター リカバリー保護の有効化に使用されるいずれかの Hyper-V サーバーと同じにすることが推奨されます。 

このツールで把握できる情報は次のとおりです。

**適合性の評価**

* ディスク数、ディスク サイズ、IOPS、変更頻度、いくつかの VM 特性に基づく VM の適格性評価。

**ネットワーク帯域幅ニーズと RPO の評価**

* 差分レプリケーションに必要な推定ネットワーク帯域幅
* オンプレミスから Azure への間で Azure Site Recovery が得られるスループット
* 特定の帯域幅で達成できる RPO
* プロビジョニングされた帯域幅が低かった場合に、必要な RPO に生じる影響

    
**Azure インフラストラクチャの要件**

* VM ごとに必要なストレージの種類 (Standard ストレージ アカウントまたは Premium ストレージ アカウント)
* レプリケーション用にセットアップする Standard ストレージ アカウントと Premium ストレージ アカウントの総数
* ストレージ アカウントの命名に関する推奨事項 (Azure Storage ガイダンスに基づく)
* 全 VM のストレージ アカウントの配置
* サブスクリプションのテスト フェールオーバー (またはフェールオーバー) の前にセットアップする Azure コアの数
* Azure VM の推奨サイズ (オンプレミスの VM ごと)

**オンプレミス インフラストラクチャの要件**
* VM レプリケーションによって望ましくないダウンタイムが運用アプリケーションに発生することがないよう、初期レプリケーションと差分レプリケーションを成功させるために必要な、Hyper-V ストレージの各ボリューム上の空き記憶域
* Hyper-V レプリケーションに関して設定される最大コピー頻度

**初期レプリケーションのバッチに関するガイダンス** 
* 保護に使用する VM バッチの数
* 各バッチの VM の一覧
* 各バッチが保護される順序
* 各バッチの初期レプリケーションの推定所要時間

**推定される Azure の DR コスト**
* 推定される Azure の合計 DR コスト: コンピューティング、ストレージ、ネットワーク、Azure Site Recovery ライセンスのコスト
* VM あたりの詳細なコスト分析



>[!IMPORTANT]
>
>使用率は時間経過に伴って増加する可能性が高いので、このツールにおける上記の計算はいずれも、ワークロード特性の増加率を 30% と仮定して実行されます。また、プロファイルのメトリック (読み取り/書き込みの IOPS、変更頻度など) はすべて 95 パーセンタイル値が採用されます。 この 2 つの要素 (増加率とパーセンタイル計算) は、設定により変更することができます。 詳細については、「増加率に関する考慮事項」セクションを参照してください。 パーセンタイル値の詳細については、「計算に使用されるパーセンタイル値」セクションを参照してください。
>

## <a name="support-matrix"></a>サポート マトリックス

|**Categories (カテゴリ)** | **VMware から Azure** |**Hyper-V から Azure**|**Azure から Azure**|**Hyper-V からセカンダリ サイト**|**VMware からセカンダリ サイト**
--|--|--|--|--|--
サポートされるシナリオ |はい|はい|いいえ|はい*|いいえ
サポートされているバージョン | vCenter 6.7、6.5、6.0、または 5.5| Windows Server 2016、Windows Server 2012 R2 | NA |Windows Server 2016、Windows Server 2012 R2|NA
サポートされている構成|vCenter、ESXi| Hyper-V クラスター、Hyper-V ホスト|NA|Hyper-V クラスター、Hyper-V ホスト|NA|
Azure Site Recovery Deployment Planner の実行中のインスタンスごとにプロファイルできるサーバーの数 |単一 (単一の vCenter Server または ESXi サーバーに含まれている VM を同時にプロファイル可能)|複数 (複数のホストまたはホスト クラスターにまたがった VM を同時にプロファイル可能)| NA |複数 (複数のホストまたはホスト クラスターにまたがった VM を同時にプロファイル可能)| NA

*このツールは本来、Hyper-V から Azure へのディザスター リカバリー シナリオ用です。 Hyper-V からセカンダリ サイトへのディザスター リカバリーについては、必要なネットワーク帯域幅、各ソース Hyper-V サーバーの必要な空き記憶域、初期レプリケーションのバッチ数とバッチ定義など、ソース側の推奨事項の確認にのみ使用できます。  レポートの Azure の推奨事項とコストは無視してください。 さらに、スループットの取得操作は、Hyper-V からセカンダリ サイトへのディザスター リカバリー シナリオには適用できません。

## <a name="prerequisites"></a>前提条件
このツールを Hyper-V で使用する場合、VM リストの取得、プロファイル、レポート生成という 3 つの主要なフェーズがあります。 さらに第 4 の選択肢として、スループットの計算のみを行うこともできます。 次の表には、各フェーズの実行に必要なサーバーの要件を示しています。

| サーバーの要件 | 説明 |
|---|---|
|VM リストの取得、プロファイル、スループット測定 |<ul><li>オペレーティング システム:Microsoft Windows Server 2016 または Microsoft Windows Server 2012 R2 </li><li>コンピューターの構成:8 vCPU、16 GB RAM、300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Visual Studio 2012 の Microsoft Visual C++ 再頒布可能パッケージ](https://aka.ms/vcplusplus-redistributable)</li><li>このサーバーから Azure (*.blob.core.windows.net) へのインターネット アクセス、ポート 443<br>[これは省略可能です。 レポート生成時に使用可能な帯域幅を手動で指定することを選択できます。]</li><li>Azure ストレージ アカウント</li><li>サーバー上の管理者アクセス権</li><li>100 GB 以上の空きディスク領域 (平均 3 台のディスクがある仮想マシン 1,000 台をそれぞれ 30 日間プロファイリングすることを想定)</li><li>Azure Site Recovery Deployment Planner ツールが実行される VM は、すべての Hyper-V サーバーの TrustedHosts リストに追加する必要があります。</li><li>プロファイル対象のすべての Hyper-V サーバーは、ツールが実行されるクライアント VM の TrustedHosts リストに追加する必要があります。 [TrustedHosts リストへのサーバーの追加について詳細を確認してください](#steps-to-add-servers-into-trustedhosts-list)。 </li><li> このツールは、クライアントで PowerShell またはコマンドライン コンソールを使用して、管理特権で実行する必要があります</ul></ul>|
| レポートの生成 | Microsoft Excel 2013 以降がインストールされた Windows PC または Windows Server |
| ユーザーのアクセス許可 | VM リストの取得操作とプロファイル操作の際に Hyper-V クラスター/Hyper-V ホストにアクセスするための管理者アカウント。<br>プロファイルが必要なすべてのホストには、同じ資格情報 (ユーザー名とパスワード) を備えたドメイン管理者アカウントが必要です
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>TrustedHosts リストにサーバーを追加する手順
1. ツールがデプロイされる VM は、プロファイル対象のすべてのホストが含まれた TrustedHosts リストを備えている必要があります。 クライアントを Trustedhosts リストに追加するには、VM で 管理特権の PowerShell を使って次のコマンドを実行します。 VM には、Windows Server 2012 R2 または Windows Server 2016 を使用できます。 

   ```powershell
   set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
   ```
1. プロファイルが必要な各 Hyper-V ホストには、以下が必要です。

    a. TrustedHosts リストに対する、ツールの実行先となる VM の追加。 Hyper-V ホストで管理特権の PowerShell セッションを使って次のコマンドを実行します。

      ```powershell
      set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
      ```

    b. PowerShell リモート処理の有効化。

      ```powershell
      Enable-PSRemoting -Force
      ```

## <a name="download-and-extract-the-deployment-planner-tool"></a>Deployment Planner ツールのダウンロードと抽出

1.  最新バージョンの [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) をダウンロードします。
ツールは .zip フォルダーにパッケージされています。 同じツールで、VMware から Azure へのディザスター リカバリー シナリオと Hyper-V から Azure へのディザスター リカバリー シナリオの両方に対応できます。 このツールは、Hyper-V からセカンダリ サイトへのディザスター リカバリー シナリオにも使用できます。ただし、レポートの Azure インフラストラクチャ推奨事項は無視してください。

1.  ツールの実行場所となる Windows Server に .zip フォルダーをコピーします。 ツールは、Windows Server 2012 R2 または Windows Server 2016 で実行できます。 プロファイル対象の VM が保持された Hyper-V クラスターまたは Hyper-V ホストに接続するために、サーバーにはネットワーク アクセスが必要です。 ツールの実行場所となる VM のハードウェア構成は、保護したい Hyper-V サーバーと同じにすることをお勧めします。 そのような構成によって、Azure Site Recovery がレプリケーションを実行している間の実際のスループットを正確に反映したレポートを得ることができます。 スループットの計算は、サーバーで利用できるネットワーク帯域幅とそのサーバーのハードウェア構成 (CPU、記憶域など) に左右されます。 このスループットは、ツールが実行されているサーバーから Azure の間で計算されます。 サーバーのハードウェア構成が Hyper-V サーバーと異なる場合、ツールによって報告される達成スループットは不正確になります。
推奨される VM の構成:8 vCPU、16 GB RAM、300 GB HDD。

1.  zip フォルダーを展開します。
このフォルダーには、複数のファイルとサブフォルダーが格納されています。 実行可能ファイルは、親フォルダーにある ASRDeploymentPlanner.exe です。

例:.zip ファイルを E:\ ドライブにコピーして展開します。 E:\ASR Deployment Planner_v2.3.zip

E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>最新バージョンの Deployment Planner への更新

最新の更新については、Deployment Planner の[バージョン履歴](site-recovery-deployment-planner-history.md)にまとめられています。

以前のバージョンの Deployment Planner をお持ちの場合、次のいずれかを行ってください。
 * 最新バージョンにプロファイリングの修正プログラムが含まれていない場合で、かつ現在ご利用のバージョンの Deployment Planner でプロファイリングが既に進行中である場合は、プロファイリングを続行してください。
 * 最新バージョンにプロファイリングの修正プログラムが含まれている場合は、現在ご利用のバージョンでのプロファイリングを中止し、新しいバージョンでプロファイリングを再開することをお勧めします。


  >[!NOTE]
  >
  >新しいバージョンでプロファイリングを開始するときは、既存のファイルにプロファイル データが追記されるよう、同じ出力ディレクトリ パスを指定してください。 そうすることで、すべてのプロファイリング データを使ってレポートが生成されます。 異なる出力ディレクトリを指定した場合は、新しいファイルが作成され、古いプロファイリング データはレポートの生成に使用されません。
  >
  >Deployment Planner の更新に使われる .zip ファイルには、その都度、累積的な更新が格納されています。 以前のフォルダーに最新のファイルをコピーする必要はありません。 新しいフォルダーを作成して使用できます。

## <a name="version-history"></a>バージョン履歴
Azure Site Recovery Deployment Planner ツールの最新バージョンは 2.5 です。
各更新で追加された修正については、「[Azure Site Recovery Deployment Planner Version History (Azure Site Recovery Deployment Planner のバージョン履歴)](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx)」を参照してください。


## <a name="next-steps"></a>次のステップ
* [Deployment Planner の実行](./hyper-v-deployment-planner-run.md)。
