---
title: Azure VMware Solution by CloudSimple - VM テンプレートを使用して Azure に仮想マシンを作成する
description: CloudSimple プライベート クラウドの VMware インフラストラクチャ上の VM テンプレートを使用して Azure に仮想マシンを作成する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898761"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>VMware インフラストラクチャ上の VM テンプレートを使用して Azure に仮想マシンを作成する

ご利用のサブスクリプションに対して CloudSimple 管理者が有効にした VMware インフラストラクチャ上の VM テンプレートを使用して、Azure portal で仮想マシンを作成することができます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-cloudsimple-virtual-machine"></a>CloudSimple 仮想マシンを作成する

1. **[すべてのサービス]** を選択します。

2. **CloudSimple 仮想マシン** を検索します。

3. **[追加]** をクリックします。

    ![CloudSimple 仮想マシンを作成する](media/create-cloudsimple-virtual-machine.png)

4. 基本情報を入力して、**[次へ:サイズ]** をクリックします。

    > [!NOTE]
    > Azure での CloudSimple 仮想マシンの作成には VM テンプレートが必要です。  この VM テンプレートは、プライベート クラウドの vCenter 上に存在している必要があります。  希望するオペレーティング システムと構成を使用して、vCenter UI からプライベート クラウド上に仮想マシンを作成します。  「[Clone a Virtual Machine to a Template in the vSphere Web Client](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)」(vSphere Web Client でテンプレートに仮想マシンを複製する) の指示に従って、テンプレートを作成します。

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
    | ユーザー名 | VM 管理者のユーザー名 (Windows テンプレートの場合)|
    | Password <br>[パスワードの確認入力] | VM 管理者のパスワード (Windows テンプレートの場合)。  |

5. VM のコア数とメモリ容量を選択して、**[次へ:構成]** をクリックします。 ハードウェア仮想化を必要とするアプリケーションを、バイナリ変換も準仮想化も使わずに仮想マシン上で実行できるよう、完全な CPU 仮想化をゲスト オペレーティング システムに公開する場合は、チェック ボックスをオンにします。 詳細については、VMware の記事「[Expose VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)」 (VMware ハードウェア アシストによる仮想化の公開) を参照してください。

    ![CloudSimple 仮想マシンを作成する - サイズ](media/create-cloudsimple-virtual-machine-size.png)

6. 以下の表の説明に従って、ネットワーク インターフェイスとディスクを構成し、**[確認および作成]** をクリックします。

    ![CloudSimple 仮想マシンを作成する - 構成](media/create-cloudsimple-virtual-machine-configurations.png)

    ネットワーク インターフェイスでは、**[Add network interface]\(ネットワーク インターフェイスの追加\)** をクリックして、次の設定を構成します。

    | コントロール | 説明 |
    | ------------ | ------------- |
    | 名前 | インターフェイスを識別する名前を入力します。  |
    | ネットワーク | プライベート クラウド vSphere で構成されている分散ポート グループのリストから選択します。  |
    | アダプター | VM 用に構成されている使用可能な種類のリストから vSphere アダプターを選択します。 詳細については、[仮想マシンのネットワーク アダプターの選択](https://kb.vmware.com/s/article/1001805)に関する VMware のサポート技術情報記事を参照してください。 |
    | Power on at Boot\(起動時の電源投入\) | VM が起動したときに NIC ハードウェアを有効にするかどうかを選択します。 既定値は **[有効化]** です。 |

    ディスクでは、**[ディスクの追加]** をクリックし、次の設定を構成します。

    | アイテム | 説明 |
    | ------------ | ------------- |
    | 名前 | ディスクを識別する名前を入力します。  |
    | サイズ | 使用可能なサイズのいずれかを選択します。  |
    | SCSI コントローラー | ディスクの SCSI コントローラーを選択します。  |
    | モード | ディスクがスナップショットに参加する方法を決定します。 次のいずれかのオプションを選択します。 <br> - 独立永続 - ディスクに書き込まれたすべてのデータが永続的に書き込まれます。<br> - 独立非永続 - ディスクに書き込まれた変更は、仮想マシンの電源をオフにしたとき、またはリセットしたときに破棄されます。  独立非永続モードでは、常に同じ状態で VM を再起動できます。 詳細については、[VMware のドキュメント](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)を参照してください。

7. 検証が完了したら、設定を確認し、**[作成]** をクリックします。 変更を加えるには、上部にあるタブをクリックします。

    ![CloudSimple 仮想マシンを作成する - 確認](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>CloudSimple 仮想マシンの一覧を表示する

1. **[すべてのサービス]** を選択します。

2. **CloudSimple 仮想マシン** を検索します。

3. プライベート クラウドが作成された場所を選択します。

    ![CloudSimple 仮想マシンの一覧](media/list-cloudsimple-virtual-machines.png)

CloudSimple 仮想マシンの一覧には、Azure portal から作成された仮想マシンが含まれます。  マップされた vCenter リソース プール内のプライベート クラウド vCenter 上に作成された仮想マシンが一覧に表示されます。  
