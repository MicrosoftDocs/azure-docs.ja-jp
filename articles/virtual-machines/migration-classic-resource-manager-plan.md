---
title: クラシックから Azure Resource Manager への移行の計画
description: この記事では、クラシックから Azure Resource Manager への IaaS リソースの移行を計画する方法について説明します。
services: virtual-machines
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 30a02a79e5e6e063c3d21e6626ed1a75c0f61133
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676095"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>クラシックから Azure Resource Manager への IaaS リソースの移行計画

> [!IMPORTANT]
> 現在、IaaS VM の約 90% で [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) が使用されています。 2020 年 2 月 28 日の時点で、クラシック VM は非推奨とされており、2023 年 3 月 1 日に完全に廃止されます。 この非推奨についての[詳細]( https://aka.ms/classicvmretirement)および[それが与える影響](classic-vm-deprecation.md#how-does-this-affect-me)について確認してください。

Azure Resource Manager には多くの優れた機能が用意されていますが、移行をスムーズに進めるには工程をしっかりと計画することが重要です。 時間をかけて計画すると、移行アクティビティの実行中に問題が発生することはありません。

> [!NOTE]
> 以下のガイダンスは、Azure Customer Advisory チームとクラウド ソリューション アーキテクトが大規模な環境を移行する際にお客様と協力して作成したものです。 このドキュメントは、新しい成功のパターンが明らかになった時点で更新される予定です。そのため、新しい推奨事項が追加されていないかどうかをときどき確認してください。

移行の工程には 4 つの一般的なフェーズがあります。

![移行フェーズ](./media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>プラン

### <a name="technical-considerations-and-tradeoffs"></a>技術的な考慮事項とトレードオフ

技術要件の規模、地理的な場所、および運用方法に応じて、次のことを検討する必要があります。

1. Azure Resource Manager が組織にとって適切な理由は何ですか。  移行を行うビジネス上の理由は何ですか。
2. Azure Resource Manager を使用する技術的な理由は何ですか。  活用したい追加の Azure サービスは何ですか (ある場合)。
3. どのアプリケーション (または仮想マシンのセット) を移行に含めますか。
4. 移行 API でどのシナリオがサポートされていますか。  「[サポートされていない機能と構成](migration-classic-resource-manager-overview.md)」をご確認ください。
5. 運用チームはクラシックと Azure Resource Manager の両方でアプリケーション/VM をサポートしますか。
6. Azure Resource Manager は VM のデプロイ、管理、監視、およびレポートのプロセスをどのように変更しますか。  デプロイ スクリプトを更新する必要がありますか。
7. 関係者 (エンド ユーザー、アプリケーション所有者、およびインフラストラクチャ所有者) に通知するためにどのようなプランを用意していますか。
8. 環境の複雑さに応じたメンテナンス期間 (エンド ユーザーおよびアプリケーション所有者がアプリケーションを使用できない期間) を設けていますか。  その場合、どのくらいの期間を予定していますか。
9. 関係者が Azure Resource Manager の使い方を習得するためにどのようなトレーニング プランを用意していますか。
10. 移行のためにどのようなプログラム管理またはプロジェクト管理のプランを用意していますか。
11. Azure Resource Manager への移行のタイムラインおよびその他の関連テクノロジのロード マップはどのようなものですか。  それらは最適に調整されていますか。

### <a name="patterns-of-success"></a>成功のパターン

成功を収めたお客様は詳細なプランを作成しており、そこでは前述の質問についてよく話し合い、その内容を文書化して管理しています。  移行プランがスポンサーや関係者に広く伝わるようにしてください。  また、移行オプションに関する知識を身に付けてください。以下に示す移行ドキュメントをお読みになることを強くお勧めします。

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-cli.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md)
* [Review most common migration errors](migration-classic-resource-manager-errors.md) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md)

### <a name="pitfalls-to-avoid"></a>回避すべき問題

