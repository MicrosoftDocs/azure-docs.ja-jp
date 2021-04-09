---
title: テナント間の管理エクスペリエンス
description: Azure の委任されたリソース管理によって、テナント間の管理エクスペリエンスが可能になります。
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 4a20f6efde9c3b01b4a2d7a1f111de23aec87647
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177921"
---
# <a name="cross-tenant-management-experiences"></a>テナント間の管理エクスペリエンス

サービス プロバイダーは、Azure Active Directory (Azure AD) 内から複数の顧客のリソースを管理するために、[Azure Lighthouse](../overview.md) を使用できます。 [Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)を使用して、多くのタスクとサービスをマネージド テナントをまたいで実行できます。

> [!TIP]
> Azure の委任されたリソース管理はまた、[独自の Azure AD テナントが複数存在する企業内で](enterprise.md)使用して、テナントにまたがる管理を簡素化することもできます。

## <a name="understanding-tenants-and-delegation"></a>テナントと委任について

Azure AD テナントは、組織を表したものです。 これは、組織が、Azure、Microsoft 365、またはその他のサービスへのサインアップによって Microsoft との関係を築いたときに提供される Azure AD の専用インスタンスです。 各 Azure AD テナントは、他の Azure AD テナントと区別され分離されており、固有のテナント ID (GUID) があります。 詳細については、「[Azure Active Directory とは](../../active-directory/fundamentals/active-directory-whatis.md)」を参照してください。

通常、顧客の Azure リソースを管理するために、サービス プロバイダーは、その顧客のテナントに関連付けられたアカウントを使用して Azure portal にサインインする必要があります。その際、顧客のテナントの管理者は、そのサービス プロバイダー用にユーザー アカウントを作成して管理する必要があります。

Azure Lighthouse により、オンボード プロセスでは、顧客のテナント内の委任されたサブスクリプションおよびリソース グループに対して操作を実行できる、サービス プロバイダーのテナント内のユーザーを指定します。 これらのユーザーは、その後、自分の資格情報を使用して Azure portal にサインインできます。 Azure portal 内では、彼らは、アクセスできるすべての顧客に属するリソースを管理できます。 これを行うには、Azure portal の [[マイ カスタマー]](../how-to/view-manage-customers.md) ページにアクセスするか、Azure portal または API を使用してその顧客のサブスクリプションのコンテキスト内で直接作業します。

Azure Lighthouse を使用すると、テナントによって異なるアカウントにサインインしなくても、複数の顧客のリソースをより柔軟に管理できます。 たとえば、サービス プロバイダーには、責任とアクセス レベルが異なる 2 人の顧客が存在するとします。 Azure Lighthouse を使用すると、許可されているユーザーは、サービス プロバイダーのテナントにサインインしてこれらのリソースにアクセスすることができます。

