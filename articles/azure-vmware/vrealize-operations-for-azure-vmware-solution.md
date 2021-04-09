---
title: Azure VMware Solution 向け vRealize Operations の設定
description: Azure VMware Solution のプライベート クラウド向けに vRealize Operations を設定する方法について説明します。
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 8015bb61a7401b4c97807e0256e06d4967c39026
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802490"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Azure VMware Solution 向け vRealize Operations の設定


vRealize Operations Manager は、VMware インフラストラクチャ管理者がシステム リソースの監視に使用できる運用管理プラットフォームです。 これらのシステム リソースとしては、アプリケーションレベルのオブジェクトまたはインフラストラクチャ レベルのオブジェクト (物理および仮想の両方) があります。 ほとんどの VMware 管理者は、vRealize Operations を使用して VMware プライベート クラウドのコンポーネント (vCenter、ESXi、NSX-T、vSAN、VMware HCX) を監視および管理していました。  プロビジョニングされた各 Azure VMware Solution プライベート クラウドには、専用の vCenter、NSX-T、vSAN、HCX デプロイが含まれています。 

最初に、「[開始する前に](#before-you-begin)」と「[前提条件](#prerequisites)」をよくお読みください。 その後、2 つの一般的なデプロイ トポロジについて説明します。

> [!div class="checklist"]
> * [Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Azure VMware Solution で動作する vRealize Operations のデプロイ](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>開始する前に
* vRealize Operations のデプロイの詳細について、[vRealize Operations Manager の製品ドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を確認します。 
* 基本的な Azure VMware Solution Software-Defined Datacenter (SDDC) の[チュートリアル シリーズ](tutorial-network-checklist.md)を確認します。
* Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開オプションでは、必要に応じて、[vRealize Operations Remote Controller](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) の製品ドキュメントを確認します。 


## <a name="prerequisites"></a>前提条件
* [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) がインストールされている。
* オンプレミスと Azure VMware Solution SDDC の間で構成されている VPN または Azure ExpressRoute。
* Azure VMware Solution のプライベート クラウドが Azure にデプロイされていること。



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開
ほとんどの顧客には、1 つ以上のオンプレミスの vCenter ドメインを管理するために既存のオンプレミスの vRealize Operations の展開があります。 彼らは、Azure VMware Solution プライベート クラウドをプロビジョニングするときに、Azure ExpressRoute またはレイヤー 3 VPN ソリューションを使用して、オンプレミス環境をプライベート クラウドに接続します。  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Azure VMware Solution を管理するオンプレミスの vRealize Operations の展開" border="false":::

vRealize Operations 機能を Azure VMware Solution プライベート クラウドにまで拡張するには、[プライベート クラウド リソース用のアダプター インスタンス](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html)を作成します。 これにより、Azure VMware Solution プライベート クラウドからデータが収集され、オンプレミスの vRealize Operations に取り込まれます。 オンプレミスの vRealize Operations Manager インスタンスは、Azure VMware Solution 上の vCenter および NSX-T Manager に直接接続できます。 必要に応じて、Azure VMware Solution プライベート クラウドに vRealize Operations Remote Collector をデプロイできます。 このコレクターにより、プライベート クラウドから収集したデータが圧縮して暗号化された後、ExpressRoute または VPN ネットワークを経由して、オンプレミスで実行されている vRealize Operations Manager に送信されます。 

> [!TIP]
> vRealize Operations Manager をインストールするためのステップ バイ ステップ ガイドについては、[VMware のドキュメント](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html)を参照してください。 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Azure VMware Solution で動作する vRealize Operations のデプロイ

もう 1 つのオプションは、vRealize Operations Manager のインスタンスを、プライベート クラウド内の vSphere クラスターにデプロイする方法です。 

>[!IMPORTANT]
>現在、このオプションは VMware ではサポートされていません。

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Azure VMware Solution で動作する vRealize Operations" border="false":::

インスタンスがデプロイされたら、vCenter、ESXi、NSX-T、vSAN、HCX からデータを収集するように vRealize Operations を構成できます。 



## <a name="known-limitations"></a>既知の制限事項

- Azure VMware Solution の **cloudadmin\@vsphere.local** ユーザーは、[特権が制限されています](concepts-identity.md)。  Azure VMware Solution 上の仮想マシン (VM) では、VMware ツールを使用したゲスト内のメモリ コレクションはサポートされません。  この場合、アクティブで消費されたメモリの利用は引き続き機能します。
- Azure VMware Solution により DRS 設定などのクラスター構成が管理されるため、ホスト ベースのビジネスの目的でのワークロードの最適化は機能しません。
- SDDC 内のクラスターをまたぐ配置向けの、クラスター ベースのビジネスの目的を使用するワークロードの最適化は、vRealize Operations Manager 8.0 以降で完全にサポートされています。 ただし、ワークロードの最適化では、リソース プールは認識されず、VM はクラスター レベルに配置されます。 ユーザーはこれを、Azure VMware Solution の vCenter Server インターフェイスで手動で修正できます。
- Azure VMware Solution の vCenter Server 資格情報を使用して、vRealize Operations Manager にサインインすることはできません。 
- Azure VMware Solution では、vRealize Operations Manager プラグインはサポートされていません。

vCenter Server クラウド アカウントを使用して Azure VMware Solution vCenter を vRealize Operations Manager に接続すると、警告が表示されます。

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="警告: アダプター インスタンスの作成に成功しました":::

この警告が発生するのは、Azure VMware Solution の **cloudadmin\@vsphere.local** ユーザーに、登録に必要なすべての vCenter Server アクションを実行するための十分な特権がないためです。 ただし、次に示すように、アダプター インスタンスでデータ収集を行うには十分な特権です。

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="データ収集を実行するアダプター インスタンス":::

詳細については、「[vCenter アダプター インスタンスの構成に必要な特権](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html)」を参照してください。

<!-- LINKS - external -->


<!-- LINKS - internal -->




