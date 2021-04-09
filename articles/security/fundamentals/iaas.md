---
title: Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス | Microsoft Docs
description: " ワークロードを Azure IaaS に移行すると、設計を再評価する機会が得られます "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 6f073777930b4d026d826d2c3586e0886f906206
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503081"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure における IaaS ワークロードのセキュリティに関するベスト プラクティス
この記事では、VM とオペレーティング システムのセキュリティに関するベスト プラクティスについて説明します。

これらのベスト プラクティスは、集約された意見に基づくものであり、Azure プラットフォームの最新の機能に対応しています。 人の考え方やテクノロジは時間の経過と共に変化する可能性があるため、この記事はそれらの変化に応じて更新されます。

ほとんどの IaaS (サービスとしてのインフラストラクチャ) で、クラウド コンピューティングを使用している組織にとっての主要なワークロードは [Azure 仮想マシン (VM)](../../virtual-machines/index.yml) です。 この事実は、ワークロードを段階的にクラウドに移行する[ハイブリッド シナリオ](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)で顕著となります。 [IaaS のセキュリティに関する一般的な考慮事項](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)に従い、セキュリティのベスト プラクティスをすべての VM に適用することになります。

## <a name="protect-vms-by-using-authentication-and-access-control"></a>認証とアクセス制御を使用して VM を保護する
VM 保護の第一歩は、承認されたユーザーのみが新しい VM を設定し、VM にアクセスできるようにすることです。

> [!NOTE]
> Azure の Linux VM のセキュリティを強化するために、Azure AD の認証と統合できます。 [Linux VM に Azure AD の認証を使用する](../../virtual-machines/linux/login-using-aad.md)ことで、VM へのアクセスを許可/拒否するポリシーを一元管理および施行します。
>
>

**ベスト プラクティス**: VM へのアクセスを制御する。   
**詳細**: [Azure ポリシー](../../governance/policy/overview.md)を使用して、組織内のリソース向けの規則を確立し、カスタマイズ ポリシーを作成します。 これらのポリシーを[リソース グループ](../../azure-resource-manager/management/overview.md)などのリソースに適用します。 リソース グループに属する VM は、それらのポリシーを継承します。

組織に多数のサブスクリプションがある場合は、これらのサブスクリプションのアクセス、ポリシー、およびコンプライアンスを効率的に管理する方法が必要になることがあります。 [Azure 管理グループ](../../governance/management-groups/overview.md)の範囲は、サブスクリプションを上回ります。 サブスクリプションを管理グループ (コンテナー) にまとめ、それらのグループに管理条件を適用できます。 管理グループ内のすべてのサブスクリプションは、グループに適用された条件を自動的に継承します。 管理グループを使うと、サブスクリプションの種類に関係なく、大きな規模でエンタープライズ レベルの管理を行うことができます。

**ベスト プラクティス**: VM の設定とデプロイ方法のばらつきを減らす。   
**詳細**: [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) テンプレートを使用して、デプロイの選択の自由を強化し、環境内の VM を理解してインベントリを実行しやすくします。

**ベスト プラクティス**: 特権アクセスをセキュリティで保護する。   
**詳細**: [最低限の特権](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)と Azure に組み込まれているロールを使用して、ユーザーが VM へのアクセスと設定を実行できるようにします。

