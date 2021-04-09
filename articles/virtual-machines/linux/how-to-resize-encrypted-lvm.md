---
title: Azure Disk Encryption を使用して暗号化されたディスクのサイズを変更する方法
description: この記事では、論理ボリューム管理を使用して ADE 暗号化ディスクのサイズを変更する手順について説明します。
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 92e3be9111abe5712549ba85ef4550a9ac1473f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596809"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Azure Disk Encryption を使用する論理ボリューム管理デバイスのサイズを変更する方法

この記事では、Azure Disk Encryption を使用するデータ ディスクのサイズを変更する方法について説明します。 ディスク サイズを変更するには、Linux で論理ボリューム管理 (LVM) を使用します。 この手順は複数のシナリオに適用されます。

このサイズ変更プロセスは次の環境で使用できます。

- Linux ディストリビューション:
    - Red Hat Enterprise Linux (RHEL) 7 以降
    - Ubuntu 16 以降
    - SUSE 12 以降
- Azure Disk Encryption のバージョン: 
    - シングルパス拡張機能
    - デュアルパス拡張機能

## <a name="prerequisites"></a>前提条件

この記事では、以下のことを前提としています。

- 既存の LVM 構成。 詳細については、[Linux VM における LVM の構成](/previous-versions/azure/virtual-machines/linux/configure-lvm)に関する記事をご覧ください。

- 既に Azure Disk Encryption によって暗号化されているディスク。 詳細については、「[暗号化されたデバイスで LVM と RAID を構成する](how-to-configure-lvm-raid-on-crypt.md)」をご覧ください。

- Linux と LVM の使用経験。

