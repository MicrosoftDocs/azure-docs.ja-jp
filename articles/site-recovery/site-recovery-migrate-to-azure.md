---
title: "Site Recovery を使用した Azure への移行 | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用した Azure への VM や物理サーバーの移行の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/05/2017
ms.author: raynew
ms.openlocfilehash: f4dfe430fba51bd009431ca72279a21be55e3a40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Site Recovery を使用した Azure への移行

仮想マシンと物理サーバーの移行に Azure Site Recovery サービスを使用する方法の概要については、この記事をご覧ください。

Site Recovery とは、クラウド (Azure) またはセカンダリ データセンターへのオンプレミスの物理サーバーおよび仮想マシンのレプリケーションを統制することで BCDR 戦略を支援する Azure サービスです。 プライマリ ロケーションで障害が発生した場合は、セカンダリ ロケーションにフェールオーバーしてアプリとワークロードの可用性を維持します。 プライマリの場所が通常の動作に戻ると、その場所にフェールバックします。 詳細については、「 [Site Recovery とは](site-recovery-overview.md) クラウドへの移行を促進し、Azure が提供するさまざまな機能を利用するために、Site Recovery を使用して既存のオンプレミス ワークロードを Azure に移行することもできます。

移行を実行する方法の簡単な概要については、次のビデオを参照してください。
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

この記事では、[Azure Portal](https://portal.azure.com) でのデプロイについて説明します。 [Azure クラシック ポータル](https://manage.windowsazure.com/)を使用すると、既存の Site Recovery コンテナーを管理できますが、新しいコンテナーを作成することはできません。

コメントがありましたら、この記事の下部に投稿してください。 技術的な質問については、 [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="what-do-we-mean-by-migration"></a>移行が意味すること

Site Recovery をデプロイすると、オンプレミス VM や物理サーバーを、Azure またはセカンダリ サイトにレプリケートできます。 マシンをレプリケートし、障害発生時にプライマリ サイトからマシンをフェールオーバーして、復旧したらプライマリ サイトにフェールバックします。 これ以外にも、Site Recovery を使用すると VM や物理サーバーを Azure に移行できるので、ユーザーは Azure VM としてこれらにアクセスできます。 移行には、レプリケーションとプライマリ サイトから Azure へのフェールオーバー、および完全な移行操作が必要となります。

## <a name="what-can-site-recovery-migrate"></a>Site Recovery で移行できるもの

そのための方法は次のとおりです。

- オンプレミスの Hyper-V VM、VMware VM、物理サーバーで実行されているワークロードを、Azure VM で実行するように移行する。 このシナリオでは、完全なレプリケーションとフェールバックも実行できます。
- Azure リージョン間で [Azure IaaS VM](site-recovery-migrate-azure-to-azure.md) を移行する。 このシナリオでは、現時点でサポートされているのは移行のみです。つまり、フェールバックはサポートされていません。
- [AWS Windows インスタンス](site-recovery-migrate-aws-to-azure.md)を Azure に移行する。 このシナリオでは、現時点でサポートされているのは移行のみです。つまり、フェールバックはサポートされていません。

## <a name="migrate-on-premises-vms-and-physical-servers"></a>オンプレミスの VM と物理サーバーの移行

オンプレミスの Hyper-V VM、VMware VM、物理サーバーを移行するには、通常のレプリケーションに使用されるものとほぼ同じ手順に従います。

1. Recovery Services コンテナーの設定
2. 移行対象に応じて必要な管理サーバー (VMware、VMM、Hyper-V) を構成し、そのサーバーをコンテナーに追加して、レプリケーションの設定を指定します。
3. 移行するマシンのレプリケーションを有効にします。
4. 初回移行の後、簡単なテスト フェールオーバーを実行して、すべてが予期したとおりに動作することを確認します。
5. レプリケーション環境が動作していることを確認したら、シナリオで[サポートされている内容](site-recovery-failover.md)に応じて、計画フェールオーバーまたは計画外フェールオーバーを使用します。 可能であれば計画フェールオーバーの使用をお勧めします。
6. 移行の場合、フェールオーバーをコミットする必要はなく、削除する必要もありません。 代わりに、移行する各マシンの **[移行の完了]** オプションを選択します。
     - **[レプリケートされたアイテム]** で VM を右クリックし、**[移行の完了]** をクリックします。 完了するには **[OK]** をクリックします。 進行状況は、VM のプロパティから **[Site Recovery ジョブ]** の [移行の完了] ジョブを監視することで追跡できます。
     - **[移行の完了]** アクションにより、移行プロセスが完了し、マシンのレプリケーションが削除され、マシンの Site Recovery の課金が停止されます。

![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Azure リージョン間での移行

Site Recovery を使用して、Azure VM をリージョン間で移行できます。 このシナリオでは、移行のみがサポートされます。 つまり、Azure VM をレプリケートし、別のリージョンにフェールオーバーできますが、フェールバックすることはできません。 このシナリオでは、Recovery Services コンテナーを設定し、レプリケーションを管理するオンプレミスの構成サーバーをデプロイして、そのサーバーをコンテナーに追加し、レプリケーション設定を指定します。 移行するマシンのレプリケーションを有効にし、簡単なテスト フェールオーバーを実行します。 **[移行の完了]** オプションを使用して、計画外フェールオーバーを実行します。

## <a name="migrate-aws-to-azure"></a>Azure への AWS の移行

AWS インスタンスを Azure VM に移行できます。 このシナリオでは、移行のみがサポートされます。 つまり、AWS インスタンスをレプリケートして、別のリージョンにフェールオーバーできますが、フェールバックすることはできません。 AWS インスタンスは、移行のために物理サーバーと同じ方法で処理されます。 Recovery Services コンテナーを設定し、レプリケーションを管理するオンプレミスの構成サーバーをデプロイして、そのサーバーをコンテナーに追加し、レプリケーション設定を指定します。 移行するマシンのレプリケーションを有効にし、簡単なテスト フェールオーバーを実行します。 **[移行の完了]** オプションを使用して、計画外フェールオーバーを実行します。




## <a name="next-steps"></a>次のステップ

- [VMware VM を Azure に移行する](site-recovery-vmware-to-azure.md)
- [VMM クラウドの Hyper-V VM を Azure に移行する](site-recovery-vmm-to-azure.md)
- [Hyper-V VM (VMM なし) を Azure に移行する](site-recovery-hyper-v-site-to-azure.md)
- [Azure リージョン間で Azure VM を移行する](site-recovery-migrate-azure-to-azure.md)
- [AWS インスタンスを Azure に移行する](site-recovery-migrate-aws-to-azure.md)
- ディザスター リカバリーの要件に沿って、[移行したマシンを別のリージョンにレプリケートできるように準備する](site-recovery-azure-to-azure-after-migration.md)
- [Azure 仮想マシンをレプリケート](site-recovery-azure-to-azure.md)してワークロードの保護を開始する
