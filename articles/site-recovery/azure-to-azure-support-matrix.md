---
title: Azure Site Recovery を使用した Azure VM のディザスター リカバリーのサポート マトリックス
description: Azure Site Recovery を使用したセカンダリ リージョンへの Azure VM ディザスター リカバリーのサポートの概要を説明します。
ms.topic: article
ms.date: 11/29/2020
ms.author: raynew
ms.openlocfilehash: 1fcb998c6f9dd53a754d348020d4d90e097e9502
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607783"
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
**Azure 専用ホスト** | サポートされていません。

## <a name="region-support"></a>リージョンのサポート

同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして、復旧できます。 地理クラスターは、データの遅延と主権を念頭に置きながら定義されます。


**地理クラスター** | **Azure リージョン**
-- | --
America | カナダ東部、カナダ中部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国中部、米国中北部
ヨーロッパ | 英国西部、英国南部、北ヨーロッパ、西ヨーロッパ、南アフリカ西部、南アフリカ北部、ノルウェー東部、フランス中部、スイス北部、ドイツ中西部
アジア | インド南部、インド中部、インド西部、東南アジア、東アジア、東日本、西日本、韓国中部、韓国南部
オーストラリア    | オーストラリア東部、オーストラリア南東部、オーストラリア中部、オーストラリア中部 2
Azure Government    | 米国政府バージニア、US Gov アイオワ、米国政府アリゾナ、米国政府テキサス、米国防総省東部、米国防総省中部
ドイツ    | ドイツ中部、ドイツ北東部
中国 | 中国東部、中国北部、中国北部 2、中国東部 2
国内のディザスター リカバリーのために予約されている、制限されたリージョン |スイス西部はスイス北部用に予約されており、フランス南部はフランス中部用に予約されており、アラブ首長国連邦中部はアラブ首長国連邦北部のお客様用に制限されており、ノルウェー西部はノルウェー東部のお客様用に制限されています

>[!NOTE]
>
> - **ブラジル南部** では、次のリージョンにレプリケートしてフェールオーバーできます: 米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、および米国中北部。
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

下の表は、1 つのストレージ アカウントにレプリケートできるディスクの数に関する制限を示しています。

**ストレージ アカウントの種類**    |    **チャーン = ディスクあたり 4 MBps**    |    **チャーン = ディスクあたり 8 MBps**
---    |    ---    |    ---
V1 ストレージ アカウント    |    300 個のディスク    |    150 個のディスク
V2 ストレージ アカウント    |    750 個のディスク    |    375 個のディスク

ディスクの平均チャーンが増加するにつれて、ストレージ アカウントでサポートできるディスクの数が減少します。 上記の表は、プロビジョニングする必要があるストレージ アカウントの数を決定するためのガイドとして使用できます。

上記の制限は、Azure 間とゾーン間の DR シナリオに固有であることに注意してください。 

## <a name="replicated-machine-operating-systems"></a>レプリケートされるマシンのオペレーティング システム

