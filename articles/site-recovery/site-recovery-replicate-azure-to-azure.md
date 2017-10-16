---
title: "アプリケーションのレプリケート (Azure 間) | Microsoft Docs"
description: "この記事では、Azure の Azure リージョン間における仮想マシンのレプリケーションをセットアップする方法について説明します。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Azure 仮想マシンを別の Azure リージョンにレプリケートする



>[!NOTE]
>
> Azure 仮想マシンの Site Recovery レプリケーションは現在プレビューの段階です。

この記事では、Azure リージョン間での仮想マシンのレプリケーションをセットアップする方法について説明します。

## <a name="prerequisites"></a>前提条件

* この記事では、Site Recovery と Recovery Services コンテナーについて既に理解していることを前提としています。 "Recovery Services コンテナー" を事前に作成しておく必要があります。

    >[!NOTE]
    >
    > VM をレプリケートする場所に "Recovery Services コンテナー" を作成することをお勧めします。 たとえば、ターゲットの場所が "米国中部" の場合は、"米国中部" にコンテナーを作成します。

* ネットワーク セキュリティ グループ (NSG) ルールまたはファイアウォール プロキシを使用して、Azure VM での送信インターネット接続へのアクセスを制御する場合は、必要な URL と IP を必ずホワイトリストに登録してください。 詳細については、[ネットワーク ガイダンスのドキュメント](./site-recovery-azure-to-azure-networking-guidance.md)を参照してください。

* オンプレミスと Azure のソースの場所が ExpressRoute または VPN で接続されている場合は、[Azure からオンプレミスへの ExpressRoute/VPN 構成に関する Site Recovery の考慮事項](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration)のドキュメントを参照してください。

* Azure ユーザー アカウントには、Azure 仮想マシンのレプリケーションを有効にするための特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。

* DR リージョンとして使用するターゲットの場所で VM を作成するには、Azure サブスクリプションを有効にする必要があります。 必要なクォータを有効にするには、サポートに連絡してください。

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Azure Site Recovery コンテナーからのレプリケーションの有効化
ここでは、Azure の "東アジア" で実行されている VM を、"東南アジア" にレプリケートします。 手順は次のとおりです。

 コンテナーの **[+ レプリケート ]** をクリックし、仮想マシンのレプリケーションを有効にします。

1. **ソース:** マシンのレプリケーション元 (ここでは **Azure**) を参照します。

2. **ソースの場所:** この Azure リージョンから仮想マシンを保護します。 ここでは、ソースの場所は "東アジア" です

3. **デプロイ モデル:** ソース マシンの Azure デプロイ モデルを参照します。 クラシックまたは Resource Manager のいずれかを選択でき、特定のモデルに所属するマシンが、次の手順で保護対象として表示されます。

      >[!NOTE]
      >
      > クラシック仮想マシンをレプリケートした場合は、クラシック仮想マシンとしてのみ復旧できます。 その仮想マシンを、Resource Manager 仮想マシンとして復旧することはできません。

4. **リソース グループ:** ソース仮想マシンが属するリソース グループです。 選択したリソース グループのすべての VM が、次の手順で保護対象として表示されます。

    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

**[仮想マシン] > [仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、[OK] をクリックします。
    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


[設定] セクションで、ターゲット サイトのプロパティを構成できます

1. **ターゲットの場所:** ソース仮想マシンのデータがレプリケートされる場所です。 選択したマシンの場所に応じて、適切なターゲット リージョンの一覧が表示されます。

    > [!TIP]
    > Recovery Services コンテナーのターゲットの場所と同じままにしておくことをお勧めします。

2. **ターゲット リソース グループ:** レプリケートされたすべての仮想マシンが属するリソース グループです。既定では、ASR によって新しいリソース グループがターゲット リージョンに作成されます。作成されたグループの名前には "asr" サフィックスが付いています。 ASR によって作成されたリソース グループが既に存在する場合は、そのグループが再利用されます。次のセクションで示すように、そのグループをカスタマイズすることもできます。    
3. **ターゲット仮想ネットワーク:** 既定では、新しい仮想ネットワークがターゲット リージョンに作成されます。作成されたネットワークの名前には "asr" サフィックスが付いています。 これはソース ネットワークにマップされ、今後の保護で使用されます。

    > [!NOTE]
    > [ネットワーク詳細を確認](site-recovery-network-mapping-azure-to-azure.md)して、ネットワーク マッピングの詳細情報を入手します。

4. **ターゲット ストレージ アカウント:** 既定では、ソース VM ストレージと同じ構成で、新しいターゲット ストレージ アカウントが作成されます。 ASR によって作成されたストレージ アカウントが既に存在する場合は、そのアカウントが再利用されます。

5. **キャッシュ ストレージ アカウント:** ASR では、キャッシュ ストレージと呼ばれる追加のストレージ アカウントがソース リージョンに必要です。 ソース VM で行われてた変更はすべて追跡され、キャッシュ ストレージ アカウントに送信されてから、ターゲットの場所にレプリケートされます。

6. **可用性セット:** 既定では、新しい可用性セットがターゲット リージョンに作成されます。作成された可用性セットの名前には "asr" サフィックスが付いています。 ASR によって作成された可用性セットが既に存在する場合は、その可用性セットが再利用されます。

7.  **レプリケーション ポリシー:** 復旧ポイントのリテンション期間の履歴とアプリ整合性スナップショットの頻度の設定が定義されます。 既定では、既定の設定で新しいレプリケーション ポリシーが作成されます。つまり、このポリシーの復旧ポイントのリテンション期間は "24 時間" に、アプリ整合性スナップショットの頻度は "60 分" に設定されます。

    ![Enable replication](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>ターゲット リソースのカスタマイズ

ASR で使用される既定値を変更する必要がある場合は、必要に応じて、設定を変更できます。

1. **カスタマイズ:** クリックして、ASR で使用される既定値を変更します。

2. **ターゲット リソース グループ:** サブスクリプション内のターゲットの場所に存在するリソース グループの一覧から、リソース グループを選択できます。

3. **ターゲット仮想ネットワーク:** ターゲットの場所にある仮想ネットワークの一覧を確認できます。

4. **可用性セット:** 可用性セットの設定のみを、ソース リージョンの可用性の一部である仮想マシンにを追加できます。

5. **ターゲット ストレージ アカウント:**

![レプリケーションを有効にする](./media/site-recovery-replicate-azure-to-azure/customize.PNG) **[Create target resource]\(ターゲット リソースを作成する\)** と [レプリケーションを有効にする] をクリックします


仮想マシンが保護されたら、**[レプリケートされたアイテム]** の VM 正常性の状態を確認できます

>[!NOTE]
>初期レプリケーション中は、状態の更新に時間がかかり、しばらくの間の進行状況が表示されないことがあります。 最新の状態を確認するには、ブレードの上部にある [更新] ボタンをクリックすることができます。
>

![Enable replication](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>次のステップ
- テスト フェールオーバーの実行に関する[詳細を確認](site-recovery-test-failover-to-azure.md)する。
- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- [復旧計画を使用](site-recovery-create-recovery-plans.md)して RTO を削減する方法の詳細を確認する。
- フェールオーバー後の [Azure VM の再保護](site-recovery-how-to-reprotect.md)の詳細を確認する。
