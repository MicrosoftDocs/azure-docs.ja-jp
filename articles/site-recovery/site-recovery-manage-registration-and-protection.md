---
title: "サーバーの削除と保護の無効化 | Microsoft Docs"
description: "この記事では、Site Recovery コンテナーからサーバーの登録を解除して、仮想マシンと物理サーバーの保護を無効にする方法を説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865
ms.lasthandoff: 01/05/2017


---

# <a name="remove-servers-and-disable-protection"></a>サーバーの削除と保護の無効化

Azure Site Recovery は、ビジネス継続性と障害復旧 (BCDR) の戦略に貢献するサービスです。 このサービスは、仮想マシンと物理サーバーのレプリケーション、フェールオーバー、および復旧を調整します。 コンピューターを Azure に、またはオンプレミスのセカンダリ データ センターにレプリケートできます。 簡単な概要については、「 [Site Recovery とは](site-recovery-overview.md)

この記事では、Azure Portal で Recovery Services コンテナーからサーバーの登録を解除する方法と、Site Recovery によって保護される仮想マシンの保護を無効にする方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

## <a name="unregister-a-connected-configuration-server"></a>接続されている構成サーバーの登録解除

VMware VM または Windows/Linux 物理サーバーを Azure にレプリケートする場合、接続されている構成サーバーを次の方法でコンテナーから登録解除できます。

1. マシンの保護を無効にします。 **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[削除]** をクリックします。
2. ポリシーの関連付けをすべて解除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For VMWare & Physical Machines (VMWare と物理マシン)]**  >  **[レプリケーション ポリシー]** で、関連付けられているポリシーをダブルクリックします。 構成サーバーを右クリックして、**[関連付け解除]** をクリックします。
3. 他にオンプレミスのプロセスやマスター ターゲット サーバーがあれば、それも削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For VMWare & Physical Machines (VMWare と物理マシン)]**  >  **[構成サーバー]** で、サーバーを右クリックして **[削除]** をクリックします。
4. 構成サーバーを削除します。
5. マスター ターゲット サーバー (個別のサーバー、または構成サーバーで実行されているサーバー) で実行されているモビリティ サービスを手動でアンインストールします。
6. 他にプロセス サーバーがあれば、それもアンインストールします。
7. 構成サーバーをアンインストールします。
8. 構成サーバーで、Site Recovery によってインストールされた MySQL インスタンスをアンインストールします。
9. 構成サーバーのレジストリで、キー ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery`` を削除します。

## <a name="unregister-a-unconnected-configuration-server"></a>接続されていない構成サーバーの登録解除

VMware VM または Windows/Linux 物理サーバーを Azure にレプリケートする場合、接続されていない構成サーバーを次の方法でコンテナーから登録解除できます。

1. マシンの保護を無効にします。 **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[削除]** をクリックします。 **[コンピューターの管理を停止する]** を選択します。
2. 他にオンプレミスのプロセスやマスター ターゲット サーバーがあれば、それも削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For VMWare & Physical Machines (VMWare と物理マシン)]**  >  **[構成サーバー]** で、サーバーを右クリックして **[削除]** をクリックします。
3. 構成サーバーを削除します。
4. マスター ターゲット サーバー (個別のサーバー、または構成サーバーで実行されているサーバー) で実行されているモビリティ サービスを手動でアンインストールします。
5. 他にプロセス サーバーがあれば、それもアンインストールします。
6. 構成サーバーをアンインストールします。
7. 構成サーバーで、Site Recovery によってインストールされた MySQL インスタンスをアンインストールします。
8. 構成サーバーのレジストリで、キー ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery`` を削除します。

## <a name="unregister-a-connected-vmm-server"></a>接続されている VMM サーバーの登録解除

ベスト プラクティスとして、VMM サーバーの登録解除は Azure に接続されているときに行うことをお勧めします。 そうすることで、VMM サーバー (およびペア クラウドを持つその他の VMM サーバー) の設定が正しくクリーンアップされます。 接続に永続的な問題がある場合は、接続されていないサーバーのみを削除することをお勧めします。 VMM サーバーが接続されていない場合は、設定をクリーンアップするスクリプトを手動で実行する必要があります。

