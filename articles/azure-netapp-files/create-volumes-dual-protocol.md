---
title: Azure NetApp Files のデュアルプロトコル (NFSv3 と SMB) ボリュームを作成する | Microsoft Docs
description: LDAP ユーザー マッピングをサポートする、NFSv3 と SMB のデュアル プロトコルを使用するボリュームを作成する方法について説明します。
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
ms.date: 01/28/2020
ms.author: b-juche
ms.openlocfilehash: 0079c123f908a38cc1e4923790439f18352bf3ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574642"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files のデュアルプロトコル (NFSv3 と SMB) ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 と NFSv4.1)、SMB3、またはデュアル プロトコルを使用したボリュームの作成がサポートされています。 この記事では、LDAP ユーザー マッピングをサポートする、NFSv3 と SMB のデュアル プロトコルを使用するボリュームを作成する方法について説明します。  


## <a name="before-you-begin"></a>開始する前に 

* あらかじめ容量プールを作成しておく必要があります。  
    「[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。   
* サブネットが Azure NetApp Files に委任されている必要があります。  
    「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="considerations"></a>考慮事項

* 「[Active Directory 接続の要件](create-active-directory-connections.md#requirements-for-active-directory-connections)」を満たしていることを確認します。 
* DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンのポインター (PTR) レコードを追加します。 そうしないと、デュアルプロトコル ボリュームの作成は失敗します。
* NFS クライアントが最新であり、オペレーティング システムの最新の更新プログラムが実行されていることを確認します。
* AD で Active Directory (AD) LDAP サーバーが稼働していることを確認します。 そのためには、AD マシンで [Active Directory ライトウェイト ディレクトリ サービス (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) ロールをインストールして構成します。
* デュアル プロトコル ボリュームでは、現在 Azure Active Directory Domain Services (AADDS) はサポートされていません。  
* デュアル プロトコル ボリュームで使用される NFS のバージョンは、NFSv3 です。 そのため、次の考慮事項が適用されます。
    * デュアル プロトコルは、NFS クライアントからの Windows ACL 拡張属性 `set/get` をサポートしていません。
    * NFS クライアントは、NTFS セキュリティ スタイルのアクセス許可を変更することはできません。また、Windows クライアントは、UNIX 形式のデュアル プロトコル ボリュームのアクセス許可を変更することはできません。   

    次の表は、セキュリティ スタイルとその効果について説明しています。  
    
    | セキュリティ スタイル    | アクセス許可を変更できるクライアント   | クライアントが使用できるアクセス許可  | 結果の有効なセキュリティ スタイル    | ファイルにアクセスできるクライアント     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 モード ビット   | UNIX  | NFS と Windows   |
    | `Ntfs`    | Windows   | NTFS ACL     | NTFS  |NFS と Windows|
* NFS を使用して NTFS セキュリティ スタイル ボリュームをマウントする UNIX ユーザーは、UNIX `root` の場合は Windows ユーザー `root`、その他のすべてのユーザーの場合は `pcuser` として認証されます。 NFS の使用中、ボリュームをマウントする前に、これらのユーザー アカウントが Active Directory に存在していることを確認してください。 
* 大規模なトポロジがあり、デュアル プロトコル ボリュームを使用した `Unix` セキュリティ スタイル、または拡張グループを使用した LDAP を使用する場合、Azure NetApp Files がトポロジ内のすべてのサーバーにはアクセスできなくなる可能性があります。  この状況が発生した場合は、アカウント チームに問い合わせて支援を受けてください。  <!-- NFSAAS-15123 --> 
* デュアルプロトコル ボリュームを作成するために、サーバー ルート CA 証明書は必要ありません。 これは、LDAP over TLS が有効になっている場合にのみ必要です。


## <a name="create-a-dual-protocol-volume"></a>デュアルプロトコル ボリュームを作成する

1.  [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
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

        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet から、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、スナップショットのパスを非表示にするかどうかを指定して、プルダウン メニューでスナップショット ポリシーを選択します。 

        スナップショット ポリシーの作成については、「[スナップショット ポリシーを管理する](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)」を参照してください。

        ![詳細セクションの表示](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **[プロトコル]** をクリックし、次のアクションを実行します。  
    * ボリュームのプロトコルの種類として **[dual-protocol (NFSv3 and SMB)]\(デュアルプロトコル (NFSv3 と SMB)\)** を選択します。   

    * ボリュームの **ボリューム パス** を指定します。   
    このボリューム パスは、共有ボリュームの名前です。 名前は英字で始める必要があり、各サブスクリプションと各リージョン内で一意である必要があります。  

    * 使用する **セキュリティ スタイル** を指定します。NTFS (既定値) または UNIX です。

    * 必要に応じて、[ボリュームのエクスポート ポリシーを構成します](azure-netapp-files-configure-export-policy.md)。

    ![デュアルプロトコルの指定](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。 次に、 **[作成]** をクリックして、ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX 属性を管理する

Active Directory ユーザーとコンピューター MMC スナップインを使用して、UID、ホーム ディレクトリ、その他の値などの POSIX 属性を管理できます。  次の例は、Active Directory の属性エディターを示しています。  

![Active Directory の属性エディター](../media/azure-netapp-files/active-directory-attribute-editor.png) 

LDAP ユーザーおよび LDAP グループには、次の属性を設定する必要があります。 
* LDAP ユーザーに必要な属性:   
    `uid`: Alice、`uidNumber`: 139、`gidNumber`: 555、`objectClass`: posixAccount
* LDAP グループに必要な属性:   
    `objectClass`: "posixGroup"、`gidNumber`: 555

## <a name="configure-the-nfs-client"></a>NFS クライアントを構成する 

NFS クライアントを構成するには、「[Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)」の手順に従います。  

## <a name="next-steps"></a>次の手順  

* [Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)
* [SMB またはデュアルプロトコル ボリュームのトラブルシューティング](troubleshoot-dual-protocol-volumes.md)
