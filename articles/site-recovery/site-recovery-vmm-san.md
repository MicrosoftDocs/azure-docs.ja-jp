---
title: "Azure Site Recovery で SAN を使用して VMM 内の Hyper-V VM をレプリケートする | Microsoft Docs"
description: "この記事では、Azure Site Recovery で SAN レプリケーションを使用して、2 つのサイト間で Hyper-V 仮想マシンをレプリケートする方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: eb480459-04d0-4c57-96c6-9b0829e96d65
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 67b4861ac564565b2a36932ae15141a1e1f56035
ms.openlocfilehash: 29084a52aca22b31a2fa42660ec2cdf050aec7ca
ms.lasthandoff: 02/23/2017


---
# <a name="replicate-hyper-v-vms-in-vmm-clouds-to-a-secondary-site-with-azure-site-recovery-by-using-san"></a>Azure Site Recovery で SAN を使って VMM クラウド内の Hyper-V VM をセカンダリ サイトにレプリケートする


セカンダリ VMM サイトへの (System Center Virtual Machine Manager クラウドによって管理された) Hyper-V VM のレプリケーションを管理するために、クラシック ポータルで Azure Site Recovery を使用して [Azure Site Recovery](site-recovery-overview.md) をデプロイする場合は、この記事をお読みください。 このシナリオは、新しい Azure Portal では使用できません。



