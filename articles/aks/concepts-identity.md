---
title: 概念 - Azure Kubernetes Services (AKS) におけるアクセスと ID
description: Azure Kubernetes Service (AKS) におけるアクセスと ID について説明します。Azure Active Directory の統合、Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC)、およびロールとバインドの説明が含まれています。
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 98044f6ff6311241717cb66a6e26a72702d749e6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181450"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアクセスと ID オプション

Kubernetes クラスターに対する認証の実行、アクセス制御と認可、セキュリティ保護には、さまざまな方法があります。 Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用して、ユーザー、グループ、サービス アカウントに対し、必要とするリソースのみへのアクセス権を付与できます。 Azure Kubernetes Service (AKS) では、Azure Active Directory と Azure RBAC を使用して、セキュリティとアクセス許可構造をさらに強化できます。 これらの方法を使用すると、クラスターへのアクセスをセキュリティで保護し、開発者やオペレーターに必要最低限のアクセス許可のみを与えることができます。

この記事では、AKS で認証とアクセス許可の割り当てを行うために役立つ中心概念を紹介します。

## <a name="aks-service-permissions"></a>AKS サービスの権限

クラスターの作成時には、クラスターを作成するユーザーに代わり、AKS によって、クラスターを作成して実行するために必要なリソース (VM や NIC など) が作成または変更されます。 この ID は、クラスターの作成時に作成される、クラスターの ID 権限とは異なります。

### <a name="identity-creating-and-operating-the-cluster-permissions"></a>クラスターの権限を作成および操作する ID

クラスターを作成および操作する ID には、次の権限が必要です。

| 権限 | 理由 |
|---|---|
| Microsoft.Compute/diskEncryptionSets/read | ディスク暗号化セット ID を読み取るために必要です。 |
| Microsoft.Compute/proximityPlacementGroups/write | 近接配置グループを更新する場合に必要です。 |
| Microsoft.Network/applicationGateways/read <br/> Microsoft.Network/applicationGateways/write <br/> Microsoft.Network/virtualNetworks/subnets/join/action | アプリケーション ゲートウェイを構成し、サブネットに参加するために必要です。 |
| Microsoft.Network/virtualNetworks/subnets/join/action | カスタム VNET を使用する場合、サブネットのネットワーク セキュリティ グループを構成するために必要です。|
| Microsoft.Network/publicIPAddresses/join/action <br/> Microsoft.Network/publicIPPrefixes/join/action | Standard Load Balancer において送信パブリック IP を構成するために必要です。 |
| Microsoft.OperationalInsights/workspaces/sharedkeys/read <br/> Microsoft.OperationalInsights/workspaces/read <br/> Microsoft.OperationsManagement/solutions/write <br/> Microsoft.OperationsManagement/solutions/read <br/> Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Log Analytics ワークスペース、およびコンテナーの Azure 監視を作成および更新するために必要です。 |

### <a name="aks-cluster-identity-permissions"></a>AKS クラスター ID 権限

クラスターの作成時に作成され、AKS クラスターに関連付けられる AKS クラスター ID によって使用されるのは、以下の権限です。 各アクセス許可は、次の理由で使用されます。

