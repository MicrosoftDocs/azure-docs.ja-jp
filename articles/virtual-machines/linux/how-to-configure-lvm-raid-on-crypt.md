---
title: 暗号化されたデバイスで LVM と RAID を構成する - Azure Disk Encryption
description: この記事では、Linux VM 用の暗号化されたデバイスで LVM と RAID を構成する手順について説明します。
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: ec9f99d0a13b5b92bc267f184d364ebabe36a050
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566109"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>暗号化されたデバイスで LVM と RAID を構成する

この記事では、暗号化されたデバイスで論理ボリューム管理 (LVM) と RAID を実行する方法について、手順を追って説明します。 このプロセスは、次の環境に適用されます。

- Linux ディストリビューション
    - RHEL 7.6 以降
    - Ubuntu 18.04 以降
    - SUSE 12 以降
- Azure Disk Encryption のシングルパス拡張機能
- Azure Disk Encryption のデュアルパス拡張機能


## <a name="scenarios"></a>シナリオ

この記事の手順では、次のシナリオをサポートしています。  

- 暗号化されたデバイスの上位に LVM を構成する (LVM-on-crypt)
- 暗号化されたデバイスの上位に RAID を構成する (RAID-on-crypt)

基になる 1 台または複数台のデバイスが暗号化されたら、その暗号化されたレイヤーの上位に LVM または RAID 構造を作成できます。 

物理ボリューム (PV) は、暗号化されたレイヤーの上位に作成されます。 物理ボリュームは、ボリューム グループの作成に使用されます。 ボリュームを作成し、必要なエントリを /etc/fstab に追加します。 

