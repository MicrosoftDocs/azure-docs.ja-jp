---
title: サーバーの削除と保護の無効化 | Microsoft Docs
description: この記事では、Site Recovery コンテナーからサーバーの登録を解除して、仮想マシンと物理サーバーの保護を無効にする方法を説明します。
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875795"
---
# <a name="remove-servers-and-disable-protection"></a>サーバーの削除と保護の無効化

この記事では、Recovery Services コンテナーからサーバーの登録を解除する方法と、Site Recovery によって保護される仮想マシンの保護を無効にする方法について説明します。


## <a name="unregister-a--configuration-server"></a>構成サーバーの登録解除

VMware VM または Windows/Linux 物理サーバーを Azure にレプリケートする場合、接続されていない構成サーバーを次の方法でコンテナーから登録解除できます。

1. [仮想マシンの保護を無効にします](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)。
2. レプリケーション ポリシーを[関連付け解除または削除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)します。
3. [構成サーバーを削除します](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)。

## <a name="unregister-a-vmm-server"></a>VMM サーバーの登録解除

1. 削除する VMM サーバー上のクラウドで仮想マシンのレプリケートを停止します。
2. 削除する VMM サーバー上のクラウドで使用されているすべてのネットワーク マッピングを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[ネットワーク マッピング]** で、ネットワーク マッピングを右クリックして **[削除]** をクリックします。
3. VMM サーバーの ID を書き留めます。
4. 削除する VMM サーバー上のクラウドからレプリケーション ポリシーの関連付けを解除します。  **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >   **[レプリケーション ポリシー]** で、関連付けられているポリシーをダブルクリックします。 クラウドを右クリックして、 **[関連付け解除]** をクリックします。
5. VMM サーバーまたはアクティブなノードを削除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For System Center VMM (System Center VMM)]**  >  **[VMM サーバー]** で、サーバーを右クリックして **[削除]** をクリックします。
6. VMM サーバーが切断状態になっていた場合は、VMM サーバーで[クリーンアップ スクリプト](https://aka.ms/asr-cleanup-script-vmm)をダウンロードして実行します。 **[管理者として実行]** オプションを使用して PowerShell を開き、既定 (LocalMachine) のスコープの実行ポリシーを変更します。 スクリプトで、削除する VMM サーバーの ID を指定します。 このスクリプトにより、登録とクラウドのペアリングの情報がサーバーから削除されます。
5. 任意のセカンダリ VMM サーバーでクリーンアップ スクリプトを実行します。
6. プロバイダーがインストールされたパッシブ VMM クラスター ノードがあれば、そのノードでクリーンアップ スクリプトを実行します。
7. VMM サーバー上のプロバイダーを手動でアンインストールします。 クラスターがある場合は、すべてのノードから削除します。
8. 仮想マシンが Azure にレプリケートしていた場合は、削除したクラウドの Hyper-V ホストから Microsoft Recovery Services エージェントをアンインストールする必要があります。

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V サイト上の Hyper-V ホストの登録解除

Hyper-V サイトには、VMM で管理されていない Hyper-V ホストが収集されます。 次の方法で Hyper-V サイト上のホストを削除します。

1. ホストに配置されている Hyper-V VM のレプリケーションを無効にします。
2. Hyper-V サイトのポリシーの関連付けを解除します。 **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]**  >  **[For Hyper-V Sites (Hyper-V サイト)]**  >   **[レプリケーション ポリシー]** で、関連付けられているポリシーをダブルクリックします。 サイトを右クリックして、 **[関連付け解除]** をクリックします。
3. Hyper-V ホストを削除します。 **[Site Recovery Infrastructure]\(Site Recouvery インフラストラクチャ\)**  >  **[For Hyper-V Sites]\(Hyper-V サイト\)**  >  **[Hyper-V Hosts]\(Hyper-V ホスト\)** で、サーバーを右クリックして **[削除]** をクリックします。
4. すべてのホストを削除したら、Hyper-V サイトを削除します。 **[Site Recovery Infrastructure]\(Site Recouvery インフラストラクチャ\)**  >  **[For Hyper-V Sites]\(Hyper-V サイト\)**  >  **[Hyper-サイト]** で、サイトを右クリックして **[削除]** をクリックします。
5. Hyper-V ホストが **[切断]** 状態だった場合は、削除した各 Hyper-V ホストで次のスクリプトを実行します。 このスクリプトは、サーバー上の設定をクリーンアップし、コンテナーからサーバーの登録を解除します。


