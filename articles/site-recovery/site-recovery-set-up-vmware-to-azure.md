---
title: "ソース環境のセットアップ (VMware から Azure へ) | Microsoft Docs"
description: "この記事では、VMware 仮想マシンを Azure にレプリケートする前に、オンプレミス環境をセットアップする方法について説明します。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 1/10/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: eccc927550aef4c9cd71ffad64d2eddedb74326e
ms.openlocfilehash: 18627223bdb4f0986f07f17233ce7daf29cb7dd9
ms.lasthandoff: 02/13/2017

---

# <a name="set-up-the-source-environment-vmware-to-azure"></a>ソース環境のセットアップ (VMware から Azure へ)
> [!div class="op_single_selector"]
> * [VMware 仮想マシン](./site-recovery-set-up-vmware-to-azure.md)
> * [物理サーバー](./site-recovery-set-up-physical-to-azure.md)

この記事では、VMware 上で実行されている仮想マシンを Azure にレプリケートする前に、オンプレミス環境をセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事では、次のものが既に作成されていることを前提としています。
- Recovery Services コンテナー ([Azure Portal](http://portal.azure.com "Azure Portal") 内)。
- [自動検出](./site-recovery-vmware-to-azure.md#vmware-account-permissions)に使用できる、VMware vCenter 内の専用アカウント。
- 構成サーバーをインストールする仮想マシン。

## <a name="configuration-server-minimum-requirements"></a>構成サーバーの最小要件
構成サーバー ソフトウェアは、可用性の高い VMware 仮想マシン上にデプロイする必要があります。 次の表は、構成サーバーに最低限必要なハードウェア、ソフトウェア、およびネットワークの要件を示したものです。
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS ベースのプロキシ サーバーは、構成サーバーではサポートされません。

## <a name="choose-your-protection-goals"></a>保護の目標を選択する

1. Azure Portal で、**Recovery Services** コンテナー ブレードに移動し、コンテナーを選択します。
2. コンテナーのリソース メニューで、**[作業の開始]** > **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[Yes, with VMware vSphere Hypervisor (はい、VMware vSphere ハイパーバイザーを使用する)]** を選択します。 次に、 **[OK]**をクリックします

    ![Choose goals](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする
ソース環境のセットアップ作業には、主に&2; つのアクティビティが含まれます。

- Site Recovery を使用して構成サーバーをインストールし、登録する。
- Site Recovery をオンプレミスの VMware vCenter または vSphere EXSi ホストに接続して、オンプレミスの仮想マシンを検出する。

### <a name="step-1-install-and-register-a-configuration-server"></a>手順 1: 構成サーバーをインストールし、登録する

1. **[手順 1: インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。 **[ソースの準備]** で、構成サーバーがない場合は **[+ 構成サーバー]** をクリックして追加します。

    ![Set up source](./media/site-recovery-set-up-vmware-to-azure/set-source1.png)
2. **[サーバーの追加]** ブレードで、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 統合セットアップを実行する際には、登録キーが必要です。 キーは生成後&5; 日間有効です。

    ![Set up source](./media/site-recovery-set-up-vmware-to-azure/set-source2.png)
6. 構成サーバーとして使用するマシンで**Azure Site Recovery 統合セットアップ**を実行して、構成サーバー、プロセス サーバー、マスター ターゲット サーバーをインストールします。

#### <a name="run-azure-site-recovery-unified-setup"></a>Azure Site Recovery 統合セットアップを実行する

> [!TIP]
> コンピューターのシステム クロックの時刻と現地時刻との差が&5; 分を超えている場合は、構成サーバーの登録が失敗します。 インストールを開始する前に、システム クロックを[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期させてください。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 構成サーバーはコマンド ラインを使用してインストールすることもできます。 詳細については、[コマンドライン ツールを使用した構成サーバーのインストール](http://aka.ms/installconfigsrv)に関するページを参照してください。

#### <a name="add-the-vmware-account-for-automatic-discovery"></a>自動検出用の VMware アカウントを追加する

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="step-2-add-a-vcenter"></a>手順 2: vCenter を追加する
オンプレミス環境で実行されている仮想マシンを Azure Site Recovery で検出できるようにするには、VMware vCenter サーバーまたは vSphere ESXi ホストを Site Recovery に接続する必要があります。

**[+vCenter]** を選択して、VMware vCenter サーバーまたは VMware vSphere ESXi ホストの接続を開始します。

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>一般的な問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>次のステップ
Azure で[ターゲット環境を設定](./site-recovery-prepare-target-vmware-to-azure.md)します。