- 計画を行わないこと。  この移行のテクノロジ面の手順は実証済みであり、結果は予測可能なものです。
- プラットフォームでサポートされる移行 API がすべてのシナリオに対応していると想定すること。 サポート対象のシナリオについて確認するには、「[サポートされていない機能と構成](migration-classic-resource-manager-overview.md)」をご覧ください。
- エンド ユーザーに関連するアプリケーションの停止の可能性について計画しないこと。  アプリケーションを使用できない可能性のある時間についてエンド ユーザーに適切に警告するための十分なバッファーを計画してください。


## <a name="lab-test"></a>ラボ テスト

**環境を複製してテスト移行を行う**
  > [!NOTE]
  > 既存の環境の正確な複製は、コミュニティに提供されたツール (Microsoft サポートが正式にサポートするものではありません) を使用して実行します。 そのため、これは **省略可能** な手順ですが、運用環境を使用せずに問題を検出するには最善の方法です。 コミュニティに提供されたツールを使用しない場合は、以下の検証/準備/ドライ ランの中止に関する推奨事項をご覧ください。
  >

  スムーズな移行を行うための最善の方法は、正確なシナリオ (コンピューティング、ネットワーク、およびストレージ) のラボ テストを実施することです。 これにより、次のメリットが得られます。

- 完全に独立したラボまたは既存の非運用環境をテストに使用できます。 繰り返し移行可能であり、破壊的な変更が可能な、完全に独立したラボの使用をお勧めします。  実際のサブスクリプションからメタデータを収集およびハイドレートするスクリプトについては後述します。
- 別のサブスクリプションでラボを作成することをお勧めします。 これは、ラボが繰り返し解体されるためであり、個別の独立したサブスクリプションを用意することで、実際に使用する項目が誤って削除される可能性を削減できます。

  これは、AsmMetadataParser ツールを使用して実施できます。 [このツールについては、こちらをご覧ください](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)。

### <a name="patterns-of-success"></a>成功のパターン

大規模な移行の多くで検出された問題を以下に示します。 これは包括的なリストではないため、詳しくは「[サポートされていない機能と構成](migration-classic-resource-manager-overview.md)」をご覧ください。 これらの技術的な問題が発生するかどうかはわかりませんが、問題が発生した場合は、移行の前に解決しておくと、移行をスムーズに行うことができます。

- **検証/準備/ドライ ランの中止の実施** - これはおそらく、クラシックから Azure Resource Manager への移行を成功させるための最も重要な手順です。 移行 API には、次の 3 つの主要ステップがあります:検証、準備、コミット。 検証では、クラシック環境の状態を把握して、すべての問題の結果を返します。 ただし、一部の問題は Azure Resource Manager スタックに存在する可能性があるため、すべての問題を検証で取得できるわけではありません。 移行プロセスの次の手順である準備では、これらの問題を公開します。 準備では、クラシックから Azure Resource Manager にメタデータを移動しますが、移動をコミットしません。また、クラシック側で何かを削除または変更することもありません。 ドライ ランでは移行の準備を行ってから、移行の準備を中止します (**コミットしません**)。 検証/準備/ドライ ランの中止の目的は、Azure Resource Manager スタック内のすべてのメタデータを確認し、(*プログラムを使用して、またはポータルで*) 調査し、すべてが正しく移行されたかどうかを検証して、技術的な問題に対処することです。  また、移行期間の感覚をつかむことができるため、それに応じたダウンタイムを計画できます。  検証/準備/中止ではユーザー ダウンタイムが発生しないため、アプリケーションの使用が中断されることはありません。
  - ドライ ランの前に以下の項目を解決する必要がありますが、ドライ ラン テストは、準備の手順が失敗しても安全に進められます。 エンタープライズでの移行中に、ドライ ランが移行の準備のための安全かつ貴重な方法であることがわかりました。
  - 準備の実施中は、コントロール プレーン (Azure の管理操作) が仮想ネットワーク全体に対してロックされるため、検証/準備/中止の際に VM のメタデータを変更することはできません。  ただし、それ以外のアプリケーション機能 (RD、VM の使用など) が影響を受けることはありません。  ドライ ランが実施されていることは VM のユーザーにはわかりません。

- **ExpressRoute 回線と VPN** - 現在、承認リンクを使用する ExpressRoute ゲートウェイをダウンタイムなしで移行することはできません。 回避策については、[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線および関連する仮想ネットワークの移行](../expressroute/expressroute-migration-classic-resource-manager.md)に関する記事をご覧ください。

