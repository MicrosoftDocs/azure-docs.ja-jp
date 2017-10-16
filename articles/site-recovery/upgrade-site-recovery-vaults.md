---
title: "Site Recovery コンテナーから Azure Recovery Services コンテナーへのアップグレード"
description: "Azure Site Recovery コンテナーを Recovery Services コンテナーにアップグレードする方法について説明します"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Site Recovery コンテナーから Azure Resource Manager ベースの Recovery Services コンテナーへのアップグレード

この記事では、進行中のレプリケーションに影響を与えることなく、Azure Site Recovery コンテナーを Azure Resource Manager ベースの Recovery Service コンテナーにアップグレードする方法について説明します。 Azure Resource Manager の機能とメリットの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。

## <a name="introduction"></a>はじめに
Recovery Services コンテナーは、バックアップやディザスター リカバリーをクラウドでネイティブに管理できる、Azure Resource Manager のリソースです。 これは新しい Azure Portal で使用できる統合コンテナーであり、従来のバックアップ コンテナーと Site Recovery コンテナーに代わるものです。

Recovery Services コンテナーは次のようなさまざまな機能を提供します。

* Azure Resource Manager のサポート: お使いの仮想マシンや物理マシンを保護し、Azure Resource Manager スタックにフェールオーバーできます。

* ディスクの除外: 一時ファイルまたは高チャーンのデータで帯域幅を使い切りたくない場合は、ボリュームをレプリケーションから除外できます。 この機能は現在、*VMware から Azure* および *Hyper-V から Azure* で有効になっており、他のシナリオにも拡張される予定です。

* Premium およびローカル冗長ストレージのサポート: Premium Storage アカウントのサーバーを保護し、1 秒あたりの高い入出力操作 (IOPS) 下で顧客のアプリケーションを保護できるようになりました。 この機能は現在、*VMware から Azure* で有効になっています。

* 効率的な入門エクスペリエンス: 入門エクスペリエンスの機能が拡張され、ディザスター リカバリーの設定が簡単になるように設計されました。

* 同じコンテナーからの Backup と Site Recovery の管理: ディザスター リカバリー用にサーバーを保護、または同じコンテナーからバックアップを実行できるようになり、管理オーバーヘッドを大幅に削減できるようになりました。

アップグレードされたエクスペリエンスと機能の詳細については、[保存、バックアップおよび復旧に関するブログ](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/)をご覧ください。

## <a name="salient-features"></a>注目すべき機能

* 進行中のレプリケーションに影響なし: アップグレード中およびアップグレード後に中断なく進行中のレプリケーションを継続できます。

* 追加コストなし: 更新された機能のセット全体を追加コストなしで入手できます。

* データ損失なし: このプロセスは移行ではなくアップグレードであるため、既存のレプリケーションの復旧ポイントや設定は、アップグレード中およびアップグレード後もそのまま残ります。


## <a name="what-happens-during-the-vault-upgrade"></a>コンテナーのアップグレード中の動作はどうなりますか?

アップグレード中は、新しいサーバーの登録や、仮想マシン (VM) のレプリケーションの有効化などの操作を実行できません。 進行中の保護された項目のコンテナーへのレプリケーションなど、コンテナーに対するデータの読み書きに関与する操作については、引き続き中断されずに行われます。

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>アップグレード後のオートメーションとツールの変更
コンテナーの種類をクラシック デプロイメント モデルから Resource Manager デプロイメント モデルにアップグレードする際は、既存のオートメーションまたはツールを、アップグレード後も確実に機能し続けるよう更新してください。

### <a name="prepare-your-environment-for-the-upgrade"></a>アップグレードのための環境を準備する

