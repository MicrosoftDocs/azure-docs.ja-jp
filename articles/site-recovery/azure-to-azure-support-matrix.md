---
title: Azure Site Recovery を使用した Azure VM のディザスター リカバリーのサポート マトリックス
description: Azure Site Recovery を使用したセカンダリ リージョンへの Azure VM ディザスター リカバリーのサポートの概要を説明します。
ms.topic: article
ms.date: 07/14/2020
ms.author: raynew
ms.openlocfilehash: 3006522f75ed732c08e453a266e660cf4c577917
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815370"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure リージョン間での Azure VM ディザスター リカバリーに関するサポート マトリックス

この記事は、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、Azure リージョン間での Azure VM のディザスター リカバリーに対するサポートと前提条件をまとめたものです。


## <a name="deployment-method-support"></a>デプロイ方法のサポート

**デプロイ** |  **サポート**
--- | ---
**Azure Portal** | サポートされています。
**PowerShell** | サポートされています。 [詳細情報](azure-to-azure-powershell.md)
**REST API** | サポートされています。
**CLI** | 現在、サポートされていません


## <a name="resource-support"></a>リソースのサポート

**リソースのアクション** | **詳細**
--- | ---
**リソース グループ間の資格情報コンテナーの移動** | サポートされていません
**リソース グループ間のコンピューティング、ストレージ、およびネットワークの移動** | サポートされていません。<br/><br/> VM のレプリケート後に VM や関連コンポーネント (ストレージやネットワークなど) を移動する場合は、その VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**ディザスター リカバリーのためにあるサブスクリプションから別のサブスクリプションに Azure VM をレプリケートする** | 同じ Azure Active Directory テナント内でサポートされます。
**サポートされている地理的なクラスター内 (サブスクリプション内外) のさまざまなリージョンにわたって VM を移行する** | 同じ Azure Active Directory テナント内でサポートされます。
**同じリージョン内の VM の移行** | サポートされていません。

## <a name="region-support"></a>リージョンのサポート

同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして、復旧できます。 地理クラスターは、データの遅延と主権を念頭に置きながら定義されます。


**地理クラスター** | **Azure リージョン**
-- | --
America | カナダ東部、カナダ中部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国中部、米国中北部
ヨーロッパ | 英国西部、英国南部、北ヨーロッパ、西ヨーロッパ、南アフリカ西部、南アフリカ北部、ノルウェー東部、ノルウェー西部、フランス中部
アジア | インド南部、インド中部、インド西部、東南アジア、東アジア、東日本、西日本、韓国中部、韓国南部
オーストラリア    | オーストラリア東部、オーストラリア南東部、オーストラリア中部、オーストラリア中部 2
Azure Government    | 米国政府バージニア、US Gov アイオワ、米国政府アリゾナ、米国政府テキサス、米国防総省東部、米国防総省中部
ドイツ    | ドイツ中部、ドイツ北東部
中国 | 中国東部、中国北部、中国北部 2、中国東部 2
国内のディザスター リカバリーのために予約されている、制限されたリージョン |ドイツ北部はドイツ中西部用に予約されており、スイス西部はスイス北部用に予約されており、フランス南部はフランス中部用に予約されており、アラブ首長国連邦中部はアラブ首長国連邦北部のお客様用に制限されています

>[!NOTE]
>
> - **ブラジル南部**では、次のリージョンにレプリケートしてフェールオーバーできます: 米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、および米国中北部。
> - ブラジル南部は、Site Recovery を使用して VM をレプリケートするソース リージョンとしてのみ使用できます。 ターゲット リージョンとしては動作できません。 これは、地理的な距離による待機時間の問題のためです。 ソース リージョンとしてのブラジル南部からターゲットにフェールオーバーする場合は、ターゲット リージョンからブラジル南部へのフェールバックがサポートされていることに注意してください。
> - 適切なアクセス権があるリージョン内では動作できます。
> - コンテナーの作成を希望しているリージョンが表示されない場合は、お使いのサブスクリプションが、そのリージョン内にリソースを作成するアクセス権を持っていることを確認します。
> - レプリケーションを有効にしても地理的クラスター内にリージョンが表示されない場合は、お使いのサブスクリプションにそのリージョンに VM を作成するアクセス許可があることを確認します。



## <a name="cache-storage"></a>キャッシュ ストレージ

