---
title: Azure NetApp Files の SMB ボリュームを作成する | Microsoft Docs
description: この記事では、Azure NetApp Files の SMB3 ボリュームを作成する方法について説明します。 Active Directory の接続と Domain Services に対する要件について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635491"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files の SMB ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 と NFSv4.1)、SMB3、またはデュアル プロトコル (NFSv3 and SMB) を使用したボリュームの作成がサポートされています。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 この記事では、SMB3 ボリュームを作成する方法について説明します。

## <a name="before-you-begin"></a>開始する前に 

* あらかじめ容量プールを設定しておく必要があります。 「[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。     
* サブネットが Azure NetApp Files に委任されている必要があります。 「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="configure-active-directory-connections"></a>Active Directory Domain Services 接続を構成する 

SMB ボリュームを作成する前に Active Directory Domain Services の接続を作成する必要があります。 Azure NetApp ファイルの Active Directory Domain Services 接続を構成していない場合は、「[Active Directory 接続の作成と管理](create-active-directory-connections.md)」に記載されている手順に従ってください。

## <a name="add-an-smb-volume"></a>SMB ボリュームを追加する

1. [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。  
    [ボリュームの作成] ウィンドウが表示されます。

3. [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
    * **ボリューム名**      
        作成するボリュームの名前を指定します。   

        ボリューム名は、各容量プール内で一意である必要があります。 3 文字以上になるようにしてください。 任意の英数字を使用できます。   

        `default` または `bin` をボリューム名として使用することはできません。

    * **容量プール**  
        ボリュームを作成する容量プールを指定します。

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **スループット (MiB/秒)**    
        ボリュームが手動 QoS 容量プールで作成されている場合は、ボリュームに必要なスループットを指定します。   

        ボリュームが自動 QoS 容量プールで作成されている場合は、このフィールドに表示される値は (クォータ x サービス レベルのスループット) です。   

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure 仮想ネットワーク (VNet) を指定します。  

        指定する VNet には、Azure NetApp Files に委任されているサブネットが存在する必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ VNet から、またはボリュームと同じリージョンにある VNet から VNet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[ボリュームの作成] ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、スナップショットのパスを非表示にするかどうかを指定して、プルダウン メニューでスナップショット ポリシーを選択します。 

        スナップショット ポリシーの作成については、「[スナップショット ポリシーを管理する](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)」を参照してください。

        ![詳細セクションの表示](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. **[プロトコル]** をクリックし、次の情報を入力します。  
    * ボリュームのプロトコルの種類として **[SMB]** を選択します。 
    * ドロップダウン リストから **Active Directory** の接続を選択します。
    * **[共有名]** に共有ボリュームの名前を指定します。

    ![SMB プロトコルを指定する](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。  **[作成]** をクリックして SMB ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="control-access-to-an-smb-volume"></a>SMB ボリュームへのアクセスを制御する  

SMB ボリュームへのアクセスはアクセス許可によって管理されます。  

### <a name="share-permissions"></a>共有アクセス許可  

既定では、新しいボリュームには **Everyone でフル コントロール** の共有アクセス許可が与えられます。 ドメイン管理者グループのメンバーは、Azure NetApp Files ボリュームに使用されているコンピューター アカウントで Computer Management を利用し、共有アクセス許可を変更できます。

![SMB マウント パス](../media/azure-netapp-files/smb-mount-path.png) 
![共有アクセス許可を設定する](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS ファイルおよびフォルダーの権限  

Windows SMB クライアントで、オブジェクトのプロパティの **[セキュリティ]** タブを利用し、ファイルまたはフォルダーのアクセス許可を設定できます。
 
![ファイルとフォルダーの権限を設定する](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>次のステップ  

* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [SMB に関する FAQ](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB またはデュアルプロトコル ボリュームのトラブルシューティング](troubleshoot-dual-protocol-volumes.md)
* [Azure サービスの仮想ネットワーク統合について理解する](../virtual-network/virtual-network-for-azure-services.md)
* [Azure CLI を使用して新しい Active Directory フォレストをインストールする](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
