---
title: "Azure Site Recovery を使用して多層 Dynamics AX デプロイをレプリケートする | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して Dynamics AX をレプリケートして保護する方法について説明します。"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 03127c8f4841b67436c4819628319705af0b2cd5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>Azure Site Recovery を使用して多層 Dynamics AX アプリケーションをレプリケートする

## <a name="overview"></a>概要


Microsoft Dynamics AX は、あらゆる場所での標準化されたプロセス、リソース管理、コンプライアンスの簡素化において、企業で最も一般的な ERP ソリューションです。 アプリケーションは組織の業務にとって非常に重要なので、障害の発生時には、アプリケーションが最短時間で再び稼働することが重要です。

現在、Microsoft Dynamics AX には、既製のディザスター リカバリー機能は含まれていません。 Microsoft Dynamics AX は、Application Object Server、Active Directory (AD)、SQL Database Server、SharePoint Server、Reporting Server などの多くのサーバー コンポーネントで構成されます。これらの各コンポーネントのディザスター リカバリーを手動で管理することは、高価なだけでなく、エラーを起こしかねません。

この記事は [Azure Site Recovery](site-recovery-overview.md) を使用して、Dynamics AX アプリケーションのディザスター リカバリー ソリューションを作成する方法について詳しく説明します。 ワンクリックの復旧計画を使用した計画された/計画されていない/テスト フェールオーバー、サポートされている構成、前提条件についても説明します。
Azure Site Recovery ベースのディザスター リカバリー ソリューションは、Microsoft Dynamics AX により完全にテスト済み、認定、推奨されています。



## <a name="prerequisites"></a>前提条件

Azure Site Recovery を使用して Dynamics AX アプリケーションのディザスター リカバリーを実装するには、次の前提条件を完了する必要があります。

•   オンプレミス Dynamics AX デプロイが設定されている

•   Azure Site Recovery Services コンテナーが Microsoft Azure サブスクリプションに作成されている

•   Azure が復旧サイトである場合は、Azure Virtual Machines 準備状況評価ツールを VM で実行し、Azure VM および Azure Site Recovery Services と互換性があることを確認する


## <a name="site-recovery-support"></a>Site Recovery のサポート

この記事の VMware 仮想マシンを作成するために、Windows Server 2012 R2 Enterprise の Dynamics AX 2012R3 を使用しました。 サイトの回復のレプリケーションはアプリケーションに依存しないため、ここで紹介する推奨事項は次のシナリオにも適用できます。

### <a name="source-and-target"></a>ソースとターゲット

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | はい | はい
**VMware** | はい | はい
**物理サーバー** | はい | あり

## <a name="enable-dr-of-dynamics-ax-application-using-azure-site-recovery"></a>Azure Site Recovery を使用して Dynamics AX アプリケーションの DR を有効にする
### <a name="protect-your-dynamics-ax-application"></a>Dynamics AX アプリケーションを保護する
アプリケーションの完全なレプリケーションと復旧を有効にするには、Dynamics AX 内の各コンポーネントを保護する必要があります。 このセクションの内容について説明します。

**1.Active Directory の保護**

**2.SQL 層の保護**

**3.アプリと Web 層の保護**

**4.ネットワーク構成**

**5.復旧計画**

### <a name="1-setup-ad-and-dns-replication"></a>1.AD と DNS のレプリケーションをセットアップする

Dynamics AX アプリケーションが機能するには、DR サイトに Active Directory が必要となります。 2 つの方法をお勧めしますので、お客様のオンプレミス環境の複雑さに応じて選択してください。

**方法 1**

お客様のアプリケーションの数が少なく、オンプレミスのサイト全体に 1 つのドメイン コントローラーを使用しており、サイト全体をフェールオーバーする場合は、ASR レプリケーションを使用して DC マシンをセカンダリ サイトにレプリケートすることをお勧めします (この方法は、サイト間、サイトと Azure 間の両方に適用できます)。

**方法 2**

お客様のアプリケーションの数が多く、Active Directory フォレストを実行しており、一度に複数のアプリケーションをフェールオーバーする場合は、DR サイト (セカンダリ サイトまたは Azure のいずれかにおいて) で追加のドメイン コントローラーを設定することをお勧めします。

[DR サイトでのドメイン コント ローラーの使用に関する必携ガイド](site-recovery-active-directory.md)を参照してください。 ドキュメントのこれ以降では、DC が DR サイトで使用可能になっていることを前提とします。

### <a name="2-setup-sql-server-replication"></a>手順 2.SQL Server レプリケーションをセットアップする
[SQL 層](site-recovery-sql.md)の保護に推奨されるオプションに関する詳細な技術ガイダンスの必携ガイドを参照してください。

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3.Dynamics AX クライアントと AOS VM の保護を有効にする
VM が[HYPER-V](site-recovery-hyper-v-site-to-azure.md)と [VMware](site-recovery-vmware-to-azure.md)のどちらにデプロイされるかに基づいて、関連する Azure Site Recovery 構成を実行してください。

> [!TIP]
> 推奨されるクラッシュ整合性頻度設定は 15 分です。
>

