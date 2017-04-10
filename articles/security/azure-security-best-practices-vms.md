---
title: "Azure 仮想マシンのセキュリティに関するベスト プラクティス | Microsoft Docs"
description: "この記事では、Azure 内の仮想マシンのセキュリティに適用されるさまざまなベスト プラクティスについて取り上げます。"
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 1d010dd85ccf2dd708a7740eb8399fc06a603574
ms.lasthandoff: 03/22/2017


---
# <a name="best-practices-for-azure-vm-security"></a>Azure VM のセキュリティのベスト プラクティス

ほとんどの IaaS (サービスとしてのインフラストラクチャ) で、クラウド コンピューティングを使用している組織にとっての主要なワークロードは [Azure 仮想マシン (VM)](https://docs.microsoft.com/en-us/azure/virtual-machines/) です。 このことは特に、ワークロードを段階的にクラウドに移行する[ハイブリッド シナリオ](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)で顕著となります。 [IaaS のセキュリティに関する一般的な考慮事項](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)に従い、セキュリティのベスト プラクティスをすべての VM に適用することになります。

この記事では、VM のセキュリティに関するさまざまなベスト プラクティスについて説明しています。いずれも、VM に関するお客様の経験や Microsoft 独自の経験から導き出されたものです。

これらのベスト プラクティスは、集約された意見に基づくものであり、Azure プラットフォームの最新の機能に対応しています。 人の考え方やテクノロジは時の経過と共に変化するため、それらの変化に応じて、この記事も定期的に更新される予定です。

それぞれのベスト プラクティスについて、この記事では次の事柄を取り上げます。

* ベスト プラクティスはどのようなものか。
* そのようにすることがなぜ推奨されるのか。
* それを実践するにはどうすればよいのか。
* 実践しなかった場合はどうなるのか。
* そのベスト プラクティスに代わる対処法。

この記事では、VM のセキュリティについて次のベスト プラクティスを詳しく見ていきます。

* VM の認証とアクセスの制御
* VM の可用性とネットワーク アクセス
* 暗号化の強制による VM 内の保存データの保護
* VM の更新の管理
* VM のセキュリティ体制の維持
* VM パフォーマンスの監視

## <a name="vm-authentication-and-access-control"></a>VM の認証とアクセスの制御

VM 保護の第一歩は、承認されたユーザーのみが新しい VM をセットアップできるようにすることです。 [Azure Resource Manager ポリシー](../azure-resource-manager/resource-manager-policy.md)を使って組織内のリソースに対する規則を確立し、カスタマイズしたポリシーを作成して、[リソース グループ](../azure-resource-manager/resource-group-overview.md)などのリソースにポリシーを適用できます。

リソース グループに属する VM は、そのポリシーを自然に継承します。 この方法で VM を管理することをお勧めしますが、[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) を使って、個々の VM に対するアクセス ポリシーを制御することもできます。

Resource Manager ポリシーと RBAC で VM のアクセス制御を行えるようにすると、VM 全体のセキュリティを高めることができます。 ライフ サイクルが同じ VM は、同じリソース グループにまとめることをお勧めします。 皆さんが使用するリソースは、リソース グループを使ってデプロイして監視し、請求額をまとめることができます。 VM へのアクセスと VM のセットアップをユーザーに許可する際は、[最小限の権限の原則](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)に従ってください。 また、ユーザーに権限を割り当てる際は、次に示した組み込みの Azure ロールの使用を検討してください。

- [仮想マシン共同作成者](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): VM を管理することはできますが、それが接続されている仮想ネットワークまたはストレージ アカウントを管理することはできません。
- [従来の仮想マシン共同作成者](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): クラシック デプロイメント モデルを使って作成された VM を管理することはできますが、その VM が接続されている仮想ネットワークまたはストレージ アカウントを管理することはできません。
- [セキュリティ マネージャー](../active-directory/role-based-access-built-in-roles.md#security-manager): セキュリティ コンポーネント、セキュリティ ポリシー、VM を管理できます。
- [DevTest Labs ユーザー](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): すべてを表示し、VM を接続、開始、再起動、シャットダウンできます。

アカウントとパスワードを管理者どうしで共有したり、複数のユーザー アカウントまたは複数のサービスに同じパスワードを使用したりしないでください。特に、ソーシャル メディアなど、管理以外の作業に使用するパスワードを再利用しないでください。 理想としては、[Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) テンプレートを使って安全に VM をセットアップすることをお勧めします。 このアプローチを使うことで、デプロイの選択肢を強化し、デプロイ全体にセキュリティ設定を適用できます。

RBAC などの機能を利用したデータ アクセス制御を組織が適用しない場合、ユーザーに必要以上の権限が付与される可能性があります。 特定のデータに対する不適切なユーザー アクセスが、そのデータのセキュリティ侵害に直結することもあります。

## <a name="vm-availability-and-network-access"></a>VM の可用性とネットワーク アクセス

高可用性を必要とする重要なアプリケーションが仮想マシンで実行されている場合は、複数の VM を使うことを強くお勧めします。 可用性を高めるには、[可用性セット](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md)に少なくとも 2 つの VM を作成します。

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) も、同じ可用性セットに属している負荷分散された VM を必要とします。 これらの VM にインターネットからアクセスをする必要がある場合は、[インターネットに接続するロード バランサー](../load-balancer/load-balancer-internet-overview.md)を構成する必要があります。

VM がインターネットに公開される場合は、[ネットワーク セキュリティ グループ (NSG) でネットワーク トラフィック フローを制御する](../virtual-network/virtual-networks-nsg.md)ことが重要です。 NSG はサブネットに適用できるため、リソースをサブネットごとにグループ化し、NSG をサブネットに適用することで、NSG の数を最小限に抑えることができます。 目的はネットワーク分離のレイヤーを作成することであり、これは Azure で[ネットワーク セキュリティ](../best-practices-network-security.md)機能を適切に構成することにより実現できます。

また、Azure Security Center の JIT (Just In Time) VM アクセス機能を使って、特定の VM にリモート アクセスできるユーザーと時間を制御することもできます。

インターネットに接続する VM にネットワーク アクセス制限を適用していない組織は、リモート デスクトップ プロトコル (RDP) ブルート フォース攻撃などのセキュリティ リスクにさらされます。

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>暗号化の強制による VM 内の保存データの保護

データ プライバシー、コンプライアンス、データ主権を確保するうえで、[保存データの暗号化](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)は欠かせません。 [Azure Disk Encryption](../security/azure-security-disk-encryption.md) を利用して、IT 管理者は Windows と Linux の IaaS VM のディスクを暗号化できます。 Disk Encryption は、業界標準である Windows の BitLocker 機能と Linux の dm-crypt 機能を組み合わせて、OS ディスクとデータ ディスクのボリューム暗号化を行う機能です。

Azure Disk Encryption を適用してデータを保護し、組織のセキュリティ要件とコンプライアンス要件を満たすことができます。 不正なデータ アクセスに関連するリスクを減らすために、暗号化の使用を検討してください。 またドライブの暗号化は、機密データを書き込む前に行うことをお勧めします。

Azure ストレージ アカウントに保存される VM データ ボリュームを保護するため、VM データ ボリュームを必ず暗号化してください。 暗号化キーとシークレットは、[Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/) を使って保護します。

データの暗号化を行っていない組織は、データの整合性に関する問題にさらされることが多くなります。 たとえば侵入を受けたアカウントのデータが許可のないユーザーや悪意のあるユーザーによって盗まれたり、平文のデータが不正アクセスを受けたりするおそれがあります。 このようなリスクにさらされることに加えて、企業は、業界の規制を遵守するために、適切なセキュリティ制御を使用してデータのセキュリティ強化に努めていることを証明する必要もあります。

Disk Encryption の詳細については、「[Windows および Linux IaaS VM の Azure ディスク暗号化](azure-security-disk-encryption.md)」を参照してください。


## <a name="manage-your-vm-updates"></a>VM の更新の管理

Azure VM は、オンプレミスのすべての VM と同様、ユーザーによって管理されることを想定しているため、Azure 側から強制的に Windows の更新プログラムを適用することはありません。 ただし、自動 Windows Update の設定は有効のままにしておくことをお勧めします。 または、[Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) などの適切な更新管理製品を、別の VM かオンプレミスにデプロイするのもひとつの方法です。 WSUS と Windows Update はどちらも、VM を最新の状態に保ちます。 また、スキャン製品を使ってすべての IaaS VM が最新の状態であることを確認することもお勧めします。

Azure によって提供されるストック イメージは、最新の Windows 更新プログラムが含まれるように定期的に更新されます。 ただし、デプロイ時にイメージが最新であることは保証されません。 パブリック リリースから少し (数週間以下) 遅れる可能性があります。 Windows の更新プログラムを確認してすべてインストールすることは、あらゆるデプロイの第一歩です。 独自にイメージをデプロイするときや、独自のライブラリからイメージをデプロイするときは、このことが特に重要となります。 Azure Marketplace の一部として提供されるイメージは常に、既定で自動更新が実行されます。

ソフトウェア更新ポリシーを適用していない組織は、既に修正されている既知の脆弱性を悪用した脅威にさらされやすくなります。 このようなリスクにさらされることに加え、企業は業界の規制を遵守するために、適切なセキュリティ制御を使用して、クラウドに存在するワークロードのセキュリティ強化に努めていることを証明する必要があります。

ソフトウェア更新のベスト プラクティスについては、従来のデータセンターと Azure IaaS とで多くの類似点があります。 したがって、現在のソフトウェア更新ポリシーを評価して、VM を対象に含めることをお勧めします。

## <a name="manage-your-vm-security-posture"></a>VM のセキュリティ体制の維持

サイバー脅威は進化しています。VM を保護するためには、脅威をすばやく検出し、リソースへの不正アクセスを防止し、アラートをトリガーして、偽陽性を減らすことができる、より高性能の監視機能が必要です。 この種のワークロードのセキュリティ体制は、更新の管理から安全なネットワーク アクセスまで、VM のセキュリティに関するさまざまな側面から成り立っています。

[Windows VM](../security-center/security-center-linux-virtual-machine.md) と [Linux VM](../security-center/security-center-intro.md) のセキュリティ体制を監視するには、[Azure Security Center](../security-center/security-center-virtual-machine.md) を使用します。 Azure Security Center では、次の機能を利用して VM を保護します。

* 推奨される構成規則を使用して OS のセキュリティ設定を適用する
* 不足している可能性のあるシステムのセキュリティ更新プログラムと重要な更新プログラムを特定してダウンロードする
* エンドポイント保護の推奨事項 (マルウェア対策) をデプロイする
* ディスク暗号化を検証する
* 脆弱性を評価して修復する
* 脅威を検出する

Security Center は脅威を積極的に監視でき、脅威のリスクは**セキュリティ通知**で公開されます。 関連性のある脅威は、**セキュリティ インシデント**と呼ばれる 1 つのビューに集約されます。

Security Center を使って Azure に存在する VM 内の脅威のリスクを特定する方法については、次の動画をご覧ください。

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

許可のないユーザーがセキュリティ機構をかいくぐろうとして行う試みは、VM に対して強固なセキュリティ体制を設けなければ認識できません。

## <a name="monitor-vm-performance"></a>VM パフォーマンスの監視

VM のプロセスが必要以上に多くのリソースを消費しているときは、リソースの酷使が問題になる可能性があります。 VM のパフォーマンスの問題はサービス中断に発展する場合があり、そうなれば可用性というセキュリティの原則を損ないます。 そのため、VM のアクセスを監視することは、問題発生時の対処としてのみならず、通常運用時に測定された基準パフォーマンスと照らして能動的に行うことが不可欠となります。

[Azure 診断ログ ファイル](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)を分析することで、VM のリソースを監視し、パフォーマンスと可用性を損なう可能性がある問題を特定することができます。 Azure 診断の拡張機能は、Windows ベースの VM に関する監視と診断機能を備えています。 この拡張機能を [Azure Resource Manager テンプレート](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)に含めることによって、これらの機能を有効にすることができます。

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) を使ってリソースの正常性を把握することもできます。

VM のパフォーマンスを監視していない組織は、パフォーマンス パターンにおけるある変化が正常であるか異常であるかを判断できません。 VM によって消費されているリソースが通常よりも多ければ、その異常は、外部リソースからの攻撃、またはその VM で実行されているプロセスが侵害を受けたことによるものであると推測できます。

