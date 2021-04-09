---
title: Azure Site Recovery を使用して Government とパブリック リージョンの間で Azure VM を移動する
description: Azure Site Recovery を使用して、Azure Government とパブリック リージョンの間で Azure VM を移動します。
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: a76ebf95b92b6e1251a04daa9ffb48a9abe15b50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425349"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure Government とパブリック リージョンの間で Azure VM を移動する 

[こちら](azure-to-azure-move-overview.md)で説明するように、既存の VM の可用性を高めたり管理を容易にしたりするため、またはガバナンス上の理由から、Azure Government とパブリック リージョンの間で IaaS VM を移動することが必要になる場合があります。

[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、ビジネス継続性およびディザスター リカバリー (BCDR) の目的でオンプレミス マシンや Azure VM のディザスター リカバリーを管理よび調整する他に、Azure VM をセカンダリ リージョンに移動するためにも Site Recovery を使用できます。       

このチュートリアルでは、Azure Site Recovery を使用して Azure Government とパブリック リージョンの間で Azure VM を移動する方法を説明します。 これを応用すれば、属しているクラスターが地理的に異なるリージョン ペア間で VM を移動することもできます。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件を確認する
> * ソース VM を準備する
> * ターゲット リージョンを準備する
> * ターゲット リージョンにデータをコピーする
> * 構成をテストする
> * 移動を実行する
> * ソース リージョンのリソースを破棄する

> [!IMPORTANT]
> このチュートリアルでは、Azure Government とパブリック リージョンの間、または Azure VM の通常のディザスター リカバリー ソリューションでサポートされていないリージョン ペア間で、Azure Site Recovery を使用して Azure VM を移動する方法について説明します。 ソース リージョンとターゲット リージョンのペアが[サポート対象](./azure-to-azure-support-matrix.md#region-support)である場合、移動については、こちらの[ドキュメント](azure-to-azure-tutorial-migrate.md)を参照してください。 可用性セット内の VM を別のリージョンのゾーン固定 VM に移動することで可用性を向上させることが要件である場合は、[こちら](move-azure-VMs-AVset-Azone.md)のチュートリアルを参照してください。

> [!IMPORTANT]
> この方法を使用して、サポート対象外のリージョン ペア間で DR を構成することはお勧めしません。これらのペアは、データの待ち時間を考慮して定義されますが、これが DR のシナリオではきわめて重要になるためです。

## <a name="verify-prerequisites"></a>前提条件を確認する

> [!NOTE]
> このシナリオの[アーキテクチャとコンポーネント](physical-azure-architecture.md)を理解している。 Azure VM の移動は、このアーキテクチャを使用し、**VM を物理サーバーとして扱う** ことによって行います。

- すべてのコンポーネントの[サポート要件](vmware-physical-secondary-support-matrix.md)を確認する。
- レプリケートするサーバーが [Azure VM 要件](vmware-physical-secondary-support-matrix.md#replicated-vm-support)に準拠していること。
- レプリケートする各サーバーにモビリティ サービスを自動インストールするためのアカウントを準備します。

- Azure でターゲット リージョンにフェールオーバーした後は、ソース リージョンに対して直接フェールバックを実行できないので注意してください。 ターゲットに戻るようにもう一度レプリケーションを設定する必要があります。

### <a name="verify-azure-account-permissions"></a>Azure アカウントのアクセス許可を確認する

VM を Azure にレプリケートするアクセス許可がお使いの Azure アカウントに与えられていることを確認します。

- Azure にマシンをレプリケートするために必要な[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)を確認します。
- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) のアクセス許可を確認して変更します。 

### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

ターゲット [Azure ネットワーク](../virtual-network/quick-create-portal.md)を設定します。

- Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
- ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。


### <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントの設定

[Azure ストレージ アカウント](../storage/common/storage-account-create.md)を設定します。

- Site Recovery は、オンプレミスのマシンを Azure Storage にレプリケートします。 Azure VM は、フェールオーバーの発生後にストレージから作成されます。
- ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。


## <a name="prepare-the-source-vms"></a>ソース VM を準備する

### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートする各サーバーにインストールする必要があります。 サーバーのレプリケーションを有効にすると、Site Recovery がこのサービスを自動的にインストールします。 自動的にインストールするには、Site Recovery でサーバーへのアクセスに使用するアカウントを準備する必要があります。

- ドメイン アカウントまたはローカル アカウントを使用できます。
- Windows VM の場合、ドメイン アカウントを使用していなければ、次のようにしてローカル マシンでのリモート ユーザー アクセス制御を無効にします。 無効にするには、レジスタで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に、値 1 を指定した DWORD エントリの **LocalAccountTokenFilterPolicy** を追加します。
- CLI からレジストリ エントリを追加し、設定を無効にするには、次のように入力します。       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux の場合、アカウントは、ソース Linux サーバーの root である必要があります。


## <a name="prepare-the-target-region"></a>ターゲット リージョンを準備する

1. Azure サブスクリプションによって、ディザスター リカバリーに使用されるターゲット リージョンに VM を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

2. サブスクリプションに、ソース VM と一致するサイズの VM をサポートするための十分なリソースがあることを確認します。 Site Recovery を使用してターゲットにデータをコピーする場合、ターゲット VM には、同じサイズ (または可能な限り近いサイズ) が選択されます。

3. ソース ネットワーク レイアウトから特定されたすべてのコンポーネントについてターゲット リソースを作成します。 これは、ターゲット リージョンへの切り替え後、ソースにあったすべての機能を VM に確保するうえで重要となります。

    > [!NOTE]
    > ソース VM のレプリケーションを有効にすると、Azure Site Recovery によって仮想ネットワークが自動的に検出されて作成されます。または、自分でネットワークを事前に作成しておき、レプリケーションを有効にするユーザー フローの中で VM に割り当てることもできます。 ただし後述のように、その他のリソースについては、ターゲット リージョンに手動で作成する必要があります。

     最も一般的に使用されるネットワーク リソースについては、次のドキュメントを参照して、実際の環境に適したものをソース VM の構成に基づいて作成してください。

    - [ネットワーク セキュリティ グループ](../virtual-network/manage-network-security-group.md)
    - [ロード バランサー](../load-balancer/index.yml)
    - [パブリック IP](../virtual-network/virtual-network-public-ip-address.md)
    
    その他のネットワーク コンポーネントについては、ネットワークに関する[ドキュメント](../index.yml?pivot=products&panel=network)を参照してください。

4. ターゲット リージョンへの最終的な切り替えを行う前に構成をテストしたい場合は、ターゲット リージョンに手動で[非運用ネットワークを作成](../virtual-network/quick-create-portal.md)します。 運用環境への影響が最小限で済むため、この方法をお勧めします。

## <a name="copy-data-to-the-target-region"></a>ターゲット リージョンにデータをコピーする
以下の手順では、Azure Site Recovery を使用してターゲット リージョンにデータをコピーする方法を説明しています。

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>任意のリージョン (ソース リージョンを除く) にコンテナーを作成します。

1. [Azure Portal](https://portal.azure.com) > **Recovery Services** にサインインします。
2. **[リソースの作成]**  >  **[管理ツール]**  >  **[バックアップおよびサイトの回復]** の順にクリックします。
3. **[名前]** に、フレンドリ名 **ContosoVMVault** を指定します。 複数のサブスクリプションがある場合は、 適切なサブスクリプションを 1 つ選択してください。
4. リソース グループ **ContosoRG** を作成します。
5. Azure リージョンを指定します。 サポートされているリージョンを確認するには、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページでご利用可能な地域をご覧ください。
6. [Recovery Services コンテナー] で、 **[概要]**  >  **[ConsotoVMVault]**  >  **[+ レプリケート]** の順にクリックします。
7. **[To Azure]\(Azure へ\)**  >  **[非仮想化/その他]** の順に選択します。

### <a name="set-up-the-configuration-server-to-discover-vms"></a>VM を検出するよう構成サーバーを設定します。


構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]**  >  **[インフラストラクチャを準備する]**  >  **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後 5 日間有効です。

   ![Set up source](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>構成サーバーのコンテナーへの登録

開始する前に次の作業を行います。 

#### <a name="verify-time-accuracy"></a>時間の精度を検証する
構成サーバー マシンのシステム クロックが[タイム サーバー](/windows-server/networking/windows-time-service/windows-time-service-top)と同期していることを確認します。 これは一致している必要があります。 15 分進んでいるか遅れている場合は、セットアップが失敗する可能性があります。

#### <a name="verify-connectivity"></a>接続を検証する
コンピューターが、環境に基づいて次の URL にアクセスできることを確認します。 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP アドレス ベースのファイアウォール規則では、HTTPS (443) ポートの上に示されているすべての Azure URL への通信が許可される必要があります。 IP 範囲を簡略化および制限するために、URL フィルタリングを実行することをお勧めします。

- **商用 IP** - [Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。 AAD、バックアップ、レプリケーション、およびストレージ URL をサポートするために、サブスクリプションの Azure リージョンの IP アドレス範囲を許可します。  
- **Government IP** - AAD、バックアップ、レプリケーション、およびストレージ URL をサポートするために、すべてのUSGov リージョン (バージニア、テキサス、アリゾナ、およびアイオワ) の [Azure Government データセンターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=57063)と HTTPS (443) ポートを許可します。  

#### <a name="run-setup"></a>セットアップを実行する
ローカル管理者として統合セットアップを実行し、構成サーバーをインストールします。 プロセス サーバーとマスター ターゲット サーバーも既定で構成サーバーにインストールされます。

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

登録が完了すると、コンテナーの **[設定]** の  >  **[サーバー]** ページに構成サーバーが表示されます。

### <a name="configure-target-settings-for-replication"></a>レプリケーションのターゲット設定を構成する

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]**  >  **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲットのデプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

   ![移行先](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

1. 新しいレプリケーション ポリシーを作成するには、 **[Site Recovery インフラストラクチャ]**  >  **[レプリケーション ポリシー]**  >  **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、復旧ポイントの目標 (RPO) の上限を指定します。 この値で、データの復旧ポイントを作成する頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。 **[OK]** をクリックしてポリシーを作成します。

    ![Replication policy](./media/physical-azure-disaster-recovery/replication-policy.png)


このポリシーは自動的に構成サーバーに関連付けられます。 既定でフェールバックの照合ポリシーが自動的に作成されます。 たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシー **rep-policy-failback** が作成されます。 このポリシーは、Azure からフェールバックを開始するまで使用されません。

### <a name="enable-replication"></a>レプリケーションを有効にする

- レプリケーションを有効にすると、Site Recovery がモビリティ サービスをインストールします。
- サーバーのレプリケーションを有効にすると、変更が反映されてポータルに表示されるまで 15 分以上かかる場合があります。

1. **[アプリケーションをレプリケートする]**  >  **[ソース]** の順にクリックします。
2. **[ソース]** で、構成サーバーを選択します。
3. **[マシンの種類]** で、 **[物理マシン]** を選択します。
4. プロセス サーバー (構成サーバー) を選択します。 次に、 **[OK]** をクリックします
5. **[ターゲット]** で、サブスクリプションと、フェールオーバー後に Azure VM を作成するリソース グループを選択します。 Azure で使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。
6. データのレプリケーションに使用する Azure Storage アカウントを選択します。 
7. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。
8. 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、 **[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、 **[後で構成する]** を選択します。 
9. **[物理マシン]** で **[+物理マシン]** をクリックします。 名前と IP アドレスを指定します。 レプリケートするマシンのオペレーティング システムを選択します。 サーバーを検出し、一覧表示するのに数分かかります。 

   > [!WARNING]
   > 移動する Azure VM の IP アドレスを入力する必要があります。

10. **[プロパティ]**  >  **[プロパティの構成]** で、プロセス サーバーがモビリティ サービスのマシンへの自動インストールで使用するアカウントを選択します。
11. **[レプリケーション設定]**  >  **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 
12. **[レプリケーションを有効にする]** をクリックします。 **[設定]**  >  **[ジョブ]**  >  **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化** ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。


追加したサーバーを監視する目的で、サーバーの最終検出時刻を **[構成サーバー]** の  >  **[前回のアクセス]** で確認できます。 定期検出を待たずにマシンを追加するには、構成サーバーを強調表示し (クリックしないでください)、 **[更新]** をクリックします。

## <a name="test-the-configuration"></a>構成をテストする


1. コンテナーに移動し、 **[設定]**  >  **[レプリケートされたアイテム]** で、ターゲット リージョンに移動する仮想マシンをクリックし、 **[+ テスト フェールオーバー]** アイコンをクリックします。
2. **[テスト フェールオーバー]** で、フェールオーバーに使用する復旧ポイントを選択します。

   - **最後に処理があった時点**:Site Recovery サービスによって処理された最新の復旧ポイントに VM をフェールオーバーします。 タイム スタンプが表示されます。 このオプションを使用すると、データの処理に時間がかからないため、低い RTO (Recovery Time Objective: 回復時刻の目標) を提示します。
   - **最新のアプリ整合性**:このオプションでは、すべての VM が最新のアプリ整合性の復旧ポイントにフェールオーバーされます。 タイム スタンプが表示されます。
   - **Custom**:任意の復旧ポイントを選択します。

3. Azure VM の移動先となるターゲット Azure 仮想ネットワークを選択し、構成をテストします。 

   > [!IMPORTANT]
   > テスト フェールオーバーには、VM の最終的な移動先となる運用環境のネットワーク (レプリケーションを有効にしたときに設定されたもの) ではなく、別個の Azure VM ネットワークを使用することをお勧めします。

4. 移動テストを開始するには、 **[OK]** をクリックします。 進行状況を追跡するには、VM をクリックして、そのプロパティを開きます。 また、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で、**テスト フェールオーバー** ジョブをクリックすることもできます。
5. フェールオーバーの完了後、レプリカの Azure VM は、Azure Portal の **[仮想マシン]** に表示されます。 VM が実行中で、適切なサイズであること、また、適切なネットワークに接続されていることを確認してください。
6. 移動テストの過程で作成した VM を削除する場合は、[レプリケートされたアイテム] の **[テスト フェールオーバーのクリーンアップ]** をクリックします。 **[メモ]** を使用して、テストに関連する観察結果をすべて記録し、保存します。

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>ターゲット リージョンへの移動を実行して確認する

1. コンテナーに移動し、 **[設定]**  >  **[レプリケートされたアイテム]** で仮想マシンをクリックして、 **[フェールオーバー]** をクリックします。
2. **[フェールオーバー]** で **[最新]** を選択します。 
3. **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 Site Recovery は、フェールオーバーを開始する前にソース VM をシャットダウンしようとします。 仮にシャットダウンが失敗したとしても、フェールオーバーは続行されます。 フェールオーバーの進行状況は **[ジョブ]** ページで確認できます。 
4. ジョブが完了したら、ターゲット Azure リージョンに VM が正しく表示されることを確認します。
5. **[レプリケートされたアイテム]** で [VM] > **[コミット]** を右クリックします。 これでターゲット リージョンへの移動プロセスは完了です。 コミット ジョブが完了するまでお待ちください。

## <a name="discard-the-resource-in-the-source-region"></a>ソース リージョンのリソースを破棄する 

- VM に移動します。  **[レプリケーションの無効化]** をクリックします。  これで VM のデータをコピーするプロセスが停止します。  

   > [!IMPORTANT]
   > これは、ASR レプリケーションの請求を避けるうえで重要な手順となります。

ソース リソースを再利用する予定がない場合は、次の一連の手順に進んでください。

1. 「[ソース VM を準備する](#prepare-the-source-vms)」の手順 4. で特定した該当するネットワーク リソースをソース リージョンからすべて削除します。 
2. 対応するストレージ アカウントをソース リージョンから削除します。



## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure VM を別の Azure リージョンに移動しました。 次には、移動した VM のディザスター リカバリーを構成できます。

> [!div class="nextstepaction"]
> [移行後のディザスター リカバリーのセットアップ](azure-to-azure-quickstart.md)