- **VM 拡張機能** - 仮想マシン拡張機能は、実行中の VM を移行する際の最も大きな障害の 1 つとなる可能性があります。 VM 拡張機能の修復には 1 ～ 2 日かかる可能性があるため、それに応じた計画を行ってください。  動作中の VM の VM 拡張機能の状態を報告するには、Azure エージェントを稼働させておく必要があります。 実行中の VM について不適切な状態が返された場合は、移行が停止します。 移行を可能にするためにエージェント自体が正常に動作している必要はありませんが、VM に拡張機能が存在する場合は、移行を進めるために、動作中のエージェントと送信インターネット接続 (DNS を使用) の両方が必要になります。
  - 移行中に DNS サーバーへの接続が失われた場合は、移行準備前のすべての VM から BGInfo v1.\* を除くすべての VM 拡張機能を削除し、Azure Resource Managerの移行後に VM に追加し直す必要があります。  **これは実行中の VM の場合のみです。**  VM が割り当てを解除した状態で停止している場合、VM 拡張機能を削除する必要はありません。 **注:** Azure Diagnostics やセキュリティ センターの監視などの多くの拡張機能は、移行後に再インストールされるため、削除しても問題ありません。
  - また、ネットワーク セキュリティ グループが送信インターネット アクセスを制限していないことを確認してください。 これは、一部のネットワーク セキュリティ グループ構成で発生することがあります。 VM 拡張機能を Azure Resource Manager に移行するには、送信インターネット アクセス (および DNS) が必要です。
  - BGInfo 拡張機能には 2 つのバージョン (v1 と v2) があります。  VM が Azure Portal または PowerShell を使って作成された場合は、VM に v1 拡張機能が搭載される可能性があります。 この拡張機能を削除する必要はありません。この拡張機能は、移行 API によってスキップされます (移行されません)。 ただし、クラシック VM が新しい Azure Portal で作成された場合は、JSON ベースの v2 バージョンの BGInfo が搭載される可能性があります。エージェントが動作しており、送信インターネット アクセス (および DNS) を使用している場合は、この拡張機能を Azure Resource Manager に移行できます。
  - **修復オプション 1**。 送信インターネット アクセス、動作中の DNS サービス、および動作中の Azure エージェントが VM にないことがわかっている場合は、準備の前に、移行の一環としてすべての VM 拡張機能をアンインストールしてから、移行後に VM 拡張機能を再インストールしてください。
  - **修復オプション 2**。 VM 拡張機能が大きな障害になる場合は、別のオプションとして、移行前にすべての VM をシャットダウン/割り当て解除します。 割り当てを解除した VM を移行し、Azure Resource Manager 側でそれらを再起動します。 このオプションのメリットは、VM 拡張機能を移行できる点です。 デメリットは、すべての公開仮想 IP が失われ (これにより移行が失敗に終わる可能性があります)、VM がシャットダウンされて動作中のアプリケーションに大きく影響する点です。

    > [!NOTE]
    > 移行対象である実行中の VM に対して Azure Security Center ポリシーが構成されている場合は、拡張機能を削除する前にセキュリティ ポリシーを停止する必要があります。それ以外の場合は、セキュリティ監視拡張機能の削除後に、VM にその拡張機能が自動的に再インストールされます。

- **可用性セット** - Azure Resource Manager に移行する仮想ネットワーク (vNet) では、クラシック デプロイメント (クラウド サービス) に含まれている VM がすべて 1 つの可用性セットに属しているか、または VM がどの可用性セットにも属していません。 クラウド サービスに複数の可用性セットがある場合は、Azure Resource Manager との互換性がなく、移行が停止します。  また、1 つの可用性セットに複数の VM を含めることはできず、1 つの可用性セットに複数の VM が含まれることもありません。 この問題を解決するには、クラウド サービスを修復または再シャッフルする必要があります。  この処理には時間がかかる可能性があるため、状況に応じた計画を行ってください。

