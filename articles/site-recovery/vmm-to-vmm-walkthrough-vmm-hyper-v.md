---
title: "Azure Site Recovery を使用したセカンダリ サイトへの VMM および Hyper-V のレプリケーションを設定する | Microsoft Docs"
description: "System Center VMM サーバーと Hyper-V ホストをセカンダリ VMM サイトにレプリケートするよう設定する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d0389e3b-3737-496c-bda6-77152264dd98
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 73bd1790c56ac52166f638de2e80c2a2cfb87e53
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="step-4-set-up-vmm-and-hyper-v-for-hyper-v-vm-replication-to-a-secondary-site"></a>手順 4: セカンダリ サイトへの VMM および Hyper-V VM のレプリケーションを設定する 

ネットワークを準備したら、System Center Virtual Machine Manager (VMM) サーバーと Hyper-V ホストを、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用して Hyper-V 仮想マシン (VM) をセカンダリ サイトにレプリケートするよう設定します。 

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。



## <a name="prepare-vmm-servers"></a>VMM サーバーを準備する 

デプロイの準備をするには:


1. VMM サーバーが[サポート要件](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)と[デプロイの前提条件](vmm-to-vmm-walkthrough-prerequisites.md)に準拠していることを確認します。
2. VMM サーバーがインターネットに接続されており、次の URL にアクセスできることを確認します。
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。
    - [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
    - ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。
3. VMM サーバーで[ネットワーク マッピングの準備](vmm-to-vmm-walkthrough-network.md#prepare-for-network-mapping)ができていることを確認します。


## <a name="prepare-hyper-v-hostsclusters"></a>Hyper-V ホストとクラスターの準備

1. Hyper-V ホストとクラスターが[サポート要件](site-recovery-support-matrix-to-sec-site.md#on-premises-servers)と[デプロイの前提条件](vmm-to-vmm-walkthrough-prerequisites.md)に準拠していることを確認します。
2. [Hyper-V VM](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions) の要件を確認します。
3. [ネットワーク](site-recovery-support-matrix-to-sec-site.md#network-configuration)と[ストレージ](site-recovery-support-matrix-to-sec-site.md#storage)の要件を確認します。
4. Hyper-V ホストがインターネットに接続されており、次の URL にアクセスできることを確認します。
    
    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
    - IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。
    - [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
    - ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。

## <a name="prepare-for-single-server-deployment"></a>単一サーバー デプロイを準備する


VMM サーバーが 1 つしかない場合は、この記事で説明されているように、VMM クラウド内の Hyper-V ホストにある VM を [Azure](hyper-v-site-walkthrough-overview.md) またはセカンダリ VMM クラウドにレプリケートできます。 クラウド間でのレプリケートはシームレスでないため、最初の方法をお勧めします。

クラウド間でレプリケートする場合は、1 つのスタンドアロン VMM サーバーを使用するか、ストレッチWindows クラスターにデプロイされた 1 つの VMM サーバーを使用して、レプリケートできます。

### <a name="replicate-with-a-standalone-vmm-server"></a>スタンドアロン VMM サーバーでレプリケートする

このシナリオでは、単一の VMM サーバーを仮想マシンとしてプライマリ サイトにデプロイし、Site Recovery と Hyper-V レプリカを使用して、この VM をセカンダリ サイトにレプリケートします。

1. **Hyper-V VM で VMM をセットアップします**。 VMM で使用される SQL Server インスタンスは同じ VM 上に配置することをお勧めします。 1 つの VM を作成するだけでよいので時間節約になります。 SQL Server のリモート インスタンスを使用したときに障害が発生した場合、VMM を回復するには、そのインスタンスを回復しておく必要があります。
2. **VMM サーバーに少なくとも 2 つのクラウドが構成されていることを確認します**。 一方のクラウドにはレプリケートを行う VM が含まれ、もう一方のクラウドはセカンダリのロケーションとして機能します。 保護する VM が含まれているクラウドは、 [前提条件](#prerequisites)を満たしている必要があります。
3. この記事で説明されているとおりに、Site Recovery をセットアップします。 VMM サーバーを作成してコンテナーに登録し、レプリケーション ポリシーを設定して、レプリケーションを有効にします。 ソースとターゲットの VMM 名は同じになります。 初期レプリケーションがネットワーク経由で行われるように指定します。
4. ネットワーク マッピングをセットアップするときは、プライマリ クラウドの VM ネットワークをセカンダリ クラウドの VM ネットワークにマップします。
5. Hyper-V マネージャー コンソールで、VMM VM を含む Hyper-V ホスト上での Hyper-V レプリカを有効にし、それから VM 上のレプリケーションを有効にします。 Site Recovery で保護されているクラウドに対して VMM 仮想マシンを追加しないようにしてください。これは、Hyper-V レプリカの設定が Site Recovery によってオーバーライドされないようにするためです。
6. フェールオーバー向けに復旧計画を作成する場合は、ソースとターゲットに同じ VMM サーバーを使用します。
7. 完全な停電の場合、フェールオーバーと回復は次のように実行します。

   1. セカンダリ サイトの Hyper-V Manager コンソールで、計画外のフェールオーバーを実行して、プライマリ VMM VM をセカンダリ サイトにフェールオーバーします。
   2. VMM VM が稼働していることを確認し、計画外のフェールオーバーをコンテナーで実行して、プライマリ クラウドの VM をセカンダリ クラウドにフェールオーバーします。 フェールオーバーをコミットし、必要に応じて、代わりの回復ポイントを選択します。
   3. 計画外のフェールオーバーが完了すると、プライマリ サイトからすべてのリソースに再びアクセスできるようになります。
   4. プライマリ サイトが再び使用可能になったら、セカンダリ サイトの Hyper-V Manager コンソールで、VMM VM のレプリケーションの反転を有効にします。 これにより、セカンダリからプライマリへの VM のレプリケーションが開始します。
   5. セカンダリ サイトの Hyper-V Manager コンソールで、計画されたフェールオーバーを実行して、VMM VM をプライマリ サイトにフェールオーバーします。 フェールオーバーをコミットします。 次に、レプリケーションの反転を有効にして、VMM VM が再びプライマリからセカンダリにレプリケートされるようにします。
   6. Recovery Services コンテナーで、ワークロード VM のレプリケーションの反転を有効にして、セカンダリからプライマリへのレプリケーションを開始します。
   7. Recovery Services コンテナーで、計画されたフェールオーバーを実行して、ワークロード VM をプライマリ サイトにフェールバックします。 フェールオーバーをコミットして完了します。 次に、レプリケーションの反転を有効にして、プライマリからセカンダリへのワークロード VM のレプリケーションを開始します。

### <a name="replicate-with-a-stretched-vmm-cluster"></a>拡張された VMM クラスターでレプリケートする

スタンドアロン VMM サーバーを、セカンダリ サイトにレプリケートする VM としてデプロイするのではなく、Windows フェールオーバー クラスターに VM としてデプロイすることで、VMM の可用性を高めることができます。 これにより、ワークロードの回復力とハードウェア障害に対する保護が提供されます。 Site Recovery でデプロイするには、VMM VM を地理的に離れたサイトにまたがるストレッチ クラスターにデプロイする必要があります。 これを行うには、次の手順を実行します。

1. Windows フェールオーバー クラスターの仮想マシンに VMM をインストールし、セットアップ時に、サーバーを可用性が高い状態で実行するオプションを選択します。
2. VMM によって使用される SQL Server インスタンスは SQL Server AlwaysOn 可用性グループでレプリケートする必要があります。それにより、セカンダリ サイト内にデータベースのレプリカが作成されます。
3. こちらの記事 に記載されている手順に従って、コンテナーを作成し、サーバーを登録して、保護を設定します。 クラスターの各 VMM サーバーを Recovery Services コンテナーに登録する必要があります。 そのためには、アクティブなノードに Provider をインストールし、VMM サーバーを登録します。 次に、プロバイダーを他のノードにインストールします。
4. 障害が発生すると、VMM サーバーと対応する SQL Server データベースがフェールオーバーされ、セカンダリ サイトからアクセスされるようになります。



## <a name="next-steps"></a>次のステップ

「[手順 5: コンテナーを設定する](vmm-to-vmm-walkthrough-create-vault.md)」に進みます。
