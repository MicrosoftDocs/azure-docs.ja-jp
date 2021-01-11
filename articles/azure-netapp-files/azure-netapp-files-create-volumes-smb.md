---
title: Azure NetApp Files の SMB ボリュームを作成する | Microsoft Docs
description: この記事では、Azure NetApp Files の SMBv3 ボリュームを作成する方法について説明します。 Active Directory の接続と Domain Services に対する要件について説明します。
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 3299865837bd14566cca54ec84b2dce452c633da
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080509"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files の SMB ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 と NFSv4.1)、SMBv3、またはデュアル プロトコル (NFSv3 and SMB) を使用したボリュームの作成がサポートされています。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 この記事では、SMBv3 ボリュームを作成する方法について説明します。

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

    |     サービス           |     Port     |     Protocol     |
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
    |    w32time            |    123       |    UDP           |

* ターゲットの Active Directory Domain Services のサイト トポロジは、特に Azure NetApp Files がデプロイされる Azure VNet ではガイドラインに従う必要があります。  

    (Azure NetApp Files によって到達可能なドメイン コントローラーが存在する) 新規または既存の Active Directory サイト に、Azure NetApp Files がデプロイされる仮想ネットワークのアドレス空間を追加する必要があります。 

* Azure NetApp Files の[委任されたサブネット](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)から、指定された DNS サーバーに到達可能である必要があります。  

    サポートされるネットワーク トポロジについては、「[Azure NetApp Files のネットワーク計画のガイドライン](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)」を参照してください。

    ネットワーク セキュリティ グループ (NSG) とファイアウォールに、Active Directory と DNS トラフィック要求を許可するように適切に構成された規則が必要です。 

* Azure NetApp Files の委任されたサブネットは、すべてのローカルおよびリモート ドメイン コントローラーを含め、ドメイン内のすべての Active Directory Domain Services (ADDS) ドメイン コントローラーに接続できる必要があります。 そうでない場合、サービスの中断が発生する可能性があります。  

    Azure NetApp Files の委任されたサブネットで到達できないドメイン コントローラーがある場合は、Active Directory 接続の作成中に Active Directory サイトを指定できます。  Azure NetApp Files は、Azure NetApp Files の委任されたサブネットのアドレス空間が存在するサイト内のドメイン コントローラーとのみ通信する必要があります。

    AD サイトとサービスに関する「[サイト トポロジの設計](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)」を参照してください。 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

その他の AD 情報については、Azure NetApp Files の「[SMB に関する FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)」を参照してください。 

## <a name="decide-which-domain-services-to-use"></a>使用するドメイン サービスを決定する 

Azure NetApp Files では、AD 接続用に [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) と Azure Active Directory Domain Services (AADDS) の両方がサポートされています。  AD 接続を作成する前に、ADDS と AADDS のどちらを使用するかを決定する必要があります。  

詳しくは、「[自己管理型の Active Directory Domain Services、Azure Active Directory、およびマネージド Azure Active Directory Domain Services の比較](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)」を参照してください。 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Azure NetApp Files では、 [[Active Directory サイトとサービス]](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) の任意のスコープを使用できます。 このオプションを使用すると、[Azure NetApp Files によってアクセス可能な](azure-netapp-files-network-topologies.md) Active Directory Domain Services (ADDS) ドメイン コントローラーに対する読み取りと書き込みが可能になります。 また、指定された [Active Directory サイトとサービス] サイトに存在しないドメイン コントローラーとサービスが通信できなくなります。 

ADDS の使用時にサイト名を確認するには、Active Directory Domain Services を担当する組織内の管理グループに問い合わせてください。 次の例は、サイト名が表示されている [Active Directory サイトとサービス] プラグインを示しています。 

