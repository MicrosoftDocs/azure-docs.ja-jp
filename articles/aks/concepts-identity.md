---
title: 概念 - Azure Kubernetes Services (AKS) におけるアクセスと ID
description: Azure Kubernetes Service (AKS) におけるアクセスと ID について説明します。Azure Active Directory の統合、Kubernetes のロールベースのアクセス制御 (RBAC)、およびロールとバインドの説明が含まれています。
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: edb6a8e04537a74b7ea7d4c9bd9bd27fdc39e402
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007082"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアクセスと ID オプション

Kubernetes クラスターに対する認証の実行、アクセス制御と認可、セキュリティ保護には、さまざまな方法があります。 Kubernetes のロールベースのアクセス制御 (RBAC) を使用して、ユーザー、グループ、サービス アカウントに対し、必要とするリソースのみへのアクセス権を付与できます。 Azure Kubernetes Service (AKS) では、Azure Active Directory と Azure RBAC を使用して、セキュリティとアクセス許可構造をさらに強化できます。 これらの方法を使用すると、クラスターへのアクセスをセキュリティで保護し、開発者やオペレーターに必要最低限のアクセス許可のみを与えることができます。

この記事では、AKS で認証とアクセス許可の割り当てを行うために役立つ中心概念を紹介します。

- [Kubernetes のロールベースのアクセス制御 (RBAC)](#kubernetes-role-based-access-control-rbac)
  - [ロールと ClusterRoles](#roles-and-clusterroles)
  - [RoleBindings と ClusterRoleBindings](#rolebindings-and-clusterrolebindings) 
  - [Kubernetes サービス アカウント](#kubernetes-service-accounts)
- [Azure Active Directory の統合](#azure-active-directory-integration)
- [Azure RBAC](#azure-role-based-access-control-azure-rbac)
  - [AKS リソースへのアクセスを認可するための Azure RBAC](#azure-rbac-to-authorize-access-to-the-aks-resource)
  - [Kubernetes 認可に対する Azure RBAC (プレビュー)](#azure-rbac-for-kubernetes-authorization-preview)


## <a name="kubernetes-role-based-access-control-rbac"></a>Kubernetes のロールベースのアクセス制御 (RBAC)

ユーザーが行うことのできるアクションの詳細なフィルター処理を提供するため、Kubernetes ではロールベースのアクセス制御 (RBAC) が使用されます。 この制御メカニズムを使用して、ユーザーまたはユーザー グループに対して、リソースの作成または変更、実行中のアプリケーション ワークロードのログの表示などの操作を行うアクセス許可を割り当てることができます。 これらのアクセス許可は、付与するスコープを単一の名前空間にすることも、AKS クラスター全体にすることもできます。 Kubernetes の RBAC では、アクセス許可を定義する "*ロール*" を作成し、それらのロールを "*ロールのバインド*" を使用してユーザーに割り当てます。

詳細については、[RBAC 承認の使用][kubernetes-rbac]に関するページを参照してください。


### <a name="roles-and-clusterroles"></a>ロールと ClusterRole

Kubernetes の RBAC を使用してアクセス許可をユーザーに割り当てる前に、これらのアクセス許可を "*ロール*" として先に定義します。 Kubernetes のロールは、アクセス許可を "*付与*" します。 "*拒否*" というアクセス許可の概念はありません。

ロールは、名前空間内にアクセス許可を付与するために使用されます。 クラスター全体または特定の名前空間の外部のクラスター リソースへのアクセス許可を付与する必要がある場合は、代わりに *ClusterRole* を使用できます。

ClusterRole は、リソースへのアクセス許可を付与するのと同じように機能しますが、特定の名前空間ではなく、クラスター全体のリソースに適用できます。

### <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding と ClusterRoleBinding

リソースへのアクセス許可を付与するロールを定義したら、*RoleBinding* を使用して、これらの Kubernetes の RBAC アクセス許可を割り当てます。 AKS クラスターが [Azure Active Directory と統合されている](#azure-active-directory-integration)場合、バインドは、クラスター内でアクションを実行するためのアクセス許可が Azure AD ユーザーにどのように付与されるかを示します。方法については、[ロールベースのアクセス制御と Azure Active Directory ID を使用したクラスター リソースへのアクセスの制限](azure-ad-rbac.md)に関する記事を参照してください。

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
4. kubectl では、APIServer に access_token が送信されます。
5. API サーバーは、検証を実行するように Auth Webhook サーバーで構成されます。
6. 認証 Webhook サーバーでは、Azure AD の公開署名キーをチェックすることによって、JSON Web トークン署名が有効であることが確認されます。
7. サーバー アプリケーションでは、ユーザーが指定した資格情報を使用して、MS Graph API からログインしたユーザーのグループ メンバーシップが照会されます。
8. 応答は、アクセス トークンのユーザー プリンシパル名 (UPN) 要求や、オブジェクト ID に基づくユーザーのグループ メンバーシップなどのユーザー情報と共に APIServer に送信されます。
9. API では、Kubernetes のロールと RoleBinding に基づいて認可の決定が実行されます。
10. 認可されると、API サーバーから kubectl に応答が返されます。
11. kubectl からユーザーにフィードバックが提供されます。
 
**AKS と AAD を統合する方法については、[こちら](managed-aad.md)を参照してください。**

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure ロールベースのアクセス制御 (Azure RBAC)

Azure RBAC は [Azure Resource Manager](../azure-resource-manager/management/overview.md) 上に構築された承認システムであり、Azure リソースに対するアクセスをきめ細かく管理できます。

 Azure の RBAC が Azure サブスクリプション内のリソースに対して機能するように設計されているのに対し、Kubernetes の RBAC は AKS クラスター内の Kubernetes リソースに対して機能するように設計されています。 

Azure の RBAC では、適用されるアクセス許可の概要を説明する*ロール定義*を作成します。 その後、ユーザーまたはグループに、特定の "*スコープ*" (個々のリソース、リソース グループ、またはサブスクリプション全体) に対する "*ロールの割り当て*" によって、このロール定義が割り当てられます。

詳細については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは][azure-rbac]」

AKS クラスターを完全に運用するには、次の 2 つのレベルのアクセスが必要です。 
1. [Azure サブスクリプションの AKS リソースへのアクセス](#azure-rbac-to-authorize-access-to-the-aks-resource)。 このプロセスでは、AKS API を使用してクラスターのスケーリングやアップグレードを制御したり、kubeconfig をプルしたりすることができます。
2. Kubernetes API へのアクセス。 このアクセスは、[Kubernetes RBAC](#kubernetes-role-based-access-control-rbac) (従来) によって、または [Kubernetes の認可のための Azure RBAC と AKS の統合](#azure-rbac-for-kubernetes-authorization-preview)によって制御されます

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


#### <a name="built-in-roles"></a>組み込みのロール

AKS には、次の 4 つの組み込みロールがあります。 これらは、[Kubernetes の組み込みロール](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#user-facing-roles)に似ていますが、CRD のサポートなど、いくつかの違いがあります。 各組み込みロールで許可されるアクションの完全な一覧については、[こちら](../role-based-access-control/built-in-roles.md)を参照してください。

| Role                                | 説明  |
|-------------------------------------|--------------|
| Azure Kubernetes Service RBAC ビューアー  | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 ロールまたはロールのバインドを表示することはできません。 このロールでは、`Secrets` の表示は許可されません。これは、Secrets の内容を読み取ると、名前空間の `ServiceAccount` 資格情報にアクセスでき、それにより名前空間の任意の `ServiceAccount` として API にアクセスできるようになるためです (特権エスカレーションの形式)  |
| Azure Kubernetes Service RBAC ライター | 名前空間内のほとんどのオブジェクトに対する読み取りと書き込みのアクセスが許可されます。 このロールでは、ロールまたはロールのバインドを表示または変更することはできません。 ただし、このロールを使用すると、`Secrets` にアクセスし、名前空間内の任意の ServiceAccount としてポッドを実行できるので、名前空間内の任意の ServiceAccount の API アクセス レベルを取得するために使用できます。 |
| Azure Kubernetes Service RBAC 管理者  | 名前空間内で付与されることが意図された、管理者アクセスが許可されます。 名前空間内でロールおよびロール バインドを作成する能力など、名前空間 (またはクラスター スコープ) 内のほとんどのリソースへの読み取りおよび書き込みアクセスが許可されます。 このロールでは、リソース クォータまたは名前空間自体への書き込みアクセスは許可されません。 |
| Azure Kubernetes Service RBAC クラスター管理者  | 任意のリソースに対して任意のアクションを実行できるスーパー ユーザー アクセスが許可されます。 これにより、クラスター内およびすべての名前空間内のすべてのリソースを完全に制御できます。 |

**Kubernetes 認可に Azure RBAC を利用する方法については、[こちら](manage-azure-rbac.md)を参照してください。**

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