Site Recovery は、このセクションに示したオペレーティング システムを実行する Azure VM のレプリケーションをサポートしています。 既にレプリケートされているマシンがその後別のメジャーなカーネルにアップグレード (またはダウングレード) される場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があることに注意してください。

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
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4564347/)、[7.9](https://support.microsoft.com/help/4578241/)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、[8.2](https://support.microsoft.com/help/4570609/)、[8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10 </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、[7.8](https://support.microsoft.com/help/4564347/)、[7.9 GA 前バージョン](https://support.microsoft.com/help/4578241/)、7.9 GA バージョンは 9.37 ホット フィックス修正プログラムからサポートされています** </br> 8.0、8.1、[8.2](https://support.microsoft.com/en-us/help/4570609)、[8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | すべての 14.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)) 
Ubuntu 16.04 LTS Server | すべての 16.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))<br/><br/> パスワード ベースの認証とサインインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのサインインが無効になっている場合があります。 パスワード ベースのサインインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Ubuntu 18.04 LTS Server | すべての 18.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))<br/><br/> パスワード ベースの認証とサインインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのサインインが無効になっている場合があります。 パスワード ベースのサインインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Ubuntu 20.04 LTS server | すべての 20.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))
Debian 7 | すべての 7. *x* バージョンのサポートが含まれます ([サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines))
Debian 8 | すべての 8. *x* バージョンのサポートが含まれます ([サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines))
Debian 9 | 9\.1 から 9.13 までのサポートが含まれています。 Debian 9.0 はサポートされていません。 [サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4、SP5 ([サポートされるカーネル バージョン](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines))
SUSE Linux Enterprise Server 15 | 15、SP1、SP2 [(サポートされるカーネル バージョン)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> レプリケートするマシンの SP3 から SP4 へのアップグレードはサポートされません。 レプリケートされたマシンがアップグレードされた場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があります。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4573888/)、[7.9](https://support.microsoft.com/help/4597409)、[8.0](https://support.microsoft.com/help/4573888/)、[8.1](https://support.microsoft.com/help/4573888/) Red Hat と互換可能なカーネルまたは Unbreakable Enterprise カーネル リリース 3、4、5 (UEK3、UEK4、UEK5) を実行している<br/><br/>8.1 すべての UEK カーネルと 3.10.0-1062.* 以前の RedHat カーネルでの実行が [9.35](https://support.microsoft.com/help/4573888/) でサポートされています。 それ以外の RedHat カーネルのサポートは [9.36](https://support.microsoft.com/help/4578241/) で利用できます

> [!NOTE]
> Linux バージョンの場合、Azure Site Recovery ではカスタマイズされた OS イメージがサポートされません。 ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。

** 注: 最新の Linux カーネルをリリースから 15 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンに加えてホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Ubuntu カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | [9.37](https://support.microsoft.com/help/4582666/)、[9.38](https://support.microsoft.com/help/4590304/)、[9.39](https://support.microsoft.com/help/4597409/)、[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure |
|||
16.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic から 4.4.0-201-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-133-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1106-azure <br/> 4.4.0-203-generic、4.15.0-136-generic、4.15.0-1108-azure から 9.41 までのホット フィックス修正プログラム**|
16.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic から 4.4.0-197-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-128-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1102-azure </br> 4.15.0-132-generic、4.4.0-200-generic、4.15.0-1106-azure、4.15.0-133-generic、4.4.0-201-generic から 9.40 までのホット フィックス修正プログラム**|
16.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-generic から 4.4.0-194-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-123-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1098-azure </br> 4.4.0-197-generic、4.15.0-126-generic、4.15.0-128-generic、4.15.0-1100-azure、4.15.0-1102-azure から 9.39 までのホット フィックス修正プログラム**|
16.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-generic ～ 4.4.0-190-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic ～ 4.15.0-118-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure ～ 4.15.0-1096-azure </br> 4.4.0-193-generic、4.15.0-120-generic、4.15.0-122-generic、4.15.0-1098-azure から 9.38 までのホット フィックス修正プログラム**|
16.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-generic から 4.4.0-189-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-115-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1093-azure </br> 4.4.0-190-generic、4.15.0-117-generic、4.15.0-118-generic、4.15.0-1095-azure、4.15.0-1096-azure から 9.37 までのホット フィックス修正プログラム**|
|||
18.04 LTS | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic から 4.15.0-135-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-65-generic </br> 5.3.0-19-generic から 5.3.0-70-generic </br> 5.4.0-37-generic から 5.4.0-59-generic</br> 5.4.0-60-generic から 5.4.0-65-generic </br> 4.15.0-1009-azure から 4.15.0-1106-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1039-azure </br> 4.15.0-136-generic、5.4.0-66-generic、4.15.0-1108-azure、5.4.0-1040-azure から 9.41 までのホット フィックス修正プログラム**|
18.04 LTS | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic から 4.15.0-129-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-63-generic </br> 5.3.0-19-generic から 5.3.0-69-generic </br> 5.4.0-37-generic から 5.4.0-59-generic</br> 4.15.0-1009-azure から 4.15.0-1103-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1035-azure </br> 4.15.0-1104-azure、4.15.0-130-generic、4.15.0-132-generic、5.4.0-1036-azure、5.4.0-60-generic、5.4.0-62-generic、4.15.0-1106-azure、4.15.0-134-generic、4.15.0-135-generic、5.4.0-1039-azure、5.4.0-64-generic、5.4.0-65-generic から 9.40 までのホット フィックス修正プログラム**|
18.04 LTS | [9.39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-generic から 4.15.0-123-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-63-generic </br> 5.3.0-19-generic から 5.3.0-69-generic </br> 5.4.0-37-generic から 5.4.0-53-generic</br> 4.15.0-1009-azure から 4.15.0-1099-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1031-azure </br> 4.15.0-124-generic、5.4.0-54-generic、5.4.0-1032-azure、5.4.0-56-generic、4.15.0-1100-azure、4.15.0-126-generic、4.15.0-128-generic、5.4.0-58-generic、4.15.0-1102-azure、5.4.0-1034-azure から 9.39 までのホット フィックス修正プログラム**|
18.04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-generic から 4.15.0-118-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-61-generic </br> 5.3.0-19-generic から 5.3.0-67-generic </br> 5.4.0-37-generic から 5.4.0-48-generic</br> 4.15.0-1009-azure から 4.15.0-1096-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1026-azure </br> 4.15.0-121-generic、4.15.0-122-generic、5.0.0-62-generic, 5.3.0-68-generic、5.4.0-51-generic、5.4.0-52-generic、4.15.0-1099-azure、5.4.0-1031-azure から 9.38 までのホット フィックス修正プログラム**|
18.04 LTS | [9.37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-generic から 4.15.0-115-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-60-generic </br> 5.3.0-19-generic から 5.3.0-66-generic </br> 5.4.0-37-generic から 5.4.0-45-generic</br> 4.15.0-1009-azure から 4.15.0-1093-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1023-azure</br> 4.15.0-117-generic、4.15.0-118-generic、5.0.0-61-generic、5.3.0-67-generic、5.4.0-47-generic、5.4.0-48-generic、4.15.0-1095-azure、4.15.0-1096-azure、5.4.0-1025-azure、5.4.0-1026-azure から 9.37 までのホット フィックス修正プログラム**|
|||
20.04 LTS |[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic から 5.4.0-65 </br> -generic 5.4.0-1010-azure から 5.4.0-1039-azure </br> 5.8.0-29-generic から 5.8.0-43-generic </br> 5.4.0-66-generic、5.8.0-44-generic、5.4.0-1040-azure から 9.41 までのホット フィックス修正プログラム**|
20.04 LTS |[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic から 5.4.0-59 </br> -generic 5.4.0-1010-azure から 5.4.0-1035-azure </br> 5.8.0-29-generic から 5.8.0-34-generic </br> 5.4.0-1036-azure、5.4.0-60-generic、5.4.0-62-generic、5.8.0-36-generic、5.8.0-38-generic、5.4.0-1039-azure、5.4.0-64-generic、5.4.0-65-generic、5.8.0-40-generic、5.8.0-41-generic から 9.40 までのホット フィックス修正プログラム**|
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic から 5.4.0-53 </br> -generic 5.4.0-1010-azure から 5.4.0-1031-azure </br> 5.4.0-54-generic、5.8.0-29-generic、5.4.0-1032-azure、5.4.0-56-generic、5.8.0-31-generic、5.8.0-33-generic、5.4.0-58-generic、5.4.0-1034-azure から 9.39 までのホット フィックス修正プログラム**
20.04 LTS |[9.39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-generic から 5.4.0-53 </br> -generic 5.4.0-1010-azure から 5.4.0-1031-azure </br> 5.4.0-54-generic、5.8.0-29-generic、5.4.0-1032-azure、5.4.0-56-generic、5.8.0-31-generic、5.8.0-33-generic、5.4.0-58-generic、5.4.0-1034-azure から 9.39 までのホット フィックス修正プログラム**
20.04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-generic から 5.4.0-48 </br> -generic 5.4.0-1010-azure から 5.4.0-1026-azure </br> 5.4.0-51-generic、5.4.0-52-generic、5.8.0-23-generic、5.8.0-25-generic、5.4.0-1031-azure から 9.38 までのホット フィックス修正プログラム**
20.04 LTS |[9.37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-generic から 5.4.0-45 </br> -generic 5.4.0-1010-azure から 5.4.0-1023-azure </br> 5.4.0-47-generic、5.4.0-48-generic、5.4.0-1025-azure、5.4.0-1026-azure から 9.37 までのホット フィックス修正プログラム**

** 注: 最新の Linux カーネルをリリースから 15 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンに加えてホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Debian カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | [9.37](https://support.microsoft.com/help/4582666/)、[9.38](https://support.microsoft.com/help/4590304/)、[9.39](https://support.microsoft.com/help/4597409/)、[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.37](https://support.microsoft.com/help/4582666/)、[9.38](https://support.microsoft.com/help/4590304/)、[9.39](https://support.microsoft.com/help/4597409/)、[9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-amd64 から 3.16.0-11-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.14-cloud-amd64
Debian 9.1 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.13-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.13-cloud-amd64 
Debian 9.1 | [9.39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.12-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.12-cloud-amd64 </br> 4.19.0-0.bpo.13-amd64、4.19.0-0.bpo.13-cloud-amd64 から 9.39 までのホット フィックス修正プログラム**</br> 
Debian 9.1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-amd64 から 4.9.0-13-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.11-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.11-cloud-amd64 </br> 4.9.0-14-amd64、4.19.0-0.bpo.12-amd64、4.19.0-0.bpo.12-cloud-amd64 から 9.38 までのホット フィックス修正プログラム**
Debian 9.1 | [9.37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-amd64 から 4.9.0-13-amd64、4.19.0-0.bpo.6-amd64 から 4.19.0-0.bpo.10-amd64、4.19.0-0.bpo.6-cloud-amd64 から 4.19.0-0.bpo.10-cloud-amd64
|||
Debian 10 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-amd64 から 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-amd64 から 4.19.0-13-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-13-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 12 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.44-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.38-azure </br> 4.12.14-16.41-azure から 9.40 までのホット フィックス修正プログラム**|
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.39](https://support.microsoft.com/help/4597409/) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.34-azure </br> 4.12.14-16.38-azure から 9.39 までのホット フィックス修正プログラム**|
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.38](https://support.microsoft.com/help/4590304/) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.28-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.37](https://support.microsoft.com/help/4582666/) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.22-azure </br> 4.12.14-16.25-azure、4.12.14-16.28-azure から 9.37 までのホット フィックス修正プログラム**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 15 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.35-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.29-azure </br> 5.3.18-18.32-azure、4.12.14-8.58-azure から 9.40 までのホット フィックス修正プログラム**
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.39](https://support.microsoft.com/help/4597409/)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.47-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.21-azure </br> 4.12.14-8.52-azure、5.3.18-18.24-azure、4.12.14-8.55-azure、5.3.18-18.29-azure から 9.39 までのホット フィックス修正プログラム**
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.44-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.18-azure </br> 4.12.14-8.47-azure、5.3.18-18.21-azure から 9.38 までのホット フィックス修正プログラム**
SUSE Linux Enterprise Server 15 および 15 SP1 | [9.37](https://support.microsoft.com/help/4582666/)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.38-azure </br> 4.12.14-8.41-azure、4.12.14-8.44-azure から 9.37 までのホット フィックス修正プログラム**


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
Azure RBAC ポリシー | サポートされていません | VM での Azure ロールベースのアクセス制御 (Azure RBAC) ポリシーは、ターゲット リージョンのフェールオーバー VM にレプリケートされません。
拡張機能 | サポートされていません | 拡張機能は、ターゲット リージョン内のフェールオーバー VM にはレプリケートされません。 これはフェールオーバー後に手動でインストールする必要があります。
近接配置グループ | サポートされています | 近接配置グループ内にある仮想マシンは、Site Recovery を使用して保護できます。
Tags  | サポートされています | ソース仮想マシンに適用されたユーザー生成タグは、テスト フェールオーバーまたはフェールオーバー後にターゲット仮想マシンに引き継がれます。 VM 上のタグは、VM がターゲット リージョンに存在する限り、24 時間に 1 回レプリケートされます。


## <a name="replicated-machines---disk-actions"></a>レプリケートされるマシン - ディスクのアクション

**操作** | **詳細**
-- | ---
レプリケートされた VM のディスク サイズの変更 | フェールオーバー前にソース VM でサポートされます。 レプリケーションを無効にしたり、もう一度有効にしたりする必要はありません。<br/><br/> フェールオーバー後にソース VM を変更した場合、変更はキャプチャされません。<br/><br/> フェールオーバー後に Azure VM のディスク サイズを変更した場合、変更は Site Recovery によってキャプチャされず、元の VM サイズにフェールバックされます。
レプリケートされた VM にディスクを追加する | サポートされています
保護されたディスクへのオフラインでの変更 | ディスクの接続を解除し、それらをオフラインで変更する場合、完全な再同期をトリガーする必要があります。

## <a name="replicated-machines---storage"></a>レプリケートされるマシン - ストレージ

この表は、Azure VM の OS ディスク、データ ディスク、一時ディスクに対するサポートをまとめたものです。

- [マネージド ディスク](../virtual-machines/disks-scalability-targets.md)でのパフォーマンスの問題を回避するために、VM ディスクの制限とターゲットを監視することが重要です。
- 既定の設定でデプロイすると、Site Recovery によって、ソースの設定に基づいてディスクとストレージ アカウントが自動的に作成されます。
- カスタマイズする場合は、必ずガイドラインに従ってください。

**コンポーネント** | **サポート** | **詳細**
--- | --- | ---
OS ディスクの最大サイズ | 2048 GB | VM ディスクに関する[詳細を表示します](../virtual-machines/managed-disks-overview.md)。
一時ディスク | サポートされていません | 一時ディスクは常にレプリケーションから除外されます。<br/><br/> 一時ディスクに永続データを格納しないでください。 [詳細については、こちらを参照してください](../virtual-machines/managed-disks-overview.md)。
データ ディスクの最大サイズ | マネージド ディスクの場合は 32 TB<br></br>アンマネージド ディスクの場合は 4 TB|
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
NVMe ストレージ インターフェイス | サポートされていません
保存時の暗号化 (SSE) | サポートされています | SSE はストレージ アカウントでの既定の設定です。
保存時の暗号化 (CMK) | サポートされています | マネージド ディスクでは、ソフトウェア キーと HSM キーの両方がサポートされています
保存時の二重暗号化 | サポートされています | [Windows](../virtual-machines/disk-encryption.md) および [Linux](../virtual-machines/disk-encryption.md) でサポートされているリージョンの詳細について参照してください
Windows OS 用 Azure Disk Encryption (ADE) | マネージド ディスクを使用する VM の場合にサポートされます。 | アンマネージド ディスクを使用する VM はサポートされていません。 <br/><br/> HSM で保護されたキーはサポートされていません。 <br/><br/> 1 つのディスク上の個々のボリュームの暗号化はサポートされていません。 |
Linux OS 用 Azure Disk Encryption (ADE) | マネージド ディスクを使用する VM の場合にサポートされます。 | アンマネージド ディスクを使用する VM はサポートされていません。 <br/><br/> HSM で保護されたキーはサポートされていません。 <br/><br/> 1 つのディスク上の個々のボリュームの暗号化はサポートされていません。 <br><br> レプリケーションの有効化に関する既知の問題。 [詳細情報。](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS キーのローテーション | サポートされていません | ストレージ アカウントの SAS キーがローテーションされている場合は、レプリケーションを無効にしてからもう一度有効にする必要があります。 |
ホスト キャッシュ | サポートされています
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
書き込みアクセラレータが有効になっているディスク | サポートされていません
Tags  | サポートされています | ユーザー生成タグは、24 時間ごとにレプリケートされます。

>[!IMPORTANT]
> [マネージド ディスク](../virtual-machines/disks-scalability-targets.md)に関するパフォーマンスの問題を回避するために、VM ディスクのスケーラビリティとパフォーマンスのターゲットに従っていることを確認します。 既定の設定を使用する場合は、ソースの構成に基づいて、必要なディスクとストレージ アカウントが Site Recovery によって作成されます。 設定をカスタマイズして独自の設定を選択する場合は、ソース VM のディスクのスケーラビリティおよびパフォーマンスのターゲットに従います。

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
インターネット Load Balancer | サポートされていません | プライマリ リージョンでパブリック/インターネット ロード バランサーを設定できます。 しかし、パブリックおよびインターネットのロード バランサーは DR リージョンの Azure Site Recovery ではサポートされていません。
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
Tags  | サポートされています | NIC 上のユーザー生成タグは、24 時間ごとにレプリケートされます。



## <a name="next-steps"></a>次のステップ

- Azure VM のレプリケートに関する[ネットワークのガイダンス](./azure-to-azure-about-networking.md)を確認します。
- [Azure VM をレプリケートして](./azure-to-azure-quickstart.md)ディザスター リカバリーを展開します。