- Azure 上のデータ ディスクの */dev/disk/scsi1/* パスの使用経験。 詳細については、[Linux VM デバイス名の問題のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)に関する記事をご覧ください。 

## <a name="scenarios"></a>シナリオ

この記事の手順は、次のシナリオに適用されます。

- 従来の LVM 構成および LVM-on-crypt 構成
- 従来の LVM 暗号化 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>従来の LVM 構成および LVM-on-crypt 構成

従来の LVM 構成および LVM-on-crypt 構成では、ボリューム グループ (VG) に使用可能な領域がある場合に、論理ボリューム (LV) が拡張されます。

### <a name="traditional-lvm-encryption"></a>従来の LVM 暗号化 

従来の LVM 暗号化では、LV が暗号化されます。 ディスク全体は暗号化されません。

従来の LVM 暗号化を使用すると、次のことができます。

- 新しい物理ボリューム (PV) を追加するときに LV を拡張する。
- 既存の PV のサイズを変更するときに LV を拡張する。

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

ディスク暗号化のために推奨される方法は、LVM-on-crypt です。 この方法の場合、LV だけでなくディスク全体が暗号化されます。

LVM-on-crypt を使用すると、次のことができます。 

- 新しい PV を追加するときに LV を拡張する。
- 既存の PV のサイズを変更するときに LV を拡張する。

> [!NOTE]
> 従来の LVM 暗号化と LVM-on-crypt を同じ VM 上で混在させることはお勧めしません。

以下のセクションでは、LVM と LVM-on-crypt の使用方法の例を紹介します。 これらの例では、ディスク、PV、VG、LV、ファイル システム、汎用一意識別子 (UUID)、マウント ポイントには既存の値を使用します。 これらの値は、ご利用の環境に合わせて独自の値に置き換えてください。

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>VG に使用可能な領域がある場合に LV を拡張する

LV のサイズを変更する従来の方法は、VG に使用可能な領域がある場合に LV を拡張することです。 この方法は、暗号化されていないディスク、従来の LVM 暗号化ボリューム、および LVM-on-crypt 構成に使用できます。

1. サイズを増やすファイル システムの現在のサイズを確認します。

    ``` bash
    df -h /mountpoint
    ```

    ![ファイル システムのサイズを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. LV を増やすための十分な領域が VG にあることを確認します。

    ``` bash
    vgs
    ```

    ![VG 上の領域を確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    `vgdisplay` を使用することもできます。

    ``` bash
    vgdisplay vgname
    ```

    ![VG 上の領域を確認する V G display コードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. サイズ変更の必要がある LV を特定します。

    ``` bash
    lsblk
    ```

    ![l s b l k コマンドの結果を示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    LVM-on-crypt の場合、この出力は暗号化されたレイヤーがディスク レベルであることを示しています。

    ![l s b l k コマンドの結果を示すスクリーンショット。 出力が強調表示されています。 暗号化されたレイヤーが示されています。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. LV のサイズを確認します。

    ``` bash
    lvdisplay lvname
    ```

    ![論理ボリュームのサイズを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. ファイル システムのサイズをオンラインで変更するために、`-r` を使用して LV のサイズを増やします。

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![論理ボリュームのサイズを増やすコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. LV とファイル システムの新しいサイズを確認します。

    ``` bash
    df -h /mountpoint
    ```

    ![LV とファイル システムのサイズを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    このサイズの出力は、LV とファイル システムのサイズが正常に変更されたことを示しています。

LV レベルで変更を確認するには、LV の情報をもう一度確認します。

``` bash
lvdisplay lvname
```

![新しいサイズを確認するコードを示すスクリーンショット。 サイズが強調表示されています。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>新しい PV を追加することで従来の LVM ボリュームを拡張する

新しいディスクを追加して VG のサイズを増やす必要がある場合は、新しい PV を追加することで従来の LVM ボリュームを拡張します。

1. サイズを増やすファイル システムの現在のサイズを確認します。

    ``` bash
    df -h /mountpoint
    ```

    ![ファイル システムの現在のサイズを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 現在の PV 構成を確認します。

    ``` bash
    pvs
    ```

    ![現在の PV 構成を確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 現在の VG の情報を確認します。

    ``` bash
    vgs
    ```

    ![現在のボリューム グループ情報を確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 現在のディスク リストを確認します。 */dev/disk/azure/scsi1/* のデバイスを確認することで、データ ディスクを特定します。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![現在のディスク リストを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. `lsblk` の出力を確認します。 

    ``` bash
    lsbk
    ```

    ![l s b l k の出力を確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. [Linux VM へのデータ ディスクのアタッチ](attach-disk-portal.md)に関する記事の手順に従って、新しいディスクを VM にアタッチします。

7. ディスク リストを確認し、新しいディスクに注目します。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![ディスク リストを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![l s b l k を使用してディスク リストを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 新しいデータ ディスクの上に新しい PV を作成します。

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![新しい PV を作成するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    この方法では、ディスク全体がパーティションなしの PV として使用されます。 代わりに、`fdisk` を使用してパーティションを作成し、そのパーティションを `pvcreate` に使用することもできます。

9. PV が PV リストに追加されたことを確認します。

    ``` bash
    pvs
    ```

    ![物理ボリュームのリストを表示するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 新しい PV を VG に追加することで、VG を拡張します。

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![ボリューム グループを拡張するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 新しい VG のサイズを確認します。

    ``` bash
    vgs
    ```

    ![ボリューム グループのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. `lsblk` を使用して、サイズ変更が必要な LV を特定します。

    ``` bash
    lsblk
    ```

    ![サイズ変更が必要なローカル ボリュームを特定するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. ファイル システムのサイズをオンラインで増やすために、`-r` を使用して LV のサイズを拡張します。

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![ファイル システムのサイズをオンラインで増やすコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. LV とファイル システムの新しいサイズを確認します。

    ``` bash
    df -h /mountpoint
    ```

    ![ローカル ボリュームとファイル システムのサイズを確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Azure Data Encryption を従来の LVM 構成で使用すると、暗号化されたレイヤーはディスク レベルではなく LV レベルで作成されます。
    >
    >この時点で、暗号化されたレイヤーは新しいディスクに展開されます。 実際のデータ ディスクにはプラットフォーム レベルでの暗号化設定はないため、暗号化の状態は更新されません。
    >
    >このような理由で、LVM-on-crypt が推奨される方法となっています。 

15. ポータルで暗号化の情報を確認します。

    ![暗号化情報が表示されているポータルを示すスクリーンショット。 ディスク名と暗号化が強調表示されています。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    ディスクの暗号化設定を更新するには、新しい LV を追加し、VM 上で拡張機能を有効にします。
    
16. 新しい LV を追加し、そこにファイル システムを作成して、それを `/etc/fstab` に追加します。

17. 暗号化拡張機能を再度設定します。 今回は、新しいデータ ディスクにプラットフォーム レベルで暗号化設定をスタンプします。 CLI の例を次に示します。

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. ポータルで暗号化の情報を確認します。

    ![暗号化情報が表示されているポータルを示すスクリーンショット。 ディスク名と暗号化情報が強調表示されています。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

暗号化設定が更新されたら、新しい LV を削除できます。 エントリを作成した `/etc/fstab` と `/etc/crypttab` からも削除してください。

![新しい論理ボリュームを削除するコードを示すスクリーンショット。 削除された F S tab と crypt tab が強調表示されています。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

クリーンアップを完了するには、次の手順に従います。

1. LV のマウントを解除します。

    ``` bash
    umount /mountpoint
    ```

1. ボリュームの暗号化されたレイヤーを閉じます。

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. LV を削除します。

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>既存の PV のサイズを変更して従来の LVM ボリュームを拡張する

シナリオによっては、制限の内容により、既存のディスクのサイズを変更しなければならない場合があります。 その方法は次のとおりです。

1. 使用する暗号化されたディスクを特定します。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![暗号化されたディスクを特定するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![暗号化されたディスクを特定する代替コードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. PV の情報を確認します。

    ``` bash
    pvs
    ```

    ![物理ボリュームに関する情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    画像内の結果は、すべての PV の全領域が現在使用されていることを示しています。

3. VG の情報を確認します。

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![ボリューム グループに関する情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. ディスク サイズを確認します。 `fdisk` または `lsblk` を使用して、ドライブのサイズを一覧表示できます。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![ディスク サイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    ここでは、どの PV がどの LV に関連付けられているかを、`lsblk -fs` を使用して特定しました。 `lvdisplay` を実行することで、関連付けを特定できます。

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![物理ボリュームとローカル ボリュームの間の関連付けを特定する別の方法を示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    この場合、4 つのデータ ドライブはすべて、同じ VG と 1 つの LV に含まれています。 実際の構成は異なる場合があります。

5. 現在のファイル システムの使用率を確認します。

    ``` bash
    df -h /datalvm*
    ```

    ![ファイル システムの使用率を確認するコードを示すスクリーンショット。 コマンドと結果が強調表示されています。](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 「[Azure マネージド ディスクの拡張](expand-disks.md#expand-an-azure-managed-disk)」の手順に従って、データ ディスクのサイズを変更します。 ポータル、CLI、または PowerShell を使用できます。

    >[!IMPORTANT]
    >VM の実行中に仮想ディスクのサイズを変更することはできません。 この手順では、お使いの VM の割り当ては解除してください。

7. VM を起動してから、`fdisk` を使用して新しいサイズを確認します。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![ディスク サイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    この場合、`/dev/sdd` は 5 G から 20 G にサイズ変更されました。

8. 現在の PV のサイズを確認します。

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![P V のサイズを確認するコードを示すスクリーンショット。結果が強調表示されています。](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    ディスクのサイズが変更された場合でも、PV は以前のサイズのままとなります。

9. PV のサイズを変更します。

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![物理ボリュームのサイズを変更するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. PV のサイズを確認します。

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![物理ボリュームのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    サイズを変更するディスクのすべてに同じ手順を適用します。

11. VG の情報を確認します。

    ``` bash
    vgdisplay vgname
    ```

    ![ボリューム グループの情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    これで、LV に割り当てるのに十分な領域が VG に確保されました。

12. LV のサイズを変更します。

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![L V のサイズを変更するコードを示すスクリーンショット。結果が強調表示されています。](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. ファイル システムのサイズを確認します。

    ``` bash
    df -h /datalvm2
    ```

    ![ファイル システムのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>新しい PV を追加することで LVM-on-crypt ボリュームを拡張する

新しい PV を追加することで LVM-on-crypt ボリュームを拡張することもできます。 この方法は、「[暗号化されたデバイスで LVM と RAID を構成する](how-to-configure-lvm-raid-on-crypt.md#general-steps)」の手順に厳密に従っています。 新しいディスクを追加し、それを LVM-on-crypt 構成で設定する方法について説明しているセクションをご覧ください。

この方法を使用して、既存の LV に領域を追加できます。 また、新しい VG または LV を作成することもできます。

1. 使用する VG の現在のサイズを確認します。

    ``` bash
    vgdisplay vgname
    ```

    ![ボリューム グループのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 展開するファイル システムと LV のサイズを確認します。

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![ローカル ボリュームのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![ファイル システムのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. 新しいデータ ディスクを VM に追加し、それを特定します。

    新しいディスクを追加する前に、ディスクを確認します。

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![ディスクのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    新しいディスクを追加する前にディスクを確認する別の方法は次のとおりです。

    ``` bash
    lsblk
    ```

    ![ディスクのサイズを確認する代替コードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    新しいディスクを追加する際は、PowerShell、Azure CLI、または Azure portal を使用できます。 詳細については、[Linux VM へのデータ ディスクのアタッチ](attach-disk-portal.md)に関する記事をご覧ください。

    カーネル名のスキームは、新しく追加されたデバイスに適用されます。 通常、新しいドライブには、使用可能な次の文字が割り当てられます。 この場合、追加されたディスクは `sdd` です。

4. ディスクを調べて、新しいディスクが追加されていることを確認します。

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![ディスクを一覧表示するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![出力に新しく追加されたディスクが表示されているようすを示すスクリーンショット。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 最近追加されたディスクの上にファイル システムを作成します。 そのディスクを、`/dev/disk/azure/scsi1/` のリンクされたデバイスと一致させます。

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![ファイル システムを作成するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![ファイル システムを作成し、そのディスクをリンクされたデバイスと一致させる別のコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 新しく追加されたディスク用の一時マウント ポイントを作成します。

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 最近作成されたファイル システムを `/etc/fstab` に追加します。

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 新しく作成したファイル システムをマウントします。

    ``` bash
    mount -a
    ```

9. 新しいファイル システムがマウントされていることを確認します。

    ``` bash
    df -h
    ```

    ![ファイル システムがマウントされていることを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![ファイル システムがマウントされていることを確認する別のコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 以前データ ドライブ用に開始した暗号化を再開します。

    >[!TIP]
    >LVM-on-crypt の場合、`EncryptFormatAll` を使用することをお勧めします。 そうしないと、追加のディスクを設定している間に、二重暗号化が発生する可能性があります。
    >
    >詳細については、「[暗号化されたデバイスで LVM と RAID を構成する](how-to-configure-lvm-raid-on-crypt.md)」をご覧ください。

    次に例を示します。

    ``` bash
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

    暗号化が完了すると、新しく追加されたディスクに crypt レイヤーが表示されます。

    ``` bash
    lsblk
    ```

    ![crypt レイヤーを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 新しいディスクの暗号化されたレイヤーのマウントを解除します。

    ``` bash
    umount ${newmount}
    ```

12. 現在の PV の情報を確認します。

    ``` bash
    pvs
    ```

    ![物理ボリュームに関する情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. ディスクの暗号化されたレイヤーの上に PV を作成します。 先程の `lsblk` コマンドからデバイス名を取得します。 デバイス名の前に `/dev/` マッパーを追加して PV を作成します。

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![暗号化されたレイヤー上に物理ボリュームを作成するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    現在の `ext4 fs` シグネチャのワイプに関する警告が表示されます。 この警告は想定されています。 この質問には `y` で回答します。

14. 新しい PV が LVM 構成に追加されたことを確認します。

    ``` bash
    pvs
    ```

    ![物理ボリュームが LVM 構成に追加されたことを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 増やす必要がある VG に新しい PV を追加します。

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![ボリューム グループに物理ボリュームを追加するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. VG の新しいサイズと空き領域を確認します。

    ``` bash
    vgdisplay vgname
    ```

    ![ボリューム グループのサイズと空き領域を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    `Total PE` の数と `Free PE / Size` の増加に注意してください。

17. LV とファイル システムのサイズを増やします。 `lvextend` で `-r` オプションを使用します。 この例では、VG 内の使用可能な領域の合計を、指定された LV に追加しています。

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![ローカル ボリュームとファイル システムのサイズを増やすコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

次の手順に従って、変更を確認します。

1. LV のサイズを確認します。

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![ローカル ボリュームの新しいサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. ファイル システムの新しいサイズを確認します。

    ``` bash
    df -h mountpoint
    ```

    ![ファイル システムの新しいサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. LVM レイヤーが暗号化されたレイヤーの上にあることを確認します。

    ``` bash
    lsblk
    ```

    ![LVM レイヤーが暗号化されたレイヤーの上にあることを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    オプションなしで `lsblk` を使用すると、マウント ポイントが複数回表示されます。 このコマンドでは、デバイスと LV で並べ替えが行われます。 

    `lsblk -fs` を使用することができます。 このコマンドでは、`-fs` によって並べ替え順序が反転され、マウント ポイントが 1 回だけ表示されるようになります。 ディスクは複数回表示されます。

    ``` bash
    lsblk -fs
    ```

    ![LVM レイヤーが暗号化されたレイヤーの上にあることを確認する代替コードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>既存の PV のサイズを変更して、crypt ボリューム上の LVM を拡張する

1. 使用する暗号化されたディスクを特定します。

    ``` bash
    lsblk
    ```

    ![暗号化されたディスクを特定するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![暗号化されたディスクを特定する代替コードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. 使用する PV の情報を確認します。

    ``` bash
    pvs
    ```

    ![物理ボリュームの情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. 使用する VG の情報を確認します。

    ``` bash
    vgs
    ```

    ![ボリューム グループの情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. 使用する LV の情報を確認します。

    ``` bash
    lvs
    ```

    ![ローカル ボリュームの情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. ファイル システムの使用率を確認します。

    ``` bash
    df -h /mountpoint(s)
    ```

    ![使用されているファイル システムの量を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 使用するディスクのサイズを確認します。

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![ディスクのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. データ ディスクのサイズを変更します。 ポータル、CLI、または PowerShell を使用できます。 詳細については、[Linux VM での仮想ハード ディスクの拡張](expand-disks.md#expand-an-azure-managed-disk)に関するページの、ディスクのサイズ変更に関するセクションをご覧ください。 

    >[!IMPORTANT]
    >VM の実行中に仮想ディスクのサイズを変更することはできません。 この手順では、お使いの VM の割り当ては解除してください。

8. 使用するディスクのサイズを確認します。

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![ディスク サイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    この場合では、両方のディスクのサイズが 2 GB から 4 GB に変更されています。 しかし、ファイル システム、LV、PV のサイズに変更はありません。

9. 現在の PV のサイズを確認します。 LVM-on-crypt では、PV は `/dev/sd*` デバイスではなく `/dev/mapper/` デバイスであることに注意してください。

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![現在の物理ボリュームのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. PV のサイズを変更します。

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![物理ボリュームのサイズを変更するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 新しい PV のサイズを確認します。

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![物理ボリュームのサイズを確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. PV 上の暗号化されたレイヤーのサイズを変更します。

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    サイズを変更するディスクのすべてに同じ手順を適用します。

13. 使用する VG の情報を確認します。

    ``` bash
    vgdisplay vgname
    ```

    ![ボリューム グループの情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    これで、LV に割り当てるのに十分な領域が VG に確保されました。

14. LV の情報を確認します。

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![ローカル ボリュームの情報を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. ファイル システムの使用率を確認します。

    ``` bash
    df -h /mountpoint
    ```

    ![ファイル システムの使用率を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. LV のサイズを変更します。

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![ローカル ボリュームのサイズを変更するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    ここでは、`-r` オプションを使用して、ファイル システムのサイズも変更します。

17. LV の情報を確認します。

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![ローカル ボリュームに関する情報を取得するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. ファイル システムの使用率を確認します。

    ``` bash
    df -h /mountpoint
    ```

    ![ファイル システムの使用率を確認するコードを示すスクリーンショット。 結果が強調表示されています。](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

同じサイズ変更プロシージャを、それを必要とする他のすべての LV に適用します。

## <a name="next-steps"></a>次のステップ

[Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)