* [PowerShell をインストール、またはバージョン 5 以降にアップグレードする](https://www.microsoft.com/download/details.aspx?id=50395)
* [最新バージョンの Azure PowerShell MSI をインストールする](https://github.com/Azure/azure-powershell/releases)
* [Recovery Services コンテナーのアップグレード スクリプトをダウンロードする](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>前提条件
Site Recovery コンテナーを Azure Resource Manager ベースの Recovery Service コンテナーにアップグレードするには、次の条件を満たす必要があります。

* エージェントの最小バージョン: サーバー上にインストールされている Azure Site Recovery プロバイダーのバージョンが 5.1.1700.0 以降である必要があります。

* サポートされている構成: コンテナーを記憶域ネットワーク (SAN) または SQL Server AlwaysOn 可用性グループで構成することはできません。 その他のすべての構成はサポートされています。

    >[!NOTE]
    >アップグレード後は、ストレージ マッピングの管理には PowerShell を使用する必要があります。

* サポートされているデプロイ シナリオ: コンテナーが従来の *VMware から Azure* のデプロイメント モデルでないこと。 続行する前に、まず拡張されたデプロイ モデルに移行します。

* ユーザーによって開始された、管理プレーン操作が関与するアクティブなジョブがない: アップグレード中は管理プレーンへのアクセスが制限されているため、管理プレーンのすべてのアクションを完了してからアップグレードをトリガーしてください。 このプロセスには進行中のレプリケーションは含まれません。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**このアップグレードは進行中のレプリケーションに影響しますか?**

いいえ。 進行中のレプリケーションは、アップグレード中およびアップグレード後も中断されずに続行されます。

**サイト間 VPN や IP 設定などのネットワーク設定はどうなりますか?**

アップグレードはネットワーク設定に影響しません。 Azure からオンプレミスへの接続はすべてそのまま残ります。

**近日中にアップグレードする予定がない場合、コンテナーはどうなりますか?**

以前の Azure Portal における Site Recovery コンテナーのサポートは 2017 年 9 月より廃止されます。 アップグレード機能を使用して、新しいポータルに移行することを強くお勧めします。

**既存のツールにとって、この移行計画はどのような意味がありますか?**  

Resource Manager デプロイメント モデルへのツールの更新は、アップグレード計画で考慮する必要がある最も重要な変更の 1 つです。 Recovery Services コンテナーは、Resource Manager デプロイメント モデルに基づいています。 

**管理プレーンのダウンタイムはどれくらい続きますか?**

通常、アップグレードには約 15 分から 30 分かかり、最大で 1 時間かかる場合があります。

**アップグレード後にロールバックすることはできますか?**

いいえ。 リソースが正常にアップグレードされた後のロールバックはサポートされていません。

**サブスクリプションまたはリソースがアップグレード可能かどうかを確認できますか?**

はい。 プラットフォームでサポートされるアップグレード オプションでは、アップグレードの最初の手順として、リソースをアップグレードできるかどうかを検証します。 検証に失敗すると、該当するエラー メッセージや警告が表示されます。

**アップグレードの問題を報告するにはどうすればよいですか?**

アップグレード中にエラーが発生した場合、エラーに記載されている操作 ID をメモしてください。 Microsoft サポートは、積極的に問題の解決に取り組みます。 サポート チームにお問い合わせいただくこともできます。その際は、サブスクリプション ID、コンテナーの名前、操作 ID をお知らせください。 サポート チームは、可能な限り早く問題を解決できるよう取り組みます。 Microsoft からの明確な指示がない限りは、操作をやり直さないでください。

## <a name="run-the-script"></a>スクリプトを実行する

PowerShell で次のコマンドを実行します。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID: アップグレードするコンテナーに関連付けられているサブスクリプション ID。

* VaultName: アップグレードするコンテナーの名前。

* Location: アップグレードするコンテナーの場所。

* ResourceType: Site Recovery コンテナーの HyperVRecoveryManagerVault。

* TargetResourceGroupName: アップグレードされたコンテナーを配置するリソース グループ。 TargetResourceGroupName には、Azure Resource Manager 内の既存のリソース グループ、または新しいリソース グループを指定できます。 指定された TargetResourceGroupName が存在しない場合、アップグレードの一環としてコンテナーと同じ場所に作成されます。 詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md#resource-groups)」の "リソース グループ" セクションをご覧ください。

    >[!NOTE]
    >リソース グループの名前付けには、特定の制約があります。 コンテナーのアップグレード エラーを防ぐために、注意して名前付け規則を遵守してください。
    >
    >For example:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

代わりに、次のスクリプトを実行してもかまいません。 必要なパラメーターの値を入力します。

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. PowerShell スクリプトによって、資格情報を入力することを求められます。 資格情報を 2 回入力します。1 回目の入力はクラシック デプロイメント モデルのアカウント用であり、2 回目は Azure Resource Manager アカウント用です。

2. 資格情報を入力するとスクリプトはチェックを開始し、インフラストラクチャの設定が上述の要件を満たしているかどうかを確認します。

3. 前提条件のチェックが完了して確認がとれると、コンテナーのアップグレードを開始するよう求められます。 アップグレード プロセスがコンテナーのアップグレードを開始します。 アップグレードの完了までに 15 分から 30 分かかる場合があります。

4. アップグレードが正常に完了すると、アップグレードされたコンテナーに新しい Azure Portal でアクセスできます。

## <a name="post-upgrade-vault-management"></a>アップグレード後のコンテナー管理

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Recovery Services コンテナーで Azure Site Recovery を使用してレプリケートする

* リージョン別に Azure VM を保護できるようになります。 詳細については、「[Azure Site Recovery を使用したリージョン間での Azure VM のレプリケーション](site-recovery-azure-to-azure.md)」をご覧ください。

* Azure への VMware VM のレプリケーションの詳細については、「[Site Recovery を使用して VMware VM を Azure にレプリケートする](vmware-walkthrough-overview.md)」をご覧ください。

* Azure への Hyper-V VM (VMM なし) のレプリケーションの詳細については、「[Hyper-V 仮想マシン (VMM なし) を Azure にレプリケートする](hyper-v-site-walkthrough-overview.md)」をご覧ください。

* Azure への Hyper-V VM (VMM あり) のレプリケーションの詳細については、「[Azure Portal の Site Recovery を使用して VMM クラウド内の Hyper-V 仮想マシンを Azure にレプリケートする](vmm-to-azure-walkthrough-overview.md)」をご覧ください。

* セカンダリ サイトへの Hyper-V VM (VMM あり) のレプリケーションの詳細については、「[Azure Portal を使用して VMM クラウド内の Hyper-V 仮想マシンをセカンダリ VMM サイトにレプリケートする](site-recovery-vmm-to-vmm.md)」をご覧ください。

* セカンダリ サイトへの VMware VM のレプリケーションの詳細については、「[Azure クラシック ポータルでオンプレミスの VMware 仮想マシンまたは物理サーバーをセカンダリ サイトにレプリケートする](site-recovery-vmware-to-vmware.md)」をご覧ください。

### <a name="view-your-replicated-items"></a>レプリケートされたアイテムを表示する

次の図は、Recovery Services コンテナーのダッシュボード ページです。コンテナーのキー エンティティが表示されています。 コンテナーで保護されたエンティティの一覧を表示するには、**[Site Recovery]**  >  **[レプリケートされたアイテム]** をクリックします。


![レプリケートされたアイテム](./media/upgrade-site-recovery-vaults/replicateditems.png)

次の図では、レプリケートされたアイテムを表示するためのワークフローと、フェールオーバーを開始するための **[フェールオーバー]** コマンドを示しています。

![レプリケートされたアイテム](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>レプリケーションの設定を表示する

Site Recovery コンテナーでは、各保護グループは、コピーの頻度、復旧ポイントのリテンション期間、アプリケーションの整合性スナップショットの作成頻度などのレプリケーション設定で構成されています。 Recovery Services コンテナーでは、これらの設定はレプリケーション ポリシーとして構成されています。 ポリシーの名前は保護グループの名前、または *primarycloud_Policy* です。

レプリケーション ポリシーの詳細については、「[VMware から Azure へのレプリケーション ポリシーの管理](site-recovery-setup-replication-settings-vmware.md)」をご覧ください。
