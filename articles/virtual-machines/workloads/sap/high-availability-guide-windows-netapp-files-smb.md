---
title: Azure NetApp Files (SMB) を使用した Windows での SAP NW 用 Azure VM の HA | Microsoft Docs
description: SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: radeltch
ms.openlocfilehash: a4c4631a0a1263e5a5398c44a8570f92571102e8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045838"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

この記事では、[Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) 上で [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) を使用して、仮想マシンのデプロイと構成、クラスター フレームワークのインストール、および Windows VM 上への高可用性 SAP NetWeaver 7.50 システムのインストールを行う方法について説明します。  

データベース レイヤーについては、詳しくは説明していません。 Azure [仮想ネットワーク](../../../virtual-network/virtual-networks-overview.md)が既に作成されていることを前提としています。  

はじめに、次の SAP Note およびガイドを確認してください

* [Azure NetApp Files のドキュメント][anf-azure-doc] 
* SAP Note [1928533][1928533]、これには次が含まれます。  
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows に必要な SAP カーネル バージョン
* SAP Note [2015553][2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2178632][2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [1999351][1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) は、SMB 3.x プロトコルの SAP でサポートされている CA 機能の前提条件を一覧表示しています。
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) には、Windows 2012 および 2016 で低速で実行されている SAP トランザクション AL11 のトラブルシューティング情報が含まれています。
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) には、SMB 3.0 プロトコルを使用した Windows Server 上のファイル共有の透過的なフェールオーバー機能に関する情報が含まれています。
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) には、データ アクセス中のファイル システムのパフォーマンスやエラーの低さに対処するための推奨事項 (8.3 名前の生成を非アクティブ化) があります。
* [Windows フェールオーバー クラスターへの SAP NetWeaver 高可用性のインストールおよび Azure 上での SAP ASCS/SCS インスタンスのファイル共有](./sap-high-availability-installation-wsfc-file-share.md) 
* [SAP NetWeaver のための Azure Virtual Machines 高可用性のアーキテクチャとシナリオ](./sap-high-availability-architecture-scenarios.md)
* [ASCS クラスター構成にプローブ ポートを追加する](sap-high-availability-installation-wsfc-file-share.md)
* [フェールオーバー クラスターへの (A)SCS インスタンスのインストール](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Azure NetApp Files の SMB ボリュームを作成する](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [Azure NetApp Files を使用した Microsoft Azure 上の NetApp SAP アプリケーション][anf-sap-applications-azure]

> [!IMPORTANT]
> 注意: [Azure NetApp Files][anf-azure-doc] SMB ボリュームでホストされている SMB 共有に、SWPM を使用して SAP システムをインストールすると、不十分なアクセス許可に対する "warningPerm is not defined" (warningPerm が定義されていません) のようなインストール エラーが発生して失敗するおそれがあることにご注意ください。 このエラーを回避するには、コンテキスト SWPM が実行されるユーザーに、SAP システムのインストール時に昇格された特権 "ドメイン管理者" が必要です。  

## <a name="overview"></a>概要

SAP によって、共有ディスクをクラスター化する方法に代わり、Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する新しい方法が開発されました。 クラスター共有ディスクを使う代わりに、SMB ファイル共有を使って SAP グローバル ホスト ファイルをデプロイできます。 Azure NetApp Files は、Active Directory を使用して、NTFS ACL と一緒に SMBv3 (NFS と共に) をサポートしています。 Azure NetApp Files は、(PaaS サービスであるため) 自動的に高可用性を実現します。 これらの機能は、Azure NetApp Files を、SAP global の SMB ファイル共有をホストするための優れたオプションとします。  
[Azure Active Directory (AD) Domain Services](../../../active-directory-domain-services/overview.md) と [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) の両方がサポートされます。 Azure NetApp Files で既存の Active Directory ドメイン コントローラーを使用することができます。 ドメイン コントローラーは、仮想マシンとして Azure に配置することも、ExpressRoute またはサイト間 VPN 経由でオンプレミスに配置することもできます。 この記事では、Azure VM でドメイン コントローラーを使用します。  
SAP Netweaver セントラル サービスの高可用性 (HA) を実現するには、共有ストレージが必要です。 それを Windows で実現するには、これまでは、SOFS クラスターを構築するか、SIOS のようなクラスター共有ディスク s/w を使用する必要がありました。 現在は、Azure NetApp Files 上にデプロイした共有ストレージを使用して、SAP Netweaver HA を実現できるようになりました。 Azure NetApp Files を共有ストレージ用に使用すると、SOFS または SIOS を使用する必要がなくなります。  

> [!NOTE]
> ファイル共有を使う SAP ASCS/SCS インスタンスのクラスター化は、SAP Kernel 7.49 (およびそれ以降) を含む SAP NetWeaver 7.40 (およびそれ以降) についてサポートされています。  

![SMB 共有を使う SAP ASCS/SCS HA のアーキテクチャ](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB ファイル共有の前提条件:
* SMB 3.0 (またはそれ以降) プロトコル。
* Active Directory ユーザー グループと computer$ コンピューター オブジェクトに Active Directory アクセス制御リスト (ACL) を設定する機能。
* ファイル共有で HA が有効になっている必要があります。

この参照アーキテクチャでの SAP セントラル サービスの共有は、Azure NetApp Files によって提供されます。

![SMB 共有を使う SAP ASCS/SCS HA のアーキテクチャの詳細](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files の SMB ボリュームを作成およびマウントする

Azure NetApp Files を使用するための準備として、次のステップを実行します。  

1. 「[Azure NetApp Files の登録](../../../azure-netapp-files/azure-netapp-files-register.md)」のステップに従います  
2. 「[NetApp アカウントを作成する](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)」で説明されているステップに従って、Azure NetApp アカウントを作成します  
3. 「[容量プールを設定する](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)」の指示に従って、容量プールを設定します
4. Azure NetApp Files リソースは、委任されたサブネット内に存在する必要があります。 「[サブネットを Azure NetApp Files に委任する](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)」の指示に従って、委任されたサブネットを作成します。  

   > [!IMPORTANT]
   > SMB ボリュームを作成する前に Active Directory の接続を作成する必要があります。 [Active Directory コンポーネント要件](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)を確認します。  

5. 「[Active Directory 接続を作成する](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)」の説明に従って、Active Directory 接続を作成します  
6. 「[SMB ボリュームを追加する](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)」の指示に従って、SMB Azure NetApp Files の SMB ボリュームを作成します  
7. Windows 仮想マシンに SMB ボリュームをマウントします。

> [!TIP]
> Azure NetApp Files ボリュームをマウントする方法の手順については、[Azure Portal](https://portal.azure.com/#home) で Azure NetApp Files オブジェクトに移動した場合は、 **[ボリューム]** ブレードをクリックし、 **[マウント手順]** をクリックします。  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Windows フェールオーバー クラスターを使用して SAP HA 向けのインフラストラクチャを準備する 

1. [Azure 内部ロード バランサーの ASCS/SCS 負荷分散規則を設定します](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716)。
2. [Windows 仮想マシンをドメインに追加します](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c)。
3. [SAP ASCS/SCS インスタンスの両方のクラスター ノードにレジストリ エントリを追加する](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [SAP ASCS/SCS インスタンス用の Windows Server フェールオーバー クラスターをセットアップする](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Windows Server 2016 を使用しているときは、[Azure クラウド監視](/windows-server/failover-clustering/deploy-cloud-witness)を構成することをお勧めします。


## <a name="install-sap-ascs-instance-on-both-nodes"></a>両方のノードに SAP ASCS インスタンスをインストールする

SAP から、次のソフトウェアが必要です。
   * SAP Software Provisioning Manager (SWPM) インストール ツール バージョン SPS25 以降。
   * SAP Kernel 7.49 以降
   * 「[クラスター化された SAP ASCS/SCS インスタンスの仮想ホスト名の作成](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097)」で説明されているように、クラステー化された SAP ASCS/SCS インスタンスの仮想ホスト名 (クラスター ネットワーク名) を作成します。

> [!NOTE]
> ファイル共有を使う SAP ASCS/SCS インスタンスのクラスター化は、SAP Kernel 7.49 (およびそれ以降) を含む SAP NetWeaver 7.40 (およびそれ以降) についてサポートされています。  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>ASCS/SCS インスタンスを最初の ASCS/SCS クラスター ノードにインストールする

1. SAP ASCS/SCS インスタンスを最初のクラスター ノードにインストールします。 SAP SWPM インストール ツールを起動し、次の場所に移動します。**製品** > **DBMS** > インストール > アプリケーション サーバー ABAP (または Java) > 高可用性システム > ASCS/SCS インスタンス > 最初のクラスター ノード。  

2. SWPM でクラスター共有の構成として、**[ファイル共有クラスター]** を選択します。  
3. **[SAP システム クラスター パラメーター]** のステップでプロンプトが表示されたら、既に作成した Azure NetApp Files SMB 共有のホスト名を、**[ファイル共有ホスト名]** として入力します。  この例では、SMB 共有ホスト名は **anfsmb-9562** です。 

   > [!IMPORTANT]
   > 前提条件チェッカーによって、SWPM に継続的可用性機能の条件が満たされていないことが示された場合は、「[Windows に存在しない共有フォルダーにアクセスしようとしたときの遅延エラー メッセージ](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)」に関する説明に従って対処できます。  

   > [!TIP]
   > 前提条件チェッカーによって、SWPM にスワップ サイズ条件が満たされていないことが示された場合は、[マイ コンピューター] > [システムのプロパティ] > [パフォーマンス設定] > [詳細] > [仮想メモリ] > [変化] の順に移動して SWAP サイズを調整できます。  

4. PowerShell を使用して、SAP クラスター リソースの `SAP-SID-IP` プローブ ポートを構成します。 この構成は、「[プローブ ポートの構成](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)」で説明したように、SAP ASCS/SCS クラスター ノードのいずれかで実行します。

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>ASCS/SCS インスタンスを 2 番目の ASCS/SCS クラスター ノードにインストールする

1. SAP ASCS/SCS インスタンスを 2 番目のクラスター ノードにインストールします。 SAP SWPM インストール ツールを起動し、**製品** > **DBMS** > インストール > アプリケーション サーバー ABAP (または Java) > 高可用性システム > ASCS/SCS インスタンス > 追加のクラスター ノードに移動します。  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>DBMS インスタンスと SAP アプリケーション サーバーのインストール

下記をインストールして、SAP のインストールを完了します。

   * DBMS インスタンス  
   * プライマリの SAP アプリケーション サーバー  
   * 追加の SAP アプリケーション サーバー  

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS インスタンス フェールオーバーをテストする 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>クラスター ノード A からクラスター ノード B およびこの逆方向へのフェール オーバー
このテスト シナリオでは、クラスター ノード sapascs1 をノード A と呼んで、クラスター ノード sapascs2 をノード B として参照します。

1. クラスター リソースがノード A 上で実行されていることを確認します。![図 1:フェールオーバー テストの前にノード A で実行されている Windows Server フェールオーバー クラスター リソース](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. クラスター ノード A を再起動します。SAP クラスター リソースは、クラスター ノード B に移動します。![図 2:フェールオーバー テストの後にノード B で実行されている Windows Server フェールオーバー クラスター リソース](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>ロック エントリのテスト

1. SAP エンキュー レプリケーション サーバー (ERS) がアクティブであることを確認します  
2. SAP システムにログオンし、transaction SU01 を実行して、変更モードでユーザー ID を開きます。 これにより、SAP ロック エントリが生成されます。  
3. SAP システムにログインしたときに、transaction ST12 に移動して、ロック エントリを表示します。  
4. クラスター ノード A からクラスター ノード B へのフェールオーバー。  
5. SAP ASCS/SCS クラスター リソースのフェールオーバーの前に生成されたロック エントリが保持されていることを確認します。  

![図 3:ロック エントリは、フェールオーバー テスト後も保持されます](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

詳細については、「[ERS を使用した ASCS のエンキュー フェールオーバーのトラブルシューティング](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)」に関する説明を参照してください
## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* 高可用性を確立し、SAP のディザスター リカバリーを計画する方法について学びます 
* HANA on Azure (大規模なインスタンス)。[Azure 上での SAP HANA (大規模なインスタンス) の高可用性およびディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)に関するページを参照してください。
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
