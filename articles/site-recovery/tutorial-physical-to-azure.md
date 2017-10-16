---
title: "Azure Site Recovery を使用して Azure に物理的オンプレミス サーバーのディザスター リカバリーを設定する |Microsoft Docs"
description: "Azure Site Recovery サービスを使用して Azure にオンプレミス Windows/Linux サーバーのディザスター リカバリーを設定する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 805f6946-c6da-491f-980e-bf724bebdf0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: raynew
ms.openlocfilehash: 2f9a5092d4dacce8aa1630830499635b3b4eb151
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Azure にオンプレミス物理サーバーのディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミス マシンと Azure Virtual Machines (VM) のレプリケーション、フェールオーバー、フェールバックを管理し、調整することでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、Azure にオンプレミス物理 Windows/Linux サーバーのディザスター リカバリーを設定する方法を紹介します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure とオンプレミスの前提条件を設定する
> * Site Recovery の Recovery Services コンテナーを作成する 
> * ソース レプリケーション環境とターゲット レプリケーション環境を設定する
> * レプリケーション ポリシーを作成する
> * サーバーのレプリケーションを有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [シナリオのアーキテクチャとコンポーネント](concepts-physical-to-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](site-recovery-support-matrix-to-azure.md)を確認すること。
- レプリケートするサーバーが [Azure VM 要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していること。
- Azure を準備します。 Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントが必要です。
- レプリケートする各サーバーにモビリティ サービスを自動インストールするためのアカウントを準備します。



### <a name="set-up-an-azure-account"></a>Azure アカウントを設定する

Microsoft [Azure アカウント](http://azure.microsoft.com/)を取得します。

- アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
- [Site Recovery の価格](site-recovery-faq.md#pricing)について理解し、[価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)を確認します。
- Site Recovery の[サポート対象のリージョン](https://azure.microsoft.com/pricing/details/site-recovery/)を見つけます。

### <a name="verify-azure-account-permissions"></a>Azure アカウントのアクセス許可を確認する

VM を Azure にレプリケートするアクセス許可がお使いの Azure アカウントに与えられていることを確認します。

- Azure にマシンをレプリケートするために必要な[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)を確認します。
- [ロールベースのアクセス許可](../active-directory/role-based-access-control-configure.md)を確認し、変更します。 



### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップする

[Azure ネットワーク](../virtual-network/virtual-network-get-started-vnet-subnet.md)をセットアップします。

- Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
- ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。


## <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定

[Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md#create-a-storage-account)を設定します。

- Site Recovery は、オンプレミスのマシンを Azure Storage にレプリケートします。 Azure VM は、フェールオーバーの発生後にストレージから作成されます。
- ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
- このストレージ アカウントには、Standard または [Premium](../storage/common/storage-premium-storage.md) を使用できます。
- Premium アカウントを設定する場合は、ログ データ用の Standard アカウントも別途必要になります。



### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートする各サーバーにインストールする必要があります。 サーバーのレプリケーションを有効にすると、Site Recovery がこのサービスを自動的にインストールします。 自動的にインストールするには、Site Recovery でサーバーへのアクセスに使用するアカウントを準備する必要があります。

- ドメイン アカウントまたはローカル アカウントを使用できます。
- Windows VM の場合、ドメイン アカウントを使用していなければ、次のようにしてローカル マシンでのリモート ユーザー アクセス制御を無効にします。 無効にするには、レジスタで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に、値 1 を指定した DWORD エントリの **LocalAccountTokenFilterPolicy** を追加します。
- CLI からレジストリ エントリを追加し、設定を無効にするには、次のように入力します。       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux の場合、アカウントは、ソース Linux サーバーの root である必要があります。


## <a name="create-a-vault"></a>コンテナーの作成

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>保護の目標を選択する

レプリケート対象とレプリケート場所を選択します。

1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャを準備する]** > **[保護の目標]** をクリックします。
3. **[保護の目標]** で、**[To Azure]\(Azure へ\)** > **[非仮想化/その他]** を選択します。

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]** > **[インフラストラクチャを準備する]** > **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後 5 日間有効です。

   ![Set up source](./media/tutorial-physical-to-azure/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>構成サーバーのコンテナーへの登録

開始する前に次の作業を行います。 

- 構成サーバー マシンのシステム クロックが[タイム サーバー](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)と同期していることを確認します。 これは一致している必要があります。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。
- マシンが次の URL にアクセスできることを確認します。       [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- IP アドレスベースのファイアウォール規則で Azure への通信を許可する必要があります。
- [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
- ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。

ローカル管理者として統合セットアップを実行し、構成サーバーをインストールします。 プロセス サーバーとマスター ターゲット サーバーも既定で構成サーバーにインストールされます。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

登録が完了すると、コンテナーの **[設定]** の  > **[サーバー]** ページに構成サーバーが表示されます。


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲットのデプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

   ![[ターゲット]](./media/tutorial-physical-to-azure/network-storage.png)


## <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

1. 新しいレプリケーション ポリシーを作成するには、**[Site Recovery インフラストラクチャ]** > **[レプリケーション ポリシー]** > **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、回復ポイントの目標 (RPO) の上限を指定します。 この値で、データの復旧ポイントを作成する頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]**で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。 **[OK]** をクリックしてポリシーを作成します。

    ![Replication policy](./media/tutorial-physical-to-azure/replication-policy.png)


このポリシーは自動的に構成サーバーに関連付けられます。 既定でフェールバックの照合ポリシーが自動的に作成されます。 たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシー **rep-policy-failback** が作成されます。 このポリシーは、Azure からフェールバックを開始するまで使用されません。

## <a name="enable-replication"></a>Enable replication

各サーバーのレプリケーションを有効にします。

- レプリケーションを有効にすると、Site Recovery がモビリティ サービスをインストールします。
- サーバーのレプリケーションを有効にすると、変更が反映されてポータルに表示されるまで 15 分以上かかる場合があります。

1. **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。
2. **[ソース]** で、構成サーバーを選択します。
3. **[マシンの種類]** で、**[物理マシン]** を選択します。
4. プロセス サーバー (構成サーバー) を選択します。 次に、 **[OK]**をクリックします
5. **[ターゲット]** で、サブスクリプションと、フェールオーバー後に Azure VM を作成するリソース グループを選択します。 Azure で使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。
6. データのレプリケーションに使用する Azure Storage アカウントを選択します。 
7. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。
8. 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 
9. **[物理マシン]** で **[+物理マシン]** をクリックします。 名前と IP アドレスを指定します。 レプリケートするマシンのオペレーティング システムを選択します。 サーバーを検出し、一覧表示するのに数分かかります。 
10. **[プロパティ]** >  の **[プロパティの構成]** で、モビリティ サービスをマシンに自動的にインストールするためにプロセス サーバーが使用するアカウントを選択します。
11. **[レプリケーションの設定]** > **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 
12. **[レプリケーションを有効にする]**をクリックします。 **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。


追加したサーバーを監視する目的で、サーバーの最終検出時刻を **[構成サーバー]** の  > **[前回のアクセス]** で確認できます。 定期検出を待たずにマシンを追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** をクリックします。

## <a name="next-steps"></a>次のステップ

[ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)
