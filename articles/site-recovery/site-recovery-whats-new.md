---
title: Azure Site Recovery の最新情報
description: Azure Site Recovery サービスの新機能と最新の更新プログラムの概要について説明します。
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 423fbc563878d0cc2a63a5366d2226281a204e87
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454600"
---
# <a name="whats-new-in-site-recovery"></a>Site Recovery の最新情報

[Azure Site Recovery](site-recovery-overview.md) サービスは、継続的に更新され、改善されています。 最新情報を入手し続ける助けになるように、この記事では、最新のリリース、新機能、および新しいコンテンツに関する情報を提供します。 このページは定期的に更新されます。

[Azure 更新プログラム](https://azure.microsoft.com/updates/?product=site-recovery) チャネルで Site Recovery の更新通知をフォローし、サブスクライブすることができます。

## <a name="supported-updates"></a>サポートされる更新プログラム

Site Recovery コンポーネントでは、N-4 バージョン (N は最新リリース バージョン) がサポートされています。 これらの概要を次の表に示します。

**アップデート** |  **統合セットアップ** | **構成サーバー ova** | **モビリティ サービス エージェント** | **Site Recovery プロバイダー** | **Recovery Services エージェント**
--- | --- | --- | --- | --- | ---
[ロールアップ 54](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 9.41.5888.1 | 5.1.6620.0 | 9.41.5888.1 | 5.1.6620.0  | 2.0.9202.0
[ロールアップ 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[ロールアップ 52](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[ロールアップ 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[ロールアップ 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[ロールアップ 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


更新プログラムのインストールとサポートの詳細については、[こちら](service-updates-how-to.md)を参照してください。

## <a name="updates-february-2021"></a>更新プログラム (2021 年 2 月)

### <a name="update-rollup-54"></a>更新プログラム ロールアップ 54

[更新プログラム ロールアップ 54](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照) 
**Azure VM のディザスター リカバリー** | Azure Site Recovery を使用したゾーンからゾーンへのディザスター リカバリーは、北ヨーロッパ、米国東部、米国中部、米国西部 2 の 4 つのリージョンで一般提供が開始されました。<br/>
**VMware VM/物理の Azure へのディザスター リカバリー** | この更新プログラムには、レプリケーションを有効にした後、VMware/物理マシンの近接配置グループを選択するためのポータルのサポートが含まれています。<br/><br/> データ ディスクのサイズが 32 TB までの VMware マシンの保護がサポートされるようになりました。
**Azure への Hyper-V のディザスター リカバリー** | この更新プログラムには、レプリケーションを有効にした後、Hyper-V マシンの近接配置グループを選択するためのポータルのサポートが含まれています。


## <a name="updates-january-2021"></a>更新プログラム (2021 年 1 月)

### <a name="update-rollup-53"></a>更新プログラム ロールアップ 53

[更新プログラム ロールアップ 53](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照) 
**Azure VM のディザスター リカバリー** | タグのレプリケーションがサポートされるようになりました。 ソース リージョン内の Azure VM、ディスク、NIC に追加されたすべてのタグが、ターゲット リージョンのマシンにレプリケートされます。<br/><br/> Debian 10 を実行する Azure VM のレプリケーションがサポートされるようになりました。
**VMware VM/物理の Azure へのディザスター リカバリー** | この更新プログラムには、Azure への VMware VM レプリケーションのログ機能強化と、エラー メッセージの改善が含まれています。<br/><br/> Debian 10 を実行する VMware VM と物理マシンのレプリケーションがサポートされるようになりました。


## <a name="updates-november-2020"></a>更新プログラム (2020 年 11 月)

### <a name="update-rollup-52"></a>更新プログラム ロールアップ 52

[更新プログラム ロールアップ 52](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | Mobility Service に対する新しい Linux のサポートを含む、ロールアップで詳細に説明されているいくつかの修正と改善。
**Azure VM のディザスター リカバリー** | RHEL 8.3 および Oracle Linux 7.9 が実行される VM でサポートされるようになりました
**VMware VM/物理の Azure へのディザスター リカバリー** | RHEL 8.3 および Oracle Linux 7.9/8.3 が実行される VM でサポートされるようになりました。

## <a name="updates-october-2020"></a>更新プログラム (2020 年 10 月)

### <a name="update-rollup-51"></a>更新プログラム ロールアップ 51

[更新プログラム ロールアップ 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | Mobility Service に対する新しい Linux のサポートを含む、ロールアップで詳細に説明されているいくつかの修正と改善。

## <a name="updates-september-2020"></a>更新プログラム (2020 年 9 月)

### <a name="update-rollup-50"></a>更新プログラム ロールアップ 50

[更新プログラム ロールアップ 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

## <a name="updates-august-2020"></a>更新プログラム (2020 年 8 月)

### <a name="update-rollup-49"></a>ロールアップ 49

[更新プログラム ロールアップ 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | Mobility Service に対する新しい Linux のサポートを含む、ロールアップで詳細に説明されているいくつかの修正と改善。

## <a name="updates-july-2020"></a>更新 (2020 年 7 月)

### <a name="update-rollup-48"></a>更新プログラム ロールアップ 48

[更新プログラム ロールアップ 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

> [!NOTE]
> 更新プログラム ロールアップ 48 には、ADE を使用して暗号化された Linux マシンのレプリケーションを有効にするときの既知の問題があります。 [詳細については、こちらを参照してください](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137)。

### <a name="update-rollup-47"></a>更新プログラム ロールアップ 47

[更新プログラム ロールアップ 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

## <a name="updates-june-2020"></a>更新プログラム (2020 年 6 月)

### <a name="update-rollup-46"></a>更新プログラム ロールアップ 46

[更新プログラム ロールアップ 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

## <a name="updates-march-2020"></a>更新プログラム (2020 年 3 月)

### <a name="update-rollup-45"></a>更新プログラム ロールアップ 45

[更新プログラム ロールアップ 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

## <a name="updates-january-2020"></a>更新プログラム (2020 年 1 月)

### <a name="update-rollup-44"></a>更新プログラム ロールアップ 44

[更新プログラム ロールアップ 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のプロバイダーとエージェント向けの更新プログラムはありませんでした。
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

### <a name="azure-vmware-disaster-recovery"></a>Azure VMware のディザスター リカバリー

Azure 仮想マシンで、お客様が管理するキーを使用した保存時の暗号化が可能な VM がサポートされるようになりました。 [詳細については、こちらを参照してください](azure-to-azure-how-to-enable-replication-cmk-disks.md)。


### <a name="update-rollup-43"></a>更新プログラム ロールアップ 43

[更新プログラム ロールアップ 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)


## <a name="updates-november-2019"></a>更新プログラム (2019 年 11 月)

### <a name="update-rollup-42"></a>更新プログラム ロールアップ 42

[更新プログラム ロールアップ 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)


### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

Azure VM ディザスター リカバリーの新機能をまとめて表に示します。

**機能** | **詳細**
--- | ---
**UEFI** | Site Recovery では、UEFI ベースのブート アーキテクチャを使用した Azure VM のディザスター リカバリーがサポートされるようになりました。
**Linux** | Site Recovery では、Azure Disk Encryption (ADE) を使用して Linux を実行する Azure VM がサポートされるようになりました。
**第 2 世代** | すべての第 2 世代 Azure VM でディザスター リカバリーがサポートされるようになりました。
**リージョン** | ノルウェー geo 内の Azure VM に対してディザスター リカバリーを有効にできるようになりました。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware から Azure へのディザスター リカバリー

VMware から Azure へのディザスター リカバリーの新機能をまとめて表に示します。

**機能** | **詳細**
--- | ---
**UEFI** | Site Recovery では、UEFI ベースのブート アーキテクチャを使用した VMware VM のディザスター リカバリーがサポートされるようになりました。<br/><br/> サポートされるオペレーティング システムには、Windows Server 2019、Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、SLES 12 SP4、RHEL 8 が含まれます。

## <a name="update-to-servicing-stack-updatesha-2"></a>サービス スタック更新プログラム/SHA-2の更新

Azure VM からセカンダリ リージョンへのディザスター リカバリー、またはオンプレミスの VMware VM や物理サーバーから Azure へのディザスター リカバリーの場合は、次の点に注意してください。

- バージョン 9.30.5407.1 のモビリティ サービス拡張機能 (Azure VM の場合) およびモビリティ サービス エージェント (VMware/物理マシンの場合) 以降、一部のコンピューター オペレーティング システムではサービス スタック更新プログラムと SHA-2 が実行されている必要があります。 詳細については、次の表を参照してください。
- リンクされた KB に従って、更新プログラムと SHA-2 をインストールします。 SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。
- SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。

**オペレーティング システム** | **Azure VM** | **VMware VM/物理マシン**
--- | --- | ---
**Windows 2008 R2 SP1** | [サービス スタック更新プログラム](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [サービス スタック更新プログラム](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [サービス スタック更新プログラム](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [サービス スタック更新プログラム](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [サービス スタック更新プログラム](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [サービス スタック更新プログラム](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>更新プログラム (2019 年 10 月)

### <a name="update-rollup-41"></a>更新プログラム ロールアップ 41

[更新プログラム ロールアップ 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)



### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

Azure VM ディザスター リカバリーの新機能をまとめて表に示します。

**機能** | **詳細**
--- | ---
**テスト フェールオーバーの設定** | テスト フェールオーバーを設定するときに、テスト フェールオーバー VM とネットワークの設定を構成できるようになりました。これには、IP アドレス、NSG、内部負荷分散、および各マシン NIC のパブリック IP アドレスが含まれます。 これらの設定は省略可能であり、現在の動作は変更されません。 これらの設定を構成しない場合は、テスト フェールオーバー時に Azure VNet を選択できます。 [詳細については、こちらを参照してください](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/)。
**復旧計画** | 復旧計画は、フェールオーバーの信頼性を確保するために、100 VM に制限されるようになりました。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware から Azure へのディザスター リカバリー

VMware から Azure へのディザスター リカバリーの新機能をまとめて表に示します。

**機能** | **詳細**
--- | ---
**復旧計画** | 復旧計画は、フェールオーバーの信頼性を確保するために、100 VM に制限されるようになりました。


## <a name="updates-september-2019"></a>更新プログラム (2019 年 9 月)

### <a name="update-rollup-40"></a>更新プログラム ロールアップ 40

[更新プログラム ロールアップ 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)




### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

Azure VM ディザスター リカバリーの新機能をまとめて表に示します。

**機能** | **詳細**
--- | ---
**フェールバック後のクリーンアップ** | セカンダリ Azure にフェールオーバーしてから、プライマリ リージョンにフェールバックした後、Site Recovery ではセカンダリ リージョンのマシンが自動的にクリーンアップされます。 VM と NIC を手動で削除する必要はありません。
**テスト フェールオーバーでの IP アドレスの保持** | ディザスター リカバリーの訓練中にソース VM の IP アドレスを保持し、テスト フェールオーバーの静的 IP アドレスを選択できるようになりました。

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
新しいプロセス サーバー アラート | 新しいプロセス サーバー アラートを追加しました。 [詳細については、こちらを参照してください](vmware-physical-azure-monitor-process-server.md)。

### <a name="hyper-v-disaster-recovery"></a>Hyper-V のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
ストレージ アカウント | Site Recovery では、Hyper-V から Azure へのディザスター リカバリーに対してファイアウォールが有効になっている、ストレージ アカウントの使用をサポートするようになりました。  ターゲット アカウントとして、またはキャッシュ ストレージに対して、ファイアウォールが有効なストレージ アカウントを選択できます。 ファイアウォールが有効なアカウントを使用している場合は必ず、[信頼された Microsoft サービスを許可] オプションを有効にしてください。<br/><br/> これは、System Center VMM の有無にかかわらず、Hyper-V VM でサポートされています。


## <a name="updates-august-2019"></a>更新プログラム (2019 年 8 月)

### <a name="update-rollup-39"></a>更新プログラム ロールアップ 39

[更新プログラム ロールアップ 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)


### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

Azure VM ディザスター リカバリーの新機能をまとめて表に示します。

**機能** | **詳細**
--- | ---
**Azure AD を使用しない暗号化** | Windows を実行しているマネージド ディスクへの Azure VM レプリケーションで、Azure AD アプリを使用しない暗号化がサポートされるようになりました。
**フェールオーバー用のネットワーク リソース** | 別のリージョンにフェールオーバーするときに、ネットワーク リソースの設定 (NSG、負荷分散、パブリック IP アドレス) を VM に接続できるようになりました。

## <a name="updates-july-2019"></a>更新 (2019 年 7 月)

### <a name="update-rollup-38"></a>更新プログラム ロールアップ 38

[更新プログラム ロールアップ 38](https://support.microsoft.com/help/4513507/) では、以下の更新が提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)


### <a name="general"></a>全般

Site Recovery では、キャッシュ ストレージまたはターゲット ストレージで汎用 v2 ストレージ アカウントの使用がサポートされるようになりました。 以前は v1 のみがサポートされていました。

### <a name="vmware-to-azure-disaster-recovery"></a>VMware から Azure へのディザスター リカバリー

マネージド ディスクがある Azure VM へのレプリケートで、最大 8 TB のディスクをレプリケートできるようになりました。


## <a name="updates-june-2019"></a>更新プログラム (2019年 6 月)

### <a name="update-rollup-37"></a>更新プログラム ロールアップ 37

[更新プログラム ロールアップ 37](https://support.microsoft.com/help/4508614/) では、次の更新プログラムが提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**GPT パーティション** | 更新プログラム ロールアップ 37 以降 (モビリティ サービス バージョン 9.25.5241.1) では、UEFI で最大 5 つの GPT パーティションがサポートされます。 この更新プログラム以前は、4 つサポートされていました。



## <a name="updates-may-2019"></a>更新プログラム (2019 年 5 月)

### <a name="update-rollup-36"></a>更新プログラム ロールアップ 36

[更新プログラム ロールアップ 36](https://support.microsoft.com/help/4503156) では、次の更新プログラムが提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**追加されたディスクのレプリケート** | 既にディザスター リカバリーが有効になっている Azure VM に追加されたデータ ディスクのレプリケーションを有効にします。 [詳細については、こちらを参照してください](azure-to-azure-enable-replication-added-disk.md)。
**自動更新** | ディザスター リカバリーが有効になっている Azure VM で実行されるモビリティ サービス拡張機能の自動更新を構成するときに、Site Recovery によって作成される既定のアカウントを使用する代わりに、既存の Automation アカウントを選択できるようになりました。 [詳細については、こちらを参照してください](azure-to-azure-autoupdate.md)。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**プロセス サーバーの監視** | オンプレミスの VMware VM と物理サーバーのディザスター リカバリーについて、強化されたサーバー正常性レポートおよびアラート機能により、プロセス サーバーの問題を監視してトラブルシューティングします。 [詳細については、こちらを参照してください](vmware-physical-azure-monitor-process-server.md)。





## <a name="updates-march-2019"></a>更新プログラム (2019 年 3 月)

### <a name="update-rollup-35"></a>更新プログラム ロールアップ 35

[更新プログラム ロールアップ 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) では、次の更新プログラムが提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)
**問題の修正/改善点** | さまざまな修正プログラムと機能強化 (詳細はロールアップを参照)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**マネージド ディスク** | オンプレミスの VMware VM および物理サーバーが、Azure のマネージド ディスクに直接レプリケートされるようになりました。 オンプレミス データは Azure のキャッシュ ストレージ アカウントに送信され、復旧ポイントはターゲットの場所のマネージド ディスクで作成されます。 これにより、複数のターゲット ストレージ アカウントを管理する必要がなくなります。
**構成サーバー** | Site Recovery で、複数の NIC がある構成サーバーがサポートされるようになりました。 コンテナーに構成サーバーを登録する前に、構成サーバー VM にさらにアダプターを追加してください。 後で追加する場合は、サーバーをコンテナーに再登録する必要があります。


## <a name="updates-february-2019"></a>更新プログラム (2019 年 2 月)

### <a name="update-rollup-34"></a>更新プログラム ロールアップ 34

[更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。


### <a name="update-rollup-33"></a>更新プログラム ロールアップ 33

[更新プログラム ロールアップ 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。


### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**ネットワーク マッピング** | Azure VM ディザスター リカバリーで、レプリケーションを有効にすると、利用可能な任意のターゲット ネットワークを使用できるようになりました。
**Standard SSD** | [Standard SSD ディスク](../virtual-machines/disks-types.md#standard-ssd)を使用して Azure VM のディザスター リカバリーを設定できるようになりました。
**記憶域スペース ダイレクト** | Azure VM アプリで実行されるアプリのディザスター リカバリーを、高可用性を実現するために[記憶域スペース ダイレクト](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)を使用して設定できるようになりました。  Site Recovery と共に記憶域スペース ダイレクト (S2D) を使用することで、Azure VM ワークロードを包括的に保護することができます。 S2D では、Azure でゲスト クラスターをホストすることができます。 これは特に、SAP ASCS レイヤー、SQL Server、スケールアウト ファイル サーバーなど、重要なアプリケーションを VM でホストするときに便利です。


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux BRTFS ファイル システム** | Site Recovery では、BRTFS ファイル システムでの VMware VM のレプリケーションがサポートされるようになりました。 次のような場合、レプリケーションはサポートされません。<br/><br/>- レプリケーションを有効にした後、BTRFS ファイル システムのサブボリュームが変更されている。<br/><br/>- ファイル システムが複数のディスクに分散されている。<br/><br/>- BTRFS ファイル システムで RAID がサポートされている。
**Windows Server 2019** | Windows Server 2019 を実行しているコンピューターのサポートが追加されました。


## <a name="updates-january-2019"></a>更新プログラム (2019 年 1 月)


### <a name="accelerated-networking-azure-vms"></a>高速ネットワーク (Azure VM)

高速ネットワークによって、VM へのシングル ルート I/O 仮想化 (SR-IOV) が可能になり、ネットワークのパフォーマンスが向上します。 Azure VM のレプリケーションを有効にすると、Site Recovery は、高速ネットワークが有効になっているかどうかを検出します。 有効な場合はフェールオーバー後に、[Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) と [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) の両方について、Site Recovery によってターゲットのレプリカ Azure VM 上に高速ネットワークが自動的に構成されます。

[詳細については、こちらを参照してください](azure-vm-disaster-recovery-with-accelerated-networking.md)。

### <a name="update-rollup-32"></a>更新プログラム ロールアップ 32

[更新プログラム ロールアップ 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) では、次の更新プログラムが提供されます。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | RedHat Workstation 6/7、および Ubuntu、Debian、SUSE 用の新しいカーネル バージョンのサポートが追加されました。
**記憶域スペース ダイレクト** | Site Recovery では、記憶域スペース ダイレクト (S2D) を使用する Azure VM がサポートされます。

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VM/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Redhat Enterprise Linux 7.6、RedHat Workstation 6/7、Oracle Linux 6.10、および Oracle Linux 7.6 と、Ubuntu、Debian、SUSE 用の新しいカーネル バージョンに対するサポートが追加されました。


### <a name="update-rollup-31"></a>更新プログラム ロールアップ 31

[更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="vmware-vmsphysical-servers-replication"></a>VMware VM/物理サーバーのレプリケーション

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** |  Red Hat 互換カーネルを使用する Oracle Linux 6.8、Oracle Linux 6.9、および Oracle Linux 7.0 と、Unbreakable Enterprise Kernel (UEK) リリース 5 に対するサポートが追加されました。
**LVM** | LVM ボリュームと LVM2 ボリュームのサポートが追加されました。<br/><br/> ディスク パーティションや LVM ボリューム上の /boot ディレクトリがサポートされるようになりました。
**Directories** | 個別のパーティション、または同一のシステム ディスク上にないファイル システムとして設定された以下のディレクトリのサポートが追加されました。<br/><br/> /(ルート)、/boot、/usr、/usr/local、/var、/etc
**Windows Server 2008** | ダイナミック ディスクのサポートが追加されました。
**[フェールオーバー]** | storvsc と vsbus がブート ドライバーではない VMware VM のフェールオーバー時間が改善されました。
**UEFI のサポート** | Azure VM は UEFI というブートの種類をサポートしていません。 UEFI が設定されたオンプレミスの物理サーバーを、Site Recovery を使用して Azure に移行できるようになりました。 Site Recovery は、移行の前にブートの種類を BIOS に変換することにより、このようなサーバーを移行します。 Site Recovery では、以前は、VM の場合のみこの変換をサポートしていました。 現在は、Windows Server 2012 移行を実行する物理サーバーもサポートされています。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Red Hat 互換カーネルを使用する Oracle Linux 6.8、Oracle Linux 6.9、および Oracle Linux 7.0 と、Unbreakable Enterprise Kernel (UEK) リリース 5 に対するサポートが追加されました。
**Linux BRTFS ファイル システム** | Azure VM でサポートされるようになりました。
**可用性ゾーンにある Azure VM** | 可用性ゾーンにデプロイされた Azure VM の別のリージョンへのレプリケーションを有効にできます。 Azure VM のレプリケーションを有効にして、フェールオーバーのターゲットを、1 つの VM インスタンス、可用性セット内の VM、または可用性ゾーン内の VM に設定できるようになりました。 この設定は、レプリケーションに影響を与えません。 お知らせを[読む](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/)
**ファイアウォールが有効なストレージ (ポータル/PowerShell)** | [ファイアウォールが有効なストレージ アカウント](../storage/common/storage-network-security.md)のサポートが追加されました。<br/><br/> ファイアウォールが有効なストレージ アカウント上のアンマネージド ディスクを持つ Azure VM を、ディザスター リカバリーのために別の Azure リージョンにレプリケートできます。<br/><br/> ファイアウォールが有効なストレージ アカウントを、アンマネージド ディスクのターゲット ストレージ アカウントとして使用できます。<br/><br/> ポータルでサポートされ、PowerShell の使用がサポートされます。

## <a name="updates-december-2018"></a>更新プログラム (2018 年 12 月)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>モビリティ サービスの自動更新 (Azure VM)

Site Recovery では、モビリティ サービス拡張機能に対する自動更新のオプションが追加されました。 モビリティ サービス拡張機能は、Site Recovery によってレプリケートされる各 Azure VM にインストールされます。 レプリケーションを有効にするときには、拡張機能に対する更新の管理を Site Recovery に許可するかどうかを選択します。

更新では VM の再起動は必要とされず、レプリケーションに影響はありません。 [詳細については、こちらを参照してください](azure-to-azure-autoupdate.md)。

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Azure VM ディザスター リカバリー用の料金計算ツール

Azure VM のディザスター リカバリーでは、VM のライセンス コストのほか、ネットワークとストレージのコストが発生します。 Azure では、これらのコストの把握に役立つ[料金計算ツール](https://aka.ms/a2a-cost-estimator)を提供しています。 Site Recovery では、6 個の VM、12 個の Standard HDD ディスク、6 個の Premium SSD ディスクを使用する 3 層アプリに基づいたサンプル デプロイに値段を付ける、[価格見積もりの例](https://aka.ms/a2a-cost-estimator)が提供されるようになりました。

- このサンプルでは、1 日に Standard では 10 GB、Premium では 20 GB のデータ変更を想定しています。
- 特定のデプロイについては、変数を変更してコストを見積もることができます。
- VM の数、マネージド ディスクの数と種類、VM 全体で予想される総データ変更率を指定します。
- さらに、帯域幅コストを見積もるための圧縮率を適用できます。

お知らせを[読む](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)


## <a name="updates-october-2018"></a>更新プログラム (2018 年 10 月)

### <a name="update-rollup-30"></a>更新プログラム ロールアップ 30

[更新プログラム ロールアップ 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**リージョンのサポート** | オーストラリア中部 1 およびオーストラリア中部 2 で Site Recovery のサポートが追加されました。
**ディスク暗号化のサポート** | Azure AD アプリを使用した、Azure Disk Encryption (ADE) で暗号化された Azure VM のディザスター リカバリーのサポートが追加されました。 [詳細については、こちらを参照してください](azure-to-azure-how-to-enable-replication-ade-vms.md)。
**ディスクの除外** | 初期化されていないディスクが、Azure VM のレプリケーション中に自動的に除外されるようになりました。
**ファイアウォールが有効なストレージ (PowerShell)** | [ファイアウォールが有効なストレージ アカウント](../storage/common/storage-network-security.md)のサポートが追加されました。<br/><br/> ファイアウォールが有効なストレージ アカウント上のアンマネージド ディスクを持つ Azure VM を、ディザスター リカバリーのために別の Azure リージョンにレプリケートできます。<br/><br/> ファイアウォールが有効なストレージ アカウントを、アンマネージド ディスクのターゲット ストレージ アカウントとして使用できます。<br/><br/> PowerShell のみの使用がサポートされるようになりました。


### <a name="update-rollup-29"></a>更新プログラム ロールアップ 29

[更新プログラム ロールアップ 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。


## <a name="updates-august-2018"></a>更新プログラム (2018 年 8 月)

### <a name="update-rollup-28"></a>更新プログラム ロールアップ 28

[更新プログラム ロールアップ 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | RedHat Enterprise Linux 6.10; CentOS 6.10 のサポートが追加されました。<br/><br/>
**クラウドのサポート** | ドイツのクラウドで Azure VM のディザスター リカバリーがサポートされるようになりました。
**サブスクリプション間のディザスター リカバリー** | 同じ Azure Active Directory テナント内の異なるサブスクリプションで、別のリージョンに Azure VM をレプリケートすることがサポートされるようになりました。 [詳細については、こちらを参照してください](https://aka.ms/cross-sub-blog)。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理サーバーのディザスター リカバリー
今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | RedHat Enterprise Linux 6.10、CentOS 6.10 のサポートが追加されました。<br/><br/> レガシ BIOS 互換モードで GUID パーティション テーブル (GPT) のパーティション スタイルを使用する Linux ベースの VM がサポートされるようになりました。 詳細については、[Azure VM の FAQ](../virtual-machines/faq-for-disks.md) を確認してください。
**移行後の VM のディザスター リカバリー** | Azure に移行されたオンプレミスの VMware VM のセカンダリ リージョンへのディザスター リカバリーの有効化がサポートされます。レプリケーションを有効にする前に VM でモビリティ サービスをアンインストールする必要はありません。
**Windows Server 2008** | Windows Server 2008 R2/2008 64 ビット版および 32 ビット版を実行するマシンの移行がサポートされるようになりました。<br/><br/> 移行のみ (レプリケーションとフェールオーバー)。 フェールバックはサポートされていません。

## <a name="updates-july-2018"></a>更新プログラム (2018 年 7 月)

### <a name="update-rollup-27-july-2018"></a>更新プログラム ロールアップ 27 (2018 年 7 月)

[更新プログラム ロールアップ 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) は次の更新プログラムを提供します。

**アップデート** | **詳細**
--- | ---
**プロバイダーおよびエージェント** | Site Recovery のエージェントとプロバイダーに対する更新プログラム (詳細はロールアップを参照)。
**問題の修正/改善点** | さまざまな修正と改善点 (詳細はロールアップを参照)。

### <a name="azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Red Hat Enterprise Linux 7.5 のサポートが追加されました。

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理サーバーのディザスター リカバリー

今月追加された機能は、表にまとめられています。

**機能** | **詳細**
--- | ---
**Linux のサポート** | Red Hat Enterprise Linux 7.5、SUSE Linux Enterprise Server 12 のサポートが追加されました。



## <a name="next-steps"></a>次のステップ

[Azure の更新情報](https://azure.microsoft.com/updates/?product=site-recovery)に関するページで、更新プログラムの最新情報を常に把握してください。