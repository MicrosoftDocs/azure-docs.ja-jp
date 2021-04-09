---
title: 'クイック スタート: Azure で VMware VM を使用する'
titleSuffix: Azure VMware Solution by CloudSimple
description: Azure VMware Solution by CloudSimple を使用して、Azure portal から VMware VM を構成して使用する方法を学習します
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77019555"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>クイック スタート - Azure で VMware VM を使用する

Azure portal で仮想マシンを作成するには、CloudSimple 管理者がサブスクリプションに対して有効にした仮想マシン テンプレートを使用します。 これらの VM テンプレートは VMware インフラストラクチャ内にあります。

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Azure での CloudSimple VM の作成には VM テンプレートが必要

vCenter UI からプライベート クラウド上に仮想マシンを作成します。 テンプレートを作成するには、「[Clone a Virtual Machine to a Template in the vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html)」 (vSphere Web Client でテンプレートに仮想マシンを複製する) の手順に従います。 プライベート クラウド vCenter に VM テンプレートを格納します。

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Azure portal で仮想マシンを作成する

1. **[すべてのサービス]** を選択します。

2. **CloudSimple 仮想マシン** を検索します。

3. **[追加]** をクリックします。

    ![CloudSimple 仮想マシンを作成する](media/create-cloudsimple-virtual-machine.png)

4. 基本情報を入力し、**[次へ:サイズ]** をクリックします。

    ![CloudSimple 仮想マシンを作成する - 基本](media/create-cloudsimple-virtual-machine-basic-info.png)

    | フィールド | 説明 |
    | ------------ | ------------- |
    | サブスクリプション | プライベート クラウドに関連付けられている Azure サブスクリプション。  |
    | リソース グループ | VM が割り当てられるリソース グループ。 既存のグループを選択することも、新しいものを作成することもできます。 |
    | 名前 | VM を識別する名前。  |
    | 場所 | この VM がホストされている Azure リージョン。  |
    | プライベート クラウド | 仮想マシンを作成する CloudSimple プライベート クラウド。 |
    | リソース プール | VM のマップされたリソース プール。 使用可能なリソース プールから選択します。 |
    | vSphere テンプレート | VM の vSphere テンプレート。  |
    | ユーザー名 | VM 管理者のユーザー名 (Windows テンプレートの場合)。|
    | Password |  VM 管理者のパスワード (Windows テンプレートの場合)。 |
    | [パスワードの確認入力] | パスワードを確認入力します。 |

5. VM のコア数とメモリ容量を選択して、**[次へ:構成]** をクリックします。 ゲスト オペレーティング システムに完全な CPU 仮想化を公開する場合は、チェックボックスをオンにします。 ハードウェアの仮想化を必要とするアプリケーションは、バイナリ変換や準仮想化をせずに、仮想マシン上で実行できます。 詳細については、VMware の記事「<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Expose VMware Hardware Assisted Virtualization</a>」 (VMware ハードウェア アシストによる仮想化の公開) を参照してください。

    ![CloudSimple 仮想マシンを作成する - サイズ](media/create-cloudsimple-virtual-machine-size.png)

6. 以下の表の説明に従って、ネットワーク インターフェイスとディスクを構成し、**[確認および作成]** をクリックします。

    ![CloudSimple 仮想マシンを作成する - 構成](media/create-cloudsimple-virtual-machine-configurations.png)

    ネットワーク インターフェイスでは、**[Add network interface]\(ネットワーク インターフェイスの追加\)** をクリックして、次の設定を構成します。

    | コントロール | 説明 |
    | ------------ | ------------- |
    | 名前 | インターフェイスを識別する名前を入力します。  |
    | ネットワーク | プライベート クラウド vSphere で構成されている分散ポート グループのリストから選択します。  |
    | アダプター | VM 用に構成されている使用可能な種類のリストから vSphere アダプターを選択します。 詳細については、<a href="https://kb.vmware.com/s/article/1001805" target="_blank">仮想マシンのネットワーク アダプターの選択</a>に関する VMware のサポート技術情報記事を参照してください。 |
    | Power on at Boot\(起動時の電源投入\) | VM が起動したときに NIC ハードウェアを有効にするかどうかを選択します。 既定値は **[有効化]** です。 |

    ディスクでは、**[ディスクの追加]** をクリックし、次の設定を構成します。

    | アイテム | 説明 |
    | ------------ | ------------- |
    | 名前 | ディスクを識別する名前を入力します。  |
    | サイズ | 使用可能なサイズのいずれかを選択します。  |
    | SCSI コントローラー | ディスクの SCSI コントローラーを選択します。  |
    | モード | ディスクがスナップショットに参加する方法を決定します。 次のいずれかのオプションを選択します。 <br> - 独立永続 - ディスクに書き込まれたすべてのデータが永続的に書き込まれます。<br> - 独立非永続 - ディスクに書き込まれた変更は、仮想マシンの電源をオフにしたとき、またはリセットしたときに破棄されます。  独立非永続モードでは、常に同じ状態で VM を再起動できます。 詳細については、<a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware のドキュメント</a>を参照してください。

7. 検証が完了したら、設定を確認し、**[作成]** をクリックします。 変更を加えるには、上部にあるタブをクリックします。

    ![CloudSimple 仮想マシンを作成する - 確認](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>次のステップ

* [CloudSimple 仮想マシンのリストを表示する](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Azure から CloudSimple 仮想マシンを管理する](azure-manage-vm.md)