この表は、レプリケーション中に Site Recovery によって使用されるキャッシュ ストレージ アカウントのサポートをまとめたものです。

**設定** | **サポート** | **詳細**
--- | --- | ---
汎用目的 V2 ストレージ アカウント (ホット層とクール層) | サポートされています | V2 のトランザクション コストは V1 ストレージ アカウントよりかなり高いため、GPv2 の使用はお勧めできません。
Premium Storage | サポートされていません | コストの最適化のために、キャッシュ ストレージには標準のストレージ アカウントが使用されます。
仮想ネットワークの Azure Storage ファイアウォール  | サポートされています | ファイアウォールが有効なキャッシュ ストレージ アカウントまたはターゲット ストレージ アカウントを使用している場合は必ず、['信頼できる Microsoft サービスを許可'](../storage/common/storage-network-security.md#exceptions) してください。<br></br>また、ソース Vnet の少なくとも 1 つ以上のサブネットにアクセスできることを確認してください。


## <a name="replicated-machine-operating-systems"></a>レプリケートされるマシンのオペレーティング システム

Site Recovery は、このセクションに示したオペレーティング システムを実行する Azure VM のレプリケーションをサポートしています。 既にレプリケートされているマシンがその後別のメジャーなカーネルにアップグレード (またはダウングレード) された場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があることに注意してください。

### <a name="windows"></a>Windows


**オペレーティング システム** | **詳細**
--- | ---
Windows Server 2019 | Server Core、Server with Desktop Experience でサポートされています。
Windows Server 2016  | サーバー コア、デスクトップ エクスペリエンス搭載サーバーに対してサポートされます。
Windows Server 2012 R2 | サポートされています。
Windows Server 2012 | サポートされています。
Windows Server 2008 R2 with SP1/SP2 | サポートされています。<br/><br/> Azure VM の Mobility Service 拡張機能のバージョン [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) から、Windows Server 2008 R2 SP1/SP2 を実行しているマシンに Windows [サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。  SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。
Windows 10 (x64) | サポートされています。
Windows 8.1 (x64) | サポートされています。
Windows 8 (x64) | サポートされています。
Windows 7 (x64) with SP1 以降 | Azure VM の Mobility Service 拡張機能のバージョン [9.30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) から、Windows 7 with SP1 を実行しているマシンに Windows [サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。  SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。



#### <a name="linux"></a>Linux

**オペレーティング システム** | **詳細**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4564347/)、[7.9](https://support.microsoft.com/help/4578241/)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、[8.2](https://support.microsoft.com/help/4570609/)
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、[7.8](https://support.microsoft.com/help/4564347/)、[7.9](https://support.microsoft.com/help/4578241/)、8.0、8.1、[8.2](https://support.microsoft.com/en-us/help/4570609)
Ubuntu 14.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> パスワード ベースの認証とサインインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのサインインが無効になっている場合があります。 パスワード ベースのサインインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Ubuntu 18.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines) </br> Ubuntu 18.4.03 (カーネル v5.4) は [9.36](https://support.microsoft.com/help/4578241/) からサポートされます|
Ubuntu 20.04 LTS server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | すべての 7. *x* バージョンのサポートが含まれます ([サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines))
Debian 8 | すべての 8. *x* バージョンのサポートが含まれます ([サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines))
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4、SP5 ([サポートされるカーネル バージョン](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)) </br> (** SP5 は、Azure から Azure への DR シナリオでは、9.33 の最新のパッチを通じて入手できます)。
SUSE Linux Enterprise Server 15 | 15 および 15 SP1。 [(サポートされるカーネル バージョン)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> レプリケートするマシンの SP3 から SP4 へのアップグレードはサポートされません。 レプリケートされたマシンがアップグレードされた場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があります。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4573888/)、[8.0](https://support.microsoft.com/help/4573888/)  <br/> Red Hat と互換可能なカーネルまたは Unbreakable Enterprise カーネル リリース 3、4、5 (UEK3、UEK4、UEK5) を実行している<br/><br/>8.1<br/>すべての UEK カーネルと RedHat カーネル <= 3.10.0-1062.* での実行は、[9.35](https://support.microsoft.com/help/4573888/) でサポートされます。それ以降の RedHat カーネルは、[9.36](https://support.microsoft.com/help/4578241/) でサポートされます

** 注: 最新の Linux カーネルをリリースから 30 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンの上にホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Ubuntu カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | 9.32、[9.33](https://support.microsoft.com/help/4564347/)、[9.34](https://support.microsoft.com/help/4570609)、[9.35](https://support.microsoft.com/help/4573888/)、[9.36](https://support.microsoft.com/help/4578241/)| 3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure |
|||
16.04 LTS | [9.36](https://support.microsoft.com/help/4578241/)| 4.4.0-21-generic ～ 4.4.0-187-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic ～ 4.15.0-112-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure ～ 4.15.0-1092-azure |
16.04 LTS | [9.34](https://support.microsoft.com/help/4570609)、[9.35](https://support.microsoft.com/help/4573888/) | 4.4.0-21-generic から 4.4.0-184-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-106-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1089-azure</br> 4.15.0-107-generic、4.4.0-185-generic、4.15.0-1091-azure (9.35 ホット フィックス修正プログラムを適用)** |
16.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.4.0-21-generic から 4.4.0-178-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-99-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1082-azure </br> 4.15.0-101-generic および 4.4.0-179-generic (9.33 ホットフィックス修正プログラムを適用)**|
16.04 LTS | 9.32 | 4.4.0-21-generic から 4.4.0-171-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-74-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1066-azure|
|||
18.04 LTS | [9.36](https://support.microsoft.com/help/4578241/) | 4.15.0-20-generic ～ 4.15.0-112-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic ～ 5.0.0-58-generic </br> 5.3.0-19-generic ～ 5.3.0-65-generic </br> 5.4.0-37-generic ～ 5.4.0-42-generic</br> 4.15.0-1009-azure ～ 4.15.0-1092-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure ～ 5.3.0-1032-azure </br> 5.4.0-1020-azure ～ 5.4.0-1022-azure|
18.04 LTS | [9.34](https://support.microsoft.com/help/4570609)、[9.35](https://support.microsoft.com/help/4573888/) | 4.15.0-20-generic から 4.15.0-108-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-52-generic </br> 5.3.0-19-generic から 5.3.0-61-generic </br> 4.15.0-1009-azure から 4.15.0-1089-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1031-azure </br> 4.15.0-109-generic、5.0.0-53-generic、5.3.0-62-generic、4.15.0-1091-azure、5.3.0-1032-azure (9.35 ホット フィックス修正プログラムを適用)**|
18.04 LTS | [9.33](https://support.microsoft.com/help/4564347/) | 4.15.0-20-generic から 4.15.0-99-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-47-generic </br> 5.3.0-19-generic から 5.3.0-51-generic </br> 4.15.0-1009-azure から 4.15.0-1082-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1020-azure </br> 4.15.0-101-generic、5.0.0-48-generic、5.3.0-1022-azure、5.3.0-53-generic (9.33 ホットフィックス修正プログラムを適用)**|
18.04 LTS | 9.32| 4.15.0-20-generic から 4.15.0-74-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-37-generic </br> 5.3.0-19-generic から 5.3.0-24-generic </br> 4.15.0-1009-azure から 4.15.0-1037-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1028-azure </br> 5.3.0-1007-azure から 5.3.0-1009-azure|
|||
20.04 LTS |[9.36](https://support.microsoft.com/help/4578241/) | 5.4.0-26-generic ～ 5.4.0-42 </br> -generic 5.4.0-1010-azure ～ 5.4.0-1022-azure

** 注: 最新の Linux カーネルをリリースから 30 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンの上にホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Debian カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | 9.32、[9.33](https://support.microsoft.com/help/4564347/)、[9.34](https://support.microsoft.com/help/4570609)、[9.35](https://support.microsoft.com/help/4573888/)、[9.36](https://support.microsoft.com/help/4578241/) | 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.35](https://support.microsoft.com/help/4573888/, )、[9.36](https://support.microsoft.com/help/4578241/) | 3.16.0-4-amd64 から 3.16.0-11-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.11-amd64 |
Debian 8 | 9.32、[9.33](https://support.microsoft.com/help/4564347/)、[9.34](https://support.microsoft.com/help/4570609) | 3.16.0-4-amd64 から 3.16.0-10-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.11-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 12 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.36](https://support.microsoft.com/help/4578241/) | すべての [SUSE 12 SP1、SP2、SP3、SP4 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.22-azure  |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.34](https://support.microsoft.com/help/4570609)、[9.35](https://support.microsoft.com/help/4573888/) | すべての [SUSE 12 SP1、SP2、SP3、SP4 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.19-azure  |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4) | 9.32、[9.33](https://support.microsoft.com/help/4564347/) [9.35](https://support.microsoft.com/help/4573888/) | すべての [SUSE 12 SP1、SP2、SP3、SP4 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | 9.33 ホットフィックス修正プログラム | すべての [SUSE 12 SP1、SP2、SP3、SP4 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.34-azure </br> 4.12.14-16.7-azure から 4.12.14-16.13-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 15 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 および 15 SP1 | [9.36](https://support.microsoft.com/help/4578241/)  | 既定では、すべての [SUSE 15 および 15 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.38-azure
SUSE Linux Enterprise Server 15 および 15 SP1 | [9.34](https://support.microsoft.com/help/4570609)、[9.35](https://support.microsoft.com/help/4573888/)  | 既定では、すべての [SUSE 15 および 15 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.33-azure 
|SUSE Linux Enterprise Server 15 および 15 SP1 | [9.33](https://support.microsoft.com/help/4564347/) | 既定では、すべての [SUSE 15 および 15 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.30-azure |
SUSE Linux Enterprise Server 15 および 15 SP1 | 9.32 | 既定では、すべての [SUSE 15 および 15 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-8.22-azure |


## <a name="replicated-machines---linux-file-systemguest-storage"></a>レプリケートされるマシン - Linux ファイル システム/ゲスト ストレージ

* ファイル システム: ext3、ext4、XFS、BTRFS
* ボリューム マネージャー:LVM2

> [!NOTE]
> マルチパス ソフトウェアはサポートされていません。 


## <a name="replicated-machines---compute-settings"></a>レプリケートされるマシン - コンピューティングの設定

**設定** | **サポート** | **詳細**
--- | --- | ---
サイズ | 少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ | [Azure 仮想マシンのサイズ](../virtual-machines/sizes.md)を確認してください。
可用性セット | サポートされています | 既定のオプションを使用して Azure VM のレプリケーションを有効にした場合は、ソース リージョンの設定に基づいて可用性セットが自動的に作成されます。 次の設定を変更できます。
可用性ゾーン | サポートされています |
Hybrid Use Benefit (HUB) | サポートされています | ソース VM で HUB ライセンスが有効になっている場合、テスト フェールオーバー VM またはフェールオーバーした VM でも HUB ライセンスが使用されます。
仮想マシン スケール セット | サポートされていません |
Azure ギャラリー イメージ - Microsoft が公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Azure ギャラリー イメージ - サード パーティが公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
カスタム イメージ - サード パーティが公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Site Recovery を使用して移行された VM | サポートされています | Site Recovery を使用して Azure に移行された VMware VM または物理マシンの場合は、マシンで実行されている古いバージョンのモビリティ サービスをアンインストールし、マシンを再起動してから、それを他の Azure リージョンにレプリケートする必要があります。
RBAC ポリシー | サポートされていません | VM でのロールベースのアクセス制御 (RBAC) ポリシーは、ターゲット リージョンのフェールオーバー VM にレプリケートされません。
拡張機能 | サポートされていません | 拡張機能は、ターゲット リージョン内のフェールオーバー VM にはレプリケートされません。 これはフェールオーバー後に手動でインストールする必要があります。
近接配置グループ | サポートされています | 近接配置グループ内にある仮想マシンは、Site Recovery を使用して保護できます。


## <a name="replicated-machines---disk-actions"></a>レプリケートされるマシン - ディスクのアクション

**操作** | **詳細**
-- | ---
レプリケートされた VM のディスク サイズの変更 | フェールオーバー前にソース VM でサポートされます。 レプリケーションを無効にしたり、もう一度有効にしたりする必要はありません。<br/><br/> フェールオーバー後にソース VM を変更した場合、変更はキャプチャされません。<br/><br/> フェールオーバー後に Azure VM のディスク サイズを変更した場合、変更は Site Recovery によってキャプチャされず、元の VM サイズにフェールバックされます。
レプリケートされた VM にディスクを追加する | サポートされています
保護されたディスクへのオフラインでの変更 | ディスクの接続を解除し、それらをオフラインで変更する場合、完全な再同期をトリガーする必要があります。

## <a name="replicated-machines---storage"></a>レプリケートされるマシン - ストレージ

この表は、Azure VM の OS ディスク、データ ディスク、一時ディスクに対するサポートをまとめたものです。

- [Linux](../virtual-machines/linux/disk-scalability-targets.md) VM と [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM でのパフォーマンスの問題を回避するため、VM ディスクの制限とターゲットを守ることが重要です。
- 既定の設定でデプロイすると、Site Recovery によって、ソースの設定に基づいてディスクとストレージ アカウントが自動的に作成されます。
- カスタマイズする場合は、必ずガイドラインに従ってください。

**コンポーネント** | **サポート** | **詳細**
--- | --- | ---
OS ディスクの最大サイズ | 2048 GB | VM ディスクに関する[詳細を表示します](../virtual-machines/managed-disks-overview.md)。
一時ディスク | サポートされていません | 一時ディスクは常にレプリケーションから除外されます。<br/><br/> 一時ディスクに永続データを格納しないでください。 [詳細については、こちらを参照してください](../virtual-machines/managed-disks-overview.md)。
データ ディスクの最大サイズ | マネージド ディスクの場合は 8,192 GB<br></br>アンマネージド ディスクの場合は 4,095 GB|
データ ディスクの最小サイズ | 非管理対象ディスクの制限はありません。 マネージド ディスクの場合は 2 GB |
データ ディスクの最大数 | 最大 64 (特定の Azure VM サイズでのサポートに従います) | VM サイズに関する[詳細を表示します](../virtual-machines/sizes.md)。
データ ディスクの変更レート | Premium ストレージではディスクあたり最大 20 MBps。 Standard ストレージではディスクあたり最大 2 MBps。 | ディスクでの平均データ変更レートが継続的に最大値より高い場合、レプリケーションが追いつかなくなります。<br/><br/>  ただし、上限の超過が散発的である場合は、レプリケーションが追いつくことができます。しかし、復旧ポイントがわずかに遅延することもあります。
データ ディスク - Standard ストレージ アカウント | サポートされています |
データ ディスク - Premium ストレージ アカウント | サポートされています | VM のディスクが Premium ストレージ アカウントと Standard ストレージ アカウントに分散している場合は、ターゲット リージョンでのストレージ構成が確実に同じになるように、ディスクごとに異なるターゲット ストレージ アカウントを選択できます。
マネージド ディスク - Standard | Azure Site Recovery がサポートされている Azure リージョンでサポートされます。 |
マネージド ディスク - Premium | Azure Site Recovery がサポートされている Azure リージョンでサポートされます。 |
Standard SSD | サポートされています |
冗長性 | LRS と GRS がサポートされます。<br/><br/> ZRS はサポートされていません。
クールおよびホット ストレージ | サポートされていません | VM ディスクは、クールおよびホット ストレージではサポートされません
記憶域スペース | サポートされています |
保存時の暗号化 (SSE) | サポートされています | SSE はストレージ アカウントでの既定の設定です。
保存時の暗号化 (CMK) | サポートされています | マネージド ディスクでは、ソフトウェア キーと HSM キーの両方がサポートされています
保存時の二重暗号化 | サポートされています | [Windows](../virtual-machines/windows/disk-encryption.md) および [Linux](../virtual-machines/linux/disk-encryption.md) でサポートされているリージョンの詳細について参照してください
Windows OS 用 Azure Disk Encryption (ADE) | マネージド ディスクを使用する VM の場合にサポートされます。 | アンマネージド ディスクを使用する VM はサポートされていません。 <br/><br/> HSM で保護されたキーはサポートされていません。 <br/><br/> 1 つのディスク上の個々のボリュームの暗号化はサポートされていません。 |
Linux OS 用 Azure Disk Encryption (ADE) | マネージド ディスクを使用する VM の場合にサポートされます。 | アンマネージド ディスクを使用する VM はサポートされていません。 <br/><br/> HSM で保護されたキーはサポートされていません。 <br/><br/> 1 つのディスク上の個々のボリュームの暗号化はサポートされていません。 <br><br> レプリケーションの有効化に関する既知の問題。 [詳細情報。](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS キーのローテーション | サポートされていません | ストレージ アカウントの SAS キーがローテーションされている場合は、レプリケーションを無効にしてからもう一度有効にする必要があります。 |
ホット アド    | サポートされています | マネージド ディスクを使用する VM では、レプリケートされた Azure VM に追加するデータ ディスクのレプリケーションの有効化がサポートされています。 <br/><br/> 一度に 1 つのディスクのみ Azure VM にホット アドできます。 複数のディスクの並列追加はサポートされていません。 |
ディスクのホット リムーブ    | サポートされていません | VM 上でデータ ディスクを削除する場合は、レプリケーションを無効にしてから、もう一度 VM に対してレプリケーションを有効にする必要があります。
ディスクの除外 | サポート。 [PowerShell](azure-to-azure-exclude-disks.md) を使用して構成する必要があります。 |    一時ディスクは既定で除外されます。
記憶域スペース ダイレクト  | クラッシュ整合性復旧ポイントに対してサポートされています。 アプリケーション整合性復旧ポイントはサポートされていません。 |
スケールアウト ファイル サーバー  | クラッシュ整合性復旧ポイントに対してサポートされています。 アプリケーション整合性復旧ポイントはサポートされていません。 |
DRBD | DRBD セットアップの一部であるディスクはサポートされていません。 |
LRS | サポートされています |
GRS | サポートされています |
RA-GRS | サポートされています |
ZRS | サポートされていません |
クールおよびホット ストレージ | サポートされていません | 仮想マシン ディスクは、クールおよびホット ストレージではサポートされません
仮想ネットワークの Azure Storage ファイアウォール  | サポートされています | ストレージ アカウントへの仮想ネットワーク アクセスを制限する場合は、[信頼できる Microsoft サービスを許可](../storage/common/storage-network-security.md#exceptions)を有効にします。
汎用目的 V2 ストレージ アカウント (ホット層とクール層の両方) | サポートされています | 汎用目的 V1 のストレージ アカウントに比べて、トランザクション コストが非常に大きくなります
第 2 世代 (UEFI ブート) | サポートされています
NVMe ディスク | サポートされていません
Azure 共有ディスク | サポートされていません
転送オプションのセキュリティ保護 | サポートされています

>[!IMPORTANT]
> パフォーマンスの問題を回避するには、[Linux](../virtual-machines/linux/disk-scalability-targets.md) または [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM に対する VM ディスクのスケーラビリティとパフォーマンスのターゲットに従っていることを確認します。 既定の設定を使用する場合は、ソースの構成に基づいて、必要なディスクとストレージ アカウントが Site Recovery によって作成されます。 設定をカスタマイズして独自の設定を選択する場合は、ソース VM のディスクのスケーラビリティおよびパフォーマンスのターゲットに従います。

## <a name="limits-and-data-change-rates"></a>制限とデータ変化率

以下の表は、Site Recovery の制限をまとめたものです。

- これらの制限は、Microsoft のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。
- 実際の結果は、ご使用のアプリで発生するさまざまな I/O によって異なることが考えられます。
- ディスクごとのデータ チャーンと仮想マシンごとのデータ チャーンという考慮すべき 2 つの制限があります。
- 仮想マシンごとのデータ チャーンは、現在、サイズに関係なく 54 MB/秒に制限されています。

**ストレージ ターゲット** | **レプリケーション元の平均ディスク I/O** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |    (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |20 MB/秒 | (ディスクあたり) 1,684 GB

## <a name="replicated-machines---networking"></a>レプリケートされるマシン - ネットワーク
**設定** | **サポート** | **詳細**
--- | --- | ---
NIC | 特定の Azure VM サイズでサポートされる最大数 | フェールオーバー中に VM が作成されるときには NIC が作成されます。<br/><br/> フェールオーバー VM 上の NIC 数は、レプリケーションが有効にされたときのソース VM 上の NIC 数によって決まります。 レプリケーションを有効にした後に NIC を追加または削除しても、フェールオーバー後、レプリケートされた VM 上にある NIC の数は影響を受けません。 <br/><br/> フェールオーバー後の NIC の順序は元の順序と同じであることが保証されないことにも注意してください。 <br/><br/> 組織の名前付け規則に基づいて、ターゲット リージョンの NIC の名前を変更できます。 NIC の名前変更は、PowerShell を使用して実行できます。
インターネット Load Balancer | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、構成済みロード バランサーを関連付けます。
内部ロード バランサー | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、構成済みロード バランサーを関連付けます。
パブリック IP アドレス | サポートされています | 既存のパブリック IP アドレスを NIC に関連付けます。 または、パブリック IP アドレスを作成し、復旧計画の Azure Automation スクリプトを使用して、それを NIC に関連付けます。
NIC 上の NSG | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、NSG を NIC に関連付けます。
サブネット上の NSG | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、NSG を サブネットに関連付けます。
予約済みの (静的) IP アドレス | サポートされています | ソース VM で NIC が静的 IP を使用していて、ターゲット サブネットで同じ IP アドレスを使用できる場合、そのアドレスはフェールオーバーした VM に割り当てられます。<br/><br/> ターゲット サブネットで同じ IP アドレスを使用できない場合は、そのサブネットで使用できる IP アドレスの 1 つが、その VM 用に予約されます。<br/><br/> **[レプリケートされたアイテム]**  >  **[設定]**  >  **[コンピューティングとネットワーク]**  >  **[ネットワーク インターフェイス]** で、固定 IP アドレスとサブネットを指定することもできます。
動的 IP アドレス | サポートされています | ソース VM で NIC に動的 IP アドレス指定が設定されている場合、フェールオーバーした VM の NIC も既定で動的になります。<br/><br/> 必要な場合は、これを固定 IP アドレスに変更できます。
複数の IP アドレス | サポートされていません | 複数の IP アドレスが設定された NIC を持つ VM をフェールオーバーするときは、ソース リージョン内の NIC のプライマリ IP アドレスのみが保持されます。 複数の IP アドレスを割り当てるには、VM を[復旧計画](recovery-plan-overview.md)に追加し、計画に追加の IP アドレスを割り当てるスクリプトをアタッチするか、フェールオーバー後に手動またはスクリプトで変更を行うことができます。
Traffic Manager     | サポートされています | トラフィックがソース リージョンのエンドポイントに定期的にルーティングされ、フェールオーバーの場合はターゲット リージョンのエンドポイントにルーティングされるように、Traffic Manager を事前に構成することができます。
Azure DNS | サポートされています |
[カスタム DNS]    | サポートされています |
非認証プロキシ | サポートされています | [詳細情報](./azure-to-azure-about-networking.md)
認証済みプロキシ | サポートされていません | VM が送信接続に認証済みプロキシを使用している場合は、Azure Site Recovery でレプリケートできません。
オンプレミスへの VPN サイト間接続<br/><br/>(ExpressRoute の有無)| サポートされています | Site Recovery トラフィックがオンプレミスにルーティングされないように、UDR と NSG が構成されていることを確認します。 [詳細情報](./azure-to-azure-about-networking.md)
VNet 間接続    | サポートされています | [詳細情報](./azure-to-azure-about-networking.md)
仮想ネットワーク サービス エンドポイント | サポートされています | ストレージ アカウントへの仮想ネットワーク アクセスを制限している場合は、信頼された Microsoft サービスがストレージ アカウントへのアクセスを許可されることを確認します。
Accelerated Networking | サポートされています | 高速ネットワークは、ソース VM で有効になっている必要があります。 [詳細については、こちらを参照してください](azure-vm-disaster-recovery-with-accelerated-networking.md)。
Palo Alto Network アプライアンス | サポートされていません | サード パーティ製のアプライアンスでは、多くの場合、仮想マシン内のプロバイダーによって課せられる制限があります。 Azure Site Recovery では、エージェント、拡張機能、送信接続を利用できるようにする必要があります。 ただし、アプライアンスでは、仮想マシン内で送信アクティビティを構成することはできません。
IPv6  | サポートされていません | IPv4 と IPv6 の両方を含む混合構成もサポートされていません。 Site Recovery 操作の前に、IPv6 の範囲のサブネットを解放してください。
Site Recovery サービスへの Private Link アクセス | サポートされています | [詳細情報](azure-to-azure-how-to-enable-replication-private-endpoints.md)



## <a name="next-steps"></a>次のステップ

- Azure VM のレプリケートに関する[ネットワークのガイダンス](./azure-to-azure-about-networking.md)を確認します。
- [Azure VM をレプリケートして](./azure-to-azure-quickstart.md)ディザスター リカバリーを展開します。
