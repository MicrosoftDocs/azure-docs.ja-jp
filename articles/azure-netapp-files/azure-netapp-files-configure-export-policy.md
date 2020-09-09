---
title: NFS ボリュームのエクスポート ポリシーを構成する - Azure NetApp Files
description: Azure NetApp Files を使用して NFS ボリュームへのアクセスを制御するエクスポート ポリシーの構成方法について説明します
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533210"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>NFS ボリュームのエクスポート ポリシーを構成する

Azure NetApp Files ボリュームへのアクセスを制御するために、エクスポート ポリシーを構成できます。 Azure NetApp Files のエクスポート ポリシーでは、NFS プロトコル (NFSv3 と NFSv 4.1 の両方) とデュアル プロトコル (NFSv3 と SMB) を使用するボリュームがサポートされています。 

最大 5 つのエクスポートポリシー ルールを作成できます。

## <a name="steps"></a>手順 

1.  [ボリューム] ページで、エクスポート ポリシーを構成するボリュームを選択し、 **[エクスポート ポリシー]** をクリックします。 

    また、ボリュームの作成時にエクスポート ポリシーを構成することもできます。

2.  次のフィールドの情報を指定して、エクスポート ポリシー ルールを作成します。   
    *  **[インデックス]**    
        ルールのインデックス番号を指定します。  
        エクスポート ポリシーは、最大 5 つのルールで構成できます。 ルールは、リスト内の対応するインデックス番号の順に評価されます インデックス番号が小さいルールが先に評価されます。 たとえば、インデックス番号が 1 のルールは、インデックス番号が 2 のルールよりも前に評価されます。 

    * **[許可されたクライアント]**    
        次のいずれかの形式で値を指定します。  
        * IPv4 アドレス (例: `10.1.12.24`) 
        * ビット数として表されたサブネット マスクを指定した IPv4 アドレス (例: `10.1.12.10/4`)

    * **Access (アクセス)**  
        次のアクセス タイプのいずれかを選択します。  
        * アクセス権なし 
        * 読み取りと書き込み
        * [読み取り専用]

    * **[読み取り専用]** と **[読み取り/書き込み]**  
        NFSv4.1 で Kerberos 暗号化を使用する場合は、「[NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md)」の手順に従ってください。  Kerberos のパフォーマンスへの影響については、「[NFSv4.1 での Kerberos のパフォーマンスに対する影響](configure-kerberos-encryption.md#kerberos_performance)」を参照してください。 

        ![Kerberos のセキュリティ オプション](../media/azure-netapp-files/kerberos-security-options.png) 

    * **ルート アクセス**  
        `root` アカウントがボリュームにアクセスできるかどうかを指定します。  既定では、ルート アクセスは **[On]\(オン\)** に設定され、`root` アカウントはボリュームにアクセスできます。

![エクスポート ポリシー](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>次のステップ 
* [仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [スナップショットを管理する](azure-netapp-files-manage-snapshots.md)
