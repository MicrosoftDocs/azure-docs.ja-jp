---
title: Azure Migrate Server Assessment の評価をカスタマイズする |Microsoft Docs
description: Azure Migrate Server Assessment で作成された評価をカスタマイズする方法について説明します
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: bcc6f41d7cc08764266ffb6705d1b8937d355199
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109724"
---
# <a name="customize-an-assessment"></a>評価のカスタマイズ

この記事では、Azure Migrate Server Assessment によって作成された評価をカスタマイズする方法について説明します。

[Azure Migrate](migrate-services-overview.md) は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。

Azure Migrate Server Assessment ツールを使用すると、Azure への移行の準備として、オンプレミスの VMware VM と Hyper-V VM の評価を作成できます。 Server Assessment ツールでは、Azure IaaS 仮想マシンおよび Azure VMware Solution (AVS) への移行についてオンプレミス サーバーを評価します。 

## <a name="about-assessments"></a>評価について

Server Assessment を使用して作成した評価は、特定の時点のデータのスナップショットです。 Azure Migrate:Server Assessment を使用して作成する方法について説明します。

**評価の種類** | **詳細**
--- | --- 
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md)、[物理サーバー](how-to-set-up-appliance-physical.md)を評価できます。(concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 <br/><br/> この種類の評価を使用すると、Azure VMware Solution (AVS) への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md) を評価できます。[詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

Server Assessment での Azure VM の評価には、サイズ変更の設定基準のオプションが 2 つあります。

**サイズ変更の設定基準** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づいて推奨を行う評価 | **Azure VM の評価**: VM サイズの推奨値は、CPU とメモリの使用率データに基づきます。<br/><br/> ディスクの種類に関する推奨事項 (標準 HDD/SSD またはプレミアム マネージド ディスク) は、オンプレミス ディスクの IOPS とスループットに基づきます。<br/><br/> **Azure VMware Solution (AVS) の評価**: AVS ノードに関する推奨事項は、CPU とメモリの使用率データに基づきます。
**現状のオンプレミス** | パフォーマンス データを使用せずに推奨を行う評価。 | **Azure VM の評価**: VM サイズに関する推奨事項は、オンプレミスの VM サイズに基づきます<br/><br> 推奨されるディスクの種類は、評価の [ストレージの種類] 設定で選択した内容に基づきます。<br/><br/> **Azure VMware Solution (AVS) の評価**: AVS ノードに関する推奨事項は、オンプレミスの VM サイズに基づきます。


## <a name="how-is-an-assessment-done"></a>評価の方法

Azure Migrate Server Assessment で行われる評価には、3 つの段階があります。 アセスメントは適合性分析から始まり、次にサイズ設定が行われ、最後に月間コストの見積もりが行われます。 前の段階で合格したマシンだけが次の段階に進みます。 たとえば、マシンが Azure 適合性チェックで不合格になった場合、Azure に不適合とマークされ、サイズとコストは計算されません。 [詳細情報。](./concepts-assessment-calculation.md)

## <a name="whats-in-an-azure-vm-assessment"></a>Azure VM の評価の内容

**プロパティ** | **詳細**
--- | ---
**ターゲットの場所** | Azure 上の移行先となる場所。<br/> Server Assessment で現在サポートされているターゲット リージョンは次のとおりです。オーストラリア東部、オーストラリア南東部、ブラジル南部、カナダ中部、カナダ東部、インド中部、米国中部、中国東部、中国北部、東アジア、米国東部、米国東部 2、ドイツ中部、ドイツ北東部、東日本、西日本、韓国中部、韓国南部、米国中北部、北ヨーロッパ、米国中南部、東南アジア、インド南部、英国南部、英国西部、US Gov アリゾナ、US Gov テキサス、US Gov バージニア、米国中西部、西ヨーロッパ、インド西部、米国西部、米国西部 2。
**ストレージの種類** | このプロパティを使用して、Azure での移行先のディスクの種類を指定できます。<br/><br/> オンプレミス同様のサイズ設定の場合は、ターゲットのストレージの種類として、Premium マネージド ディスク、Standard SSD マネージド ディスク、Standard HDD マネージド ディスクのいずれかを指定できます。 パフォーマンス ベースのサイズ設定の場合は、ターゲットのディスクの種類として、Automatic、Premium マネージド ディスク、Standard HDD マネージド ディスク、Standard SSD マネージド ディスクのいずれかを指定できます。<br/><br/> ストレージの種類を Automatic として指定すると、ディスクのパフォーマンス データ (IOPS とスループット) に基づいてディスクに関する推奨事項が実行されます。 ストレージの種類に Premium/Standard を指定すると、選択したストレージの種類におけるディスク SKU が評価で推奨されます。 99.9% の単一インスタンス VM SLA を達成したい場合は、ストレージの種類に Premium マネージド ディスクを指定できます。 これにより、評価に含まれるすべてのディスクが Premium マネージド ディスクとして推奨されるようになります。 Azure
**予約済みインスタンス (RI)** | このプロパティでは、Azure に[予約済みインスタンス](https://azure.microsoft.com/pricing/reserved-vm-instances/)があるかどうかを指定し、評価のコスト見積もりで RI 割引が考慮されるようにするのに役立ちます。 予約インスタンスは、現在、Azure Migrate では従量課金制についてのみサポートされています。
**サイズ変更の設定基準** | VM を Azure に適したサイズにするために使用される基準。 "*パフォーマンスベース*" でサイズ設定するか、またはパフォーマンス履歴を考慮せずに "*オンプレミスに合わせて*" VM のサイズを設定できます。
**パフォーマンス履歴** | マシンのパフォーマンス データを評価するために考慮する期間。 このプロパティは、サイズ設定の基準が "*パフォーマンス ベース*" の場合にのみ適用されます。
**百分位数の使用率** | 適切なサイズ設定のために考慮するパフォーマンス サンプル セットのパーセンタイル値。 このプロパティは、サイズ設定が "*パフォーマンスベース*" の場合にのみ適用されます。
**VM シリーズ** |     適切なサイズ変更を検討する VM シリーズを指定できます。 たとえば、Azure で A シリーズ VM に移行する予定がない運用環境がある場合は、リストまたはシリーズから A シリーズを除外することができるため、選択したシリーズでのみ、適切なサイズ変更が実行されます。
**快適性係数** | Azure Migrate Server Assessment では、評価時にバッファー (快適性係数) が考慮されます。 VM のマシン使用率データ (CPU、メモリ、ディスク、ネットワーク) に加えて、このバッファーが適用されます。 快適性係数は、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題に相当します。<br/><br/> たとえば、使用率 20% の 10 コア VM の結果は、通常 2 コア VM になります。 一方、快適性係数を 2.0x とした場合は、結果が 4 コア VM になります。
**プラン** | 登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate はそれに応じてコストを見積もります。
**通貨** | 請求通貨です。
**割引率 (%)** | Azure オファーに適用される任意のサブスクリプション固有の割引です。<br/> 既定の設定は 0% です。
**VM のアップタイム** | VM が Azure 上で 24 時間 365 日稼働する予定ではない場合は、Azure 上で稼働する期間 (1 か月あたりの日数と 1 日あたりの時間数) を指定できます。それに応じてコストの見積もりが行われます。<br/> 既定値は、1 か月あたり 31 日、1 日あたり 24 時間です。
**Azure ハイブリッド特典** | ソフトウェア アシュアランスがあり、かつ [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を受ける資格があるかどうかを指定します。 [はい] に設定すると、Windows VM に Windows Azure 以外の価格は考慮されません。 既定値は Yes です。

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware Solution (AVS) の評価の内容

Server Assessment での AVS の評価に含まれる内容は次のとおりです。


| **プロパティ** | **詳細** |
| - | - |
| **ターゲットの場所** | 移行先となる AVS プライベート クラウドの場所を指定します。<br/><br/> Server Assessment での AVS の評価で現在サポートされているのは、以下のターゲット リージョンです。米国東部、西ヨーロッパ、米国西部。 |
| **ストレージの種類** | AVS で使用するストレージ エンジンを指定します。<br/><br/> AVS の評価では、既定のストレージの種類として vSAN のみがサポートされることに注意してください。 |
**予約インスタンス (RI)** | このプロパティは、AVS で予約インスタンスを指定するのに役立ちます。 AVS ノードでは、現在 RI はサポートされていません。 |
**ノードの種類** | オンプレミスの VM をマップするために使用する [AVS ノードの種類](../azure-vmware/concepts-private-clouds-clusters.md)を指定します。 既定のノードの種類は、AV36 であることに注意してください。 <br/><br/> Azure Migrate では、VM を AVS に移行するために必要なノードの数が推奨されます。 |
**FTT 設定、RAID レベル** | 適用可能な、許容するエラーと RAID の組み合わせを指定します。 選択した FTT オプションとオンプレミスの VM ディスク要件を組み合わせて、AVS で必要とされる vSAN ストレージの合計が決定されます。 |
**サイズ変更の設定基準** | VM を AVS にとって_適切なサイズ_にするために使用される基準を設定します。 _パフォーマンスに基づく_サイズ設定、またはパフォーマンス履歴を考慮しない_オンプレミス同様_のサイズ設定を選択できます。 |
**パフォーマンス履歴** | マシンのパフォーマンス データを評価するために考慮する期間を設定します。 このプロパティは、サイズ設定の基準が_パフォーマンス ベース_の場合にのみ適用されます。 |
**百分位数の使用率** | 適切なサイズ設定のために考慮するパフォーマンス サンプル セットのパーセンタイル値を指定します。 このプロパティは、サイズ設定がパフォーマンス ベースの場合にのみ適用されます。|
**快適性係数** | Azure Migrate Server Assessment では、評価時にバッファー (快適性係数) が考慮されます。 VM のマシン使用率データ (CPU、メモリ、ディスク、ネットワーク) に加えて、このバッファーが適用されます。 快適性係数は、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題に相当します。<br/><br/> たとえば、使用率 20% の 10 コア VM の結果は、通常 2 コア VM になります。 一方、快適性係数を 2.0x とした場合は、結果が 4 コア VM になります。 |
**プラン** | 登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)を表示します。 Azure Migrate はそれに応じてコストを見積もります。|
**通貨** | アカウントの請求通貨を表示します。 |
**割引率 (%)** | Azure オファーに適用される任意のサブスクリプション固有の割引を一覧表示します。 既定の設定は 0% です。 |
**Azure ハイブリッド特典** | ソフトウェア アシュアランスがあり、かつ [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を受ける資格があるかどうかを指定します。 ノード ベースの料金であるため、これが Azure VMware Solution の価格に影響することはありませんが、お客様は AVS で Azure ハイブリッド特典を利用し、使用中のオンプレミスの OS ライセンス (Microsoft ベース) を引き続き適用できます。 RHEL など、その他のソフトウェア OS ベンダーは、独自のライセンス条項を提供する必要があります。 |
**vCPU オーバーサブスクリプション** | AVS ノードの 1 つの物理コアに関連付けられる仮想コアの数の比率を指定します。 計算での既定値は、4 vCPU 対1 物理コア (AVS 内) です。 <br/><br/> API ユーザーは、この値を整数として設定できます。 vCPU オーバーサブスクリプションが 4 対 1 を上回る場合、パフォーマンスの低下が発生し始める可能性がありますが、Web サーバー タイプのワークロードには使用できます。 |

## <a name="edit-assessment-properties"></a>評価のプロパティの編集

評価を作成した後で評価のプロパティを編集するには、次の手順を実行します。

1. Azure Migrate プロジェクトで、 **[サーバー]** をクリックします。
2. **Azure Migrate:Server Assessment** で、評価カウントをクリックします。
3. **[評価]** で、関連する評価 > **[プロパティの編集]** をクリックします。
5. 上の表に従って、評価のプロパティをカスタマイズします。
6. **[保存]** をクリックして評価を更新します。


評価のプロパティは、評価の作成時にも編集できます。


## <a name="next-steps"></a>次のステップ

- Azure VM 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
- AVS 評価の計算方法の[詳細を確認](concepts-azure-vmware-solution-assessment-calculation.md)します。
