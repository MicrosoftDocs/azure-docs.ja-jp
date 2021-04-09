---
title: Azure VMware Solution by CloudSimple - Azure でプライベート クラウド VM を管理する
description: ディスクの追加、VM 容量の変更、ネットワーク インターフェイスの追加など、Azure portal で CloudSimple プライベート クラウドVM を管理する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895191"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Azure で CloudSimple プライベート クラウドの仮想マシンを管理する

[CloudSimple プライベート クラウド用に作成した](azure-create-vm.md)仮想マシンを管理するには、[Azure portal](https://portal.azure.com) にサインインします。 仮想マシンを探して選択します (サイド メニューの **[すべてのサービス]** または **[仮想マシン]** から検索します)。

## <a name="control-virtual-machine-operation"></a>仮想マシンの運用をコントロールする

選択した仮想マシンの **[概要]** ページから、以下のコントロールを使用できます。

| コントロール | 説明 |
| ------------ | ------------- |
| 接続 | 指定された VM ポートに接続します。  |
| 開始 | 指定された VM を起動します。  |
| やり直し | 指定された VM をシャットダウンした後、電源を入れます。  |
| Stop | 特定の VM をシャットダウンします。  |
| キャプチャ | 指定された VM のイメージをキャプチャして、他の VM を作成するためのイメージとして使用できるようにします。 「[Azure で一般化された VM のマネージド イメージを作成する](../virtual-machines/windows/capture-image-resource.md)」を参照してください。   |
| 詳細ビュー | 指定された VM に移動します。  |
| 削除 | 指定された VM を削除します。  |
| 更新 | 表示内のデータを更新します。  |

### <a name="view-performance-information"></a>パフォーマンス情報の表示

**[概要]** ページの下部領域のグラフには、選択した間隔 (過去 1 時間から過去 30 日間。既定では過去 1 時間) のパフォーマンス データが表示されます。 各グラフ内で、カーソルをグラフ上で前後に動かすことによって、間隔内の任意の時点の数値を表示できます。

次のグラフが表示されます。

| アイテム | 説明 |
| ------------ | ------------- |
| CPU (average) (CPU (平均)) | 選択された間隔を通しての平均 CPU 使用率 (%)。   |
| ネットワーク | 選択された間隔を通してネットワークを出入りしたトラフィック (MB)。  |
| Disk Bytes (ディスクのバイト数) | 選択された間隔を通してディスクから読み取られた、またディスクに書き込まれたデータの合計 (MB)。  |
| Disk Operations (ディスク操作) | 選択された間隔を通してのディスク操作の平均速度 (操作数/秒)。 |

## <a name="manage-vm-disks"></a>VM ディスクの管理

VM ディスクを追加するには、選択した VM の **[ディスク]** ページを開きます。 ディスクを追加するには、**[ディスクの追加]** をクリックします。 インライン オプションを入力または選択して、次の各設定を構成します。 **[保存]** をクリックします。

   | アイテム | 説明 |
   | ------------ | ------------- |
   | 名前 | ディスクを識別する名前を入力します。  |
   | サイズ | 使用可能なサイズのいずれかを選択します。  |
   | SCSI コントローラー | SCSI コントローラーを選択します。 使用可能なコントローラーは、サポートされているオペレーティング システムによって異なります。  |
   | モード | ディスクがスナップショットに参加する方法を決定します。 次のいずれかのオプションを選択します。 <br> - 独立永続: ディスクに書き込まれたすべてのデータが永続的に書き込まれます。<br> - 独立非永続: ディスクに書き込まれた変更は、仮想マシンの電源をオフにしたとき、またはリセットしたときに破棄されます。  このモードでは、常に同じ状態で VM を再起動できます。 詳細については、[VMware のドキュメント](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)を参照してください。 |

ディスクを削除するには、ディスクを選択して **[削除]** をクリックします。

## <a name="change-the-capacity-of-the-vm"></a>VM の容量を変更する

VM の容量を変更するには、選択した VM の **[サイズ]** ページを開きます。 次のいずれかを指定し、**[保存]** をクリックします。

| アイテム | 説明 |
| ------------ | ------------- |
| コア数 | VM に割り当てられているコアの数。  |
| Hardware virtualization (ハードウェア仮想化) | ハードウェア仮想化をゲスト OS に公開するには、チェックボックスをオンにします。 VMware の記事「[Expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)」 (VMware ハードウェア アシストによる仮想化の公開) を参照してください。 |
| メモリ サイズ | VM に割り当てるメモリの量を選択します。  

## <a name="manage-network-interfaces"></a>ネットワーク インターフェイスを管理する

インターフェイスを追加するには、**[Add network interface]\(ネットワーク インターフェイスの追加\)** をクリックします。 インライン オプションを入力または選択して、次の各設定を構成します。 **[保存]** をクリックします。

   | コントロール | 説明 |
   | ------------ | ------------- |
   | 名前 | インターフェイスを識別する名前を入力します。  |
   | ネットワーク | プライベート クラウドの vSphere で構成されているネットワークのリストから選択します。  |
   | アダプター | VM 用に構成されている使用可能な種類のリストから vSphere アダプターを選択します。 詳細については、[仮想マシンのネットワーク アダプターの選択](https://kb.vmware.com/s/article/1001805)に関する VMware のサポート技術情報記事を参照してください。 |
   | Power on at Boot\(起動時の電源投入\) | VM が起動したときに NIC ハードウェアを有効にするかどうかを選択します。 既定値は **[有効化]** です。 |

ネットワーク インターフェイスを削除するには、対象を選択して **[削除]** をクリックします。
