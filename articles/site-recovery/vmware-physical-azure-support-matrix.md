---
title: Azure Site Recovery における VMware/物理ディザスター リカバリーのサポート マトリックス
description: Azure Site Recovery を使用して VMware VM および物理サーバーを Azure にディザスター リカバリーする場合のサポートについてまとめています。
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: c7f2d6ecd01959e239a1ab048018452b2ae5fc20
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495217"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM および物理サーバーの Azure へのディザスター リカバリーのサポート マトリックス

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、VMware VM と物理サーバーの Azure へのディザスター リカバリーを行う場合にサポートされるコンポーネントと設定の概要について説明します。

- VMware VM/物理サーバー ディザスター リカバリーのアーキテクチャの[詳細](vmware-azure-architecture.md)についてご確認ください。
- [チュートリアル](tutorial-prepare-azure.md)に従ってディザスター リカバリーをお試しください。

> [!NOTE]
> Site Recovery では、ソース マシンのディザスター リカバリーがセットアップされているターゲット リージョンから顧客データを移動または格納することはありません。 お客様は、必要に応じて、別のリージョンから Recovery Services コンテナーを選択することもできます。 Recovery Services コンテナーにはメタデータが含まれますが、実際の顧客データは含まれません。

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

**シナリオ** | **詳細**
--- | ---
VMware VM のディザスター リカバリー | オンプレミス VMware VM の Azure へのレプリケーション。 このシナリオは、Azure portal または [PowerShell](vmware-azure-disaster-recovery-powershell.md) を使用してデプロイできます。
物理サーバーのディザスター リカバリー | オンプレミスの Windows または Linux の物理サーバーから Azure へのレプリケーション。 このシナリオは、Azure Portal で展開できます。

## <a name="on-premises-virtualization-servers"></a>オンプレミスの仮想化サーバー

**サーバー** | **必要条件** | **詳細**
--- | --- | ---
vCenter Server | バージョン 7.0 およびバージョン 6.7、6.5、6.0、または 5.5 での後続の更新プログラム | ディザスター リカバリーのデプロイでは vCenter サーバーを使用することをお勧めします。
vSphere ホスト | バージョン 7.0 およびバージョン 6.7、6.5、6.0、または 5.5 での後続の更新プログラム | vSphere ホストと vCenter サーバーはプロセス サーバーと同じネットワーク内に存在することが推奨されます。 既定では、プロセス サーバーは構成サーバーで実行されます。 [詳細については、こちらを参照してください](vmware-physical-azure-config-process-server-overview.md)。

## <a name="site-recovery-configuration-server"></a>Site Recovery 構成サーバー

構成サーバーはオンプレミスのマシンで、構成サーバー、プロセス サーバー、マスター ターゲット サーバーを含む Site Recovery のコンポーネントを実行します。

- VMware VM の場合は、VMware VM を作成する OVF テンプレートをダウンロードすることで構成サーバーを設定します。
- 物理サーバーの場合は、構成サーバーのマシンを手動で設定します。

