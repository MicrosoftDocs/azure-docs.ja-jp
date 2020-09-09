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
ms.date: 8/11/2020
ms.author: b-juche
ms.openlocfilehash: f4cc253de0de9d099cfc4881f48182cf9b2a1616
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134583"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files のデュアルプロトコル (NFSv3 と SMB) ボリュームを作成する

Azure NetApp Files では、NFS (NFSv3 と NFSv4.1)、SMBv3、またはデュアル プロトコルを使用したボリュームの作成がサポートされています。 この記事では、LDAP ユーザー マッピングをサポートする、NFSv3 と SMB のデュアル プロトコルを使用するボリュームを作成する方法について説明します。  


## <a name="before-you-begin"></a>開始する前に 

* あらかじめ容量プールを設定しておく必要があります。  
    「[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)」を参照してください。   
* サブネットが Azure NetApp Files に委任されている必要があります。  
    「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」を参照してください。

## <a name="considerations"></a>考慮事項

* 「[Active Directory 接続の要件](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)」を満たしていることを確認します。 
* DNS サーバーに逆引き参照ゾーンを作成してから、その逆引き参照ゾーンに AD ホストマシンのポインター (PTR) レコードを追加します。 そうしないと、デュアルプロトコル ボリュームの作成は失敗します。
* NFS クライアントが最新であり、オペレーティング システムの最新の更新プログラムが実行されていることを確認します。

## <a name="create-a-dual-protocol-volume"></a>デュアルプロトコル ボリュームを作成する

1.  [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  [ボリュームの作成] ウィンドウで **[作成]** をクリックし、[基本] タブで次のフィールドの情報を入力します。   
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

        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet から、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 既存のスナップショット ポリシーをボリュームに適用する場合は、 **[詳細セクションの表示]** をクリックして展開し、プルダウン メニューでスナップショット ポリシーを選択します。 

        スナップショット ポリシーの作成については、「[スナップショット ポリシーを管理する](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)」を参照してください。

        ![詳細セクションの表示](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **[プロトコル]** をクリックし、次のアクションを実行します。  
    * ボリュームのプロトコルの種類として **[dual-protocol (NFSv3 and SMB)]\(デュアルプロトコル (NFSv3 と SMB)\)** を選択します。   

    * ドロップダウン リストから **Active Directory** の接続を選択します。  
    使用する Active Directory には、サーバー ルート CA 証明書が存在する必要があります。 

    * ボリュームの**ボリューム パス**を指定します。   
    このボリューム パスは、共有ボリュームの名前です。 名前は英字で始める必要があり、各サブスクリプションと各リージョン内で一意である必要があります。  

    * 使用する**セキュリティ スタイル**を指定します。NTFS (既定値) または UNIX です。

    * 必要に応じて、[ボリュームのエクスポート ポリシーを構成します](azure-netapp-files-configure-export-policy.md)。

    ![デュアルプロトコルの指定](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。 次に、 **[作成]** をクリックして、ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Active Directory 証明機関の公開ルート証明書をアップロードする  

1.  「[証明機関をインストールする](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)」に従って、ADDS 証明機関をインストールして構成します。 

2.  「[MMC スナップインを使用して証明書を参照する](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)」に従って、MMC スナップインと証明書マネージャー ツールを使用します。  
    証明書マネージャー スナップインを使用して、ローカル デバイスのルートまたは発行元の証明書を検索します。 証明書管理スナップイン コマンドは、次のいずれかの設定から実行する必要があります。  
    * ドメインに参加し、ルート証明書がインストールされている Windows ベースのクライアント 
    * ルート証明書を含むドメイン内の別のマシン  

3. ルート証明書をエクスポートします。  
    証明書が Base 64 encoded x.509 (.CER) 形式でエクスポートされていることを確認します。 

    ![証明書のエクスポート ウィザード](../media/azure-netapp-files/certificate-export-wizard.png)

4. デュアルプロトコル ボリュームの NetApp アカウントにアクセスし、 **[Active Directory 接続]** をクリックして、 **[Active Directory に参加します]** ウィンドウを使用してルート CA 証明書をアップロードします。  

    ![サーバー ルート CA 証明書](../media/azure-netapp-files/server-root-ca-certificate.png)

    証明機関名が DNS で解決できることを確認します。 この名前は、証明書の [発行元] または [発行者] フィールドです。  

    ![証明書情報](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX 属性を管理する

Active Directory ユーザーとコンピューター MMC スナップインを使用して、UID、ホーム ディレクトリ、その他の値などの POSIX 属性を管理できます。  次の例は、Active Directory の属性エディターを示しています。  

![Active Directory の属性エディター](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>NFS クライアントを構成する 

NFS クライアントを構成するには、「[Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)」の手順に従います。  

## <a name="next-steps"></a>次の手順  

* [デュアルプロトコルに関する FAQ](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md) 