コメントがありましたら、この記事の末尾に投稿してください。 技術的な質問については、[Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で回答を得られます。


## <a name="why-replicate-with-san-and-site-recovery"></a>SAN と Site Recovery でレプリケートする理由

* SAN は、エンタープライズ レベルのスケーラブルなレプリケーション ソリューションを提供します。Hyper-V と SAN を含むプライマリ サイトは、SAN を使ってセカンダリ サイトに LUN をレプリケートできます。 記憶域は VMM によって管理され、レプリケーションとフェールオーバーは Site Recovery によって統制されます。
* Site Recovery は、いくつかの [SAN 記憶域パートナー](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)と協力して、ファイバー チャネルと iSCSI 記憶域の間のレプリケーションを提供します。  
* 既存の SAN インフラストラクチャを使用し、Hyper-V クラスターにデプロイされているミッション クリティカルなアプリを保護します。 VM はグループとしてレプリケートできるので、整合性を維持して N 層アプリをフェールオーバーできます。
* SAN レプリケーションは、(ストレージ アレイの機能に応じて) RTO と RPO の短縮を実現する同期レプリケーションと、優れた柔軟性を実現する非同期レプリケーションを使用して、アプリケーションのさまざまな階層でレプリケーションの一貫性を確保します。  
* VMM ファブリックで SAN 記憶域を管理し、VMM で SMI-S を使って既存の記憶域を検出できます。  

以下の点に注意してください。
- SAN を使ったサイト間レプリケーションは、Azure Portal では利用できません。 クラシック ポータルでのみ利用できます。 クラシック ポータルでは、新しいコンテナーを作成できません。 既存のコンテナーを管理することはできます。
- SAN から Azure へのレプリケーションはサポートされていません。
- 共有 VHDX、あるいは iSCSI またはファイバー チャネル経由で VM に直接接続されている論理ユニット (LUN) をレプリケートすることはできません。 ゲスト クラスターはレプリケートできます。


## <a name="architecture"></a>アーキテクチャ

![SAN アーキテクチャ](./media/site-recovery-vmm-san/architecture.png)

- **Azure**: Azure Portal で Site Recovery コンテナーを設定します。
- **SAN 記憶域**: SAN 記憶域は VMM ファブリックで管理されます。 記憶域プロバイダーを追加し、記憶域の分類を作成して、記憶域プールを設定します。
- **VMM と Hyper-V**: 各サイトに&1; つの VMM サーバーをお勧めします。 VMM プライベート クラウドを設定し、それらのクラウドに Hyper-V クラスターを配置します。 デプロイの間に、Azure Site Recovery プロバイダーが各 VMM サーバーにインストールされ、サーバーがコンテナーに登録されます。 プロバイダーは、Site Recovery サービスと通信して、レプリケーション、フェールオーバー、フェールバックを管理します。
- **レプリケーション**: VMM で記憶域を設定し、Site Recovery でレプリケーションを構成すると、プライマリとセカンダリの SAN 記憶域間でレプリケーションが実行されるようになります。 Site Recovery にはレプリケーション データは送信されません。
- **フェールオーバー**: Site Recovery ポータルでフェールオーバーを有効にします。 レプリケーションは同期的に行われるため、フェールオーバー中のデータ損失はありません。
- **フェールバック**: フェールバックを行うには、レプリケーションの反転を有効にして、セカンダリ サイトからプライマリ サイトに差分変更を転送します。 レプリケーションの反転が完了した後、セカンダリからプライマリに計画されたフェールオーバーを実行します。 この計画されたフェールオーバーにより、セカンダリ サイトのレプリカ VM が停止し、プライマリ サイトで開始します。


## <a name="before-you-start"></a>開始する前に


**前提条件** | **詳細**
--- | ---
**Azure** |[Microsoft Azure](https://azure.microsoft.com/) アカウントが必要です。 アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。 [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。 レプリケーションとフェールオーバーを構成して管理するために、Azure Site Recovery コンテナーを作成します。
**VMM** | 単一の VMM サーバーを使って異なるクラウド間でレプリケートできますが、プライマリ サイトとセカンダリ サイトに個別に VMM を置くことをお勧めします。 VMM は、物理または仮想のスタンドアロン サーバーとして、またはクラスターとして、デプロイできます。 <br/><br/>VMM サーバーでは、最新の累積的な更新プログラムがインストールされている System Center 2012 R2 以降が実行されている必要があります。<br/><br/> 保護するプライマリ VMM サーバーに少なくとも&1; つのクラウドを構成し、フェールオーバーに使うセカンダリ VMM サーバーに&1; つのクラウドを構成する必要があります。<br/><br/> ソース クラウドには、1 つ以上の VMM ホスト グループが含まれている必要があります。<br/><br/> すべての VMM クラウドには Hyper-V キャパシティ プロファイル セットが必要です。<br/><br/> VMM クラウドの設定の詳細については、[プライベート VM クラウドのデプロイ](https://technet.microsoft.com/en-us/system-center-docs/vmm/scenario/cloud-overview)に関するページを参照してください。
**Hyper-V** | プライマリおよびセカンダリの VMM クラウドに、Hyper-V クラスターが少なくとも&1; つ必要です。<br/><br/> ソース Hyper-V クラスターには、1 つ以上の VM が含まれている必要があります。<br/><br/> プライマリ サイトとセカンダリ サイトの VMM ホスト グループには、少なくとも&1; つの Hyper-V クラスターが含まれる必要があります。<br/><br/>ホストとターゲットの Hyper-V サーバーは、Hyper-V ロールを持つ Windows Server 2012 以降を実行しており、最新の更新プログラムがインストールされている必要があります。<br/><br/> クラスターで Hyper-V を実行していて、静的 IP アドレスベースのクラスターが存在する場合、クラスター ブローカーは自動的に作成されません。 手動で構成する必要があります。 詳細については、「[Preparing host clusters for Hyper-V replica (Hyper-V レプリカのホスト クラスターの準備)](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters)」を参照してください。
**SAN 記憶域** | iSCSI 記憶域、チャネル記憶域、または共有仮想ハード ディスク (vhdx) を使って、クラスター化されたゲスト仮想マシンをレプリケートできます。<br/><br/> 2 つの SAN アレイが必要です (プライマリ サイトに&1; つ、セカンダリ サイトに&1; つ)。<br/><br/> ネットワーク インフラストラクチャが、アレイ間にセットアップされている必要があります。 ピアリングとレプリケーションが構成されていること。 レプリケーション ライセンスが、記憶域配列の要件に従って設定されていること。<br/><br/>iSCSI またはファイバー チャネルを使って、ホストが記憶域の LUN と通信できるように、Hyper-V ホスト サーバーとストレージ アレイの間のネットワークをセットアップします。<br/><br/> [サポート対象の記憶域アレイ](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx)を確認してください。<br/><br/> ストレージ アレイの製造元が提供する SMI-S プロバイダーをインストールし、そのプロバイダーで SAN アレイを管理する必要があります。 製造元の指示に従って、プロバイダーを設定します。<br/><br/>アレイの SMI-S プロバイダーは、VMM サーバーが IP アドレスまたは FQDN によりネットワーク経由でアクセスできるサーバーに配置します。<br/><br/> 各 SAN アレイには、使用できる&1; つ以上のストレージ プールが必要です。<br/><br/> プライマリ VMM サーバーでプライマリ アレイを管理し、セカンダリ VMM サーバーでセカンダリ アレイを管理する必要があります。
**ネットワーク マッピング** | フェールオーバー後に、レプリケートされた仮想マシンがセカンダリ Hyper-V ホスト サーバーに最適に配置され、適切な VM ネットワークに接続できるように、ネットワーク マッピングを設定します。 ネットワーク マッピングを構成しない場合、レプリカ VM はフェールオーバー後にどの VM ネットワークにも接続されません。<br/><br/> Site Recovery のデプロイ中にネットワーク マッピングを設定できるよう、VMM ネットワークが正しく構成されていることを確認します。 VMM では、ソース Hyper-V ホスト上の VM が VMM VM ネットワークに接続されている必要があります。 そのネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。<br/><br/> ターゲット クラウドには対応する VM ネットワークがあり、そのネットワークが、ターゲット クラウドに関連付けられている、対応する論理ネットワークにリンクされている必要があります。<br/><br/>に関するページを参照してください。

## <a name="step-1-prepare-the-vmm-infrastructure"></a>手順 1: VMM インフラストラクチャの準備
VMM インフラストラクチャを準備するには、以下を行う必要があります。

1. VMM クラウドを確認する。
2. VMM で、SAN 記憶域の統合と分類を行う。
3. LUN を作成して記憶域を割り当てる。
4. レプリケーション グループを作成する。
5. VM ネットワークを設定する。

### <a name="verify-vmm-clouds"></a>VMM クラウドを確認する

Site Recovery のデプロイを開始する前に、VMM クラウドが適切に設定されていることを確認します。

### <a name="integrate-and-classify-san-storage-in-the-vmm-fabric"></a>VMM ファブリックで、SAN 記憶域の統合と分類を行う

1. VMM コンソールで、**[ファブリック]** > **[記憶域]** > **[リソースの追加]** > **[記憶装置]** の順に移動します。
2. **記憶装置の追加**ウィザードの **[記憶域プロバイダーの種類を選択します]** で、**[SMI-S プロバイダーにより検出および管理される SAN および NAS デバイス]** を選びます。

    ![プロバイダーの種類](./media/site-recovery-vmm-san/provider-type.png)

3. **[記憶域 SMI-S プロバイダーのプロトコルとアドレスを指定します]** ページで、**[SMI-S CIMXML]** を選択し、プロバイダーに接続するための設定を指定します。
4. **[プロバイダーの IP アドレスまたは FQDN]** と **[TCP/IP ポート]** で、プロバイダーに接続するための設定を指定します。 SSL 接続は、SMI-S CIMXML にのみ使用できます。

    ![プロバイダーの接続](./media/site-recovery-vmm-san/connect-settings.png)
5. **[実行アカウント]** で、プロバイダーにアクセスできる VMM 実行アカウントを指定するか、アカウントを作成します。
6. **[情報の収集]** ページでは、VMM が記憶装置の情報を自動的に検出してインポートします。 検出を再試行するには、 **[プロバイダーのスキャン]**をクリックします。 検出プロセスが成功した場合は、検出された記憶域配列、記憶域プール、製造元、モデル、容量が、ページに一覧表示されます。

    ![ストレージの検出](./media/site-recovery-vmm-san/discover.png)
7. **[管理下に置く記憶域プールの選択と分類の指定]**で、VMM が管理し、分類を割り当てる記憶域プールを選択します。 LUN 情報が、記憶域プールからインポートされます。 保護する必要があるアプリケーション、容量の要件、一緒にレプリケートする必要があるものの要件に基づいて、LUN を作成します。

    ![ストレージの分類](./media/site-recovery-vmm-san/classify.png)

### <a name="create-luns-and-allocate-storage"></a>LUN の作成とストレージの割り当て

保護する必要があるアプリケーション、容量の要件、一緒にレプリケートする必要があるものの要件に基づいて、LUN を作成します。

1. VMM ファブリックに記憶域が表示されたら、[LUN をプロビジョニング](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-storage-host-groups#create-a-lun-in-vmm)できます。

     > [!NOTE]
     > LUN へのレプリケーションが有効になっている VM への VHD の追加は、 行わないでください。LUN が Site Recovery のレプリケーション グループに存在しない場合、VHD は Site Recovery によって検出されません。
     >

2. VMM がプロビジョニングされた記憶域に仮想マシンのデータをデプロイできるように、Hyper-V ホスト クラスターに記憶域容量を割り当てます。

   * 記憶域をクラスターに割り当てる前に、クラスターが存在する VMM ホスト グループに割り当てる必要があります。 詳細については、「[How to allocate storage logical units to a host group in VMM (VMM でホスト グループに記憶域論理ユニットを割り当てる方法)](https://technet.microsoft.com/library/gg610686.aspx)」と「[How to allocate storage pools to a host group in VMM (VMM でホスト グループに記憶域プールを割り当てる方法)](https://technet.microsoft.com/library/gg610635.aspx)」をご覧ください。
   * 「[VMM で Hyper-V ホスト クラスターの記憶域を構成する方法](https://technet.microsoft.com/library/gg610692.aspx)」の説明に従って、クラスターに記憶域容量を割り当てます。

    ![プロバイダーの種類](./media/site-recovery-vmm-san/provider-type.png)
3. **[記憶域 SMI-S プロバイダーのプロトコルとアドレスを指定します]** で、**[SMI-S CIMXML]** を選びます。 プロバイダーへの接続の設定を指定します。 SSL 接続は、SMI-S CIMXML にのみ使用できます。

    ![プロバイダーの接続](./media/site-recovery-vmm-san/connect-settings.png)
4. **[実行アカウント]** で、プロバイダーにアクセスできる VMM 実行アカウントを指定するか、アカウントを作成します。
5. **[情報の収集]** では、VMM が記憶装置の情報を自動的に検出してインポートします。 再試行する必要がある場合は、**[プロバイダーのスキャン]** をクリックします。 検出プロセスが成功した場合は、記憶域アレイ、記憶域プール、製造元、モデル、容量が、ページに一覧表示されます。

    ![ストレージの検出](./media/site-recovery-vmm-san/discover.png)
7. **[管理下に置く記憶域プールの選択と分類の指定]** で、VMM が管理し、分類を割り当てる記憶域プールを選びます。 LUN 情報が、記憶域プールからインポートされます。

    ![ストレージの分類](./media/site-recovery-vmm-san/classify.png)


### <a name="create-replication-groups"></a>レプリケーション グループの作成

一緒にレプリケートする必要があるすべての LUN を含むレプリケーション グループを作成します。

1. VMM コンソールで、ストレージ アレイ プロパティの **[レプリケーション グループ]** タブを開き、**[新規]** をクリックします。
2. レプリケーション グループを作成します。

    ![SAN レプリケーション グループ](./media/site-recovery-vmm-san/rep-group.png)

### <a name="set-up-networks"></a>ネットワークの設定

ネットワーク マッピングを構成する場合は、次の手順に従います。

1. Site Recovery のネットワーク マッピングに関するドキュメントを読みます。
2. VMM で VM ネットワークを準備します。

   * [論理ネットワークを設定](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-logical-networks)します。
   * [VM ネットワークを設定](https://technet.microsoft.com/en-us/system-center-docs/vmm/manage/manage-network-vm-networks)します。


## <a name="step-2-create-a-vault"></a>手順 2: 資格情報コンテナーの作成

1. コンテナーに登録する VMM サーバーから [Azure Portal](https://portal.azure.com) にサインインします。
2. **[Data Services]** > **[Recovery Services]** の順に展開し、**[Site Recovery コンテナー]** をクリックします。
3. **[新規作成]**  >  **[簡易作成]** の順にクリックします。
4. **[名前]**ボックスに、コンテナーを識別する表示名を入力します。
5. **[リージョン]**ボックスで、コンテナーのリージョンを選択します。 サポートされているリージョンについては、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページをご覧ください。
6. **[コンテナーの作成]**をクリックします。

    ![新しいコンテナー](./media/site-recovery-vmm-san/create-vault.png)

ステータス バーを確認して、コンテナーが正常に作成されたことを確かめます。 メイン **[Recovery Services]** ページで、コンテナーは **[アクティブ]** と表示されています。

### <a name="register-the-vmm-servers"></a>VMM サーバーの登録

1. **[Recovery Services]** ページから **[クイック スタート]** ページを開きます。 [クイック スタート] は、アイコンを選択していつでも開くことができます。

    ![[クイック スタート] アイコン](./media/site-recovery-vmm-san/quick-start-icon.png)
2. ドロップダウン ボックスで、**[Between Hyper-V on-premises site using array replication (Hyper-V オンプレミス サイト間でアレイ レプリケーションを使用)]** を選択します。

    ![[登録キー]](./media/site-recovery-vmm-san/select-san.png)
3. **[VMM サーバーの準備]**で、Azure Site Recovery プロバイダ インストール ファイルの最新バージョンをダウンロードします。
4. ソース VMM サーバーでこのファイルを実行します。 プロバイダーを初めてインストールするときに、VMM がクラスターにデプロイされている場合は、プロバイダーをアクティブなノードにインストールします。インストールが終了したら、VMM サーバーをコンテナーに登録します。 次に、プロバイダーを他のノードにインストールします。 プロバイダーをアップグレードする場合は、すべてのノードをアップグレードして、すべてのノードでプロバイダーのバージョンを同じにする必要があります。
5. インストーラーによって要件がチェックされ、プロバイダーのセットアップを開始するために VMM サービスを停止する許可が求められます。 セットアップが完了すると、サービスは自動的に再起動されます。 VMM クラスターでは、クラスター ロールを停止するよう求められます。
6. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。

    ![Microsoft 更新プログラム](./media/site-recovery-vmm-san/ms-update.png)

7. 既定では、プロバイダーのインストール場所は、<SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin です。 **[インストール]** をクリックして開始します。

    ![インストール場所](./media/site-recovery-vmm-san/install-location.png)

8. プロバイダーがインストールされた後、**[登録]** をクリックして VMM サーバーをコンテナーに登録します。

    ![インストール完了](./media/site-recovery-vmm-san/install-complete.png)

9. **[インターネット接続]** で、プロバイダーがインターネットに接続する方法を指定します。 サーバーでの既定のインターネット接続設定を使う場合は、**[既定のシステム プロキシ設定を使用]** を選びます。

    ![インターネット設定](./media/site-recovery-vmm-san/proxy-details.png)

   * カスタム プロキシを使う場合は、プロバイダーをインストールする前にカスタム プロキシを設定します。 カスタム プロキシの設定を構成すると、プロキシの接続を確認するためのテストが実施されます。
   * カスタム プロキシを使う場合、または既定のプロキシで認証が必要な場合は、アドレスやポートなどの詳細を入力する必要があります。
   * 必要な URL に VMM サーバーからアクセスできるようにする必要があります。
   * カスタム プロキシを使用する場合、指定したプロキシの資格情報を使用して VMM 実行アカウント (DRAProxyAccount) が自動的に作成されます。 このアカウントが認証されるようにプロキシ サーバーを構成します。 VMM コンソールでは、実行アカウントの設定を変更できます (**[設定]** > **[セキュリティ]** > **[実行アカウント]** > **[DRAProxyAccount]**)。 変更を反映するには、VMM サービスを再起動する必要があります。
10. **[登録キー]** で、ポータルからダウンロードして VMM サーバーにコピーした登録キーを選びます。
11. **[コンテナー名]**で、サーバーが登録されるコンテナーの名前を確認します。

    ![サーバー登録](./media/site-recovery-vmm-san/vault-creds.png)
12. 暗号化の設定は、VMM から Azure へのレプリケーションをにのみ使われます。 これは無視できます。

    ![サーバー登録](./media/site-recovery-vmm-san/encrypt.png)
13. **[サーバー名]**に、コンテナーで VMM サーバーを識別する表示名を入力します。 クラスター構成で、VMM クラスターのロール名を指定します。
14. **[Initial cloud metadata sync (初期クラウド メタデータ同期)]** で、VMM サーバー上のすべてのクラウドのメタデータを同期するかどうか選びます。 この操作は、各サーバーで&1; 回のみ実行する必要があります。 すべてのクラウドを同期したくない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。

    ![サーバー登録](./media/site-recovery-vmm-san/friendly-name.png)

15. **[次へ]** をクリックしてプロセスを完了します。 登録後に、VMM サーバーからのメタデータが、Azure Site Recovery によって取得されます。 サーバーは、コンテナーの **[サーバー]** > **[VMM サーバー]** に表示されます。

### <a name="command-line-installation"></a>コマンドライン インストール

Azure Site Recovery プロバイダーは、次のコマンド ラインを使用してインストールすることもできます。 この方法を使うと、Windows Server 2012 R2 の Server Core にプロバイダーをインストールできます。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。 たとえば、C:\ASR です。
2. VMM サービスを停止します。
3. プロバイダーのインストーラーを抽出します。 管理者として次のコマンドを実行します。

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. プロバイダーをインストールします。

        C:\ASR> setupdr.exe /i
5. プロバイダーを登録します。

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

パラメーター:

* **/Credentials**: 登録キー ファイルの場所を指定する必須パラメーターです。  
* **/Friendlyname**: Azure Site Recovery ポータルに表示される、Hyper-V ホスト サーバーの名前を表す必須パラメーターです。
* **/EncryptionEnabled**: 省略可能。VMM から Azure にレプリケートする場合にのみ使います。
* **/proxyAddress**: 省略可能。プロキシ サーバーのアドレスを指定します。
* **/proxyport**: 省略可能。プロキシ サーバーのポートを指定します。
* **/proxyUsername**: 省略可能。プロキシのユーザー名を指定します (認証が必要なプロキシの場合)。
* **/proxyPassword**: 省略可能。プロキシ サーバーの認証に使うパスワードを指定します (認証が必要なプロキシの場合)。

## <a name="step-3-map-storage-arrays-and-pools"></a>手順 3: 記憶域アレイとプールのマッピング

プライマリとセカンダリのアレイをマップして、プライマリ プールからレプリケーション データを受け取るセカンダリ記憶域プールを指定します。 レプリケーション グループの保護を有効にするときにマッピング情報が使われるため、レプリケーションを構成する前に、ストレージをマッピングします。

開始する前に、コンテナーに VMM クラウドが表示されることを確認します。 クラウドは、プロバイダーのインストール中にすべてのクラウドを同期するとき、または VMM コンソールで特定のクラウドを同期するときに、検出されます。

1. **[リソース]** > **[サーバー記憶域]** > **[ソース アレイとターゲット アレイのマッピング]** の順にクリックします。
    ![サーバー登録](./media/site-recovery-vmm-san/storage-map.png)

2. プライマリ サイトで記憶域アレイを選び、それらをセカンダリ サイトの記憶域アレイにマッピングします。 **[記憶域プール]** で、マッピングするソースとターゲットの記憶域プールを選びます。

    ![サーバー登録](./media/site-recovery-vmm-san/storage-map-pool.png)

## <a name="step-4-configure-replication-settings"></a>手順 4: レプリケーションの設定を構成する

VMM サーバーを登録した後、クラウドの保護設定を構成します。

1. **[クイック スタート]** ページで、**[VMM クラウドの保護を設定します]** をクリックします。
2. **[保護された項目]** タブで、クラウドの **[構成]** を選びます。
3. **[ターゲット]** で **[VMM]** を選択します。
4. **[ターゲットの場所]** で、回復時に使うクラウドを管理する VMM サーバーを選びます。
5. **[ターゲット クラウド]** で、VM のフェールオーバーに使うターゲット クラウドを選びます。
   * 保護する仮想マシンの復旧要件を満たしたターゲット クラウドを選択することをお勧めします。
   * クラウドは、プライマリ クラウドまたはターゲット クラウドとして、1 つのクラウド ペアのみに属することができます。
6. Site Recovery は、クラウドが SAN 記憶域にアクセスできること、および記憶域アレイがマッピングされていることを検証します。
7. 検証が成功した場合、**[レプリケーションの種類]** で **[SAN]** を選択します。

この設定を保存すると、ジョブが作成され、これを **[ジョブ]** タブで監視できます。 設定は **[構成]** タブで変更できます。 ターゲットの場所やターゲット クラウドを変更する場合は、クラウド構成を削除して、クラウドを再構成する必要があります。

## <a name="step-5-enable-network-mapping"></a>手順 5: ネットワーク マッピングの有効化

1. **[クイック スタート]** ページで、**[ネットワークのマッピング]** をクリックします。
2. ソース VMM サーバーを選び、次に、ネットワークがマップされるターゲット VMM サーバーを選びます。 ソース ネットワークとそれに関連付けられたターゲット ネットワークの一覧が表示されます。 マッピングされていないネットワークには、空白の値が表示されます。 ソース ネットワーク名とターゲット ネットワーク名の横にある情報アイコンをクリックして各ネットワークのサブネットを表示します。
3. **[ソースのネットワーク]** でネットワークを選択し、**[マップ]** をクリックします。 サービスによってターゲット サーバー上の VM ネットワークが検出され、表示されます。

    ![SAN アーキテクチャ](./media/site-recovery-vmm-san/network-map1.png)
4. ターゲット VMM サーバーのいずれかの VM ネットワークを選びます。

    ![SAN アーキテクチャ](./media/site-recovery-vmm-san/network-map2.png)

5. ターゲット ネットワークを選択すると、ソース ネットワークを使用する保護されたクラウドが表示されます。 使用可能なターゲット ネットワークも表示されます。 レプリケーションに使っているすべてのクラウドで使用可能なターゲット ネットワークを選ぶことをお勧めします。
6. チェック マークをクリックして、マッピング処理を完了します。 ジョブが開始し、進行状況を追跡します。 進行状況は **[ジョブ]** タブで確認できます。

## <a name="step-6-enable-replication-for-replication-groups"></a>手順 6: レプリケーション グループのレプリケーションの有効化

仮想マシンの保護を有効にする前に、ストレージ レプリケーション グループのレプリケーションを有効にする必要があります。

1. Site Recovery ポータルのプライマリ クラウドの **[プロパティ]** ページで、**[Virtual Machines]** タブを開き、**[レプリケーション グループの追加]** をクリックします。
2. クラウドに関連付けられている&1; つ以上の VMM レプリケーション グループを選択し、ソースとターゲットのアレイを確認して、レプリケーションの頻度を指定します。

Site Recovery、VMM、SMI-S プロバイダーは、ターゲット サイトの記憶域 LUN をプロビジョニングし、記憶域のレプリケーションを有効にします。 レプリケーション グループが既にレプリケートされている場合、Site Recovery は既存のレプリケーション関係を再利用し、情報を更新します。

## <a name="step-7-enable-protection-for-virtual-machines"></a>ステップ 7: 仮想マシンの保護を有効化する


記憶域グループのレプリケート中、VMM コンソールで次のいずれかの方法を使って、VM の保護を有効にします。

* **新しい仮想マシン** - VM を作成するときに、レプリケーションを有効にして、VM をレプリケーション グループと関連付けます。
  このオプションでは、VMM はインテリジェントな配置機能を使い、VM の記憶域をレプリケーション グループの LUN に適切に配置します。 Site Recovery は、セカンダリ サイトでのシャドウ VM の作成を調整し、レプリカ VM がフェールオーバー後に起動できるように、容量を割り当てます。
* **既存の仮想マシン**: 仮想マシンが VMM に既にデプロイされている場合は、レプリケーションを有効にし、レプリケーション グループへの記憶域の移行を実行できます。 完了した後、VMM と Site Recovery は、新しい VM を検出し、Site Recovery による管理を開始します。 シャドウ VM は、セカンダリ サイトに作成され、フェールオーバー後にレプリカ VM が起動できるように、容量が割り当てられます。

![保護を有効にする](./media/site-recovery-vmm-san/enable-protect.png)

レプリケーションが有効になった VM は、Site Recovery コンソールに表示されます。 VM のプロパティの表示、状態の追跡、複数の VM を含むフェールオーバー レプリケーション グループの追跡を実行できます。 SAN レプリケーションでは、レプリケーション グループに関連付けられているすべての VM は、一緒にフェールオーバーする必要があります。 これは、フェールオーバーが最初にストレージ層で行われるためです。 レプリケーション グループを適切にグループ化し、関連する VM のみを一緒に配置することが重要です。

> [!NOTE]
> VM のレプリケーションを有効にした後で、Site Recovery のレプリケーション グループに配置されていない LUN にその VHD を追加しないでください。 VHD は、Site Recovery のレプリケーション グループに配置されている場合にのみ Site Recovery によって検出されます。
>
>

**[ジョブ]** タブで、初期レプリケーションなどの進行状況を確認できます。 保護の最終処理のジョブが実行されると、仮想マシンは、フェールオーバーを実行できる状態になります。

![仮想マシン保護ジョブ](./media/site-recovery-vmm-san/job-props.png)

## <a name="step-8-test-the-deployment"></a>ステップ 8: デプロイのテスト

デプロイをテストし、VM が意図したとおりにフェールオーバーされることを確認します。 そのためには、復旧計画を作成してテスト フェールオーバーを実行します。

1. **[復旧計画]** タブで、**[復旧計画の作成]** をクリックします。
2. 復旧計画の名前を指定し、ソースとターゲットの VMM サーバーを選択します。 ソース サーバーには、フェールオーバーと復旧が有効になった VM が必要です。 **[SAN]** を選択して、SAN レプリケーションが構成されたクラウドだけを表示します。

    ![復旧計画の作成](./media/site-recovery-vmm-san/r-plan.png)

3. **[仮想マシンの選択]** で、レプリケーション グループを選択します。 グループに関連付けられているすべての VM が、復旧計画に追加されます。 これらの VM は、復旧計画の既定のグループ (グループ 1) に追加されます。 必要に応じて、さらにグループを追加できます。 レプリケーションの後、VM は復旧計画グループの順序に従って番号が付けられます。

    ![仮想マシンの選択](./media/site-recovery-vmm-san/r-plan-vm.png)
4. 作成した復旧計画は、**[復旧計画]** タブに一覧表示されます。 計画を選び、**[テスト フェールオーバー]** を選択します。
5. **[テスト フェールオーバーの確認]** ページで、**[なし]** を選択します。 このオプションが有効な場合、フェールオーバーしたレプリカ VM はネットワークに接続されません。 VM が意図したとおりにフェールオーバーすることはテストされますが、ネットワーク環境はテストされません。 他のネットワーク オプションの詳細については、[Site Recovery でのフェールオーバー](site-recovery-failover.md)に関するページを参照してください。

    ![テスト ネットワークの選択](./media/site-recovery-vmm-san/test-fail1.png)

6. テスト VM は、レプリカ VM が存在するホストに作成されます。 レプリカ VM が配置されているクラウドには追加されません。
2. レプリケーション後に、レプリカ VM は、プライマリ仮想マシンの IP アドレスとは異なる IP アドレスを保有します。 DHCP からアドレスを発行している場合は、アドレスは自動的に更新されます。 DHCP を使っていない場合に、同じアドレスにしたいときは、2 つのスクリプトを実行する必要があります。
3. 次のスクリプトを実行して、IP アドレスを取得します。

       $vm = Get-SCVirtualMachine -Name <VM_NAME>
       $na = $vm[0].VirtualNetworkAdapters>
       $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
       $ip.address  

4. 次のサンプル スクリプトを実行して、DNS を更新します。 取得した IP アドレスを指定します。

       [string]$Zone,
       [string]$name,
       [string]$IP
       )
       $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
       $newrecord = $record.clone()
       $newrecord.RecordData[0].IPv4Address  =  $IP
       Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## <a name="next-steps"></a>次のステップ

テスト フェールオーバーを実行して、環境が予想どおりに動作することを確認したら、[Site Recovery でのフェールオーバー](site-recovery-failover.md)に関するページでさまざまな種類のフェールオーバーについて確認します。

