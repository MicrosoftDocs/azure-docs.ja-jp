---
title: リモート ツールを使用して Azure VM の問題をトラブルシューティングする | Microsoft Docs
description: PsExec、PowerShell スクリプトなど、RDP を使わずにリモート Azure VM の問題をトラブルシューティングする際に使用できるリモート ツールについて説明します。
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 5abb509f1753c65554bd74ababe9acca4103c15a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509088"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>リモート ツールを使用して Azure VM の問題をトラブルシューティングする

Azure 仮想マシン (VM) に関する問題をトラブルシューティングする場合、リモート デスクトップ プロトコル (RDP) を使用するのではなく、この記事で説明されているリモート ツールを使用して VM に接続できます。

## <a name="serial-console"></a>シリアル コンソール

[Azure Virtual Machines 用のシリアル コンソール](serial-console-windows.md)を使用して、リモートの Azure VM でコマンドを実行します。

## <a name="remote-cmd"></a>リモート CMD

[PsExec](/sysinternals/downloads/psexec) をダウンロードします。 次のコマンドを実行して、VM に接続します。

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* コマンドは、同じ仮想ネットワークにあるコンピューターで実行する必要があります。
>* DIP または HostName を使用して、\<computer> を置き換えることができます。
>* -s パラメーターを指定すると、システム アカウント (管理者権限) を使用してコマンドが確実に呼び出されるようになります。
>* PsExec では TCP ポートの 135 と 445 を使用します。 そのため、2 つのポートをファイアウォールで開く必要があります。

## <a name="run-command"></a>実行コマンド

実行コマンド機能を使用して VM でスクリプトを実行する方法の詳細については、「[実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する](../windows/run-command.md)」を参照してください。

## <a name="custom-script-extension"></a>カスタム スクリプト拡張機能

カスタム スクリプト拡張機能を使用して、ターゲット VM でカスタム スクリプトを実行することができます。 この機能を使用するには、次の条件を満たす必要があります。

* VM が接続されている。
* Azure Virtual Machine Agent がインストールされており、VM で期待どおりに動作している。
* VM にまだ拡張機能がインストールされていない。
 
  拡張機能では、初回使用時にのみ、スクリプトが挿入されます。 後でこの機能を使用する場合、拡張機能では、それが既に使用されていることが認識され、新しいスクリプトはアップロードされません。

スクリプトをストレージ アカウントにアップロードし、独自のコンテナーを生成します。 次に、VM に接続されているコンピューター上の Azure PowerShell で以下のスクリプトを実行します。

### <a name="for-classic-deployment-model-vms"></a>クラシック デプロイ モデル VM の場合

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Azure Resource Manager VM の場合

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>リモート PowerShell

>[!NOTE]
>このオプションを使用できるように、TCP ポート 5986 (HTTPS) を開く必要があります。
>
>Azure Resource Manager VM の場合は、ネットワーク セキュリティ グループ (NSG) でポート 5986 を開く必要があります。 詳細については、セキュリティ グループに関する記述を参照してください。 
>
>RDFE VM の場合は、プライベート ポート (5986) とパブリック ポートがあるエンドポイントが必要です。 その後、NSG でそのパブリック側のポートを開く必要もあります。

### <a name="set-up-the-client-computer"></a>クライアント コンピューターを設定する

PowerShell を使用して VM にリモート接続するには、まず、接続を許可するようにクライアント コンピューターを設定する必要があります。 これを行うには、必要に応じて、次のコマンドを実行して、VM を PowerShell の信頼されたホストの一覧に追加します。

信頼されたホスト一覧に 1 つの VM を追加する場合:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

信頼されたホスト一覧に複数の VM を追加する場合:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

信頼されたホスト一覧にすべてのコンピューターを追加する場合:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>VM で RemotePS を有効にする

クラシック デプロイ モデルを使用して作成された VM の場合は、カスタム スクリプト拡張機能を使用して次のスクリプトを実行します。

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Azure Resource Manager VM の場合は、ポータルから実行コマンドを使用して、EnableRemotePS スクリプトを実行します。

![実行コマンド](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>VM に接続します

クライアント コンピューターの場所に応じて、以下のコマンドを実行します。

* 仮想ネットワークまたはデプロイの外部

  * クラシック デプロイ モデルを使用して作成された VM の場合は、次のコマンドを実行します。

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Azure Resource Manager VM の場合は、最初にパブリック IP アドレスに DNS 名を追加します。 詳しい手順については、「[Windows VM 用の Azure Portal での完全修飾ドメイン名の作成](../windows/portal-create-fqdn.md)」を参照してください。 次に、次のコマンドを実行します。

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* 仮想ネットワークまたはデプロイ内で、次のコマンドを実行します。
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>SkipCaCheck フラグを設定すると、セッションを開始するときに VM に証明書をインポートする要件をバイパスできます。

Invoke-Command コマンドレットを使用して、VM でスクリプトをリモートで実行することもできます。

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>リモート レジストリ

>[!NOTE]
>このオプションを使用するには、TCP ポート 135 または 445 を開く必要があります。
>
>Azure Resource Manager VM の場合は、NSG でポート 5986 を開く必要があります。 詳細については、セキュリティ グループに関する記述を参照してください。 
>
>RDFE VM の場合は、プライベート ポート 5986 とパブリック ポートがあるエンドポイントが必要です。 NSG でそのパブリック側のポートを開く必要もあります。

1. 同じ仮想ネットワーク上の別の VM から、レジストリ エディター (regedit.exe) を開きます。

2. **[ファイル]**  >  **[Connect Network Registry]\(ネットワーク レジストリへの接続\)** の順に選択します。

   ![レジストリ エディター](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. **[選択するオブジェクト名を入力してください]** ボックスに**ホスト名**または**動的 IP** (推奨) を入力して、ターゲット VM を検索します。

   ![[選択するオブジェクト名を入力してください] ボックス](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. ターゲット VM の資格情報を入力します。

5. すべての必要なレジストリ変更を行います。

## <a name="remote-services-console"></a>リモート サービス コンソール

>[!NOTE]
>このオプションを使用するには、TCP ポート 135 または 445 を開く必要があります。
>
>Azure Resource Manager VM の場合は、NSG でポート 5986 を開く必要があります。 詳細については、セキュリティ グループに関する記述を参照してください。 
>
>RDFE VM の場合は、プライベート ポート 5986 とパブリック ポートがあるエンドポイントが必要です。 NSG でそのパブリック側のポートを開く必要もあります。

1. 同じ仮想ネットワークの別の VM から、**Services.msc** のインスタンスを開きます。

2. **[サービス (ローカル)]** を右クリックします。

3. **[Connect to another computer]\(別のコンピューターに接続\)** を選択します。

   ![リモート サービス](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. ターゲット VM の動的 IP アドレスを入力します。

   ![動的 IP の入力](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. サービスに対してすべての必要な変更を行います。

## <a name="next-steps"></a>次のステップ

- Enter-PSSession コマンドレットの詳細については、[Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession?view=powershell-5.1) を参照してください。
- クラシック デプロイ モデルを使用した Windows のカスタム スクリプト拡張機能の詳細については、「[Windows でのカスタムのスクリプト拡張機能](../extensions/custom-script-windows.md)」を参照してください。
- PsExec は、[PSTools スイート](https://download.sysinternals.com/files/PSTools.zip)の一部です。
- PSTools スイートの詳細については、[PSTools](/sysinternals/downloads/pstools) に関するページを参照してください。
