---
title: Azure NetApp Files および Azure VMware Solution
description: Azure NetApp Files および Azure VMware Solution VM を使用して、オンプレミス サーバー、Azure VMware Solution VM、クラウド インフラストラクチャの間でデータを移行および同期します。
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 2f2e8fdeb777e7e4b2b4e89c1bb36b51c3083257
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575425"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files および Azure VMware Solution

この記事では、Azure NetApp Files と Azure VMware Solution ベースのワークロードを統合する手順について説明します。 ゲスト オペレーティング システムは、Azure NetApp Files ボリュームにアクセスする仮想マシン (VM) 内で実行されます。 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files の概要

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) は、クラウド内の最も要求の厳しいエンタープライズ ファイル ワークロード (データベース、SAP、ハイ パフォーマンス コンピューティング アプリケーション) を、コードを変更することなく移行および実行するための Azure サービスです。

### <a name="features"></a>機能
(Azure NetApp Files が使用されているサービス)。

- **Active Directory 接続**: Azure NetApp Files では、[Active Directory Domain Services と Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use) がサポートされています。

- **共有プロトコル**: Azure NetApp Files では、サーバー メッセージ ブロック (SMB) およびネットワーク ファイル システム (NFS) プロトコルがサポートされています。 このサポートは、Linux クライアントでボリュームをマウントし、それを Windows クライアントにマップできることを示しています。

- **Azure VMware Solution**: Azure NetApp Files 共有を Azure VMware Solution 環境で作成された VM からマウントできます。

Azure NetApp Files は多くの Azure リージョンで使用でき、リージョン間レプリケーションをサポートしています。 Azure NetApp Files の構成方法については、「[Azure NetApp Files のストレージ階層](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)」を参照してください。

## <a name="reference-architecture"></a>参照アーキテクチャ

次の図は、Azure ExpressRoute を経由した Azure VMware Solution プライベート クラウドへの接続を示しています。 Azure VMware Solution 環境によって、Azure VMware Solution VM にマウントされた Azure NetApp Files 共有にアクセスされます。

![Azure VMware Solution アーキテクチャのための NetApp Files を示す図。](media/net-app-files/net-app-files-topology.png)

この記事では、Azure VMware Solution VM 用のファイル共有としての Azure NetApp Files ボリュームを設定、テスト、検証するための手順について説明します。 このシナリオでは、NFS プロトコルを使用してきました。 Azure NetApp Files と Azure VMware Solution は同じ Azure リージョン内に作成されています。

## <a name="prerequisites"></a>前提条件 

> [!div class="checklist"]
> * Azure NetApp Files の Azure サブスクリプションが有効になっていること
> * Azure NetApp Files 用のサブネット
> * Azure VMware Solution 上の Linux VM
> * Azure VMware Solution 上の Windows VM

## <a name="regions-supported"></a>サポートされているリージョン

サポートされているリージョンの一覧は、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)に関するページにあります。

## <a name="verify-pre-configured-azure-netapp-files"></a>事前に構成された Azure NetApp Files を検証する 

次の記事にある詳細な手順に従って、Azure NetApp Files ボリュームを作成し、Azure VMware Solution VM にマウントします。

- [NetApp アカウントを作成する](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [容量プールを設定する](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files の SMB ボリュームを作成する](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Azure NetApp Files の NFS ボリュームを作成する](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [サブネットを Azure NetApp Files に委任する](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

次の手順には、Azure NetApp Files の Premium サービス レベルで Azure 内に作成された事前に構成された Azure NetApp Files の検証が含まれています。

1. Azure portal の **[ストレージ]** で、 **[Azure NetApp Files]** を選択します。 構成されている Azure NetApp Files の一覧が表示されます。 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="事前に構成された Azure NetApp Files の一覧を示すスクリーンショット。"::: 

2. 構成された NetApp Files アカウントを選択して、その設定を表示します。 たとえば、 **[Contoso-anf2]** を選択します。 

3. **[容量プール]** を選択して、構成されているプールを確認します。 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="構成された NetApp Files アカウントの容量プールとボリュームを表示するオプションを示すスクリーンショット。":::

    容量とサービス レベルを示す [容量プール] ページが開きます。 この例では、記憶域プールは Premium サービス レベルの 4 TiB として構成されています。

4. **[ボリューム]** を選択して、容量プールの下に作成されているボリュームを表示します。 (前のスクリーンショットを参照)。

5. ボリュームを選択して、その構成を表示します。  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="容量プールの下に作成されているボリュームを示すスクリーンショット。":::

    ボリュームの構成の詳細を示すウィンドウが開きます。

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="ボリュームの構成の詳細を示すスクリーンショット。":::

    anfvolume は、200 GiB のサイズで、容量プール anfpool1 にあることがわかります。 10.22.3.4:/ANFVOLUME を使用して NFS ファイル共有としてエクスポートされます。 Azure NetApp Files と、VM にマウントするための NFS パスのために Azure Virtual Network (VNet) から 1 つのプライベート IP が作成されました。

    サイズまたは "クォータ" 別の Azure NetApp Files ボリュームのパフォーマンスについては、「[Azure NetApp Files のパフォーマンスに関する考慮事項](../azure-netapp-files/azure-netapp-files-performance-considerations.md)」を参照してください。 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>事前に構成された Azure VMware Solution VM の共有マッピングを検証する

Azure NetApp Files 共有に Azure VMware Solution VM からアクセスできるようにするには、SMB と NFS の共有マッピングを理解する必要があります。 SMB または NFS ボリュームは、構成した後にのみ、ここで説明する手順に従ってマウントできます。

- SMB 共有: SMB ボリュームをデプロイする前に、Active Directory 接続を作成します。 接続を成功させるには、指定されたドメイン コントローラーが Azure NetApp Files の委任されたサブネットからアクセス可能である必要があります。 Active Directory は、Azure NetApp Files アカウント内で構成されると、SMB ボリュームの作成中に選択可能な項目として表示されます。

- NFS 共有: Azure NetApp Files は、NFS またはデュアル プロトコル (NFS と SMB) を使用したボリュームの作成に寄与します。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 NFS は、コマンド ラインまたは /etc/fstab エントリを使用して Linux サーバーにマウントできます。

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files および Azure VMware Solution のユース ケース

Azure NetApp Files の魅力的なユース ケースのほんの一部を次に示します。 
- Horizon プロファイルの管理
- Citrix プロファイルの管理
- リモート デスクトップ サービス プロファイルの管理
- Azure VMware Solution 上のファイル共有

## <a name="next-steps"></a>次のステップ

Azure NetApp Files とご利用の Azure VMware Solution ワークロードの統合について理解したので、次のことについて確認する必要がある可能性があります。

- [Azure NetApp Files のリソース制限](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)。
- [Azure NetApp Files のネットワーク計画のガイドライン](../azure-netapp-files/azure-netapp-files-network-topologies.md)。
- [Azure NetApp Files ボリュームのリージョン間レプリケーション](../azure-netapp-files/cross-region-replication-introduction.md)。 
- [Azure NetApp Files についての FAQ](../azure-netapp-files/azure-netapp-files-faqs.md)。
