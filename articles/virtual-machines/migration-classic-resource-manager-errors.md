---
title: クラシックから Azure Resource Manager への移行中に発生する一般的なエラー
description: この記事では、Azure サービス管理から Azure Resource Manager への IaaS リソースの移行中に発生する一般的なエラーと対応策を一覧で示しています。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 6d803d1a66c069f5eb42deead453a8526577f76b
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615212"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>クラシックから Azure Resource Manager への移行中によく発生するエラー

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨の[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

この記事では、Azure クラシック デプロイ モデルから Azure Resource Manager スタックへの IaaS リソースの移行中に発生する一般的なエラーと軽減策を一覧で示しています。


## <a name="list-of-errors"></a>エラー一覧

| エラー文字列 | 対応策 |
| --- | --- |
| 内部サーバー エラー |場合によっては、これは再試行によって解消される一時的なエラーです。 エラーが解消されない場合は、プラットフォームのログを調べる必要があるため、[Azure サポートにご連絡](../azure-portal/supportability/how-to-create-azure-support-request.md)ください。 <br><br> **注:** サポート チームによるインシデントの追跡が開始されたら、ご自分で対応しようとしないでください。環境で予期しない結果が発生する可能性があります。 |
| HostedService {hosted-service-name} 内の配置 {deployment-name} は PaaS 配置 (Web/Worker) であるため、移行がサポートされません。 |これは、デプロイに Web/worker ロールが含まれている場合に発生します。 仮想マシンの移行のみがサポートされているため、デプロイから Web/ワーカー ロールを削除し、移行をやり直してください。 |
| テンプレート {template-name} をデプロイできませんでした。 関連付け ID = {guid} |移行サービスのバックエンドでは、Azure Resource Manager テンプレートを使用して Azure Resource Manager スタック内にリソースを作成します。 テンプレートはべき等であるため、通常は移行操作を繰り返すことでこのエラーを解決しても問題はありません。 エラーが解消されない場合は、[Azure サポートに連絡](../azure-portal/supportability/how-to-create-azure-support-request.md)して関連付け ID をお知らせください。 <br><br> **注:** サポート チームによるインシデントの追跡が開始されたら、ご自分で対応しようとしないでください。環境で予期しない結果が発生する可能性があります。 |
| 仮想ネットワーク {virtual-network-name} は存在していません。 |これは、新しい Azure Portal で Virtual Network を作成した場合に発生する可能性があります。 実際の仮想ネットワーク名は、"グループ * \<VNET name>" のパターンに従います。 |
| HostedService {hosted-service-name} 内の VM {vm-name} に、Azure Resource Manager でサポートされない拡張機能 {extension-name} が含まれています。 移行を続行する前に、この拡張機能を VM からアンインストールすることをお勧めします。 |Azure Resource Manager では、BGInfo 1.\* などの XML 拡張機能はサポートされていません。 そのため、これらの拡張機能を移行することはできません。 これらの拡張機能が仮想マシンにインストールされている場合は、移行を完了する前に自動的にアンインストールされます。 |
| HostedService {hosted-service-name} 内の VM {vm-name} に、移行用に現在サポートされていない拡張機能 VMSnapshot/VMSnapshotLinux が含まれています。 この機能を一度 VM からアンインストールし、移行が完了したら Azure Resource Manager を使って再インストールしてください。 |これは、仮想マシンが Azure Backup 用に構成されるシナリオです。 これは、現時点ではサポートされていないシナリオであるため、 https://aka.ms/vmbackupmigration の回避策に従ってください。 |
| HostedService {hosted-service-name} の VM {vm-name} には、拡張機能 {extension-name} が含まれています。この拡張機能のステータスについて VM から報告がないため、 この VM を移行できません。 この拡張機能のステータスが報告されるようにするか、VM からこの拡張機能をアンインストールしたうえで、移行をやり直してください。 <br><br> HostedService {hosted-service-name} の VM {vm-name} には、拡張機能 {extension-name} が含まれています。この拡張機能のハンドラー ステータスとして {handler-status} が報告されているため、 この VM を移行できません。 この拡張機能のハンドラー ステータスとして {handler-status} が報告されるようにするか、VM からこの拡張機能をアンインストールしたうえで、移行をやり直してください。 <br><br> HostedService {hosted-service-name} 内の VM {vm-name} 用の VM エージェントが、エージェントの全体的なステータスとして [準備不完了] を報告しています。 そのため、VM に移行可能な拡張機能があっても、VM を移行できません。 VM エージェントがエージェントの全体的なステータスとして [準備完了] を報告していることを確認してください。 [https://aka.ms/classiciaasmigrationfaqs](https://aka.ms/classiciaasmigrationfaqs ) を参照してください。 |Azure ゲスト エージェントと VM 拡張機能では、各自の状態を設定するために VM ストレージ アカウントへの発信インターネット アクセスが必要です。 状態エラーの一般的な原因には、以下が考えられます。 <li> ネットワーク セキュリティ グループがインターネットへの発信アクセスがブロックしている <li> VNET にオンプレミスの DNS サーバーがあるときに DNS 接続が失われている <br><br> サポートされていない状態が解決しない場合は、拡張機能をアンインストールしてこのチェックをスキップし、移行を進めることができます。 |
| HostedService {hosted-service-name} 内の配置 {deployment-name} には複数の可用性セットがあるため、移行がサポートされません。 |現時点では、1 つ以下の可用性セットを持つホストされるサービスのみを移行できます。 この問題を回避するには、余分な可用性セットと可用性セット内の Virtual Machines を別のホストされるサービスに移動してください。 |
| HostedService {hosted-service-name} 内の配置 {deployment-name} に含まれている VM が可用性セットの一部ではないため、HostedService に可用性セットが含まれているとしても、この配置の移行はサポートされません。 |このシナリオの回避策は、すべての仮想マシンを 1 つの可用性セット内に移動するか、ホストされるサービスの可用性セットからすべての仮想マシンを削除することです。 |
| ストレージ アカウント/HostedService/Virtual Network {virtual-network-name} は移行中のため、変更できません。 |このエラーは、リソースの "準備" 移行処理が完了し、リソースに変更を加える可能性がある操作がトリガーされた場合に発生します。 "準備" 操作の後、管理プレーンはロックされるため、リソースの変更はすべてブロックされます。 管理プレーンのロックを解除するには、"コミット" 移行操作を実行して移行を完了するか、"中止" 移行操作で "準備" 操作をロールバックします。 |
| 次の状態の VM {vm-name} が存在するため、HostedService {hosted-service-name} の移行は許可されていません:RoleStateUnknown。 VM が [実行中]、[停止済み]、[停止済み (割り当て解除)] のいずれかの状態の場合にのみ、移行が許可されます。 |VM が状態遷移中である可能性があります。通常、この状態は、再起動や拡張機能のインストールなどの HostedService の更新操作を実行しているときに発生します。移行を試行する前に、HostedService の更新操作が完了するまで待つことをお勧めします。 |
| HostedService {hosted-service-name} のデプロイ {deployment-name} には、物理 BLOB サイズ ({size-of-the-vhd-blob-backing-the-data-disk} バイト) が VM データ ディスクの論理サイズ ({size-of-the-data-disk-specified-in-the-vm-api} バイト) と一致しないデータ ディスク {data-disk-name} を持つ VM {vm-name} が含まれています。 移行は、Azure Resource Manager VM のデータ ディスクのサイズを指定せずに続行されます。 | このエラーは、VM API モデルのサイズを更新せずに VHD の BLOB サイズを変更した場合に発生します。 対応策の詳細な手順を[以下](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes)に示します。|
| クラウド サービス {クラウド サービス名} 内の VM {VM 名} のメディア リンク {データ ディスク URI} を使用してデータ ディスク {データ ディスク名} を検証中にストレージ例外が発生しました。 この仮想マシンが VHD メディア リンクにアクセスできることを確認してください。 | このエラーは、VM のディスクが削除された場合、またはディスクにアクセスできなくなった場合に発生します。 VM のディスクが存在することを確認してください。|
| HostedService {cloud-service-name} 内の VM {vm-name} に、Azure Resource Manager でサポートされない BLOB 名 {vhd-blob-name} の MediaLink {vhd-uri} があるディスクが含まれています。 | このエラーは、BLOB の名前に "/" が含まれている場合に発生します。この記号は現在、コンピューティング リソース プロバイダーでサポートされていません。 |
| HostedService {cloud-service-name} 内のデプロイ {deployment-name} はリージョン範囲に含まれていないため、移行することができません。 このデプロイをリージョン範囲に移動する方法については、https:\//aka.ms/regionalscope を参照してください。 | 2014 年、Azure は、ネットワーク リソースをクラスター レベル スコープからリージョン スコープに移動すると発表しました。 詳細については、「[https://aka.ms/regionalscope](https://aka.ms/regionalscope)」を参照してください。 移行中のデプロイで更新操作が行われていない場合に、このエラーが発生します。その場合は、自動的にリージョン スコープに移動されます。 このエラーを回避するには、エンドポイントを VM に追加するか、データ ディスクを VM に追加してから、移行を再試行してください。 <br> [Azure 上でクラシック仮想マシンにエンドポイントをセットアップする方法](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint)または[クラシック デプロイ モデルを使用して作成された仮想マシンにデータ ディスクをアタッチする](./linux/attach-disk-portal.md)に関するページを参照してください。|
| Virtual Network {vnet-name} の移行はサポートされていません。この Virtual Network にはゲートウェイ以外の PaaS 展開が含まれいるためです。 | このエラーは、Virtual Network に接続されている Application Gateway サービスや API Management サービスなどのゲートウェイ以外の PaaS デプロイがある場合に発生します。|


## <a name="detailed-mitigations"></a>詳細な対応策

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>VM のデータ ディスクの物理 BLOB サイズのバイト数が、VM データ ディスクの論理サイズのバイト数と一致していません。

これは、データ ディスクの論理サイズが実際の VHD BLOB サイズと同期されていない可能性がある場合に発生します。 次のコマンドを使用すると、簡単に検証できます。

#### <a name="verifying-the-issue"></a>問題の検証

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>問題の軽減

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>移行の完了後に VM を別のサブスクリプションに移動する

移行プロセスの完了後に、VM を別のサブスクリプションに移動する場合があります。 ただし、Key Vault リソースを参照する VM にシークレット/証明書がある場合、現時点では移動はサポートされていません。 次の手順を実行すると、この問題を回避できます。 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-cli.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md)