**コンポーネント** | **必要条件**
--- |---
CPU コア数 | 8
RAM | 16 GB
ディスクの数 | ディスク 3 台<br/><br/> ディスクには、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブが含まれます。
ディスクの空き領域 | プロセス サーバーのキャッシュ用に 600 GB の領域。
ディスクの空き領域 | リテンション ドライブ用に 600 GB の領域。
オペレーティング システム  | Windows Server 2012 R2 またはデスクトップ エクスペリエンス搭載 Windows Server 2016 <br/><br> このアプライアンスの組み込みのマスター ターゲットをフェールバックに使用する予定の場合は、OS のバージョンが、レプリケートされるアイテムと同じかそれよりも高いことを確認してください。|
オペレーティング システムのロケール | 英語 (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | バージョン [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) 以降の構成サーバーの場合は不要です。
Windows Server の役割 | Active Directory Domain Services、インターネット インフォメーション サービス (IIS)、Hyper-V は有効にしないでください。
グループ ポリシー| - コマンド プロンプトへのアクセス禁止。 <br/> - レジストリ編集ツールへのアクセス禁止。 <br/> - ファイル添付の信頼ロジック。 <br/> - スクリプト実行の有効化。 <br/> - [詳細情報](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | 以下を実行します。<br/><br/> - 既定の Web サイトが事前に存在しないようにする <br/> - [匿名認証](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10))を有効にする <br/> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 設定を有効にする  <br/> - ポート 443 でリッスンしている既存の Web サイト/アプリがないようにする<br/>
NIC の種類 | VMXNET3 (VMware VM としてデプロイされている場合)
IP アドレスの種類 | 静的
Port | コントロール チャネルのオーケストレーションに使用される 443<br/>データ転送用の 9443

> [!NOTE]
オペレーティング システムは、英語ロケールでインストールする必要があります。 インストール後のロケールの変換によって、潜在的な問題が発生する可能性があります。

## <a name="replicated-machines"></a>レプリケートされるマシン

Site Recovery は、サポートされているマシンで実行されているすべてのワークロードのレプリケーションをサポートします。

**コンポーネント** | **詳細**
--- | ---
マシンの設定 | Azure にレプリケートするマシンは、[Azure の要件](#azure-vm-requirements)を満たしている必要があります。
マシンのワークロード | Site Recovery は、サポートされているマシンで実行されているすべてのワークロードのレプリケーションをサポートします。 [詳細については、こちらを参照してください](./site-recovery-workload.md)。
コンピューター名 | コンピューターの表示名が [Azure 予約済みリソース名](../azure-resource-manager/templates/error-reserved-resource-name.md)に含まれていないことを確認します。<br/><br/> 論理ボリューム名では、大文字と小文字は区別されません。 デバイス上の 2 つのボリュームに同じ名前が付いていないことを確認します。 例:"voLUME1" と "volume1" という名前のボリュームは、Azure Site Recovery を使用して保護できません。

### <a name="for-windows"></a>Windows の場合

**オペレーティング システム** | **詳細**
--- | ---
Windows Server 2019 | [更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016) (モビリティ サービスのバージョン 9.22) 以降でサポートされています。
Windows Server 2016 64 ビット | Server Core、Server with Desktop Experience でサポートされています。
Windows Server 2012 R2 / Windows Server 2012 | サポートされています。
Windows Server 2008 R2 SP1 以降。 | サポートされています。<br/><br/> モビリティ サービス エージェントのバージョン [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 以降では、Windows 2008 R2 SP1 以降を実行しているコンピューターに[サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。 SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)でご確認ください。
Windows Server 2008 SP2 以降 (64 ビット/32 ビット) |  移行についてのみサポートされています。 [詳細については、こちらを参照してください](migrate-tutorial-windows-server-2008.md)。<br/><br/> モビリティ サービス エージェントのバージョン [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 以降では、Windows 2008 SP2 コンピューター上に[サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4493730) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。 SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)でご確認ください。
Windows 10、Windows 8.1、Windows 8 | 64 ビット システムのみがサポートされています。 32 ビット システムはサポートされていません。
Windows 7 SP1 64 ビット | [更新プログラム ロールアップ 36](https://support.microsoft.com/help/4503156) (モビリティ サービスのバージョン 9.22) 以降でサポートされています。 </br></br> モビリティ サービス エージェントの [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) 以降では、Windows 7 SP1 コンピューター上に[サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。  SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)でご確認ください。

### <a name="for-linux"></a>Linux の場合

**オペレーティング システム** | **詳細**
--- | ---
Linux | 64 ビット システムのみがサポートされています。 32 ビット システムはサポートされていません。<br/><br/>すべての Linux サーバーには [Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)がインストールされている必要があります。 テスト フェールオーバー/フェールオーバー後に Azure でサーバーを起動するために必要です。 組み込みの LIS コンポーネントがない場合は、Azure で起動するマシンのレプリケーションを有効にする前に、確実に[コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)をインストールしてください。 <br/><br/> Site Recovery では、Azure で Linux サーバーを実行するためにフェールオーバーが調整されます。 ただし Linux ベンダーによっては、サポート終了前のディストリビューション バージョンしかサポート対象に含まれない場合もあります。<br/><br/> Linux ディストリビューションでは、ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。<br/><br/> 保護されているマシンの Linux ディストリビューションのメジャー バージョン間のアップグレードはサポートされていません。 アップグレードするには、いったんレプリケーションを無効にしてオペレーティング システムをアップグレードしてから、レプリケーションを再び有効にします。<br/><br/> Azure での Linux およびオープン ソース テクノロジのサポートについて詳しくは、[こちら](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)をご覧ください。
Linux Red Hat Enterprise | 5.2 から 5.11</b><br/> 6.1 から 6.10</b> </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4564347/)、[7.9 ベータ版](https://support.microsoft.com/help/4578241/)、[7.9](https://support.microsoft.com/help/4590304/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、[8.2](https://support.microsoft.com/help/4570609)、[8.3](https://support.microsoft.com/help/4597409/) <br/> Red Hat Enterprise Linux 5.2 から 5.11 および 6.1 から 6.10 を実行しているサーバーの古いカーネルには、[Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)が事前インストールされていないものがいくつかあります。 組み込みの LIS コンポーネントがない場合は、Azure で起動するマシンのレプリケーションを有効にする前に、確実に[コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)をインストールしてください。
Linux: CentOS | 5.2 から 5.11</b><br/> 6.1 から 6.10</b><br/> </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4564347/)、[7.9](https://support.microsoft.com/help/4578241/) </br> [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、[8.2](https://support.microsoft.com/help/4570609)、[8.3](https://support.microsoft.com/help/4597409/) <br/><br/> CentOS 5.2 から 5.11 および 6.1 から 6.10 を実行しているサーバーの古いカーネルには、[Linux Integration Services (LIS) コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)が事前インストールされていないものがいくつかあります。 組み込みの LIS コンポーネントがない場合は、Azure で起動するマシンのレプリケーションを有効にする前に、確実に[コンポーネント](https://www.microsoft.com/download/details.aspx?id=55106)をインストールしてください。
Ubuntu | Ubuntu 14.04* LTS サーバー [(サポートされるカーネルのバージョンを確認してください)](#ubuntu-kernel-versions)<br/>Ubuntu 16.04* LTS サーバー [(サポートされるカーネルのバージョンを確認してください)](#ubuntu-kernel-versions) </br> Ubuntu 18.04* LTS サーバー [(サポートされるカーネルのバージョンを確認してください)](#ubuntu-kernel-versions) </br> Ubuntu 20.04* LTS サーバー [(サポートされるカーネルのバージョンを確認してください)](#ubuntu-kernel-versions) </br> (*すべての 14.04.* x *、16.04.* x *、18.04.* x *、20.04.* x* バージョンのサポートが含まれます)
Debian | Debian 7/Debian 8 (すべての 7. *x*、8. *x* バージョン)、Debian 9 (9.1 から 9.13 までのサポートが含まれます。 Debian 9.0 はサポートされていません。)、Debian 10 [(サポートされているカーネルのバージョンの確認)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1、SP2、SP3、SP4、[SP5](https://support.microsoft.com/help/4570609) [(サポートされているカーネル バージョンの確認)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15、15 SP1 [(サポートされているカーネル バージョンの確認)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3。 [構成サーバーに最新のモビリティ エージェント インストーラーをダウンロードしてください](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)。 </br> SUSE Linux Enterprise Server 11 SP4 </br> **注**:レプリケートされたマシンの SUSE Linux Enterprise Server 11 SP3 から SP4 へのアップグレードはサポートされていません。 アップグレードするには、レプリケーションを無効にし、アップグレードの後に再び有効にします。 <br/>|
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4573888/)、[7.9](https://support.microsoft.com/help/4597409/)、[8.0](https://support.microsoft.com/help/4573888/)、[8.1](https://support.microsoft.com/help/4573888/)、[8.3](https://support.microsoft.com/help/4597409/)  <br/> Red Hat と互換可能なカーネルまたは Unbreakable Enterprise カーネル リリース 3、4、5 (UEK3、UEK4、UEK5) を実行している<br/><br/>8.1<br/>すべての UEK カーネルと RedHat カーネル <= 3.10.0-1062.* での実行は、[9.35](https://support.microsoft.com/help/4573888/) でサポートされます。それ以降の RedHat カーネルは、[9.36](https://support.microsoft.com/help/4578241/) でサポートされます

> [!Note]
>- Windows の各バージョンについては、Azure Site Recovery では [長期サービス チャネル (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) のビルドのみがサポートされます。  現時点では、[半期チャネル](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel)のリリースはサポートされていません。
>- Linux バージョンについては、Azure Site Recovery ではカスタマイズされた OS イメージがサポートされないことを確認してください。 ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。

### <a name="ubuntu-kernel-versions"></a>Ubuntu カーネルのバージョン

**サポートされているリリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | [9.37](https://support.microsoft.com/help/4582666/)、[9.38](https://support.microsoft.com/help/4590304/)、[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure |
|||
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic から 4.4.0-201-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-133-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1106-azure|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic から 4.4.0-197-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-128-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1102-azure |
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic から 4.4.0-194-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-123-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1098-azure|
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic ～ 4.4.0-190-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic ～ 4.15.0-118-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure ～ 4.15.0-1096-azure|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic から 4.4.0-189-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-115-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1093-azure |
|||
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic から 4.15.0-135-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-65-generic </br> 5.3.0-19-generic から 5.3.0-70-generic </br> 5.4.0-37-generic から 5.4.0-59-generic</br> 5.4.0-60-generic から 5.4.0-65-generic </br> 4.15.0-1009-azure から 4.15.0-1106-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1039-azure|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic から 4.15.0-129-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-63-generic </br> 5.3.0-19-generic から 5.3.0-69-generic </br> 5.4.0-37-generic から 5.4.0-59-generic</br> 4.15.0-1009-azure から 4.15.0-1103-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1035-azure|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic から 4.15.0-123-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-63-generic </br> 5.3.0-19-generic から 5.3.0-69-generic </br> 5.4.0-37-generic から 5.4.0-53-generic</br> 4.15.0-1009-azure から 4.15.0-1099-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1031-azure|
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic から 4.15.0-118-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-61-generic </br> 5.3.0-19-generic から 5.3.0-67-generic </br> 5.4.0-37-generic から 5.4.0-48-generic</br> 4.15.0-1009-azure から 4.15.0-1096-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1026-azure|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic から 4.15.0-115-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-60-generic </br> 5.3.0-19-generic から 5.3.0-66-generic </br> 5.4.0-37-generic から 5.4.0-45-generic</br> 4.15.0-1009-azure から 4.15.0-1093-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1023-azure|
|||
20.04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic から 5.4.0-65 </br> -generic 5.4.0-1010-azure から 5.4.0-1039-azure </br> 5.8.0-29-generic から 5.8.0-43-generic </br>
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic から 5.4.0-59 </br> -generic 5.4.0-1010-azure から 5.4.0-1035-azure </br> 5.8.0-29-generic から 5.8.0-34-generic|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic から 5.4.0-53 </br> -generic 5.4.0-1010-azure から 5.4.0-1031-azure
20.04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic から 5.4.0-48 </br> -generic 5.4.0-1010-azure から 5.4.0-1026-azure
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic から 5.4.0-45 </br> -generic 5.4.0-1010-azure から 5.4.0-1023-azure

### <a name="debian-kernel-versions"></a>Debian カーネルのバージョン


**サポートされているリリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | [9.37](https://support.microsoft.com/help/4582666/)、[9.38](https://support.microsoft.com/help/4590304/)、[9.39](https://support.microsoft.com/help/4597409/)、[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.37](https://support.microsoft.com/help/4582666/)、[9.38](https://support.microsoft.com/help/4590304/)、[9.39](https://support.microsoft.com/help/4597409/)、[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 から 3.16.0-11-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.14-cloud-amd64
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.13-cloud-amd64 </br>
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.12-cloud-amd64 </br> 
Debian 9.1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 から 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.11-cloud-amd64 </br> 
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 から 4.9.0-13-amd64、4.19.0-0.bpo.6-amd64 から 4.19.0-0.bpo.10-amd64、4.19.0-0.bpo.6-cloud-amd64 から 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 から 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 から 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 のサポートされるカーネルのバージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.44-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.38-azure|
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.39](https://support.microsoft.com/help/4597409/) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.34-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.38](https://support.microsoft.com/help/4590304/) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.37](https://support.microsoft.com/help/4582666/)  | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.22-azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 のサポートされるカーネルのバージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.35-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.29-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.21-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.18-azure
SUSE Linux Enterprise Server 15 および 15 SP1 | [9.37](https://support.microsoft.com/help/4582666/)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.38-azure

## <a name="linux-file-systemsguest-storage"></a>Linux ファイル システム/ゲストのストレージ

**コンポーネント** | **サポートされています**
--- | ---
ファイル システム | ext3、ext4、XFS、BTRFS (このテーブルごとに条件が適用されます)
論理ボリューム管理 (LVM) のプロビジョニング| シック プロビジョニング - はい <br></br> シン プロビジョニング - いいえ
ボリューム マネージャー | - LVM はサポートされています。<br/> - LVM での/boot は [更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/) (モビリティ サービスのバージョン 9.20) 以降でサポートされています。 モビリティ サービスの以前のバージョンではサポートされていません。<br/> - 複数の OS ディスクはサポートされていません。
準仮想化ストレージ デバイス | 準仮想化ドライバーによってエクスポートされたデバイスはサポートされません。
マルチ キュー ブロック IO デバイス | サポートされていません。
HP CCISS ストレージ コントローラーを使用する物理サーバー | サポートされていません。
デバイス/マウント ポイントの名前付け規則 | デバイス名またはマウント ポイント名は、一意である必要があります。<br/> 2 つのデバイス/マウント ポイントに大文字と小文字を区別した名前を付けていないことを確認します。 たとえば、同じ VM のデバイスに *device1* と *Device1* という名前を付けることはサポートされていません。
ディレクトリ | バージョン 9.20 ([更新プログラム ロールアップ 31](https://support.microsoft.com/help/4478871/) でリリース) より前のモビリティ サービスのバージョンを実行している場合、次の制限事項が適用されます。<br/><br/> - / (ルート)、/boot、/usr、/usr/local、/var、/etc の各ディレクトリ (個別のパーティション/ファイルシステムとしてセットアップされた場合) は、ソース サーバーの同じ OS ディスク上に存在する必要があります。</br> - /boot ディレクトリはディスク パーティション上にあり、LVM ボリュームではないことが必要です。<br/><br/> バージョン 9.20 以降、これらの制限事項は適用されません。 
ブート ディレクトリ | - GPT パーティション形式のブート ディスクは使用できません。 これは Azure アーキテクチャの制限です。 GPT ディスクは、データ ディスクとしてサポートされています。<br/><br/> VM での複数のブート ディスクはサポートされていません。<br/><br/> - 複数のディスクにまたがる LVM ボリュームでの /boot はサポートされていません。<br/> - ブート ディスクのないマシンをレプリケートすることはできません。
空き領域要件| /rootパーティションで 2GB <br/><br/> インストール フォルダーで 250MB
XFSv5 | メタデータ チェックサムなど、XFS ファイル システム上の XFSv5 機能はサポートされています (モビリティ サービスのバージョン 9.10 以降)。<br/> xfs_info ユーティリティを使用して、パーティションの XFS スーパーブロックを確認します。 `ftype` が 1 に設定されている場合は、XFSv5 の機能が使用されます。
BTRFS | \- BTRFS は[更新プログラム ロールアップ 34](https://support.microsoft.com/help/4490016) (モビリティ サービスのバージョン 9.22) 以降でサポートされています。 次の場合、BTRFS はサポートされません。<br/><br/> - 保護を有効にした後、BTRFS ファイル システムのサブボリュームが変更されている。</br> - BTRFS ファイル システムが複数のディスクに分散されている。</br> - BTRFS ファイル システムで RAID がサポートされている。

## <a name="vmdisk-management"></a>VM/ディスク管理

**操作** | **詳細**
--- | ---
レプリケートされた VM のディスク サイズの変更 | フェールオーバー前に VM プロパティで直接的に、ソース VM でサポートされます。 レプリケーションを無効にしたり、もう一度有効にしたりする必要はありません。<br/><br/> フェールオーバー後にソース VM を変更した場合、変更はキャプチャされません。<br/><br/> フェールオーバー後に Azure VM のディスク サイズを変更する場合は、フェールバックすると、Site Recovery で更新プログラム付きの新しい VM が作成されます。
レプリケートされた VM のディスクの追加 | サポートされていません。<br/> VM のレプリケーションを無効にし、ディスクを追加してから、レプリケーションを再び有効にします。

> [!NOTE]
> ディスク ID に対する変更はサポートされていません。 たとえば、ディスクのパーティション分割が GPT から MBR、またはその逆に変更された場合は、ディスク ID が変更されます。 このようなシナリオでは、レプリケーションが中断され、新しいセットアップが必要になります。 Linux マシンの場合、デバイス名の変更は、ディスク ID に影響を与えるため、サポートされていません。

## <a name="network"></a>ネットワーク

**コンポーネント** | **サポートされています**
--- | ---
ホスト ネットワークの NIC チーミング | VMware VM でサポートされています。 <br/><br/>物理マシンのレプリケーションではサポートされません。
ホスト ネットワークの VLAN | はい。
ホスト ネットワークの IPv4 | はい。
ホスト ネットワークの IPv6 | いいえ。
ゲスト/サーバー ネットワークの NIC チーミング | いいえ。
ゲスト/サーバー ネットワークの IPv4 | はい。
ゲスト/サーバー ネットワークの IPv6 | いいえ。
ゲスト/サーバー ネットワークの静的 IP (Windows) | はい。
ゲスト/サーバー ネットワークの静的 IP (Linux) | はい。 <br/><br/>フェールバックで DHCP を使用するように VM が構成されます。
ゲスト/サーバー ネットワークのマルチ NIC | はい。
Site Recovery サービスへの Private Link アクセス | はい。 [詳細については、こちらを参照してください](hybrid-how-to-enable-replication-private-endpoints.md)。


## <a name="azure-vm-network-after-failover"></a>Azure VM ネットワーク (フェールオーバー後)

**コンポーネント** | **サポートされています**
--- | ---
Azure ExpressRoute | はい
ILB | はい
ELB | はい
Azure の Traffic Manager | はい
マルチ NIC | はい
予約済み IP アドレス | はい
IPv4 | はい
送信元 IP アドレスを保持する | はい
Azure 仮想ネットワーク サービス エンドポイント<br/> | はい
Accelerated Networking | いいえ

## <a name="storage"></a>ストレージ
**コンポーネント** | **サポートされています**
--- | ---
ダイナミック ディスク | OS ディスクは、ベーシック ディスクである必要があります。 <br/><br/>データ ディスクは、ダイナミック ディスクにすることができます
Docker ディスク構成 | いいえ
ホスト NFS | VMware = はい<br/><br/> 物理サーバー = いいえ
ホスト SAN (iSCSI/FC) | はい
ホスト vSAN | VMware = はい<br/><br/> 物理サーバー = 該当なし
ホスト マルチパス (MPIO) | はい - テスト環境: Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
ホストの仮想ボリューム (VVols) | VMware = はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー VMDK | はい
ゲスト/サーバー共有クラスター ディスク | いいえ
ゲスト/サーバー暗号化ディスク | いいえ
ゲスト/サーバー NFS | いいえ
ゲスト/サーバー iSCSI | 移行の場合 - はい<br/>ディザスター リカバリーの場合 - いいえ、iSCSI は接続されたディスクとして VM にフェールバックされます
ゲスト/サーバー SMB 3.0 | いいえ
ゲスト/サーバー RDM | はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー ディスク > 1 TB | はい。ディスクは 1,024 MB 以上である必要があります。<br/><br/>マネージド ディスクにレプリケートする場合は最大 8,192 GB (9.26 バージョン以降)<br></br> ストレージ アカウントにレプリケートする場合は最大 4,095 GB
4K 論理および 4K 物理セクター サイズのゲスト/サーバー ディスク | いいえ
4K 論理および 512 バイト物理セクター サイズのゲスト/サーバー ディスク | いいえ
ストライピングされたディスクのゲスト/サーバー ボリューム > 4 TB | はい
論理ボリューム管理 (LVM)| シック プロビジョニング - はい <br></br> シン プロビジョニング - いいえ
ゲスト/サーバー - 記憶域スペース | いいえ
ゲスト/サーバー - NVMe インターフェイス | いいえ
ゲスト/サーバー ディスクのホット アド/削除 | いいえ
ゲスト/サーバー - ディスクの除外 | はい
ゲスト/サーバー マルチパス (MPIO) | いいえ
ゲスト/サーバー GPT パーティション | \- 5 個のパーティションが[更新プログラム ロールアップ 37](https://support.microsoft.com/help/4508614/) (モビリティ サービスのバージョン 9.25) 以降でサポートされています。 以前は 4 個までサポートしていました。
ReFS | Resilient File System は、モビリティ サービスのバージョン 9.23 以降でサポートされています。
ゲスト/サーバー EFI/UEFI ブート | - Site Recovery モビリティ エージェント バージョン 9.30 以降のすべての [Azure Marketplace UEFI オペレーティング システム](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace)でサポートされています。 <br/> - セキュリティで保護された UEFI ブートの種類はサポートされていません。 [詳細情報。](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)
RAID ディスク| いいえ

## <a name="replication-channels"></a>レプリケーション チャネル

|**レプリケーションの種類**   |**サポートされています**  |
|---------|---------|
|オフロード データ転送 (ODX)    |       いいえ  |
|オフライン シード処理        |   いいえ      |
| Azure Data Box | いいえ

## <a name="azure-storage"></a>Azure Storage

**コンポーネント** | **サポートされています**
--- | ---
ローカル冗長ストレージ | はい
geo 冗長ストレージ | はい
読み取りアクセス geo 冗長ストレージ | はい
クール ストレージ | いいえ
ホット ストレージ| いいえ
ブロック blob | いいえ
保存時の暗号化 (SSE)| はい
保存時の暗号化 (CMK)| はい (PowerShell Az 3.3.0 モジュール以降を使用)
保存時の二重暗号化 | はい (PowerShell Az 3.3.0 モジュール以降を使用)。 [Windows](../virtual-machines/disk-encryption.md) および [Linux](../virtual-machines/disk-encryption.md) でサポートされているリージョンの詳細について参照してください。
Premium Storage | はい
転送オプションのセキュリティ保護 | はい
インポート/エクスポート サービス | いいえ
VNet 用 Azure Storage ファイアウォール | はい。<br/> ターゲット ストレージ/キャッシュ ストレージ アカウント (レプリケーション データの保存に使用) で構成されたもの。
汎用目的 V2 ストレージ アカウント (ホット層とクール層) | はい (V1 に比べて V2 のトランザクション コストは非常に大きくなります)

## <a name="azure-compute"></a>Azure コンピューティング

**機能** | **サポートされています**
--- | ---
可用性セット | はい
可用性ゾーン | いいえ
ハブ | はい
マネージド ディスク | はい

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートされたオンプレミス VM は、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery がレプリケーションの前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **必要条件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | レプリケートするマシンの[サポート対象のオペレーティング システム](#replicated-machines)を確認します。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 第 1 世代のマシンでは最大 2,048 GB です。 <br> 第 2 世代のマシンでは最大 4,095 GB です。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 </br> ブート パーティションとシステム パーティションが異なるディスク上にある場合はサポートされていません | サポートされていない場合、確認は失敗します。
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。
データ ディスク サイズ | マネージド ディスクにレプリケートする場合は最大 32,767 GB (9.41 バージョン以降)<br> ストレージ アカウントにレプリケートする場合は最大 4,095 GB </br> 最小ディスク サイズ要件 - 1024 MB 以上| サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。 |
VM 名 | 1 から 63 文字。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。

## <a name="resource-group-limits"></a>リソース グループの制限

1 つのリソース グループで保護できる仮想マシンの数については、[サブスクリプションの制限とクォータ](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)に関する記事を参照してください。

## <a name="churn-limits"></a>チャーンの制限

以下の表は、Azure Site Recovery の制限を示したものです。
- これらの制限は、Microsoft のテストに基づいて公開されていますが、アプリ I/O として想定されるすべての組み合わせを網羅したものではありません。
- 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。
- 最良の結果を得るためには、[Deployment Planner ツール](site-recovery-deployment-planner.md)を実行し、テスト フェールオーバーを使用して広範なアプリケーション テストを実行し、アプリの実際のパフォーマンスを把握することを強くお勧めします。

**レプリケーション ターゲット** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |    (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |20 MB/秒 | (ディスクあたり) 1,684 GB


**ソース データ変更頻度** | **上限**
---|---
VM 上の全ディスクにおけるデータ変更頻度のピーク | 54 MB/秒
プロセス サーバーでサポートされる 1 日あたりのデータ変更頻度の上限 | 2 TB

- 前述の数値は、I/O のオーバーラップを 30% とした場合の平均値です。
- Site Recovery は、オーバーラップ比に基づくより高いスループットと、より大きな書き込みサイズ、そして実ワークロード I/O 動作を扱うことができます。
- これらの数値には、標準的なバックログとして約 5 分が想定されています。 つまりデータはアップロード後 5 分以内に処理されて復旧ポイントが作成されます。

## <a name="storage-account-limits"></a>ストレージ アカウントの制限

ディスクの平均チャーンが増加するにつれて、ストレージ アカウントでサポートできるディスクの数が減少します。 以下の表は、プロビジョニングする必要があるストレージ アカウントの数を決定するためのガイドとして使用できます。
 
**ストレージ アカウントの種類**    |    **チャーン = ディスクあたり 4 MBps**    |    **チャーン = ディスクあたり 8 MBps**
---    |    ---    |    ---
V1 ストレージ アカウント    |    600 個のディスク    |    300 個のディスク
V2 ストレージ アカウント    |    1500 個のディスク    |    750 個のディスク

上記の制限がハイブリッドの DR シナリオにのみ適用されることに注意してください。

## <a name="vault-tasks"></a>資格情報コンテナーのタスク

**操作** | **サポートされています**
--- | ---
リソース グループ間の資格情報コンテナーの移動 | いいえ
サブスクリプション内およびサブスクリプション間でコンテナーの移動 | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動 | いいえ
サブスクリプション内およびサブスクリプション間でストレージ、ネットワーク、Azure VM を移動 | いいえ


## <a name="obtain-latest-components"></a>最新のコンポーネントを入手する

**名前** | **説明** | **詳細**
--- | --- | ---
構成サーバー | オンプレミスにインストール済み。<br/> オンプレミスの VMware サーバーまたは物理マシンと Azure の間の通信を調整します。 | 構成サーバーに関する- [詳細情報](vmware-physical-azure-config-process-server-overview.md)をご覧ください。<br/> 最新バージョンへのアップグレードに関する- [詳細情報](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)をご覧ください。<br/> 構成サーバーの設定に関する- [詳細情報](vmware-azure-deploy-configuration-server.md)をご覧ください。
プロセス サーバー | 構成サーバーに既定でインストールされます。<br/> レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure に送信します。<br/> デプロイの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行するプロセス サーバーを追加できます。 | プロセス サーバーに関する- [詳細情報](vmware-physical-azure-config-process-server-overview.md)をご覧ください。<br/> 最新バージョンへのアップグレードに関する- [詳細情報](vmware-azure-manage-process-server.md#upgrade-a-process-server)をご覧ください。<br/> スケールアウト プロセス サーバーの設定に関する- [詳細情報](vmware-physical-large-deployment.md#set-up-a-process-server)をご覧ください。
モビリティ サービス | レプリケートする VMware VM または物理サーバーにインストールされます。<br/> オンプレミスの VMware サーバー/物理サーバーと Azure の間のレプリケーションを調整します。| モビリティ サービスに関する- [詳細情報](vmware-physical-mobility-service-overview.md)をご覧ください。<br/> 最新バージョンへのアップグレードに関する- [詳細情報](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)をご覧ください。<br/>



## <a name="next-steps"></a>次のステップ
VMware VM のディザスター リカバリーのために Azure を準備する[方法を学びます](tutorial-prepare-azure.md)。

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