| 権限 | 理由 |
|---|---|
| Microsoft.ContainerService/managedClusters/*  <br/> | ユーザーを作成し、クラスターを操作するために必要です
| Microsoft.Network/loadBalancers/delete <br/> Microsoft.Network/loadBalancers/read <br/> Microsoft.Network/loadBalancers/write | LoadBalancer サービスのロード バランサーを構成するために必要です。 |
| Microsoft.Network/publicIPAddresses/delete <br/> Microsoft.Network/publicIPAddresses/read <br/> Microsoft.Network/publicIPAddresses/write | LoadBalancer サービスのパブリック IP を検索して構成するために必要です。 |
| Microsoft.Network/publicIPAddresses/join/action | LoadBalancer サービスのパブリック IP を構成する場合に必要です。 |
| Microsoft.Network/networkSecurityGroups/read <br/> Microsoft.Network/networkSecurityGroups/write | LoadBalancer サービスのセキュリティ規則を作成または削除するために必要です。 |
| Microsoft.Compute/disks/delete <br/> Microsoft.Compute/disks/read <br/> Microsoft.Compute/disks/write <br/> Microsoft.Compute/locations/DiskOperations/read | AzureDisks を構成するために必要です。 |
| Microsoft.Storage/storageAccounts/delete <br/> Microsoft.Storage/storageAccounts/listKeys/action <br/> Microsoft.Storage/storageAccounts/read <br/> Microsoft.Storage/storageAccounts/write <br/> Microsoft.Storage/operations/read | AzureFile または AzureDisk のストレージ アカウントを構成するために必要です。 |
| Microsoft.Network/routeTables/read <br/> Microsoft.Network/routeTables/routes/delete <br/> Microsoft.Network/routeTables/routes/read <br/> Microsoft.Network/routeTables/routes/write <br/> Microsoft.Network/routeTables/write | ノードのルート テーブルとルートを構成するために必要です。 |
| Microsoft.Compute/virtualMachines/read | ゾーン、障害ドメイン、サイズ、データ ディスクなど、VMAS 内の仮想マシンに関する情報を検索するために必要です。 |
| Microsoft.Compute/virtualMachines/write | VMAS 内の仮想マシンに AzureDisks をアタッチするために必要です。 |
| Microsoft.Compute/virtualMachineScaleSets/read <br/> Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read <br/> Microsoft.Compute/virtualMachineScaleSets/virtualmachines/instanceView/read | ゾーン、障害ドメイン、サイズ、データ ディスクなど、仮想マシン スケール セット内の仮想マシンに関する情報を検索するために必要です。 |
| Microsoft.Network/networkInterfaces/write | VMAS 内の仮想マシンをロード バランサーのバックエンド アドレス プールに追加するために必要です。 |
| Microsoft.Compute/virtualMachineScaleSets/write | 仮想マシン スケール セットをロード バランサーのバックエンド アドレスプールに追加し、仮想マシン スケール セット内のノードをスケール アウトするために必要です。 |
| Microsoft.Compute/virtualMachineScaleSets/virtualmachines/write | AzureDisks をアタッチし、仮想マシン スケール セットからロード バランサーに仮想マシンを追加するために必要です。 |
| Microsoft.Network/networkInterfaces/read | VMAS 内の仮想マシン用の内部 IP およびロード バランサーのバックエンド アドレス プールを検索するために必要です。 |
| Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | 仮想マシン スケール セット内の仮想マシン用の内部 IP およびロード バランサーのバックエンド アドレス プールを検索するために必要です。 |
| Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipconfigurations/publicipaddresses/read | 仮想マシン スケール セット内の仮想マシンのパブリック IP を検索するために必要です。 |
| Microsoft.Network/virtualNetworks/read <br/> Microsoft.Network/virtualNetworks/subnets/read | 別のリソース グループ内の内部ロード バランサーに対してサブネットが存在するかどうかを確認するために必要です。 |
| Microsoft.Compute/snapshots/delete <br/> Microsoft.Compute/snapshots/read <br/> Microsoft.Compute/snapshots/write | AzureDisk のスナップショットを構成するために必要です。 |
| Microsoft.Compute/locations/vmSizes/read <br/> Microsoft.Compute/locations/operations/read | AzureDisk ボリュームの制限を把握する目的で仮想マシンのサイズを検索するために必要です。 |

### <a name="additional-cluster-identity-permissions"></a>追加のクラスター ID 権限

特定の属性を持つクラスターを作成する場合、クラスター ID には次の追加の権限が必要です。 これらの権限は自動的に割り当てられないため、クラスター ID を作成したら、これらの権限をそれに追加する必要があります。

| 権限 | 理由 |
|---|---|
| Microsoft.Network/networkSecurityGroups/write <br/> Microsoft.Network/networkSecurityGroups/read | 別のリソース グループ内のネットワーク セキュリティ グループを使用する場合に必要です。 LoadBalancer サービスのセキュリティ規則を構成するために必要です。 |
| Microsoft.Network/virtualNetworks/subnets/read <br/> Microsoft.Network/virtualNetworks/subnets/join/action | カスタム VNET など、別のリソース グループ内のサブネットを使用する場合に必要です。 |
| Microsoft.Network/routeTables/routes/read <br/> Microsoft.Network/routeTables/routes/write | カスタム ルート テーブルを持つカスタム VNET など、別のリソース グループ内のルート テーブルに関連付けられているサブネットを使用する場合に必要です。 他のリソース グループ内のサブネットに対してサブネットが既に存在しているかどうかを確認するために必要です。 |
| Microsoft.Network/virtualNetworks/subnets/read | 別のリソース グループの内部ロード バランサーを使用する場合に必要です。 リソース グループの内部ロード バランサーに対してサブネットが既に存在しているかどうかを確認するために必要です。 |

## <a name="kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC)

ユーザーが行うことのできるアクションの詳細なフィルター処理を提供するために、Kubernetes では Kubernetes ロールベースのアクセス制御 (Kubernetes RBAC) が使用されます。 この制御メカニズムを使用して、ユーザーまたはユーザー グループに対して、リソースの作成または変更、実行中のアプリケーション ワークロードのログの表示などの操作を行うアクセス許可を割り当てることができます。 これらのアクセス許可は、付与するスコープを単一の名前空間にすることも、AKS クラスター全体にすることもできます。 Kubernetes の RBAC では、アクセス許可を定義する "*ロール*" を作成し、それらのロールを "*ロールのバインド*" を使用してユーザーに割り当てます。

詳細については、[Kubernetes RBAC 認可の使用][kubernetes-rbac]に関するページをご覧ください。

### <a name="roles-and-clusterroles"></a>ロールと ClusterRole

Kubernetes の RBAC を使用してアクセス許可をユーザーに割り当てる前に、これらのアクセス許可を "*ロール*" として先に定義します。 Kubernetes のロールは、アクセス許可を "*付与*" します。 "*拒否*" というアクセス許可の概念はありません。

ロールは、名前空間内にアクセス許可を付与するために使用されます。 クラスター全体または特定の名前空間の外部のクラスター リソースへのアクセス許可を付与する必要がある場合は、代わりに *ClusterRole* を使用できます。

ClusterRole は、リソースへのアクセス許可を付与するのと同じように機能しますが、特定の名前空間ではなく、クラスター全体のリソースに適用できます。

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding と ClusterRoleBinding

リソースへのアクセス許可を付与するロールを定義したら、*RoleBinding* を使用して、これらの Kubernetes の RBAC アクセス許可を割り当てます。 AKS クラスターが [Azure Active Directory (Azure AD) と統合されている](#azure-active-directory-integration)場合、バインドは、クラスター内でアクションを実行するためのアクセス許可が Azure AD ユーザーにどのように付与されるかを示します。方法については、[Kubernetes ロールベースのアクセス制御と Azure Active Directory ID を使用したクラスター リソースへのアクセスの制限](azure-ad-rbac.md)に関する記事をご覧ください。

ロールのバインドを使用して、特定の名前空間に対してロールを割り当てます。 このアプローチによって、1 つの AKS クラスターを論理的に分離でき、ユーザーは各自に割り当てられた名前空間内のアプリケーション リソースにのみアクセスが可能になります。 クラスター全体または特定の名前空間の外部のクラスター リソースにロールをバインドする必要がある場合は、代わりに *ClusterRoleBinding* を使用できます。

ClusterRoleBinding は、ユーザーにロールをバインドするのと同じように機能しますが、特定の名前空間ではなく、クラスター全体のリソースに適用できます。 このアプローチでは、管理者またはサポートエンジニアに対して、AKS クラスター内のすべてのリソースへのアクセス許可を付与できます。


> [!NOTE]
> Microsoft/AKS によって実行されるクラスター アクションはすべて、ユーザーの同意を得たうえで、組み込みの Kubernetes ロール `aks-service` と組み込みのロール バインド `aks-service-rolebinding` に基づいて行われます。 このロールにより、AKS はクラスターの問題のトラブルシューティングや診断を行うことができますが、アクセス許可の変更や、ロールやロール バインドの作成など、高い特権を必要とする操作を行うことはできません。 ロールによるアクセスは、アクティブなサポート チケットで Just-In-Time (JIT) アクセスを使用する場合にのみ有効になります。 詳細については、[AKS サポート ポリシー](support-policies.md)に関する記事を参照してください。


### <a name="kubernetes-service-accounts"></a>Kubernetes サービス アカウント

Kubernetes のプライマリ ユーザー タイプの 1 つは、"*サービス アカウント*" です。 サービス アカウントは、Kubernetes API 内に存在し、Kubernetes API によって管理されます。 サービス アカウントの資格情報は Kubernetes シークレットとして格納され、承認されたポッドが API サーバーと通信するために使用できます。 ほとんどの API 要求では、サービス アカウントまたは通常のユーザー アカウント用の認証トークンが提供されします。

通常のユーザー アカウントでは、人間の管理者または開発者は、サービスとプロセスだけではなく、従来のアクセスも許可されます。 Kubernetes 自体には、通常のユーザー アカウントとパスワードを格納する ID 管理ソリューションはありません。 代わりに、外部 ID ソリューションを Kubernetes に統合できます。 AKS クラスターの場合、この統合される ID ソリューションは Azure Active Directory です。

Kubernetes における ID オプションの詳細については、[Kubernetes の認証][kubernetes-authentication]に関する記事を参照してください。

## <a name="azure-active-directory-integration"></a>Azure Active Directory の統合

Azure Active Directory (AD) の統合によって、AKS クラスターのセキュリティを拡張できます。 数十年に及ぶエンタープライズ ID 管理の上に構築された Azure AD は、マルチテナントに対応したクラウドベースのディレクトリであり、中核となるディレクトリ サービス、アプリケーションのアクセス管理機能、および ID 保護機能が統合された ID 管理サービスです。 Azure AD を使用してオンプレミスの ID を AKS クラスターに統合することで、アカウント管理とセキュリティのための単一のソースを提供できます。

![Azure Active Directory と AKS クラスターの統合](media/concepts-identity/aad-integration.png)

Azure AD が統合された AKS クラスターでは、名前空間内またはクラスター間での Kubernetes リソースへのアクセス権をユーザーまたはグループに付与できます。 `kubectl` 構成コンテキストを取得するには、ユーザーは、[az aks get-credentials][az-aks-get-credentials] コマンドを実行します。 その後、ユーザーが `kubectl` を使用して AKS クラスターにアクセスすると、Azure AD 資格情報でサインインするように求められます。 このアプローチでは、ユーザー アカウントの管理とパスワードの資格情報のために 1 つのソースが提供されます。 ユーザーは、クラスター管理者によって定義されているリソースにのみアクセスできます。

Azure AD 認証は、OpenID Connect によって AKS クラスターに提供されます。 OpenID Connect は、OAuth 2.0 プロトコル上に構築された ID レイヤーです。 OpenID Connect の詳細については、[OpenID Connect のドキュメント][openid-connect]を参照してください。 Kubernetes クラスターの内部からは、[Webhook トークン認証][webhook-token-docs]を使用して認証トークンが確認されます。 webhook トークン認証は、AKS クラスターの一部として構成および管理されます。

### <a name="webhook-and-api-server"></a>Webhook と API サーバー

![Webhook と API サーバーの認証フロー](media/concepts-identity/auth-flow.png)

上の図に示すように、API サーバーでは、AKS Webhook サーバーが呼び出されて、次の手順が実行されます。

1. [OAuth 2.0 デバイス承認許可フロー](../active-directory/develop/v2-oauth2-device-code.md)でユーザーをサインインさせるため、kubectl では Azure AD クライアント アプリケーションが使用されます。
2. Azure AD では、access_token、id_token、refresh_token が提供されます。
3. ユーザーは、kubeconfig からの access_token を使用して kubectl に対する要求を行います。
4. kubectl から API Server に access_token が送信されます。
5. API サーバーは、検証を実行するように Auth Webhook サーバーで構成されます。
6. 認証 Webhook サーバーでは、Azure AD の公開署名キーをチェックすることによって、JSON Web トークン署名が有効であることが確認されます。
7. サーバー アプリケーションでは、ユーザーが指定した資格情報を使用して、MS Graph API からログインしたユーザーのグループ メンバーシップが照会されます。
8. 応答は、アクセス トークンのユーザー プリンシパル名 (UPN) 要求や、オブジェクト ID に基づくユーザーのグループ メンバーシップなどのユーザー情報と共に API Server に送信されます。
9. API では、Kubernetes のロールと RoleBinding に基づいて認可の決定が実行されます。
10. 認可されると、API サーバーから kubectl に応答が返されます。
11. kubectl からユーザーにフィードバックが提供されます。
 
**AKS と AAD を統合する方法については、[こちら](managed-aad.md)を参照してください。**

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure ロールベースのアクセス制御 (Azure RBAC)

Azure RBAC は [Azure Resource Manager](../azure-resource-manager/management/overview.md) 上に構築された承認システムであり、Azure リソースに対するアクセスをきめ細かく管理できます。

 Azure の RBAC が Azure サブスクリプション内のリソースに対して機能するように設計されているのに対し、Kubernetes の RBAC は AKS クラスター内の Kubernetes リソースに対して機能するように設計されています。 

Azure の RBAC では、適用されるアクセス許可の概要を説明する *ロール定義* を作成します。 その後、ユーザーまたはグループに、特定の "*スコープ*" (個々のリソース、リソース グループ、またはサブスクリプション全体) に対する "*ロールの割り当て*" によって、このロール定義が割り当てられます。

詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは][azure-rbac]」

AKS クラスターを完全に運用するには、次の 2 つのレベルのアクセスが必要です。 
1. [Azure サブスクリプションの AKS リソースへのアクセス](#azure-rbac-to-authorize-access-to-the-aks-resource)。 このプロセスでは、AKS API を使用してクラスターのスケーリングやアップグレードを制御したり、kubeconfig をプルしたりすることができます。
2. Kubernetes API へのアクセス。 このアクセスは、[Kubernetes RBAC](#kubernetes-role-based-access-control-kubernetes-rbac) (従来) によって、または [Kubernetes の認可のための Azure RBAC と AKS の統合](#azure-rbac-for-kubernetes-authorization-preview)によって制御されます

### <a name="azure-rbac-to-authorize-access-to-the-aks-resource"></a>AKS リソースへのアクセスを認可するための Azure RBAC

Azure RBAC を使用すると、ユーザー (または ID) に、1 つ以上のサブスクリプションの AKS リソースに対するきめ細かいアクセスを提供できます。 たとえば、[Azure Kubernetes Service 共同作成者ロール](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)を使用すると、クラスターのスケーリングやアップグレードなどの操作を実行できます。 別のユーザーには [Azure Kubernetes Service クラスター管理者ロール](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role)を割り当てて、管理者の kubeconfig をプルするアクセス許可のみを付与できます。

または、ユーザーに一般的な[共同作成者](../role-based-access-control/built-in-roles.md#contributor)ロールを付与することもできます。これには、アクセス許可自体の管理を除き、上記のアクセス許可および AKS リソースで可能なすべての操作が含まれます。

Kubernetes API へのアクセスを提供する kubeconfig ファイルへのアクセスを、Azure RBAC を使用してセキュリティで保護する方法の詳細については、[こちら](control-kubeconfig-access.md)を参照してください。

### <a name="azure-rbac-for-kubernetes-authorization-preview"></a>Kubernetes 認可に対する Azure RBAC (プレビュー)

Azure RBAC 統合では、AKS で Kubernetes 認可 Webhook サーバーが使用されることにより、ユーザーは、Azure ロールの定義とロールの割り当てを使用して、Azure AD と統合された K8s クラスター リソースのアクセス許可と割り当てを管理できます。

![Kubernetes 認可に対する Azure RBAC のフロー](media/concepts-identity/azure-rbac-k8s-authz-flow.png)

上の図で示されているように、Azure RBAC 統合を使用すると、Kubernetes API に対するすべての要求は、「[Azure Active Directory の統合](#azure-active-directory-integration)」セクションで説明されているものと同じ認証フローに従います。 

ただし、その後は、認可を Kubernetes RBAC だけに依存するのではなく、要求を行った ID が AAD に存在する限り、要求は Azure によって認可されます。 ID が AAD に存在しない場合 (たとえば、Kubernetes サービス アカウント)、Azure RBAC は開始されず、通常の Kubernetes RBAC になります。

このシナリオでは、4 つの組み込みロールのいずれかをユーザーに付与できます。または、Kubernetes ロールの場合と同じようにカスタム ロールを作成することもできますが、この場合は Azure RBAC のメカニズムと API を使用します。 

この機能を使用すると、たとえば、サブスクリプション間で AKS リソースへのアクセス許可をユーザーに付与するだけでなく、Kubernetes API へのアクセスを制御する各クラスター内でのロールとアクセス許可を設定してユーザーに付与することができます。 たとえば、`Azure Kubernetes Service RBAC Viewer` ロールをサブスクリプションのスコープで付与することができ、それを受け取ったユーザーは、すべてのクラスターのすべての Kubernetes オブジェクトを一覧表示して取得することができますが、変更することはできません。

> [!IMPORTANT]
> この機能を使用する前に、Kubernetes 認可に対して Azure RBAC を有効にする必要があることに注意してください。 詳細およびステップ バイ ステップ ガイダンスについては、[こちらを参照してください](manage-azure-rbac.md)。

#### <a name="built-in-roles"></a>組み込みのロール

AKS には、次の 4 つの組み込みロールがあります。 これらは、[Kubernetes の組み込みロール](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles)に似ていますが、CRD のサポートなど、いくつかの違いがあります。 各組み込みロールで許可されるアクションの完全な一覧については、[こちら](../role-based-access-control/built-in-roles.md)を参照してください。

| Role                                | 説明  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC ビューアー  | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 ロールまたはロールのバインドを表示することはできません。 このロールでは、`Secrets` の表示は許可されません。これは、Secrets の内容を読み取ると、名前空間の `ServiceAccount` 資格情報にアクセスでき、それにより名前空間の任意の `ServiceAccount` として API にアクセスできるようになるためです (特権エスカレーションの形式)  |
| Azure Kubernetes Service RBAC ライター | 名前空間内のほとんどのオブジェクトに対する読み取りと書き込みのアクセスが許可されます。 このロールでは、ロールまたはロールのバインドを表示または変更することはできません。 ただし、このロールを使用すると、`Secrets` にアクセスし、名前空間内の任意の ServiceAccount としてポッドを実行できるので、名前空間内の任意の ServiceAccount の API アクセス レベルを取得するために使用できます。 |
| Azure Kubernetes Service RBAC 管理者  | 名前空間内で付与されることが意図された、管理者アクセスが許可されます。 名前空間内でロールおよびロール バインドを作成する能力など、名前空間 (またはクラスター スコープ) 内のほとんどのリソースへの読み取りおよび書き込みアクセスが許可されます。 このロールでは、リソース クォータまたは名前空間自体への書き込みアクセスは許可されません。 |
| Azure Kubernetes Service RBAC クラスター管理者  | 任意のリソースに対して任意のアクションを実行できるスーパー ユーザー アクセスが許可されます。 これにより、クラスター内およびすべての名前空間内のすべてのリソースを完全に制御できます。 |


## <a name="summary"></a>まとめ

次の表は Azure AD 統合が有効になっている場合にユーザーが Kubernetes に対して認証する方法をまとめたものです。  すべての場合において、ユーザーのコマンド実行順序は次のようになります。
1. `az login` を実行して Azure に対して認証を行います。
1. `az aks get-credentials` を実行して、クラスターの資格情報を `.kube/config` にダウンロードします。
1. `kubectl` のコマンドを実行します (このうちの最初のものによって、次の表に示すように、 ブラウザーベースの認証がトリガーされ、クラスターに対して認証を行うことができます)。

2 番目の列に記載されているロール付与は、Azure portal の **[Access Control]** タブに表示される Azure RBAC ロール付与です。 クラスター管理 Azure AD グループは、ポータルの **[構成]** タブに表示されます (または、Azure CLI でパラメーター名 `--aad-admin-group-object-ids` を指定します)。

| 説明        | ロール付与が必要| クラスター管理 Azure AD グループ | 使用する場合 |
| -------------------|------------|----------------------------|-------------|
| クライアント証明書を使用した、レガシの管理者ログイン| **Azure Kubernetes 管理者ロール**。 このロールにより、`--admin` フラグを指定して `az aks get-credentials` を使用できます。これを実行すると、[レガシ (非 Azure AD) クラスター管理用証明書](control-kubeconfig-access.md)がユーザーの `.kube/config` にダウンロードされます。 これが、"Azure Kubernetes 管理者ロール" の唯一の目的です。|該当なし|お使いのクラスターへのアクセス権を持つ有効な Azure AD グループへのアクセス権がないため、永続的にブロックされている場合。| 
| 手動の (Cluster)RoleBindings を使用した Azure AD| **Azure Kubernetes ユーザー ロール**。 "ユーザー" ロールにより、`--admin` フラグなしで `az aks get-credentials` を使用できます。 (これが、"Azure Kubernetes ユーザー ロール" の唯一の目的です。)この結果、Azure AD 対応のクラスターで、[空のエントリ](control-kubeconfig-access.md)が `.kube/config` にダウンロードされ、それが最初に `kubectl`によって使用されたときにブラウザーベースの認証がトリガーされます。| ユーザーはこれらのグループのいずれにも属しません。 ユーザーがどのクラスター管理グループにも属さないため、その権利は全面的に、クラスター管理者が設定した任意の RoleBindings または ClusterRoleBindings によって制御されます。 (Cluster)RoleBindings により、その `subjects` として[Azure AD ユーザーまたは Azure AD グループが指名されます](azure-ad-rbac.md)。 このようなバインドが設定されていない場合、ユーザーはどの `kubectl` コマンドも行できません。|きめ細かなアクセス制御が必要で、Kubernetes 認可に Azure RBAC を使用しない場合。 バインドを設定するユーザーは、この表に記載されている他の方法のいずれかでログインする必要があることに注意してください。|
| 管理グループのメンバーによる Azure AD| 同上|ユーザーは、こちらに一覧表示されているいずれかのグループのメンバーです。 一覧表示されているすべてのグループを `cluster-admin` Kubernetes ロールにバインドする ClusterRoleBinding が自動的に生成されます。 そのため、これらのグループのユーザーは、すべての `kubectl` コマンドを `cluster-admin`として実行できます。|ユーザーに完全な管理者権限を簡単に付与する必要があり、Kubernetes 認可に Azure RBAC を使用 "_しない_" 場合。|
| Kubernetes 認可に Azure RBAC を使用した Azure AD|2 つのロール:1 つ目は、**Azure Kubernetes ユーザー ロール** (上記と同じ)。 2 つ目は、上記の "Azure Kubernetes Service **RBAC**..."ロールのいずれか、または代わりとなる独自のカスタム ロール。|Kubernetes 認可に対する Azure RBAC が有効になっている場合、[構成] タブの [管理者ロール] フィールドは関係ありません。|Kubernetes 認可に Azure RBAC を使用しない場合。 この方法を使用すると、RoleBindings バインドや ClusterRoleBindings を設定することなく、きめ細かく制御できます。|

## <a name="next-steps"></a>次のステップ

- Azure AD と Kubernetes の RBAC の使用を開始するには、[Azure Active Directory と AKS の統合][aks-aad]に関する記事を参照してください。
- 関連するベスト プラクティスについては、[AKS の認証と認可のベスト プラクティス][operator-best-practices-identity]に関する記事を参照してください。
- Kubernetes 認可に Azure RBAC を使い始めるには、[Azure RBAC を使用して azure Kubernetes Service (AKS) クラスター内のアクセスを認可する方法](manage-azure-rbac.md)に関する記事を参照してください。
- kubeconfig ファイルのセキュリティ保護については、[クラスター構成ファイルへのアクセスの制限](control-kubeconfig-access.md)に関する記事を参照してください

Kubernetes と AKS の中心概念の詳細については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS のセキュリティ][aks-concepts-security]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
[upgrade-per-cluster]: ../azure-monitor/containers/container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli
