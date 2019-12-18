---
title: Azure Site Recovery を使用した多層 Dynamics AX デプロイのディザスター リカバリー | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して Dynamics AX のディザスター リカバリーを設定する方法について説明します
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5b8aaff3a3418177f92c3b54fb3bb3e99f93810e
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620753"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>多層 Dynamics AX アプリケーションのディザスター リカバリーの設定   




 Dynamics AX は、複数の場所のプロセスの標準化、リソースの管理、およびコンプライアンスの簡素化を行うために企業によって使用されている最も一般的な ERP ソリューションです。 アプリケーションは組織にとってきわめて重要であるため、障害が発生した場合でも、最小の時間で再起動して稼働させる必要があります。

現在、Dynamics AX には、既製のディザスター リカバリー機能は含まれていません。 Dynamics AX は、Windows Application Object Server、Azure Active Directory、Azure SQL Database、SharePoint Server、Reporting Services などの多くのサーバー コンポーネントで構成されています。 これらの各コンポーネントのディザスター リカバリーを手動で管理することは、時間がかかるだけでなく、エラーも起こりがちです。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、Dynamics AX アプリケーションのディザスター リカバリー ソリューションを作成する方法について説明します。 ワンクリックの復旧計画を使用した計画された/計画されていない/テスト フェールオーバー、サポートされている構成、前提条件についても説明します。



## <a name="prerequisites"></a>前提条件

Site Recovery を使用して Dynamics AX アプリケーションのディザスター リカバリーを実装するには、次の前提条件を満たす必要があります。

• オンプレミスの Dynamics AX デプロイを設定する。

• Site Recovery コンテナーを Azure サブスクリプション内に作成する。

• Azure が復旧サイトの場合は、VM 上で Azure Virtual Machines 準備状況評価ツールを実行する。 VM は、Azure Virtual Machines サービスおよび Site Recovery サービスに対応できる必要があります。

## <a name="site-recovery-support"></a>Site Recovery のサポート

この記事を作成するために、VMware 仮想マシンと Dynamics AX 2012 R3 を Windows Server 2012 R2 Enterprise で使用しました。 Site Recovery レプリケーションはアプリケーションに依存しないため、ここで紹介する推奨事項は次のシナリオにも適用できるはずです。

### <a name="source-and-target"></a>ソースとターゲット

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | はい | はい
**VMware** | はい | はい
**物理サーバー** | はい | はい

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Site Recovery を使用して Dynamics AX アプリケーションのディザスター リカバリーを可能にする
### <a name="protect-your-dynamics-ax-application"></a>Dynamics AX アプリケーションを保護する
アプリケーションの完全なレプリケーションとリカバリーを可能にするには、Dynamics AX の各コンポーネントを保護する必要があります。

### <a name="1-set-up-active-directory-and-dns-replication"></a>1.Active Directory と DNS レプリケーションを設定する

Dynamics AX アプリケーションが機能するには、ディザスター リカバリー サイトに Active Directory が必要です。 2 つの方法をお勧めしますので、お客様のオンプレミス環境の複雑さに応じて選択してください。

**方法 1**

オンプレミスのサイト全体で少数のアプリケーションと 1 つのドメイン コントローラーを使用しており、サイト全体をまとめてフェールオーバーすることを計画している。 Site Recovery レプリケーションを使用して、セカンダリ サイトにドメイン コントローラー コンピューターをレプリケートすることをお勧めします (サイトからサイトへのシナリオとサイトから Azure へのシナリオの両方に適用できます)。

**方法 2**

多数のアプリケーションを使用し、Active Directory フォレストを実行中であり、一度にいくつかのアプリケーションをフェールオーバーすることを計画している。 追加のドメイン コントローラーをディザスター リカバリー サイト (セカンダリ サイトまたは Azure) に設定することをお勧めします。

 詳細については、[ディザスター リカバリー サイトでドメイン コントローラーを使用できるようにする](site-recovery-active-directory.md)ための記事を参照してください。 これ以降のドキュメントでは、ドメイン コントローラーがディザスター リカバリー サイトで使用可能になっていることを前提とします。

### <a name="2-set-up-sql-server-replication"></a>2.SQL Server レプリケーションをセットアップする
SQL 層を保護するための推奨されるオプションの技術的なガイダンスについては、[SQL Server と Azure Site Recovery を使用したアプリケーションのレプリケート](site-recovery-sql.md)に関する記事を参照してください。

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3.Dynamics AX クライアントと Application Object Server VM の保護を有効にする
VM が[HYPER-V](site-recovery-hyper-v-site-to-azure.md) または [VMware](site-recovery-vmware-to-azure.md) のどちらにデプロイされるかに基づいて、関連する Site Recovery 構成を実行します。

> [!TIP]
> クラッシュ整合性の頻度を 15 分に構成することをお勧めします。
>

次のスナップショットは、VMware サイトから Azure への保護シナリオにおける Dynamics コンポーネントの保護状態を示しています。