![Active Directory サイトとサービス](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Azure NetApp Files の AD 接続を構成するときに、 **[AD サイト名]** フィールドのスコープにサイト名を指定します。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services (AADDS) の構成とガイダンスについては、「[Azure AD Domain Services のドキュメント](https://docs.microsoft.com/azure/active-directory-domain-services/)」を参照してください。

Azure NetApp Files では、以下の追加の考慮事項が適用されます。 

* AADDS がデプロイされている VNet またはサブネットが、Azure NetApp Files デプロイと同じ Azure リージョンにあることを確認してください。
* Azure NetApp Files がデプロイされているリージョンで別の VNet を使用する場合は、2 つの VNet 間のピアリングを作成する必要があります。
* Azure NetApp Files では、`user` と `resource forest` の種類がサポートされています。
* 同期の種類では、`All` または `Scoped` を選択できます。   
    `Scoped` を選択した場合、SMB 共有にアクセスするための正しい Azure AD グループが選択されていることを確認してください。  不明な場合は、`All` の同期の種類を使用できます。
* Enterprise または Premium SKU を使用する必要があります。 Standard SKU はサポートされていません。

Active Directory 接続を作成する場合は、次のような AADDS の詳細を確認してください。

* AADDS メニューで **プライマリ DNS**、**セカンダリ DNS**、**AD DNS ドメイン名**の情報を確認できます。  
DNS サーバーでは、Active Directory 接続を構成する際に 2 つの IP アドレスが使用されます。 
* **組織単位パス**は `OU=AADDC Computers` です。  
この設定は、 **[NetApp アカウント]** の下の **[Active Directory 接続]** で構成されます。

  ![組織単位パス](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **[ユーザー名]** 資格情報には、Azure AD グループの **Azure AD DC 管理者**のメンバーである任意のユーザーを設定できます。


## <a name="create-an-active-directory-connection"></a>Active Directory 接続を作成する

1. NetApp アカウントで **[Active Directory 接続]** をクリックし、 **[参加]** をクリックします。  

    ![Active Directory 接続](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. [Active Directory に参加します] ウィンドウで、使用するドメイン サービスに基づいて、次の情報を指定します。  

    使用するドメイン サービスに固有の情報については、「[使用するドメイン サービスを決定する](#decide-which-domain-services-to-use)」を参照してください。 

    * **プライマリ DNS**  
        これは、Active Directory ドメイン参加と SMB 認証操作に必要な DNS です。 
    * **セカンダリ DNS**   
        これは、冗長ネーム サービスを確保するためのセカンダリ DNS サーバーです。 
    * **AD DNS ドメイン名**  
        これは、参加させる Active Directory Domain Services のドメイン名です。
    * **AD サイト名**  
        これは、ドメイン コントローラーの検出が制限されるサイト名です。
    * **SMB サーバー (コンピューター アカウント) プレフィックス**  
        これは、Azure NetApp Files で新しいアカウントの作成に使用される Active Directory のコンピューター アカウントの命名規則プレフィックスです。

        たとえば、ファイル サーバーに対して組織が使用する命名規則標準が NAS-01, NAS-02..., NAS-045 であれば、プレフィックスとして「NAS」を入力します。 

        サービスによって、必要に応じて、Active Directory で追加のコンピューター アカウントが作成されます。

        > [!IMPORTANT] 
        > Active Directory 接続を作成した後に SMB サーバー プレフィックスの名前を変更すると混乱が生じます。 SMB サーバー プレフィックスの名前を変更したら、既存の SMB 共有を再マウントする必要があります。

    * **組織単位名**  
        これは、SMB サーバー コンピューター アカウントが作成される組織単位 (OU) の LDAP パスです。 つまり、OU=second level, OU=first level です。 

        Azure Active Directory Domain Services と組み合わせて Azure NetApp Files を使用している場合、NetApp アカウント用に Active Directory を構成する際の組織単位のパスは `OU=AADDC Computers` になります。

     * **バックアップ ポリシー ユーザー**  
        Azure NetApp Files で使用するために作成されたコンピューター アカウントに対して昇格された特権を必要とする追加のアカウントを含めることができます。 指定したアカウントは、ファイルまたはフォルダー レベルで NTFS アクセス許可を変更できます。 たとえば、Azure NetApp Files の SMB ファイル共有にデータを移行するために使用される非特権サービス アカウントを指定できます。  

        **バックアップ ポリシー ユーザー**機能は、現在プレビューの段階です。 この機能を初めて使用する場合は、使用する前に機能を登録してください。 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        機能の登録の状態を確認します。 

        > [!NOTE]
        > **RegistrationState** が `Registering` 状態から `Registered` に変化するまでに最大 60 分間かかる場合があります。 この状態が **Registered** になってから続行してください。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

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

3. [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
    * **ボリューム名**      
        作成するボリュームの名前を指定します。   

        ボリューム名は、各容量プール内で一意である必要があります。 3 文字以上になるようにしてください。 任意の英数字を使用できます。   

        ボリューム名として `default` を使用することはできません。

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
        
        サブネットを委任していない場合は、[ボリュームの作成] ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、プルダウン メニューでスナップショット ポリシーを選択します。 

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

既定では、新しいボリュームには **Everyone でフル コントロール**の共有アクセス許可が与えられます。 ドメイン管理者グループのメンバーは、Azure NetApp Files ボリュームに使用されているコンピューター アカウントで Computer Management を利用し、共有アクセス許可を変更できます。

![SMB マウント パス](../media/azure-netapp-files/smb-mount-path.png) 
![共有アクセス許可を設定する](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS ファイルおよびフォルダーの権限  

Windows SMB クライアントで、オブジェクトのプロパティの **[セキュリティ]** タブを利用し、ファイルまたはフォルダーのアクセス許可を設定できます。
 
![ファイルとフォルダーの権限を設定する](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>次のステップ  

* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [SMB に関する FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure サービスの仮想ネットワーク統合について理解する](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure CLI を使用して新しい Active Directory フォレストをインストールする](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
