---
title: Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する | Microsoft Docs
description: Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する方法とそのパフォーマンスへの影響について説明します。
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
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: 6ff87d046c60f588e133010895ec3e7ce08cb71f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740564"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する

Azure NetApp Files では、AES-256 暗号化を使用した Kerberos モード (krb5、krb5i、および krb5p) の NFS クライアント暗号化がサポートされています。 この記事では、Kerberos 暗号化を備えた NFSv4.1 ボリュームを使用するために必要な構成について説明します。

## <a name="requirements"></a>必要条件

NFSv4.1 クライアントの暗号化には、次の要件が適用されます。 

* Kerberos チケット発行を容易にするための Active Directory Domain Services (AD DS) 接続 
* クライアントと Azure NetApp Files NFS サーバーの両方の IP アドレスに対する DNS A/PTR レコードの作成
* Linux クライアント  
    この記事では、RHEL および Ubuntu クライアントに関するガイダンスを提供します。  他のクライアントも同様の構成手順で動作します。 
* NTP サーバー アクセス  
    一般的に使用される、Active Directory ドメイン コントローラー (AD DC) のいずれかのドメイン コントローラーを使用できます。

## <a name="create-an-nfs-kerberos-volume"></a>NFS Kerberos ボリュームを作成する

1.  「[Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)」の手順に従って、NFSv4.1 ボリュームを作成します。   

    [ボリュームの作成] ページで、NFS バージョンを **[NFSv4.1]** に設定し、Kerberos を **[有効]** に設定します。

    > [!IMPORTANT] 
    > ボリュームの作成後に Kerberos の有効化の選択を変更することはできません。

    ![NFSv4.1 の Kerberos ボリュームを作成する](../media/azure-netapp-files/create-kerberos-volume.png)  

2. ボリュームに必要なレベルのアクセスおよびセキュリティ オプション (Kerberos 5、Kerberos 5i、または Kerberos 5p) に合わせて **[Export Policy]\(エクスポート ポリシー\)** を選択します。   

    Kerberos のパフォーマンスへの影響については、「[NFSv4.1 での Kerberos のパフォーマンスへの影響](#kerberos_performance)」を参照してください。  

    Azure NetApp Files ナビゲーション ウィンドウの [Export Policy]\(エクスポート ポリシー\) をクリックして、ボリュームの Kerberos セキュリティ メソッドを変更することもできます。

3.  **[確認および作成]** をクリックして、NFSv4.1 ボリュームを作成します。

## <a name="configure-the-azure-portal"></a>Azure portal を構成する 

1.  「[Active Directory 接続を作成する](create-active-directory-connections.md)」の手順に従います。  

    Kerberos では、Active Directory に少なくとも 1 つのマシン アカウントを作成する必要があります。 指定したアカウント情報は、SMB "*と*" NFSv4.1 Kerberos の両方のボリュームのアカウントを作成するために使用されます。 このマシン アカウントは、ボリュームの作成時に自動的に作成されます。

2.  **[Kerberos 領域]** で、 **[AD サーバー名]** と **[KDC IP]** アドレスを入力します。

    AD サーバーと KDC IP は同じサーバーにすることができます。 この情報は、Azure NetApp Files によって使用される SPN マシン アカウントを作成するために使用されます。 マシン アカウントが作成されると、Azure NetApp Files は DNS サーバー レコードを使用して、必要に応じて追加の KDC サーバーを検索します。 

    ![Kerberos 領域](../media/azure-netapp-files/kerberos-realm.png)
 
3.  **[参加]** をクリックして構成を保存します。

## <a name="configure-active-directory-connection"></a>Azure Active Directory 接続を構成する 

NFSv4.1 Kerberos を構成すると、Active Directory に 2 つのコンピューター アカウントが作成されます。
* SMB 共有のコンピューター アカウント
* NFSv4.1 のコンピューター アカウント。このアカウントは `NFS-` プレフィックスによって識別できます。 

最初の NFSv4.1 Kerberos ボリュームの作成後、次の PowerShell コマンドを使用して、そのコンピューター アカウントの暗号化の種類を設定します。

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>NFS クライアントを構成する 

NFS クライアントを構成するには、「[Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md)」の手順に従います。  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>NFS Kerberos ボリュームをマウントする

1. **[ボリューム]** ページで、マウントする NFS ボリュームを選択します。

2. ボリュームから **[マウントに関する指示]** を選択して、指示を表示します。

    次に例を示します。 

    ![Kerberos ボリュームのマウントに関する指示](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. 新しいボリューム用のディレクトリ (マウント ポイント) を作成します。  

4. コンピューター アカウントの既定の暗号化の種類を AES 256 に設定します。  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * このコマンドは、コンピューター アカウントごとに 1 回だけ実行する必要があります。
    * このコマンドの実行は、ドメイン コントローラーから、または [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) がインストールされている PC から行うことができます。 
    * `$NFSCOMPUTERACCOUNT` 変数は、Kerberos ボリュームをデプロイしたときに Active Directory に作成されたコンピューター アカウントです。 これは、先頭に `NFS-` が付いているアカウントです。 
    * `$ANFSERVICEACCOUNT` 変数は、コンピューター アカウントが作成された組織単位に対する管理を委任されている、特権のない Active Directory ユーザー アカウントです。 

5. ホストでボリュームをマウントします。 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * `$ANFEXPORT` 変数は、マウントに関する指示に記載されていた `host:/export` パスです。
    * `$ANFMOUNTPOINT` 変数は、Linux ホストでユーザーが作成したフォルダーです。

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>NFSv4.1 での Kerberos のパフォーマンスに対する影響 

NFSv4.1 ボリュームに使用できるセキュリティ オプション、テスト済みのパフォーマンス ベクトル、および Kerberos の予想されるパフォーマンスへの影響について理解しておく必要があります。 詳細については、[NFSv4.1 ボリュームでの Kerberos のパフォーマンスに対する影響](performance-impact-kerberos.md)に関する記事を参照してください。  

## <a name="next-steps"></a>次のステップ  

* [NFSv4.1 ボリュームでの Kerberos のパフォーマンスに対する影響](performance-impact-kerberos.md)
* [NFSv4.1 Kerberos ボリュームの問題のトラブルシューティング](troubleshoot-nfsv41-kerberos-volumes.md)
* [Azure NetApp Files についての FAQ](azure-netapp-files-faqs.md)
* [Azure NetApp Files の NFS ボリュームを作成する](azure-netapp-files-create-volumes.md)
* [Active Directory 接続を作成する](create-active-directory-connections.md)
* [Azure NetApp Files 用に NFS クライアントを構成する](configure-nfs-clients.md) 