1. 削除する VMM サーバー上のクラウドで仮想マシンのレプリケートを停止します。
2. 削除する VMM サーバー上のクラウドで使用されているすべてのネットワーク マッピングを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[ネットワーク マッピング]** で、ネットワーク マッピングを右クリックして **[削除]** をクリックします。
3. 削除する VMM サーバー上のクラウドからレプリケーション ポリシーの関連付けを解除します。  **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >   **[レプリケーション ポリシー]** で、関連付けられているポリシーをダブルクリックします。 クラウドを右クリックして、**[関連付け解除]** をクリックします。
4. VMM サーバーまたはアクティブな VMM ノードを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[VMM サーバー]** で、サーバーを右クリックして **[削除]** をクリックします。
5. VMM サーバー上のプロバイダーを手動でアンインストールします。 クラスターがある場合は、すべてのノードから削除します。
6. Azure にレプリケートしている場合は、削除したクラウドの Hyper-V ホストから Microsoft Recovery Services エージェントを手動で削除します。



### <a name="unregister-an-unconnected-vmm-server"></a>接続されていない VMM サーバーの登録解除

1. 削除する VMM サーバー上のクラウドで仮想マシンのレプリケートを停止します。
2. 削除する VMM サーバー上のクラウドで使用されているすべてのネットワーク マッピングを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[ネットワーク マッピング]** で、ネットワーク マッピングを右クリックして **[削除]** をクリックします。
3. VMM サーバーの ID を書き留めます。
4. 削除する VMM サーバー上のクラウドからレプリケーション ポリシーの関連付けを解除します。  **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >   **[レプリケーション ポリシー]** で、関連付けられているポリシーをダブルクリックします。 クラウドを右クリックして、**[関連付け解除]** をクリックします。
5. VMM サーバーまたはアクティブなノードを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[VMM サーバー]** で、サーバーを右クリックして **[削除]** をクリックします。
6. VMM サーバーで、[クリーンアップ スクリプト](http://aka.ms/asr-cleanup-script-vmm)をダウンロードして実行します。 **[管理者として実行]** オプションを使用して PowerShell を開き、既定 (LocalMachine) のスコープの実行ポリシーを変更します。 スクリプトで、削除する VMM サーバーの ID を指定します。 このスクリプトにより、登録とクラウドのペアリングの情報がサーバーから削除されます。
5. 削除する VMM サーバー上のクラウドとペアリングされているクラウドを含む VMM サーバーがあれば、そのサーバーでクリーンアップ スクリプトを実行します。
6. プロバイダーがインストールされたパッシブ VMM クラスター ノードがあれば、そのノードでクリーンアップ スクリプトを実行します。
7. VMM サーバー上のプロバイダーを手動でアンインストールします。 クラスターがある場合は、すべてのノードから削除します。
8. Azure にレプリケートしている場合は、削除したクラウドの Hyper-V ホストから Microsoft Recovery Services エージェントを削除できます。

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V サイト上の Hyper-V ホストの登録解除

Hyper-V サイトには、VMM で管理されていない Hyper-V ホストが収集されます。 次の方法で Hyper-V サイト上のホストを削除します。

1. ホストに配置されている Hyper-V VM のレプリケーションを無効にします。
2. Hyper-V サイトのポリシーの関連付けを解除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For Hyper-V Sites (Hyper-V サイト)]**  >   **[レプリケーション ポリシー]** で、関連付けられているポリシーをダブルクリックします。 サイトを右クリックして、**[関連付け解除]** をクリックします。
3. Hyper-V ホストを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[Hyper-V ホスト]** で、ホストを右クリックして **[削除]** をクリックします。
4. すべてのホストを削除したら、Hyper-V サイトを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[Hyper-V サイト]** で、サイトを右クリックして **[削除]** をクリックします。
5. 削除する各 Hyper-V ホストで、次のスクリプトを実行します。 このスクリプトは、サーバー上の設定をクリーンアップし、コンテナーからサーバーの登録を解除します。


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>VMware VM または物理サーバーの保護の無効化

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[削除]** をクリックします。
2. **[コンピューターの削除]** で、次のいずれかのオプションを選択します。
    - **[コンピューターの保護を無効にする (推奨)]** -  このオプションを使用すると、マシンのレプリケートが停止します。 Site Recovery の設定は自動的にクリーンアップされます。 このオプションは、次の状況でのみ表示されます。
        - **VM のボリュームのサイズを変更している** - ボリュームのサイズを変更すると、仮想マシンが重大な状態になります。 このオプションを選択すると、Azure での復旧ポイントを維持したまま、保護が無効になります。 マシンの保護を再度有効にすると、サイズが変更されたボリュームのデータが Azure に転送されます。
        - **最近フェールオーバーを実行した** - フェールオーバーを実行して環境をテストした後は、このオプションを選択してオンプレミスのマシンの保護を再開します。 このオプションにより各仮想マシンが無効になり、各マシンの保護を再度有効にしなければならなくなります。 この設定を使用してマシンを無効にしても、Azure のレプリカ仮想マシンには影響しません。 マシンからモビリティ サービスをアンインストールしないでください。
    - **[コンピューターの管理を停止する]** -  このオプションを選択した場合、マシンはコンテナーからのみ削除されます。 マシンのオンプレミスの保護設定には影響しません。 マシンの設定を削除したり、Azure のサブスクリプションからマシンを削除したりするには、モビリティ サービスをアンインストールして、設定をクリーンアップする必要があります。

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>VMM クラウドの Hyper-V VM の保護の無効化

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[削除]** をクリックします。
2. **[コンピューターの削除]** で、次のいずれかのオプションを選択します。

    - **[コンピューターの保護を無効にする (推奨)]** -  このオプションを使用すると、マシンのレプリケートが停止します。 Site Recovery の設定は自動的にクリーンアップされます。
    - **[コンピューターの管理を停止する]** -  このオプションを選択した場合、マシンはコンテナーからのみ削除されます。 マシンのオンプレミスの保護設定には影響しません。 マシンの設定を削除したり、Azure のサブスクリプションからマシンを削除したりするには、以下の手順を使用して、設定を手動でクリーンアップする必要があります。 仮想マシンとそのハード ディスクを削除することを選択した場合、それらはターゲットの場所から削除されます。

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>保護設定のクリーンアップ - セカンダリ VMM サイトへのレプリケーション

