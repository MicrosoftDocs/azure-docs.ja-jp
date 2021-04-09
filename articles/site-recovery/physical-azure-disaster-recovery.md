---
title: Azure Site Recovery を使用してオンプレミスの物理サーバーのディザスター リカバリーを設定する
description: Azure Site Recovery サービスを使用して Azure にオンプレミス Windows/Linux サーバーのディザスター リカバリーを設定する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 9b05d9952628e550beae5cedc49e051936a9d633
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87927285"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Azure にオンプレミス物理サーバーのディザスター リカバリーを設定する

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのコンピューターと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、Azure にオンプレミス物理 Windows/Linux サーバーのディザスター リカバリーを設定する方法を紹介します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure とオンプレミスの前提条件を設定する
> * Site Recovery の Recovery Services コンテナーを作成する 
> * ソース レプリケーション環境とターゲット レプリケーション環境を設定する
> * レプリケーション ポリシーを作成する
> * サーバーのレプリケーションを有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- このシナリオの[アーキテクチャとコンポーネント](physical-azure-architecture.md)を理解している。
- すべてのコンポーネントの[サポート要件](vmware-physical-secondary-support-matrix.md)を確認する。
- レプリケートするサーバーが [Azure VM 要件](vmware-physical-secondary-support-matrix.md#replicated-vm-support)に準拠していること。
- Azure を準備します。 Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントが必要です。
- レプリケートする各サーバーにモビリティ サービスを自動インストールするためのアカウントを準備します。

開始する前に、次のことに注意してください。

- Azure にフェールオーバーした後は、物理サーバーをオンプレミスの物理コンピューターにフェールバックできません。 フェールバックできるのは VMware VM に対してだけです。 
- このチュートリアルでは、最も簡単な設定で Azure に物理サーバーのディザスター リカバリーを設定します。 その他のオプションについて知りたい場合は、次のハウツー ガイドをご覧ください。
    - Site Recovery 構成サーバーなどの[レプリケーション ソース](physical-azure-set-up-source.md)を設定する。
    - [レプリケーション ターゲット](physical-azure-set-up-target.md)を設定する。
    - [レプリケーション ポリシー](vmware-azure-set-up-replication.md)を設定して、[レプリケーションを有効にする](vmware-azure-enable-replication.md)。


### <a name="set-up-an-azure-account"></a>Azure アカウントを設定する

Microsoft [Azure アカウント](https://azure.microsoft.com/)を取得します。

- アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。
- [Site Recovery の価格](site-recovery-faq.md#pricing)について理解し、[価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)を確認します。
- Site Recovery の[サポート対象のリージョン](https://azure.microsoft.com/pricing/details/site-recovery/)を見つけます。

### <a name="verify-azure-account-permissions"></a>Azure アカウントのアクセス許可を確認する

VM を Azure にレプリケートするアクセス許可がお使いの Azure アカウントに与えられていることを確認します。

- Azure にマシンをレプリケートするために必要な[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)を確認します。
- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) のアクセス許可を確認して変更します。 



### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

[Azure ネットワーク](../virtual-network/quick-create-portal.md)をセットアップします。

- Azure VM は、フェールオーバー後に作成されたときに、このネットワークに配置されます。
- ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。


## <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントの設定

[Azure ストレージ アカウント](../storage/common/storage-account-create.md)を設定します。

- Site Recovery は、オンプレミスのマシンを Azure Storage にレプリケートします。 Azure VM は、フェールオーバーの発生後にストレージから作成されます。
- ストレージ アカウントは、Recovery Services コンテナーと同じリージョンに存在する必要があります。


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
2. リソース メニューで、 **[Site Recovery]**  >  **[インフラストラクチャの準備]**  >  **[保護の目標]** の順にクリックします。
3. **[保護の目標]** で、 **[To Azure]\(Azure へ\)**  >  **[非仮想化/その他]** を選択します。

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

構成サーバーをセットアップし、コンテナーに登録して、VM を検出します。

1. **[Site Recovery]**  >  **[インフラストラクチャを準備する]**  >  **[ソース]** の順にクリックします。
2. 構成サーバーがない場合は **[+ 構成サーバー]** をクリックします。
3. **[サーバーの追加]** で、**[サーバーの種類]** に **[構成サーバー]** が表示されていることを確認します。
4. Site Recovery 統合セットアップ インストール ファイルをダウンロードします。
5. コンテナー登録キーをダウンロードします。 このキーは、統合セットアップを実行するときに必要になります。 キーは生成後 5 日間有効です。

   ![インストール ファイルと登録キーをダウンロードするためのオプションを示すスクリーンショット。](./media/physical-azure-disaster-recovery/source-environment.png)


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


## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする

ターゲット リソースを選択して確認します。

1. **[インフラストラクチャの準備]**  >  **[ターゲット]** の順にクリックし、使用する Azure サブスクリプションを選択します。
2. ターゲットのデプロイ モデルを指定します。
3. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

   ![ターゲット環境を設定するためのオプションのスクリーンショット。](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>レプリケーション ポリシーを作成する

1. 新しいレプリケーション ポリシーを作成するには、 **[Site Recovery インフラストラクチャ]**  >  **[レプリケーション ポリシー]**  >  **[+ レプリケーション ポリシー]** の順にクリックします。
2. **[レプリケーション ポリシーの作成]** で、ポリシー名を指定します。
3. **[RPO しきい値]** で、復旧ポイントの目標 (RPO) の上限を指定します。 この値で、データの復旧ポイントを作成する頻度を指定します。 継続的なレプリケーションがこの制限を超えると、アラートが生成されます。
4. **[復旧ポイントの保持期間]** で、各復旧ポイントのリテンション期間の長さ (時間単位) を指定します。 レプリケートされた VM は、期間内の任意の時点に復旧できます。 Premium Storage にレプリケートされたマシンでは最大 24 時間のリテンション期間がサポートされ、Standard Storage の場合は 72 時間です。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (分単位) を指定します。 **[OK]** をクリックしてポリシーを作成します。

    ![レプリケーション ポリシーを作成するためのオプションのスクリーンショット。](./media/physical-azure-disaster-recovery/replication-policy.png)


このポリシーは自動的に構成サーバーに関連付けられます。 既定でフェールバックの照合ポリシーが自動的に作成されます。 たとえば、レプリケーション ポリシーが **rep-policy** の場合、フェールバック ポリシー **rep-policy-failback** が作成されます。 このポリシーは、Azure からフェールバックを開始するまで使用されません。

## <a name="enable-replication"></a>レプリケーションを有効にする

各サーバーのレプリケーションを有効にします。

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
10. **[プロパティ]**  >  **[プロパティの構成]** で、プロセス サーバーがモビリティ サービスのマシンへの自動インストールで使用するアカウントを選択します。
11. **[レプリケーション設定]**  >  **[レプリケーション設定の構成]** で、正しいレプリケーション ポリシーが選択されていることを確認します。 
12. **[レプリケーションを有効にする]** をクリックします。 **[設定]**  >  **[ジョブ]**  >  **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化** ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。


追加したサーバーを監視する目的で、サーバーの最終検出時刻を **[構成サーバー]** の  >  **[前回のアクセス]** で確認できます。 定期検出を待たずにマシンを追加するには、構成サーバーを強調表示し (クリックしないでください)、**[更新]** をクリックします。

## <a name="next-steps"></a>次の手順

[ディザスター リカバリーのテストを実行する](tutorial-dr-drill-azure.md)。