![1 つのサービス プロバイダーのテナントを通じて管理される顧客のリソースを示す図。](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API と管理ツールのサポート

委任されたリソースに対して管理タスクをポータル上で直接実行するか、API および管理ツール (Azure CLI や Azure PowerShell など) を使用して実行することができます。 既存の API はすべて、委任されたリソースを操作するときに使用できます。ただし、テナント間の管理において機能がサポートされており、ユーザーが適切なアクセス許可を持っている必要があります。

Azure PowerShell の [Get-AzSubscription コマンドレット](/powershell/module/Az.Accounts/Get-AzSubscription)では、返されたサブスクリプションが管理対象のテナントと管理側テナントのどちらに属しているかを識別できるように、各サブスクリプションの `HomeTenantId` と `ManagedByTenantIds` 属性が表示されます。

同様に、[az account list](/cli/azure/account#az-account-list) などの Azure CLI コマンドでは、`homeTenantId` 属性と `managedByTenants` 属性が表示されます。 Azure CLI の使用時にこれらの値が表示されない場合は、`az account clear` を実行してから `az login --identity` を実行して、キャッシュをクリアしてみてください。

Azure REST API では、[Subscriptions - Get](/rest/api/resources/subscriptions/get) コマンドと [Subscriptions - List](/rest/api/resources/subscriptions/list) コマンドに `ManagedByTenant` が含まれています。

> [!NOTE]
> Azure Lighthouse に関連するテナント情報に加え、これらの API で表示されるテナントには、Azure Databricks または Azure マネージド アプリケーションのパートナー テナントが反映されることがあります。

また、Azure Lighthouse タスクの実行に固有の API も用意されています。 詳細については、**リファレンス** を参照してください。

## <a name="enhanced-services-and-scenarios"></a>強化されたサービスとシナリオ

ほとんどのタスクとサービスは、委任されたリソースに対して、マネージド テナントをまたいで実行できます。 テナント間の管理が特に効果的な重要なシナリオの一部を以下に示します。

[Azure Arc](../../azure-arc/index.yml):

- ハイブリッド サーバーを大規模に管理する - [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md):
  - Azure 内の委任されたサブスクリプションまたはリソース グループに[接続されている Azure 外の Windows Server または Linux コンピューターを管理する](../../azure-arc/servers/onboard-portal.md)
  - Azure Policy やタグ付けなどの Azure コンストラクトを使用して接続されたコンピューターを管理する
  - 顧客のハイブリッド環境全体に同じポリシー セットが提供されていることを確認する
  - Azure Security Center を使用して、顧客のハイブリッド環境全体のコンプライアンスを監視する
- ハイブリッド Kubernetes クラスターを大規模に管理する - [Azure Arc 対応 Kubernetes (プレビュー)](../../azure-arc/kubernetes/overview.md):
  - Azure 内の委任されたサブスクリプションまたはリソース グループに[接続されている Kubernetes クラスターを管理する](../../azure-arc/kubernetes/connect-cluster.md)
  - 接続されたクラスターに [GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) を使用する
  - 接続されたクラスター全体にポリシーを適用する

[Azure Automation](../../automation/index.yml):

- Automation アカウントを使用して、委任されたリソースにアクセスして操作する

[Azure Backup](../../backup/index.yml):

- [オンプレミスのワークロード、Azure VM、Azure ファイル共有などから](../..//backup/backup-overview.md#what-can-i-back-up)顧客データをバックアップして復元する
- [Backup Center](../../backup/backup-center-overview.md) を使用して委任されたすべての顧客リソースのデータを表示する
- [バックアップ エクスプローラー](../../backup/monitor-azure-backup-with-backup-explorer.md)を使用すると、バックアップ項目 (まだバックアップ対象として構成されていない Azure リソースを含む) の運用情報と、委任されたサブスクリプションの監視情報 (ジョブとアラート) を表示できます。 バックアップ エクスプローラーは、現在、Azure VM データに対してのみ使用できます。
- 委任されたサブスクリプション全体で[バックアップ レポート](../../backup/configure-reports.md)を使用して、過去の傾向を追跡し、バックアップ ストレージの使用量を分析し、バックアップと復元を監査します。

[Azure Blueprints](../../governance/blueprints/index.yml):

- Azure Blueprints を使用して、リソース テンプレートおよびその他の成果物のデプロイを調整します (顧客のサブスクリプションを準備するには、[追加のアクセス](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/)が必要です)。

[Azure Cost Management および Billing](../../cost-management-billing/index.yml):

- CSP パートナーは、管理しているテナントから、Azure プランに含まれるお客様に対する、(購入を含めずに) 税引き前消費コストを表示、管理、分析できます。 コストは、小売価格と、お客様のサブスクリプションに対してパートナーが持っている Azure ロールベースのアクセス制御 (Azure RBAC) アクセスに基づきます。

[Azure Key Vault](../../key-vault/general/index.yml):

- 顧客テナント内で Key Vault を作成する
- マネージド ID を使用して顧客テナント内で Key Vault を作成する

[Azure Kubernetes Service (AKS)](../../aks/index.yml):

- ホストされている Kubernetes 環境を管理し、顧客のテナント内でコンテナー化されたアプリケーションをデプロイして管理する
- 顧客テナントのクラスターをデプロイおよび管理する
-   コンテナーに対して Azure Monitor を使用して、顧客テナント全体のパフォーマンスを監視する

[Azure Migrate](../../migrate/index.yml):

- 顧客テナントで移行プロジェクトを作成して VM を移行する

[Azure Monitor](../../azure-monitor/index.yml):

- すべてのサブスクリプションにわたるアラートを表示する機能を使って、委任されたサブスクリプションに対するアラートを表示および更新する
- 委任されたサブスクリプションのアクティビティ ログの詳細を表示する
- [ログ分析](../../azure-monitor/logs/service-providers.md):複数のテナントにあるリモートのワークスペースからデータを照会する (顧客テナントのワークスペースからデータにアクセスするために使用される Automation アカウントは、同じテナント内に作成する必要があることに注意してください)
- [顧客のテナント内でアクティビティ ログ アラートの作成、表示、および管理を行う](../../azure-monitor/alerts/alerts-activity-log.md)
- 顧客のテナント内に、Webhook を使用して管理側テナントで Azure Automation Runbook や Azure Functions などの自動化をトリガーするアラートを作成する
- 顧客テナント内で[診断設定](../..//azure-monitor/essentials/diagnostic-settings.md)を作成して、管理テナント内のワークスペースにリソース ログを送信する
- SAP ワークロードに対しては、[顧客のテナント全体で集計されたビューを使って SAP ソリューションのメトリックを監視する](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure のネットワーク](../../networking/networking-overview.md):

- 管理対象のテナント内で [Azure Virtual Network](../../virtual-network/index.yml) と仮想ネットワーク インターフェイス カード (vNIC) をデプロイして管理する
- 顧客の Virtual Network リソースを保護するために [Azure Firewall](../../firewall/overview.md) をデプロイして構成する
- [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md)、[ExpressRoute](../../expressroute/expressroute-introduction.md)、[VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) などの接続サービスを管理する
- Azure Lighthouse を使用して [Azure ネットワーク MSP プログラムの MSP プログラム](../../networking/networking-partners-msp.md)をサポートする

[Azure Policy](../../governance/policy/index.yml):

- 委任されたサブスクリプション内でポリシー定義を作成および編集する
- 複数のテナントにわたってポリシー定義とポリシー割り当てをデプロイする
- 委任されたサブスクリプション内で顧客が定義したポリシー定義を割り当てる
- 顧客には、サービス プロバイダーが作成したポリシーと顧客自身が作成したポリシーが並べて表示される
- [管理対象のテナント内で deployIfNotExists の修復または割り当ての変更を行う](../how-to/deploy-policy-remediation.md)ことができる
- 現在、顧客のテナント内にある準拠していないリソースのコンプライアンスの詳細を表示することはできません

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- 返されるクエリ結果にテナント ID が含まれるため、サブスクリプションが管理対象のテナントに属しているかどうかを特定できる

[Azure Security Center](../../security-center/index.yml):

- テナント間の表示
  - セキュリティ ポリシーへの準拠を監視し、セキュリティの適用範囲がすべてのテナントのリソースになるようにする
  - 1 つのビューで複数のテナントの規制へのコンプライアンスを継続的に監視する
  - セキュリティ スコアの計算を使用して実行可能なセキュリティの推奨事項の監視、トリアージ、優先度付けを行う
- テナント間のセキュリティ体制の管理
  - セキュリティ ポリシーの管理
  - 実行可能なセキュリティの推奨事項に準拠していないリソースに対処する
  - セキュリティ関連のデータを収集して保存する
- テナント間の脅威の検出と保護
  - テナントのリソース全体で脅威を検出する
  - Just-In-Time (JIT) VM アクセスなど、高度な脅威保護コントロールを適用する
  - アダプティブ ネットワーク強化を使用してネットワーク セキュリティ グループの構成を強化する
  - サーバーで、適応型アプリケーション制御の対象とすべきアプリケーションとプロセスのみが実行されるようにする
  - ファイルの整合性の監視 (FIM) を使用して、重要なファイルとレジストリ エントリに対する変更を監視する
- サブスクリプション全体を管理テナントに委任する必要があることにご注意ください。Azure Security Center シナリオは、委任されたリソース グループではサポートされません。

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- [顧客テナントで](../../sentinel/multiple-tenants-service-providers.md) Azure Sentinel リソースを管理する
- [複数のテナントにわたる攻撃を追跡し、セキュリティ アラートを表示する](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- テナント間に分散している複数の Azure Sentinel ワークスペースにわたる[インシデントを表示する](../../sentinel/multiple-workspace-view.md)

[Azure Service Health](../../service-health/index.yml):

- Azure Resource Health を使用して、顧客のリソースの正常性を監視する
- 顧客が使用している Azure サービスの正常性を追跡する

[Azure Site Recovery](../../site-recovery/index.yml):

- 顧客のテナント内にある Azure 仮想マシンのディザスター リカバリー オプションを管理する (VM 拡張機能のコピーには `RunAs` アカウントを使用できないことに注意してください)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- 仮想マシン拡張機能を使用して、Azure VM のデプロイ後の構成と自動タスクを提供する
- ブート診断を使用して、Azure VM のトラブルシューティングを行う
- シリアル コンソールを使用して VM にアクセスする
- [ポリシーによるマネージド ID](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) を使用して、ディスク暗号化のためのパスワード、シークレット、または暗号化キー用に VM を Azure Key Vault と統合して、シークレットが管理対象のテナントの Key Vault に確実に保存されるようにする
- VM へのリモート ログインに Azure Active Directory を使用できないことに注意する

サポート リクエスト:

- Azure portal で、委任されたリソースに対する [サポート リクエストを **[ヘルプとサポート]** で開く](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) (委任されたスコープで利用可能なサポート プランを選択する)
- [Azure クォータ API](/rest/api/reserved-vm-instances/quotaapi) を使用して、委任された顧客リソースの Azure サービス クォータを表示および管理する

## <a name="current-limitations"></a>現在の制限

すべてのシナリオで、次に示す現在の制限事項に注意してください。

- Azure Resource Manager で処理される要求は、Azure Lighthouse を使用して実行できます。 これらの要求の操作 URI は、`https://management.azure.com` で始まります。 ただし、リソースの種類のインスタンス (Key Vault のシークレット アクセスやストレージのデータ アクセスなど) によって処理される要求は、Azure Lighthouse ではサポートされていません。 これらの要求の操作 URI は、通常、`https://myaccount.blob.core.windows.net` や `https://mykeyvault.vault.azure.net/` など、実際のインスタンスに固有のアドレスで始まります。 また、通常、後者は管理操作ではなくデータ操作です。
- ロールの割り当てには [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)を使用する必要があります。 現在、組み込みロールはすべて、Azure の委任されたリソース管理によってサポートされています。ただし、所有者または [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) アクセス許可を持つ組み込みロールは除きます。 [マネージド ID へのロールの割り当て](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)において、ユーザー アクセス管理者ロールは、限定された用途のみに対してサポートされています。  カスタム ロールと[従来のサブスクリプション管理者ロール](../../role-based-access-control/classic-administrators.md)はサポートされていません。
- Azure Databricks を使用するサブスクリプションをオンボードすることはできますが、現時点では、管理テナントのユーザーは、委任されたサブスクリプションで Azure Databricks ワークスペースを起動することはできません。
- リソース ロックがあるサブスクリプションとリソース グループをオンボードすることはできますが、このようなロックがあっても、管理テナントのユーザーによるアクションの実行は妨げられません。 Azure マネージド アプリケーションまたは Azure Blueprints (システム割り当ての拒否割り当て) によって作成されたものなど、システムの管理対象リソースを保護する[拒否割り当て](../../role-based-access-control/deny-assignments.md)がある場合、管理テナントのユーザーはそれらのリソースを操作できません。ただし、現時点では、顧客テナントのユーザーは自分の拒否割り当て (ユーザー割り当て拒否割り当て) を作成できません。
- [各国のクラウド](../../active-directory/develop/authentication-national-cloud.md)と Azure パブリック クラウドにわたって行われる、または 2 つの独立した国内クラウドにわたって行われるサブスクリプションの委任はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [Azure Resource Manager テンプレートを使用する](../how-to/onboard-customer.md)か[プライベートまたはパブリックのマネージド サービスのオファーを Azure Marketplace に公開する](../how-to/publish-managed-services-offers.md)ことで、Azure Lighthouse に顧客をオンボードします。
- Azure portal の **[マイ カスタマー]** に移動して、[顧客を表示および管理](../how-to/view-manage-customers.md)します。