![LVM 構造のレイヤーの図](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

同様の方法で、RAID デバイスはディスク上の暗号化されたレイヤーの上位に作成されます。 ファイル システムは RAID デバイスの上位に作成され、通常のデバイスとして /etc/fstab に追加されます。

## <a name="considerations"></a>考慮事項

LVM-on-crypt を使用することをお勧めします。 RAID は、特定のアプリケーションまたは環境の制限のために LVM を使用できない場合のオプションです。

**EncryptFormatAll** オプションを使用します。 このオプションの詳細については、「[Linux VM 上のデータ ディスクに対して EncryptFormatAll 機能を使用する](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)」を参照してください。

OS も暗号化しているときにこの方法を使用できますが、ここではデータ ドライブを暗号化するだけです。

この手順では、「[Linux VM での Azure Disk Encryption シナリオ](./disk-encryption-linux.md)」と「[クイックスタート: Azure CLI を使用して Linux VM を作成、暗号化する](./disk-encryption-cli-quickstart.md)」で前提条件を既に確認していることを前提としています。

Azure Disk Encryption のデュアルパス バージョンは非推奨のパスにあり、新しい暗号化では使用できません。

## <a name="general-steps"></a>一般的な手順

"on-crypt" 構成を使用している場合は、次の手順に記載されているプロセスを使用します。

>[!NOTE] 
>この記事では、変数を使用しています。 値は適宜置き換えてください。

### <a name="deploy-a-vm"></a>VM をデプロイする 
次のコマンドは省略可能ですが、新しくデプロイされた仮想マシン (VM) に適用することをお勧めします。

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>ディスクを VM に接続する
VM に接続する新しいディスク数 `$N` に応じて次のコマンドを繰り返します。

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>ディスクが VM に接続されていることを確認します。
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell での接続されているディスクの一覧](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI での接続されているディスクの一覧](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

ポータル:

![ポータルでの接続されているディスクの一覧](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

OS:

```bash
lsblk 
```
![OS での接続されているディスクの一覧](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>暗号化するディスクを構成する
この構成は、オペレーティング システム レベルで実行されます。 対応するディスクは、Azure Disk Encryption による従来の暗号化用に構成されています。

- ファイル システムはディスクの上位に作成されます。
- ファイル システムをマウントするために、一時マウント ポイントが作成されます。
- ファイル システムは、起動時にマウントされるように /etc/fstab 上に構成されます。

新しいディスクに割り当てられているデバイス文字を確認します。 この例では、4 つのデータ ディスクを使用しています。

```bash
lsblk 
```
![OS に接続されているデータ ディスク](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>各ディスクの上位にファイル システムを作成する
このコマンドを使用すると、各ディスク上で、"for" サイクルの "in" 部分で定義された ext4 ファイル システムの作成が反復されます。

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![ext4 ファイル システムの作成](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

最近作成したファイル システムの汎用一意識別子 (UUID) を見つけ、一時フォルダーを作成し、対応するエントリを /etc/fstab に追加して、すべてのファイル システムをマウントします。

このコマンドは、"for" サイクルの "in" 部分に定義されている各ディスクに対しても反復します。

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>ディスクが正しくマウントされていることを確認する
```bash
lsblk
```
![マウントされた一時ファイル システムの一覧](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

ディスクが構成されていることも確認します。

```bash
cat /etc/fstab
```
![fstab を使用した構成情報](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>データ ディスクを暗号化する
キー暗号化キー (KEK) を使用する PowerShell:

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

KEK を使用する Azure CLI:

```azurecli
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>暗号化の状態を確認する

すべてのディスクが暗号化されている場合にのみ、次の手順に進みます。

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell の暗号化の状態](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLI の暗号化の状態](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

ポータル:

![ポータルの暗号化の状態](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

OS レベル:

```bash
lsblk
```
![OS の暗号化の状態](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

拡張機能によりファイル システムが /var/lib/azure_disk_encryption_config/azure_crypt_mount (以前の暗号化) または /etc/crypttab (新しい暗号化) に追加されます。

>[!NOTE] 
>これらのファイルは変更しないでください。

このファイルは、起動プロセス中にこれらのディスクをアクティブ化する処理を行い、後で LVM または RAID からそれらを使用できるようにします。 

このファイルのマウント ポイントについて心配する必要はありません。 それらの暗号化されたデバイスの上位に物理ボリュームまたは RAID デバイスを作成した後、Azure Disk Encryption ではディスクを通常のファイル システムとしてマウントできなくなります (これにより、準備プロセス中に使用したファイル システム形式が削除されます)。

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>一時フォルダーと一時的な fstab エントリを削除する
LVM の一部として使用されるディスク上のファイル システムをマウント解除します。

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
また、/etc/fstab のエントリを削除します。

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>ディスクがマウントされていないこと、/etc/fstab のエントリが削除されたことを確認する

```bash
lsblk
```
![一時ファイル システムがマウント解除されていることの確認](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

ディスクが構成されていることを確認します。
```bash
cat /etc/fstab
```
![一時的な fstab エントリが削除されたことの確認](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM-on-crypt の手順
基になるディスクが暗号化されたので、LVM 構造を作成できます。

デバイス名を使用するのではなく各ディスクの /dev/mapper パスを使用して (ディスク自体の上ではなく、ディスクの上位にある crypt レイヤー上に) 物理ボリュームを作成します。

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>暗号化されたレイヤーの上位に LVM を構成する
#### <a name="create-the-physical-volumes"></a>物理ボリュームを作成する
ファイル システムの署名を消去するかどうかを確認する警告が表示されます。 「**y**」を入力して続行するか、次のように「**echo "y"** 」を使用します。

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![物理ボリュームが作成されたことの確認](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>ここでは /dev/mapper/デバイス名を **lsblk** の出力に基づいて実際の値に置き換える必要があります。

#### <a name="verify-the-information-for-physical-volumes"></a>物理ボリュームの情報を確認する
```bash
pvs
```

![物理ボリュームに関する情報](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>ボリューム グループを作成する
既に初期化されている同じデバイスを使用してボリューム グループを作成します。

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>ボリューム グループの情報を確認する

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![ボリューム グループの情報](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>論理ボリュームを作成する

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>作成した論理ボリュームを確認する

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![論理ボリュームに関する情報](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>論理ボリュームの構造の上位にファイル システムを作成する

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>新しいファイル システムのマウント ポイントを作成する

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>新しいファイルシステムを /etc/fstab に追加してマウントする

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>新しいファイル システムがマウントされていることを確認する

```bash
lsblk -fs
df -h
```
![スクリーンショットには、data0 および data1 としてマウントされたファイル システムが表示されているコンソール ウィンドウが示されています。](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

この **lsblk** のバリエーションでは、依存関係を逆順にしてデバイスを一覧表示しています。 このオプションを使用すると、元の /dev/sd[disk] デバイス名ではなく、論理ボリューム別にグループ化されたデバイスを識別できます。

Azure Disk Encryption で暗号化されたデバイスの上位に作成された LVM ボリュームのマウント ポイント オプションに、**nofail** オプションが追加されていることを確認することが重要です。 起動プロセス中 (またはメンテナンス モード中) の OS の停止を防ぐことができます。

**nofail** オプションを使用しない場合:

- Azure Disk Encryption が開始され、データ ディスクのロックが解除されてマウントされる段階に OS がなることはありません。 
- 暗号化されたディスクは、起動プロセスの最後にロック解除されます。 LVM ボリュームとファイル システムは、Azure Disk Encryption によってロックが解除されるまでに自動的にマウントされます。 

VM の再起動をテストし、ファイル システムが起動後に自動的にマウントされるかどうかを検証できます。 ファイル システムの数とサイズによっては、このプロセスに数分かかることがあります。

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>VM を再起動し、再起動後に検証する

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>RAID-on-crypt の手順
基になるディスクが暗号化されたので、引き続き RAID 構造を作成することができます。 このプロセスは LVM の場合と同じですが、デバイス名を使用するのではなく、各ディスクの /dev/mapper パスを使用します。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>ディスクの暗号化されたレイヤーの上位に RAID を構成する
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![mdadm コマンドを使用して構成された RAID の情報](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>ここでは /dev/mapper/デバイス名を **lsblk** の出力に基づいて実際の値に置き換える必要があります。

### <a name="checkmonitor-raid-creation"></a>RAID の作成を確認および監視する
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID の状態](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>新しい RAID デバイスの上位にファイル システムを作成する
```bash
mkfs.ext4 /dev/md10
```

ファイル システム用の新しいマウント ポイントを作成し、新しいファイル システムを /etc/fstab に追加してマウントします。

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

新しいファイル システムがマウントされていることを確認します。

```bash
lsblk -fs
df -h
```
![スクリーンショットには、raiddata としてマウントされているファイル システムが表示されたコンソール ウィンドウが示されています。](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Azure Disk Encryption で暗号化されたデバイスの上位に作成された RAID ボリュームのマウント ポイント オプションに、**nofail** オプションが追加されていることを確認することが重要です。 起動プロセス中 (またはメンテナンス モード中) の OS の停止を防ぐことができます。

**nofail** オプションを使用しない場合:

- Azure Disk Encryption が開始され、データ ディスクのロックが解除されてマウントされる段階に OS がなることはありません。
- 暗号化されたディスクは、起動プロセスの最後にロック解除されます。 RAID ボリュームとファイル システムは、Azure Disk Encryption によってロックが解除されるまでに自動的にマウントされます。

VM の再起動をテストし、ファイル システムが起動後に自動的にマウントされるかどうかを検証できます。 ファイル システムの数とサイズによっては、このプロセスに数分かかることがあります。

```bash
shutdown -r now
```

ログインできるとき:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption で暗号化された論理ボリューム管理デバイスのサイズを変更する](how-to-resize-encrypted-lvm.md)
- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)