**[コンピューターの管理を停止する]** を選択し、セカンダリ サイトにレプリケートしている場合は、プライマリ サーバーでこのスクリプトを実行して、プライマリ仮想マシンの設定をクリーンアップします。 VMM コンソールで、[PowerShell] ボタンをクリックし、VMM PowerShell コンソールを開きます。 SQLVM1 を仮想マシンの名前に置き換えます。

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. 次のように、セカンダリ VMM サーバーでこのスクリプトを実行して、セカンダリ仮想マシンの設定をクリーンアップします。

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. セカンダリ VMM サーバーで、Hyper-V ホスト サーバー上の仮想マシンを更新し、セカンダリ VM が VMM コンソールで再検出されるようにします。
4. 上記の手順によって、VMM サーバーのレプリケーション設定がクリアされます。 仮想マシンのレプリケーションを停止する場合は、プライマリとセカンダリの VM で次のスクリプトを実行します。 SQLVM1 を仮想マシンの名前に置き換えます。

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>保護設定のクリーンアップ - Azure へのレプリケーション

1. **[コンピューターの管理を停止する]** を選択し、Azure にレプリケートしている場合は、VMM コンソールから PowerShell を使用して、ソース VMM サーバーでこのスクリプトを実行します。
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. 上記の手順によって、VMM サーバーのレプリケーション設定がクリアされます。 Hyper-V ホスト サーバーで実行されている仮想マシンのレプリケーションを停止するには、このスクリプトを実行します。 SQLVM1 を仮想マシンの名前に、host01.contoso.com を Hyper-V ホスト サーバーの名前に置き換えます。

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Hyper-V サイトの Hyper-V VM の保護の無効化

VMM サーバーを使用せずに Hyper-V VM を Azure にレプリケートしている場合は、この手順を使用します。

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[削除]** をクリックします。
2. **[コンピューターの削除]** で、次のオプションを選択できます。

   - **[コンピューターの保護を無効にする (推奨)]** -  このオプションを使用すると、マシンのレプリケートが停止します。 Site Recovery の設定は自動的にクリーンアップされます。
   - **[コンピューターの管理を停止する]** -  このオプションを選択した場合、マシンはコンテナーからのみ削除されます。 マシンのオンプレミスの保護設定には影響しません。 マシンの設定を削除したり、Azure のサブスクリプションから仮想マシンを削除したりするには、設定を手動でクリーンアップする必要があります。 仮想マシンとそのハード ディスクを削除することを選択した場合は、それらはターゲットの場所から削除されます。
3. **[コンピューターの管理を停止する]** を選択した場合は、ソース Hyper-V ホスト サーバーでこのスクリプトを実行して、仮想マシンのレプリケーションを削除します。 SQLVM1 を仮想マシンの名前に置き換えます。

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