```powershell
        pushd .
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
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


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
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
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
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>VMware VM または物理サーバーの保護の無効化 (VMware から Azure)

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
2. **[レプリケーションの無効化]** ページで、次のいずれかのオプションを選択します。
    - **[レプリケーションを無効にして削除 (推奨)]** - このオプションは、レプリケートされたアイテムを Azure Site Recovery から削除し、マシンのレプリケーションを停止します。 構成サーバー上のレプリケーション構成がクリーンアップされ、この保護されたサーバーに対する Site Recovery の請求が停止されます。 このオプションは、構成サーバーが接続状態にある場合にしか使用できないことに注意してください。
    - **[削除]** - このオプションは、ソース環境が削除されたか、アクセスできない (接続されていない) 場合にのみ使用します。 これを使用すると、レプリケートされたアイテムが Azure Site Recovery から削除されます (請求は停止されます)。 構成サーバーでは、レプリケーションの構成はクリーンアップ**されません**。 

> [!NOTE]
> どちらのオプションでもモビリティ サービスは保護されたサービスからアンインストールされないため、手動でアンインストールする必要があります。 同じ構成サーバーを使用して再度サーバーを保護する場合は、モビリティ サーバーのアンインストールを省略できます。

> [!NOTE]
> VM を既にフェールオーバーしていて、それが Azure で実行されている場合は、保護を無効にしても、フェールオーバーされた VM は削除されず、影響を受けることもない点に注意してください。
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Azure VM の保護の無効化 (Azure から Azure)

-  **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
> [!NOTE]
> モビリティ サービスは保護されたサービスからアンインストールされないため、手動でアンインストールする必要があります。 再度サーバーを保護する場合は、モビリティ サービスのアンインストールを省略できます。

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Hyper-V 仮想マシンの保護の無効化 (Hyper-V から Azure)

> [!NOTE]
> VMM サーバーを使用せずに Hyper-V VM を Azure にレプリケートしている場合は、この手順を使用します。 **System Center VMM から Azure へ**のシナリオを使用している場合は、「System Center VMM から Azure へのシナリオを使用してレプリケートしている Hyper-V 仮想マシンの保護の無効化」の手順に従ってください

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
2. **[レプリケーションの無効化]** では、次のオプションを選択できます。
   - **[レプリケーションを無効にして削除 (推奨)]** - このオプションでは、レプリケートされたアイテムを Azure Site Recovery から削除し、マシンのレプリケーションを停止します。 オンプレミスの仮想マシン上のレプリケーション構成がクリーンアップされ、この保護されたサーバーに対する Site Recovery の請求が停止されます。
   - **[削除]** - このオプションは、ソース環境が削除されたか、アクセスできない (接続されていない) 場合にのみ使用します。 これを使用すると、レプリケートされたアイテムが Azure Site Recovery から削除されます (請求は停止されます)。 オンプレミスの仮想マシン上のレプリケーション構成はクリーンアップ**されません**。 

 > [!NOTE]
     > **[削除]** オプションを選択した場合、オンプレミスの Hyper-V Server 上のレプリケーション設定をクリーンアップするには、次のスクリプトのセットを実行します。

> [!NOTE]
> VM を既にフェールオーバーしていて、それが Azure で実行されている場合は、保護を無効にしても、フェールオーバーされた VM は削除されず、影響を受けることもない点に注意してください。

1. ソース Hyper-V ホスト サーバーで、仮想マシンのレプリケーションを削除します。 管理 PowerShell から次のスクリプトを実行します。SQLVM1 は仮想マシンの名前に置き換えます。

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>System Center VMM から Azure へのシナリオを使用して Azure にレプリケートしている Hyper-V 仮想マシンの保護の無効化

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
2. **[レプリケーションの無効化]** で、次のいずれかのオプションを選択します。

   - **[レプリケーションを無効にして削除 (推奨)]** - このオプションでは、レプリケートされたアイテムを Azure Site Recovery から削除し、マシンのレプリケーションを停止します。 オンプレミスの仮想マシン上のレプリケーション構成がクリーンアップされ、この保護されたサーバーに対する Site Recovery の請求が停止されます。
   - **[削除]** - このオプションは、ソース環境が削除されたか、アクセスできない (接続されていない) 場合にのみ使用します。 これを使用すると、レプリケートされたアイテムが Azure Site Recovery から削除されます (請求は停止されます)。 オンプレミスの仮想マシン上のレプリケーション構成はクリーンアップ**されません**。 

     > [!NOTE]
     > **[削除]** オプションを選択した場合、オンプレミスの Hyper-V Server 上のレプリケーション設定をクリーンアップするには、次のスクリプトを実行します。
3. VMM コンソールから PowerShell を使用して、ソース VMM サーバーで次のスクリプトを実行します (管理者特権が必要です)。 プレースホルダー **SQLVM1** を仮想マシンの名前に置き換えます。

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. 上記の手順によって、VMM サーバーのレプリケーション設定がクリアされます。 Hyper-V ホスト サーバーで実行されている仮想マシンのレプリケーションを停止するには、このスクリプトを実行します。 SQLVM1 を仮想マシンの名前に、host01.contoso.com を Hyper-V ホスト サーバーの名前に置き換えます。

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>System Center VMM から VMM へのシナリオを使用してセカンダリ VMM サーバーにレプリケートしている Hyper-V 仮想マシンの保護の無効化

1. **[保護されているアイテム]**  >  **[レプリケートされたアイテム]** で、マシンを右クリックして **[レプリケーションの無効化]** をクリックします。
2. **[レプリケーションの無効化]** で、次のいずれかのオプションを選択します。

   - **[レプリケーションを無効にして削除 (推奨)]** - このオプションでは、レプリケートされたアイテムを Azure Site Recovery から削除し、マシンのレプリケーションを停止します。 オンプレミスの仮想マシン上のレプリケーション構成がクリーンアップされ、この保護されたサーバーに対する Site Recovery の請求が停止されます。
   - **[削除]** - このオプションは、ソース環境が削除されたか、アクセスできない (接続されていない) 場合にのみ使用します。 これを使用すると、レプリケートされたアイテムが Azure Site Recovery から削除されます (請求は停止されます)。 オンプレミスの仮想マシン上のレプリケーション構成はクリーンアップ**されません**。 オンプレミスの仮想マシン上のレプリケーション設定をクリーンアップするには、以下のスクリプトのセットを実行します。
     > [!NOTE]
     > **[削除]** オプションを選択した場合、オンプレミスの Hyper-V Server 上のレプリケーション設定をクリーンアップするには、次のスクリプトを実行します。

3. VMM コンソールから PowerShell を使用して、ソース VMM サーバーで次のスクリプトを実行します (管理者特権が必要です)。 プレースホルダー **SQLVM1** を仮想マシンの名前に置き換えます。

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. セカンダリ VMM サーバーで次のスクリプトを実行して、セカンダリ仮想マシンの設定をクリーンアップします。

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. セカンダリ VMM サーバーで、Hyper-V ホスト サーバー上の仮想マシンを更新し、セカンダリ VM が VMM コンソールで再検出されるようにします。
6. 上記の手順によって、VMM サーバーのレプリケーション設定がクリアされます。 仮想マシンのレプリケーションを停止する場合は、プライマリとセカンダリの VM で次のスクリプトを実行します。 SQLVM1 を仮想マシンの名前に置き換えます。

        Remove-VMReplication –VMName “SQLVM1”