- **Web/ワーカー ロール デプロイメント** - Web およびワーカー ロールを含む Cloud Services を Azure Resource Manager に移行することはできません。 移行を開始するには、最初に Web/worker ロールを仮想ネットワークから削除する必要があります。  一般的な解決策として、ExpressRoute 回線にもリンクされている別のクラシック仮想ネットワークに Web/worker ロール インスタンスを移動するか、コードを新しい PaaS App Services (このドキュメントでは説明しません) に移行します。 前者の再デプロイの場合は、新しいクラシック仮想ネットワークを作成し、Web/worker ロールをその新しい仮想ネットワークに移動/再デプロイして、移動元の仮想ネットワークからデプロイメントを削除します。 コードの変更が不要な方法。 新しい [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)機能を使用すると、Web/worker ロールを含むクラシック仮想ネットワークと、Azure リージョン内の他の仮想ネットワーク (移行対象の仮想ネットワークなど) をピアリングできます (**ピアリングした仮想ネットワークは移行できないため、仮想ネットワークの移行の完了後**)。そのため、同じ機能を提供しても、パフォーマンスが失われたり、待ち時間や帯域幅のペナルティが発生したりすることはありません。 [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)の追加により、Web/worker ロールのデプロイメントを簡単に移行できるようになりました。また、Azure Resource Manager への移行がブロックされることもありません。

- **Azure Resource Manager のクォータ** - Azure リージョンには、クラシックと Azure Resource Manager の両方に個別のクォータ/制限があります。 移行シナリオにおいて新しいハードウェアを使用していない " *(クラシックの既存の VM を Azure Resource Manager に交換した)* " としても、移行の開始前には、十分な容量を持つ Azure Resource Manager のクォータが必要です。 問題となる主要な制限を以下に示します。  制限を引き上げるためのサポート チケットを開いてください。

    > [!NOTE]
    > 移行対象の現在の環境と同じリージョンでは、これらの制限を引き上げる必要があります。
    >

  - ネットワーク インターフェイス
  - ロード バランサー
  - パブリック IP
  - 静的パブリック IP
  - コア
  - ネットワーク セキュリティ グループ
  - ルート テーブル

    最新バージョンの Azure CLI で次のコマンドを使用すると、現在の Azure Resource Manager のクォータを確認できます。

    **コンピューティング** *(コア、可用性セット)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **ネットワーク** *(仮想ネットワーク、静的パブリック IP、パブリック IP、ネットワーク セキュリティ グループ、ネットワーク インターフェイス、ロード バランサー、ルート テーブル)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **ストレージ** *(ストレージ アカウント)*

    ```azurecli
    az storage account show-usage
    ```

- **Azure Resource Manager API 調整制限** - 環境の規模が十分に大きい ( VNET に 400 台を超える VM がある環境など) 場合は、Azure Resource Manager における既定の書き込みの API 調整制限 (現時点では **1200 書き込み/時**) に達する可能性があります。 移行を開始する前に、サブスクリプション用にこの制限を引き上げるためのサポート チケットを作成する必要があります。

- **プロビジョニング タイムアウト VM 状態** - いずれかの VM の状態が **プロビジョニング タイムアウト** の場合、移行前に解決する必要があります。 そのためには、VM をプロビジョニング解除/再プロビジョニングしてダウンタイムを発生させます (ディスクを保持した状態で VM を削除して再作成します)。

- **RoleStateUnknown VM 状態** - **ロール状態不明** エラー メッセージのために、移行が停止した場合は、ポータルを使用して VM を調べ、VM が実行中であることを確認してください。 通常、このエラーは数分後に自然に表示されなくなります (修復は不要です)。多くの場合、仮想マシンの **起動**、**停止**、**再起動** の各操作の際に表示される一時的なものです。 **推奨:** 数分後に移行を再試行します。

- **ファブリック クラスターが存在しない** - さまざまな理由で、特定の VM を移行できない場合があります。 これらの既知のケースの 1 つとして、VM が最近 (たとえば先週) 作成されて、Azure Resource Manager ワークロード用の設定がまだ行われていない Azure クラスターに追加された場合などがあります。  この場合は、**ファブリック クラスターが存在しません** というエラーが表示され、VM を移行できません。 クラスターで Azure Resource Manager がすぐに有効になるため、通常、この問題は数日経つと解決します。 ただし、即時の解決策の 1 つとして、VM の `stop-deallocate` を実行して移行を続行し、移行の完了後に Azure Resource Manager で VM を起動する方法があります。

