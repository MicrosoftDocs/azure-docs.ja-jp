---
title: Azure NetApp Files の SMB ボリュームを作成する | Microsoft Docs
description: Azure NetApp Files の SMB ボリュームを作成する方法について説明します。
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
ms.topic: conceptual
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 3cd60f390f0233e2923660fc39675b5a307d8d8f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515419"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files の SMB ボリュームを作成する

Azure NetApp Files は NFS ボリュームと SMBv3 ボリュームをサポートしています。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 この記事では、SMBv3 ボリュームを作成する方法について説明します。 NFS ボリュームを作成する場合は、[Azure NetApp Files の NFS ボリュームの作成](azure-netapp-files-create-volumes.md)に関するページを参照してください。 

## <a name="before-you-begin"></a>開始する前に 
あらかじめ容量プールを設定しておく必要があります。   
[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)   
サブネットが Azure NetApp Files に委任されている必要があります。  
[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 接続の要件

 SMB ボリュームを作成する前に Active Directory の接続を作成する必要があります。 Active Directory 接続の要件は次のとおりです。 

* 使用する管理者アカウントは、指定する組織単位 (OU) パスにマシン アカウントを作成できる必要があります。  

* 適切なポートは、該当する Windows Active Directory (AD) のサーバーで開く必要があります。  
    必要なポートは次のとおりです。 

    |     Service           |     Port     |     Protocol     |
    |-----------------------|--------------|------------------|
    |    AD Web サービス    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    該当なし       |    エコー応答    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 名       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    セキュリティで保護された LDAP        |    636       |    TCP           |
    |    セキュリティで保護された LDAP        |    3269      |    TCP           |
    |    w32time            |    123       |    UDP           |

* ターゲットの Active Directory Domain Services のサイト トポロジについて、特に Azure NetApp Files がデプロイされる Azure VNet ではベスト プラクティスに従う必要があります。  

    (Azure NetApp Files によって到達可能なドメイン コントローラーが存在する) 新規または既存の Active Directory サイト に、Azure NetApp Files がデプロイされる仮想ネットワークのアドレス空間を追加する必要があります。 

* Azure NetApp Files の[委任されたサブネット](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)から、指定された DNS サーバーに到達可能である必要があります。  

    サポートされるネットワーク トポロジについては、「[Azure NetApp Files のネットワーク計画のガイドライン](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)」を参照してください。

    ネットワーク セキュリティ グループ (NSG) とファイアウォールに、Active Directory と DNS トラフィック要求を許可するように適切に構成された規則が必要です。

    AD サイトとサービスに関する「[サイト トポロジの設計](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)」を参照してください。 

## <a name="create-an-active-directory-connection"></a>Active Directory 接続を作成する

1. NetApp アカウントで **[Active Directory 接続]** をクリックし、 **[参加]** をクリックします。  

    ![Active Directory 接続](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. [Active Directory に参加します] ウィンドウで、次の情報を指定します。

    * **プライマリ DNS**  
        これは、Active Directory ドメイン参加と SMB 認証操作に必要な DNS です。 
    * **セカンダリ DNS**   
        これは、冗長ネーム サービスを確保するためのセカンダリ DNS サーバーです。 
    * **ドメイン**  
        これは、参加させる Active Directory Domain Services のドメイン名です。
    * **SMB サーバー (コンピューター アカウント) プレフィックス**  
        これは、Azure NetApp Files で新しいアカウントの作成に使用される Active Directory のコンピューター アカウントの命名規則プレフィックスです。

        たとえば、ファイル サーバーに対して組織が使用する命名規則標準が NAS-01, NAS-02..., NAS-045 であれば、プレフィックスとして「NAS」を入力します。 

        サービスによって、必要に応じて、Active Directory で追加のコンピューター アカウントが作成されます。

    * **組織単位名**  
        これは、SMB サーバー コンピューター アカウントが作成される組織単位 (OU) の LDAP パスです。 つまり、OU=second level, OU=first level です。 
    * **ユーザー名**や**パスワード**などの資格情報

    ![Active Directory に参加する](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **[結合]** をクリックします。  

    作成した Active Directory の接続が表示されます。

    ![Active Directory 接続](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>SMB ボリュームを追加する

1. [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。  
    [ボリュームの作成] ウィンドウが表示されます。

3. [ボリュームの作成] ウィンドウの **[作成]** をクリックし、次のフィールドの情報を指定します。   
    * **ボリューム名**      
        作成するボリュームの名前を指定します。   

        ボリューム名は、各容量プール内で一意である必要があります。 3 文字以上になるようにしてください。 任意の英数字を使用できます。

    * **容量プール**  
        ボリュームを作成する容量プールを指定します。

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure 仮想ネットワーク (VNet) を指定します。  

        指定する VNet には、Azure NetApp Files に委任されているサブネットが存在する必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ VNet から、またはボリュームと同じリージョンにある VNet から VNet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **[プロトコル]** をクリックし、次の情報を入力します。  
    * ボリュームのプロトコルの種類として **[SMB]** を選択します。 
    * ドロップダウン リストから **Active Directory** の接続を選択します。
    * **[共有名]** に共有ボリュームの名前を指定します。

    ![SMB プロトコルを指定する](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。  **[作成]** をクリックして SMB ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="next-steps"></a>次の手順  

* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [SMB に関する FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure サービスの仮想ネットワーク統合について理解する](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure CLI を使用して新しい Active Directory フォレストをインストールする](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