以下のスナップショットは、「VMware サイトから Azure」の保護のシナリオにおける Dynamics コンポーネントの保護状態を示しています。
![保護された項目](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4.ネットワークを構成する
VM Compute とネットワーク設定を構成する

AX クライアントと AOS VM は、VM ネットワークがフェールオーバー後に適切な DR ネットワークに接続されるように、Azure Site Recovery でネットワーク設定を構成します。 これらの層の DR ネットワークが、SQL 層にルーティング可能なことを確認します。

ネットワーク設定を構成するには、次のスナップショットに示すように、レプリケートされた項目で VM を選択します。

* AOS サーバーには、適切な可用性セットを選択します。

* 静的 IP を使用している場合は、![[ネットワーク設定]](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png) の **[ターゲット IP]** フィールドで仮想マシンに割り当てる IP を指定します。



### <a name="5-creating-a-recovery-plan"></a>5.復旧計画の作成

Azure Site Recovery で復旧計画を作成し、フェールオーバー プロセスを自動化することができます。 復旧計画には、アプリ層と Web 層を追加します。 アプリ層の前にフロントエンドがシャットダウンするように、異なるグループに順序付けます。

1)  サブスクリプションで Azure Site Recovery コンテナーを選択し、[復旧計画] タイルをクリックします。

2)  [+ 復旧計画] をクリックして名前を指定します。

3)  [ソース] と [ターゲット] を選択します。 ターゲットには、Azure またはセカンダリ サイトを指定できます。 Azure を選択した場合は、デプロイ モデルを指定する必要があります。

![復旧計画の作成](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  AOS とクライアント VM に復旧計画を選択し、[✓] をクリックします。
![復旧計画を作成する](./media/site-recovery-dynamics-ax/selectvms.png)


![復旧計画](./media/site-recovery-dynamics-ax/recoveryplan.png)

Dynamics AX アプリケーションの復旧計画をカスタマイズするには、後述するようにさまざまな手順を追加します。 上記のスナップショットは、すべての手順を追加した後の完全な復旧計画を示しています。

*手順:*

*1.SQL Server のフェールオーバー手順*

SQL Server 特有の復旧手順に関する詳細は、[[SQL Server の DR ソリューション]](site-recovery-sql.md) 必携ガイドを参照してください。

*2.フェールオーバー グループ 1: AOS VM のフェールオーバー*

選択された復旧ポイントがデータベース PIT にできるだけ近いものの、先ではないことを確認してください。

*3.スクリプト: ロード バランサー (E～A のみ) を追加する*AOS VM グループが立ち上がった後に (Azure Automation を介して) スクリプトを追加して、ロード バランサーを追加します。 このタスクを実行するのにスクリプトを使用することができます。 [多層アプリケーション DR にロード バランサーを追加する方法](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)の記事を参照してください。

*4.フェールオーバー グループ 2: AX クライアント VM のフェールオーバー*
復旧計画の一部として、Web 層の VM をフェールオーバーします。


### <a name="doing-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバー実行時、それぞれ AD と SQL Server に固有の考慮事項については [AD DR ソリューション] と [SQL Server の DR ソリューション] 必携ガイドを参照してください。

1.  Azure Portal で、Site Recovery コンテナーを選択します。
2.  Dynamics AX 用に作成された復旧計画をクリックします。
3.  [テスト フェールオーバー] をクリックします。
4.  仮想ネットワークを選択して、テスト フェールオーバー プロセスを開始します。
5.  セカンダリ環境が立ち上がったら、検証を行うことができます。
6.  検証が完了したら、[Validations Complete (検証完了)] をクリックします。テスト フェールオーバー環境がクリーニングされます。

[このガイダンス](site-recovery-test-failover-to-azure.md)に従って、テスト フェールオーバーを実行します。

### <a name="doing-a-failover"></a>フェールオーバーの実行

1.  Azure Portal で、Site Recovery コンテナーを選択します。
2.  Dynamics AX 用に作成された復旧計画をクリックします。
3.  [フェールオーバー] をクリックし、[フェールオーバー] を選択します。
4.  ターゲット ネットワークを選択し、[✓] をクリックしてフェールオーバー プロセスを開始します。

[このガイダンス](site-recovery-failover.md)に従って、フェールオーバーを実行します。

### <a name="perform-a-failback"></a>フェールバックを実行する

フェールバック実行時、SQL Server に固有の考慮事項については [SQL Server の DR ソリューション] 必携ガイドを参照してください。

1.  Azure Portal で、Site Recovery コンテナーを選択します。
2.  Dynamics AX 用に作成された復旧計画をクリックします。
3.  [フェールオーバー] をクリックし、フェールオーバーを選択します。
4.  [方向の変更] をクリックします。
5.  適切なオプションであるデータ同期と VM 作成のオプションを選択します。
6.  [✓] をクリックして、フェールバック プロセスを開始します。


[このガイダンス](site-recovery-failback-azure-to-vmware.md)に従って、フェールバックを実行します。

##<a name="summary"></a>概要
Azure Site Recovery を使用すると、Dynamics AX アプリケーションに合わせて完全な自動障害復旧計画を作成できます。 障害発生時には、任意の場所から数秒以内にフェールオーバーを開始し、数分以内にアプリケーションを稼働させることができます。

## <a name="next-steps"></a>次のステップ
Azure Site Recovery によるエンタープライズ ワークロード保護の詳細については、「[Azure Site Recovery で保護できるワークロード](site-recovery-workload.md)」をご覧ください。

