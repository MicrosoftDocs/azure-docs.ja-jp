---
title: 第 2 世代 VM に対する Azure のサポート (プレビュー) | Microsoft Docs
description: 第 2 世代 VM に対する Azure のサポートの概要
services: virtual-machines-windows
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/10/2019
ms.author: lahugh
ms.openlocfilehash: 5e342418dc6cc9ed0a3bbbfaad42801d5ffe9e9d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900255"
---
# <a name="support-for-generation-2-vms-preview-on-azure"></a>Azure での第 2 世代 VM (プレビュー) のサポート

> [!IMPORTANT]
> 第 2 世代 VM に対する Azure のサポートは現在、プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

第 2 世代仮想マシン (VM) のサポートが、Azure でプレビューで使用できるようになりました。 仮想マシンを作成した後にその世代を変更することはできないため、世代を選択する前に、このページの考慮事項を確認してください。 

第 2 世代 VM では、第 1 世代 VM ではサポートされていない重要な機能がサポートされています。 これらの機能には、メモリの増加、Intel ソフトウェア ガード エクステンションズ (Intel SGX)、および仮想化された永続メモリ (vPMEM) が含まれます。 また、第 2 世代 VM には、Azure ではまだサポートされていない機能もいくつか含まれています。 詳細については、「[特徴と機能](#features-and-capabilities)」セクションを参照してください。

第 2 世代 VM では、第 1 世代 VM で使用されている BIOS ベースのアーキテクチャではなく、新しい UEFI ベースのブート アーキテクチャが使用されます。 第 1 世代 VM と比較して、第 2 世代 VM ではブート時間やインストール時間が改善されている可能性があります。 第 2 世代 VM の概要、および第 1 世代と第 2 世代のいくつかの違いについては、「[Should I create a generation 1 or 2 virtual machine in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)」(Hyper-V で第 1 世代または第 2 世代のどちらの仮想マシンを作成すべきか) をご覧ください。

## <a name="generation-2-vm-sizes"></a>第 2 世代 VM のサイズ

第 1 世代 VM は、Azure のすべての VM サイズでサポートされています。 Azure では現在、次の選択された VM シリーズに対して第 2 世代のプレビュー サポートが提供されています。

* [B シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DC シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dc-series)
* [Dsv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv2-series)および [Dsv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)
* [Esv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#esv3-series)
* [Fsv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute#fsv2-series-1)
* [GS シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hb-series)
* [HC シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#hc-series)
* [Ls シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series)と [Lsv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-storage#lsv2-series)
* [Mv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#mv2-series)
* [NCv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv2-series)と [NCv3 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#ncv3-series)
* [ND シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nd-series)
* [NVv2 シリーズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu#nvv3-series--1)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace の第 2 世代 VM のイメージ

第 2 世代 VM では、次の Marketplace イメージがサポートされています。

* Windows Server 2019 Datacenter
* Windows Server 2016 Datacenter
* Windows Server 2012 R2 Datacenter
* Windows Server 2012 Datacenter
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4

## <a name="on-premises-vs-azure-generation-2-vms"></a>オンプレミスと Azure 第 2 世代 VM の比較

Azure では現在、オンプレミスの Hyper-V が第 2 世代 VM に対してサポートする機能の一部がサポートされていません。

| 第 2 世代の機能                | オンプレミス Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| セキュア ブート                         | :heavy_check_mark:  | :x:   |
| シールドされた VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| 仮想化ベースのセキュリティ (VBS) | :heavy_check_mark:  | :x:   |
| VHDX 形式                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>特徴と機能

### <a name="generation-1-vs-generation-2-features"></a>第 1 世代と第 2 世代の特徴の比較

| 機能 | 第 1 世代 | 第 2 世代 |
|---------|--------------|--------------|
| ブート             | PCAT                      | UEFI                               |
| ディスク コントローラー | IDE                       | SCSI                               |
| VM サイズ         | すべての VM サイズ | Premium Storage をサポートする VM のみ |

### <a name="generation-1-vs-generation-2-capabilities"></a>第 1 世代と第 2 世代の機能の比較

| 機能 | 第 1 世代 | 第 2 世代 |
|------------|--------------|--------------|
| 2 TB より大きい OS ディスク                    | :x:                        | :heavy_check_mark: |
| カスタム ディスク/イメージ/スワップ OS         | :heavy_check_mark:         | :heavy_check_mark: |
| 仮想マシン スケール セットのサポート | :heavy_check_mark:         | :heavy_check_mark: |
| ASR/バックアップ                        | :heavy_check_mark:         | :x:                |
| 共有イメージ ギャラリー              | :heavy_check_mark:         | :x:                |
| Azure Disk Encryption             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>第 2 世代 VM の作成

### <a name="marketplace-image"></a>Marketplace イメージ

Azure portal または Azure CLI では、UEFI ブートをサポートする Marketplace イメージから第 2 世代 VM を作成できます。

`windowsserver-gen2preview` サービスには、Windows 第 2 世代イメージのみが含まれます。 このパッケージングにより、第 1 世代と第 2 世代のイメージの間の混乱が回避されます。 第 2 世代 VM を作成するには、このサービスから **[イメージ]** を選択し、標準のプロセスに従って VM を作成します。

現在、Marketplace では、次の Windows 第 2 世代イメージが提供されています。

* 2019-datacenter-gen2
* 2016-datacenter-gen2
* 2012-r2-datacenter-gen2
* 2012-datacenter-gen2

サポートされている Marketplace イメージの現在の一覧については、「[特徴と機能](#features-and-capabilities)」のセクションを参照してください。

### <a name="managed-image-or-managed-disk"></a>マネージド イメージまたはマネージド ディスク

第 2 世代 VM は、第 1 世代 VM を作成する場合と同じ方法でマネージド イメージまたはマネージド ディスクから作成できます。

### <a name="virtual-machine-scale-sets"></a>仮想マシン スケール セット

第 2 世代 VM はまた、仮想マシン スケール セットを使用して作成することもできます。 Azure CLI では、Azure スケール セットを使用して第 2 世代 VM を作成します。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

* **第 2 世代 VM はすべての Azure リージョンで利用できますか?**  
    はい。 ただし、どのリージョンでも[第 2 世代 VM のサイズ](#generation-2-vm-sizes)のすべてを使用できるわけではありません。 第 2 世代 VM の可用性は、VM サイズの可用性によって異なります。

* **第 1 世代 VM と第 2 世代 VM の価格に違いはありますか?**  
   いいえ。

* **オンプレミスに第 2 世代の VM の .vhd ファイルがあります。この .vhd ファイルを使用して、Azure に第 2 世代の VM を作成できますか?**
  はい。ご自身の第 2 世代の .vhd ファイルを Azure に取り込み、それを使用して第 2 世代の VM を作成できます。 それには、次の手順を実行してください。
    1. ご自身の VM を作成するのと同じリージョンのストレージ アカウントに .vhd をアップロードします。
    1. .vhd ファイルからマネージド ディスクを作成します。 HyperV Generation プロパティを V2 に設定します。 HyperV Generation プロパティは、マネージド ディスクの作成時に、次の PowerShell コマンドによって設定されます。

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. ディスクが使用可能になったら、このディスクを接続して VM を作成します。 作成される VM は第 2 世代 VM です。
    第 2 世代の VM の作成時には、必要に応じてこの VM のイメージを汎用化できます。 汎用化したイメージは、作成する複数の VM で使用できます。

* **OS ディスク サイズを増やすにはどうすればよいですか?**  
  第 2 世代 VM の新規のものとして、2 TB を超える OS ディスクがあります。 既定では、第 2 世代 VM の OS ディスクは 2 TB 未満です。 ディスク サイズは、4 TB の推奨される最大サイズまで増やすことができます。 OS ディスク サイズを増やすには、Azure CLI または Azure portal を使用します。 プログラムでディスクを拡張する方法については、[ディスクのサイズ変更](expand-os-disk.md)に関するページを参照してください。

  Azure portal から OS ディスク サイズを増やすには:

  1. Azure portal で、VM プロパティ ページに移動します。
  1. VM をシャットダウンして割り当てを解除するには、 **[停止]** ボタンを選択します。
  1. **[ディスク]** セクションで、サイズを増やす OS ディスクを選択します。
  1. **[ディスク]** セクションで、 **[構成]** を選択し、 **[サイズ]** を目的の値に更新します。
  1. VM プロパティ ページに戻り、VM を **[開始]** します。
  
  2 TB を超える OS ディスクに対して警告が表示されることがあります。 この警告は、第 2 世代 VM には適用されません。 ただし、4 TB を超える OS ディスク サイズは "*お勧めできません*"。

* **第 2 世代 VM では高速ネットワークはサポートされていますか?**  
    はい。 詳細については、[高速ネットワークを使用した VM の作成](../../virtual-network/create-vm-accelerated-networking-cli.md)に関するページを参照してください。

* **第 2 世代では VHDX はサポートされていますか?**  
    いいえ。第 2 世代 VM では VHD のみがサポートされています。

* **第 2 世代 VM では Azure Ultra Disk Storage はサポートされていますか?**  
    はい。

* **VM を第 1 世代から第 2 世代に移行できますか?**  
    いいえ。VM を作成した後にその世代を変更することはできません。 VM の世代を切り替える必要がある場合は、異なる世代の新しい VM を作成してください。

## <a name="next-steps"></a>次の手順

* [Hyper-V の第 2 世代仮想マシン](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)について学習します。

* オンプレミス システムから Azure にアップロードする [VHD を準備する](prepare-for-upload-vhd-image.md)方法を学習します。