- [仮想マシンの共同作業者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): VM を管理することはできますが、それが接続されている仮想ネットワークまたはストレージ アカウントを管理することはできません。
- [従来の仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor):クラシック デプロイ モデルを使って作成された VM を管理することはできますが、その VM が接続されている仮想ネットワークまたはストレージ アカウントを管理することはできません。
- [セキュリティ管理者](../../role-based-access-control/built-in-roles.md#security-admin):Security Center のみ: セキュリティ ポリシーの表示、セキュリティ状態の表示、セキュリティ ポリシーの編集、アラートと推奨事項の表示、アラートと推奨事項の却下を行うことができます。
- [DevTest Labs ユーザー](../../role-based-access-control/built-in-roles.md#devtest-labs-user):すべてを表示し、VM を接続、開始、再起動、シャットダウンできます。

サブスクリプション管理者と共同管理者は、この設定を変更して、彼らをサブスクリプション内のすべての VM の管理者にすることができます。 マシンにログインするすべてのサブスクリプション管理者と共同管理者を信頼できることを確認してください。

> [!NOTE]
> ライフサイクルが同じ VM は、同じリソース グループにまとめることをお勧めします。 皆さんが使用するリソースは、リソース グループを使ってデプロイして監視し、請求額をまとめることができます。
>
>

組織は、VM へのアクセスと設定を制御することで、VM の総合的なセキュリティを向上させることができます。

## <a name="use-multiple-vms-for-better-availability"></a>可用性を高めるために複数の VM を使用する
高可用性を必要とする重要なアプリケーションが仮想マシンで実行されている場合は、複数の VM を使うことを強くお勧めします。 可用性を高めるには、[可用性セット](../../virtual-machines/availability-set-overview.md)または可用性[ゾーン](../../availability-zones/az-overview.md)を使用します。

可用性セットは、Azure で使用できる論理グループであり、グループに配置された VM リソースは、Azure データ センター内にデプロイされるときに互いに分離されます。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されることが保証されます。 ハードウェアまたは Azure ソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、顧客は引き続きアプリケーション全体を利用できます。 可用性セットは、信頼性の高いクラウド ソリューションを構築する際に不可欠な機能です。

## <a name="protect-against-malware"></a>マルウェアを防ぐ
ウイルスやスパイウェアなどの悪意のあるソフトウェアを識別して削除するために、マルウェア対策保護を導入する必要があります。 [Microsoft Antimalware](antimalware.md) または Microsoft パートナーのエンドポイント保護ソリューション ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html)、[Broadcom](https://www.broadcom.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://www.microsoft.com/windows/comprehensive-security)、および [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)) をインストールします。

Microsoft Antimalware には、リアルタイム保護、スケジュールされたスキャン、マルウェアの駆除、シグネチャの更新、エンジンの更新、サンプルのレポート、および除外イベントの収集などの機能が含まれます。 運用環境とは別にホストされている環境では、マルウェア対策拡張機能を使用して、VM とクラウド サービスを保護できます。

デプロイと検出の組み込み (アラートとインシデント) を容易にするために、Microsoft Antimalware とパートナー ソリューションを [Azure Security Center](../../security-center/index.yml) と統合できます。

**ベスト プラクティス**: マルウェアから保護するためにマルウェア対策ソリューションをインストールする。   
**詳細**: [Microsoft パートナーのソリューションまたは Microsoft Antimalware をインストール](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-)します。

**ベスト プラクティス**: マルウェア対策ソリューションを Security Center と統合して、保護の状態を監視する。   
**詳細**: [Security Center でエンドポイントの保護に関する問題を管理](../../security-center/security-center-partner-integration.md)します。

## <a name="manage-your-vm-updates"></a>VM の更新の管理
Azure VM は、他の VM と同じように、ユーザーによって管理されることを意図しています。 Azure では、それらに対して Windows 更新プログラムをプッシュしません。 VM の更新は、お客様が管理する必要があります。

**ベスト プラクティス**: VM を最新の状態に保つ。   
**詳細**: Azure Automation の [Update Management](../../automation/update-management/overview.md) ソリューションを使用すると、Azure、オンプレミスの環境、またはその他のクラウド プロバイダーにデプロイされた Windows コンピューターと Linux コンピューターに関して、オペレーティング システムの更新プログラムを管理できます。 すべてのエージェント コンピューターで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

Update Management で管理されるコンピューターでは、評価と更新プログラムのデプロイに次の構成を使用します。

- Windows または Linux 用の Microsoft Monitoring Agent (MMA)
- PowerShell Desired State Configuration (DSC) (Linux の場合)
- Automation Hybrid Runbook Worker
- Microsoft Update または Windows Server Update Services (WSUS) (Windows コンピューターの場合)

Windows Update を使用している場合は、自動 Windows Update の設定は有効のままにしておきます。

**ベスト プラクティス**: デプロイ時に作成したイメージに Windows 更新プログラムの最新の適用が含まれていることを確認する。   
**詳細**: Windows の更新プログラムを確認してすべてインストールすることは、あらゆるデプロイの第一歩です。 独自にイメージをデプロイするときや、独自のライブラリからイメージをデプロイするときは、このことが特に重要となります。 ただし、Azure Marketplace のイメージは既定で自動的に更新されますが、公開リリース後は遅れが発生する可能性があります (最大数週間)。

**ベスト プラクティス**: VM を定期的に再デプロイして、OS の最新バージョンを使用する。   
**詳細**: VM を [Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用して VM を定義して、簡単に再デプロイできるようにします。 テンプレートを使用すると、必要なときに、修正プログラムが適用されセキュリティで保護された VM を設定できます。

**ベスト プラクティス**: VM にセキュリティ更新プログラムを迅速に適用する。   
**詳細**: Azure Security Center (Free レベルまたは Standard レベル) を有効にし、[不足しているセキュリティ更新プログラムを特定して適用](../../security-center/asset-inventory.md)します。

**ベスト プラクティス**: 最新のセキュリティ更新プログラムをインストールする。   
**詳細**: お客様が最初に Azure に移動するワークロードに、ラボと外部向けのシステムがあります。 インターネットへのアクセスが必要なアプリケーションまたはサービスを Azure VM でホストしている場合は、修正プログラムの適用を忘れずに実行してください。 これは、オペレーティング システムへの修正プログラムの適用だけではありません。 サード パーティ アプリケーションでも、修正プログラムの未適用による脆弱性が原因で問題が発生する可能性があります。このような問題は、適切な修正プログラム管理が行われていれば回避できます。

**ベスト プラクティス**: バックアップ ソリューションをデプロイしてテストする。   
**詳細**: セキュリティ更新プログラムと同様、バックアップについても、他の操作と同じ方法で対処する必要があります。 これは、クラウドに拡張する運用環境に含まれるシステムに該当します。

テスト用システムと開発用システムは、ユーザーがオンプレミス環境の経験で使い慣れているのと同様の復元機能を提供できるバックアップ戦略に沿う必要があります。 Azure に移動する運用ワークロードは、可能な限り、既存のバックアップ ソリューションと統合するのが適切です。 または、[Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) を使用してバックアップ要件に対処することもできます。

ソフトウェア更新ポリシーを適用していない組織は、既に修正されている既知の脆弱性を悪用した脅威にさらされやすくなります。 企業は業界の規制を遵守するために、適切なセキュリティ制御を使用して、クラウドに存在するワークロードのセキュリティ強化に努めていることを証明する必要があります。

ソフトウェア更新のベスト プラクティスについては、従来のデータセンターと Azure IaaS とで多くの類似点があります。 現在のソフトウェア更新ポリシーを評価して、Azure 内に配置されている VM を対象に含めることをお勧めします。

## <a name="manage-your-vm-security-posture"></a>VM のセキュリティ体制の維持
サイバー攻撃の脅威は進化しています。 VM を保護するには、脅威をすばやく検出し、リソースへの不正アクセスを防止し、アラートをトリガーし、偽陽性を減らすことができる、より高性能の監視機能が必要です。

[Windows VM](../../security-center/security-center-introduction.md) と [Linux VM](../../security-center/security-center-introduction.md) のセキュリティ体制を監視するには、[Azure Security Center](../../security-center/security-center-introduction.md) を使用します。 Security Center では、次の機能を利用して VM を保護します。

- 推奨される構成規則を使用して OS のセキュリティ設定を適用する。
- 不足している可能性のあるシステムのセキュリティ更新プログラムと重要な更新プログラムを特定してダウンロードする。
- エンドポイント保護の推奨事項をデプロイする。
- ディスク暗号化を検証する。
- 脆弱性を評価して修復する。
- 脅威を検出する。

Security Center は脅威を積極的に監視でき、脅威のリスクはセキュリティ通知で公開されます。 関連性のある脅威は、セキュリティ インシデントと呼ばれる 1 つのビューに集約されます。

Security Center では、データを [Azure Monitor ログ](../../azure-monitor/logs/log-query-overview.md)に格納します。 Azure Monitor ログには、アプリケーションとリソースの操作に関する分析情報を提供するクエリ言語と分析エンジンがあります。 データは、[Azure Monitor](../../batch/monitoring-overview.md)、管理ソリューション、およびクラウドやオンプレミスの仮想マシンにインストールされたエージェントからも収集されます。 この共有機能は、環境の全体像を把握するうえで役に立ちます。

許可のないユーザーがセキュリティ制御を回避しようとする試みは、VM を強固なセキュリティで保護していなければ認識できません。

## <a name="monitor-vm-performance"></a>VM パフォーマンスの監視
VM のプロセスが必要以上に多くのリソースを消費しているときは、リソースの酷使が問題になる可能性があります。 VM のパフォーマンスの問題はサービス中断に発展する場合があり、そうなれば可用性というセキュリティの原則を損ないます。 CPU またはメモリの使用率の高さはサービス拒否 (DoS) 攻撃を示唆していることがあるため、IIS や他の Web サーバーをホストしている VM にとって、これは特に重要です。 VM のアクセスを監視することは、問題発生時の対処としてのみならず、通常運用時に測定された基準パフォーマンスと照らして能動的に行うことが不可欠となります。

[Azure Monitor](../../azure-monitor/data-platform.md) を使用してリソースの正常性を把握することをお勧めします。 Azure Monitor には次の特長があります。

- [Azure 診断ログ ファイル](../../azure-monitor/essentials/platform-logs-overview.md):VM のリソースを監視し、パフォーマンスと可用性を損なう可能性がある問題を特定できます。
- [Microsoft Azure Diagnostics 拡張機能](../../azure-monitor/agents/diagnostics-extension-overview.md):Windows VM 監視と診断機能を備えています。 この拡張機能を [Azure Resource Manager テンプレート](../../virtual-machines/extensions/diagnostics-template.md)に含めることによって、これらの機能を有効にすることができます。

VM のパフォーマンスを監視していない組織は、パフォーマンス パターンにおけるある変化が正常であるか異常であるかを判断できません。 消費しているリソースが通常よりも多い VM は、外部リソースからの攻撃を受けているか、その VM で実行されているプロセスが侵害されていることを示唆している可能性があります。

## <a name="encrypt-your-virtual-hard-disk-files"></a>仮想ハード ディスク ファイルを暗号化する
ブート ボリュームとストレージに保存されているデータ ボリュームに加え、暗号化キーとシークレットを保護できるように、仮想 ハード ディスク (VHD) を暗号化することをお勧めします。

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) を使用して、Windows と Linux の IaaS 仮想マシンのディスクを暗号化できます。 Azure Disk Encryption では、Windows の業界標準である [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を使用して、OS とデータ ディスクのボリュームの暗号化を提供します。 このソリューションは [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 また、このソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。

Azure Disk Encryption 使用時のベスト プラクティスを次に示します。

**ベスト プラクティス**: VM の暗号化を有効にする。   
**詳細**: Azure Disk Encryption は、暗号化キーを生成してキー コンテナーに書き込みます。 Key Vault の暗号化キーを管理するには、Azure AD 認証が必要です。 この目的で Azure AD アプリケーションを作成します。 認証には、クライアント シークレット ベースの認証か、[クライアント証明書ベースの Azure AD 認証](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)を使用できます。

**ベスト プラクティス**: 暗号化キーのセキュリティに対する追加レイヤーとしてキー暗号化キー (KEK) を使用する。 キー コンテナーに KEK を追加します。   
**詳細**: キー コンテナーにキー暗号化キーを作成するには、[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) コマンドレットを使用します。 キー管理用のオンプレミスのハードウェア セキュリティ モジュール (HSM) から KEK をインポートすることもできます。 詳細については、[Key Vault](../../key-vault/keys/hsm-protected-keys.md) のドキュメントを参照してください。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 このキーのエスクロー コピーをオンプレミスのキー管理 HSM で保持することは、キーを誤って削除した場合の二重の保護を提供します。

**ベスト プラクティス**: ディスクを暗号化する前に、[スナップショット](../../virtual-machines/windows/snapshot-copy-managed-disk.md)またはバックアップ、あるいはその両方を作成する。 バックアップは、暗号化中に予期しないエラーが発生した場合の回復オプションを提供します。   
**詳細**: マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成した後、**Set-AzVMDiskEncryptionExtension** コマンドレットで *-skipVmBackup* パラメーターを指定して、マネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、[Azure Backup](../../backup/backup-azure-vms-encryption.md) に関する記事を参照してください。

**ベスト プラクティス**: 暗号化シークレットがリージョンの境界を越えないようにするため、Azure Disk Encryption ではキー コンテナーと VM を同じリージョンに併置する必要がある。   
**詳細**: 暗号化する VM と同じリージョン内にキー コンテナーを作成して使用します。

Azure Disk Encryption を適用すると、次のビジネス ニーズに対応できます。

- 業界標準の暗号化テクノロジを通して保存中の IaaS VM をセキュリティで保護し、組織のセキュリティおよびコンプライアンス要件に対処します。
- IaaS VM はお客様が管理するキーとポリシーに従って起動します。さらに、キー コンテナー内のそれらの使用状況を監査できます。

## <a name="restrict-direct-internet-connectivity"></a>インターネットへの直接接続を制限する
VM からインターネットへの直接接続を監視して制限します。 攻撃者は、パブリック クラウドの IP 範囲を常にスキャンして開いている管理ポートを検索し、よく使用されるパスワードや修正プログラムが適用されていない既知の脆弱性などの "簡単な" 攻撃を試みます。 次の表に、こうした攻撃から保護するためのベスト プラクティスを示します。

**ベスト プラクティス**: ネットワーク ルーティングやセキュリティの不注意による漏洩を回避する。   
**詳細**: Azure RBAC を使用して、中央のネットワーク グループのみがネットワーク リソースへのアクセス許可を持つようにします。

**ベスト プラクティス**: "あらゆる" 発信元 IP アドレスからアクセス可能な露出した VM を特定して修復する。   
**詳細**: Azure Security Center を使用します。 Security Center では、ネットワーク セキュリティ グループのいずれかに、"あらゆる" 発信元 IP アドレスからのアクセスを許可する 1 つ以上の受信規則が含まれている場合に、インターネットに接続するエンドポイント経由のアクセスを制限するよう推奨します。 Security Center では、これらの受信規則を編集して、実際にアクセスを必要とする発信元 IP アドレスに[アクセスを制限](../../security-center/security-center-network-recommendations.md)するよう推奨します。

**ベスト プラクティス**: 管理ポートを制限する (RDP、SSH)。   
**詳細**: [Just-In-Time (JIT) VM アクセス](../../security-center/security-center-just-in-time.md)を使用すると、Azure VM への受信トラフィックをロックダウンすることができるので、攻撃に対する露出が減り、VM への接続が必要な場合は簡単にアクセスできます。 JIT が有効になっている場合、Security Center ではネットワーク セキュリティ グループの規則の作成により、Azure VM への受信トラフィックがロックダウンされます。 ユーザーは VM 上の受信トラフィックがロックダウンされるポートを選択します。 これらのポートは、JIT ソリューションによって制御されます。

## <a name="next-steps"></a>次のステップ
Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](best-practices-and-patterns.md)」を参照してください。

Azure のセキュリティとそれに関連する Microsoft サービスの一般情報については、以下のリソースを参照してください。
* [Azure セキュリティ チーム ブログ](/archive/blogs/azuresecurity/) – Azure のセキュリティに関する最新情報を提供しています。
* [Microsoft セキュリティ レスポンス センター](https://technet.microsoft.com/library/dn440717.aspx) - このサイトでは、Azure に関する問題を含め、マイクロソフトのセキュリティの脆弱性を報告できます。メールの場合は、secure@microsoft.com 宛に報告してください。