![保護された項目](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4.ネットワークを構成する
**VM のコンピューティングとネットワーク設定を構成する**

Dynamics AX クライアントと Application Object Server VM 用に、Site Recovery にネットワーク設定を構成して、フェールオーバー後に VM ネットワークが適切なディザスター リカバリー ネットワークに接続されるようにします。 これらの層のディザスター リカバリー ネットワークが、SQL 層にルーティング可能なことを確認します。

次のスナップショットに示すように、レプリケートされた項目で VM を選択してネットワーク設定を構成できます。

* Application Object Server サーバー用に、適切な可用性セットを選択します。

* 静的 IP を使用している場合は、VM に割り当てる IP を **[ターゲット IP]** テキスト ボックスに指定します。

    ![ネットワーク設定](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5.復旧計画の作成

Site Recovery で復旧計画を作成して、フェールオーバー プロセスを自動化できます。 復旧計画には、アプリ層と Web 層を追加します。 アプリ層の前にフロントエンドがシャットダウンするように、異なるグループに順序付けます。

1. サブスクリプションで Site Recovery コンテナーを選択し、 **[復旧計画]** タイルを選択します。

2. **[+ 復旧計画]** をクリックし、名前を指定します。

3. **[ソース]** と **[ターゲット]** を選択します。 ターゲットには、Azure またはセカンダリ サイトを指定できます。 Azure を選択した場合は、デプロイ モデルを指定する必要があります。

    ![[復旧計画の作成]](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. 復旧計画用の Application Object Server とクライアント VM を選択し、[✓] を選択します。

    ![項目を選択する](./media/site-recovery-dynamics-ax/selectvms.png)

    復旧計画の例:

    ![復旧計画の詳細](./media/site-recovery-dynamics-ax/recoveryplan.png)

Dynamics AX アプリケーションの復旧計画は、次の手順を追加することでカスタマイズできます。 先のスナップショットは、すべての手順を追加した後の完全な復旧計画を示しています。


* **SQL Server のフェールオーバー手順**:SQL Server 固有のリカバリー手順については、[SQL Server と Azure Site Recovery を使用したアプリケーションのレプリケート](site-recovery-sql.md)に関する記事を参照してください。

* **フェールオーバー グループ 1**:Application Object Server VM をフェールオーバーします。
選択された復旧ポイントは、データベース PIT にできるだけ近いが、先ではないことを確認してください。

* **スクリプト**:ロード バランサーを追加します (E-A のみ)。
Application Object Server VM グループが立ち上がった後、そのグループにロード バランサーを追加する (Azure Automation 経由の) スクリプトを追加します。 このタスクを実行するのにスクリプトを使用することができます。 詳細については、[多層アプリケーションのディザスター リカバリー用のロード バランサーの追加方法](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)に関する記事を参照してください。

* **フェールオーバー グループ 2**:Dynamics AX クライアント VM をフェールオーバーします。 復旧計画の一部として、Web 層の VM をフェールオーバーします。


### <a name="perform-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバー中の Active Directory 固有の詳細については、Active Directory のディザスター リカバリー ソリューションに関する必携ガイドを参照してください。

テスト フェールオーバー中の SQL Server 固有の詳細については、[SQL Server と Azure Site Recovery を使用したアプリケーションのレプリケート](site-recovery-sql.md)に関する記事を参照してください。

1. Azure ポータルに移動し、Site Recovery コンテナーを選択します。

2. Dynamics AX 用に作成された復旧計画を選択します。

3. **[テスト フェールオーバー]** を選択します。

4. テスト フェールオーバー プロセスを開始する仮想ネットワークを選択します。

5. セカンダリ環境が立ち上がったら、検証を行うことができます。

6. 検証が完了したら、 **[Validations Complete]\(検証完了\)** をクリックします。テスト フェールオーバー環境がクリーニングされます。

テスト フェールオーバーの実行の詳細については、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」を参照してください。

### <a name="perform-a-failover"></a>フェールオーバーの実行

1. Azure ポータルに移動し、Site Recovery コンテナーを選択します。

2. Dynamics AX 用に作成された復旧計画を選択します。

3. **[フェールオーバー]** を選択し、 **[フェールオーバー]** を選択します。

4. ターゲット ネットワークを選択し、 **[✓]** をクリックしてフェールオーバー プロセスを開始します。

フェールオーバーの実行の詳細については、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」を参照してください。

### <a name="perform-a-failback"></a>フェールバックの実行

フェールバック中の SQL Server 固有の詳細については、[SQL Server と Azure Site Recovery を使用したアプリケーションのレプリケート](site-recovery-sql.md)に関する記事を参照してください。

1. Azure ポータルに移動し、Site Recovery コンテナーを選択します。

2. Dynamics AX 用に作成された復旧計画を選択します。

3. **[フェールオーバー]** を選択し、 **[フェールオーバー]** を選択します。

4. **[方向の変更]** を選択します。

5. 適切なオプション (データ同期と VM 作成) を選択します。

6. **[✓]** をクリックして、フェールバック プロセスを開始します。


フェールバックの実行の詳細については、[VMware VM の Azure からオンプレミスへのフェールバック](site-recovery-failback-azure-to-vmware.md)に関する記事を参照してください。

## <a name="summary"></a>まとめ
Site Recovery を使用することで、Dynamics AX アプリケーション用の完全に自動されたディザスター リカバリー計画を作成できます。 障害発生時には、任意の場所から数秒以内にフェールオーバーを開始し、数分以内にアプリケーションを稼働させることができます。

## <a name="next-steps"></a>次の手順
Site Recovery によるエンタープライズ ワークロード保護の詳細については、「[Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)」をご覧ください。