### <a name="pitfalls-to-avoid"></a>回避すべき問題

- 検証/準備/ドライ ランの中止の移行を省略しないでください。
- ほとんど (すべてではありません) の潜在的な問題は、検証/準備/中止の手順で発生します。

## <a name="migration"></a>移行

### <a name="technical-considerations-and-tradeoffs"></a>技術的な考慮事項とトレードオフ

環境に関する既知の問題に対処したので、これで準備が整いました。

実際の移行では、以下の点を考慮する必要があります。

1. 優先度が高くなると共に、仮想ネットワーク (移行の最小単位) について計画し、スケジュールを設定します。  最初はシンプルな仮想ネットワークを使用し、より複雑な仮想ネットワークに進みます。
2. ほとんどのお客様は非運用環境と運用環境を用意します。  運用環境のスケジュールは最後に設定します。
3. **(省略可能)** 予期しない問題の発生に備えて、十分なバッファーを用意したメンテナンスのためのダウンタイムのスケジュールを設定します。
4. 問題が発生した場合に備えて、サポート チームと連携します。

### <a name="patterns-of-success"></a>成功のパターン

実際の移行を行う前に、前述の「ラボ テスト」に記載されている技術的なガイダンスを考慮し、問題を軽減する必要があります。  十分なテストを実施しておけば、移行はそれほど難しくはありません。  運用環境については、信頼できる Microsoft パートナーや Microsoft プレミア サービスなどの追加のサポートを準備しておくとよいでしょう。

### <a name="pitfalls-to-avoid"></a>回避すべき問題

完全なテストを実施しておかないと、問題が発生し、移行に遅れが出る可能性があります。  

## <a name="beyond-migration"></a>移行後の注意事項

### <a name="technical-considerations-and-tradeoffs"></a>技術的な考慮事項とトレードオフ

これで、Azure Resource Manager への移行が完了し、プラットフォームを最大限に活用できます。  その他のメリットについては、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」をご覧ください。

また、以下の点を考慮してください。

- 移行とその他のアクティビティについてまとめて検討します。  ほとんどのお客様は、アプリケーションのメンテナンス期間を選択します。  その場合は、このダウンタイムを使用して、暗号化や Managed Disks への移行など、Azure Resource Manager のその他の機能を有効にすることができます。
- Azure Resource Manager を使用する技術的な理由とビジネス上の理由について再検討します。Azure Resource Manager でのみ使用可能な追加のサービスを有効にして、環境に適用します。
- PaaS サービスを使用して環境をモダナイズします。

### <a name="patterns-of-success"></a>成功のパターン

Azure Resource Manager で有効にするサービスを、目的を持って選択してください。  多くのお客様は、Azure 環境について、以下のような魅力的な機能を活用しています。

- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md)。
- [より詳細に制御されたデプロイを簡単に行うための Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)。
- [タグ](../azure-resource-manager/management/tag-resources.md)。
- [アクティビティ コントロール](../azure-resource-manager/management/view-activity-logs.md)
- [Azure のポリシー](../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>回避すべき問題

このクラシックから Azure Resource Manager への移行を開始した理由を思い出してください。  そもそものビジネス上の理由は何でしたか。 また、ビジネス上の目的を達成しましたか。


## <a name="next-steps"></a>次のステップ

* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行の概要](migration-classic-resource-manager-overview.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](migration-classic-resource-manager-deep-dive.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行計画](migration-classic-resource-manager-plan.md)
* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](migration-classic-resource-manager-ps.md)
* [クラシックから Azure Resource Manager への IaaS リソースの移行を支援するコミュニティ ツール](migration-classic-resource-manager-community-tools.md)
* [Review most common migration errors](migration-classic-resource-manager-errors.md) (移行の一般的なエラーを確認する)
* [クラシックから Azure Resource Manager への IaaS リソースの移行に関してよく寄せられる質問を確認する](migration-classic-resource-manager-faq.md)
