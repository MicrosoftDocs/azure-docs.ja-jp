---
title: オペレーターのベスト プラクティス - Azure Kubernetes Services (AKS) の ID
description: Azure Kubernetes Service (AKS) でクラスターの認証と認可を管理する方法に関するクラスター オペレーター ベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 82bf59dddeecab0addf00a935f55be8d1d7952d3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614778"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の認証と認可のベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターをデプロイし、保守管理するとき、リソースやサービスへのアクセスを管理する方法を実装する必要があります。 このような制御がなければ、必要としないリソースやサービスへのアクセスがアカウントに与えられることがあります。 また、変更を行う際に使用された資格情報セットを追跡することが難しくなる場合があります。

このベスト プラクティス記事では、クラスター オペレーターが AKS クラスターのアクセスと ID を管理する方法について取り上げます。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Azure Active Directory (AAD) を利用して AKS クラスター ユーザーを認証する
> * ロールベースのアクセス制御 (RBAC) を使用し、リソースへのアクセスを制御する
> * マネージド ID を使用し、他のサービスで認証する

## <a name="use-azure-active-directory"></a>Azure Active Directory を使用する

**ベスト プラクティス ガイダンス** - Azure AD 統合で AKS クラスターをデプロイします。 Azure AD を使用することで、ID 管理コンポーネントが一元化されます。 ユーザー アカウントまたはグループの状態が変わると、AKS クラスターにアクセスしたとき、その変更内容が自動的に更新されます。 ユーザーまたはグループに必要最低限のアクセス許可を与える場合、次のセクションで説明する Roles または ClusterRoles と Bindings を使用します。

お使いの Kubernetes クラスターの開発者とアプリケーション所有者はさまざまなリソースへのアクセスを必要とします。 Kubernetes からは、リソースを使用するユーザーとユーザーに使用されるリソースを制御する ID 管理ソリューションが提供されません。 代わりに、一般的には自分のクラスターと既存の ID ソリューションを統合します。 Azure Active Directory (AD) では企業対応の ID 管理ソリューションが提供されます。また、AD は AKS クラスターと統合できます。

Azure AD 統合クラスターを AKS を使用するとき、リソースへのアクセス許可を定義する *Roles* または *ClusterRoles* を作成します。 作成後、Azure AD のユーザーまたはグループにロールを*バインド*します。 このような Kubernetes のロールベースのアクセス制御 (RBAC) については、次のセクションで説明します。 Azure AD の統合とリソースへのアクセスの制御方法をまとめたのが次の図です。

![Azure Active Directory と AKS の統合のためのクラスターレベルの認証](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 開発者が Azure AD を利用して認証します。
1. Azure AD トークン発行エンドポイントがアクセス トークンを発行します。
1. 開発者は、`kubectl create pod` など、Azure AD トークンを使用して操作を実行します。
1. Kubernetes では Azure Active Directory を利用してトークンの有効性が検証され、開発者のグループ メンバーシップが取得されます。
1. Kubernetes のロールベースのアクセス制御 (RBAC) とクラスター ポリシーが適用されます。
1. 先の Azure AD グループ メンバーシップの検証、Kubernetes RBAC、ポリシーに基づき、開発者の要求が通過するか、却下されます。

Azure AD を使用する AKS クラスターを作成する方法については、「[Azure Active Directory と Azure Kubernetes Service を統合する][aks-aad]」を参照してください。

## <a name="use-role-based-access-controls-rbac"></a>ロールベースのアクセス制御 (RBAC) を使用する

**ベスト プラクティス ガイダンス** - Kubernetes RBAC を使用し、クラスターのリソースに対するユーザーまたはグループのアクセス許可を定義します。 必要最低限のアクセス許可を割り当てるロールとバインディングを作成します。 ユーザーの状態やグループ メンバーシップが変わったとき、それが自動的に更新されるように、また、クラスター リソースへのアクセスが最新の状態になるように、Azure AD と統合します。

Kubernetes では、クラスターのリソースへのアクセスを細かく制御できます。 アクセス許可はクラスター レベルで定義するか、特定の名前空間に定義できます。 管理できるリソースとアクセス許可の種類を定義できます。 このようなロールは、バインディングが与えられているユーザーまたはグループに適用されます。 *Roles*、*ClusterRoles*、*Bindings* に関する詳細については、「[Azure Kubernetes Service (AKS) でのアクセスと ID オプション][aks-concepts-identity]」を参照してください。

たとえば、*finance-app* という名前の名前空間にあるリソースに完全アクセスできる Role を作成できます。次のサンプル YAML マニフェストをご覧ください。

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

次に、Azure AD ユーザー *developer1\@contoso.com* を RoleBinding にバインドする RoleBinding が作成されます。次の YAML マニフェストをご覧ください。

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

*developer1\@contoso.com* が AKS クラスターに対して認証されると、*finance-app* 名前空間に対する完全アクセスが与えられます。 このように、リソースへのアクセスが論理的に分離されて制御されます。 Kubernetes RBAC は、前のセクションで説明したように、Azure AD 統合との連動で使用してください。

RBAC で Azure AD グループを使用して Kubernetes クラスター リソースへのアクセスを制御するには、「[Control access to cluster resources using role-based access controls and Azure Active Directory identities in AKS][azure-ad-rbac]」 (AKS でロールベースのアクセス制御と Azure AD の ID を使用してクラスター リソースへのアクセス制御する) を参照してください。

## <a name="use-pod-identities"></a>ポッド ID を使用する

**ベスト プラクティス ガイダンス** - ポッドやコンテナー イメージ内で固定の資格情報を使用しないでください。開示や悪用の危険にさらされます。 代わりに、ポッド ID を使用し、中央の Azure AD ID ソリューションによるアクセスを自動要求してください。 ポッド ID は、Linux のポッドおよびコンテナー イメージのみでの使用を目的としています。

Cosmos DB、Key Vault、Blob Storage など、他の Azure サービスへのアクセスをポッドが必要とするとき、ポッドはアクセス資格情報を必要とします。 このような資格情報はコンテナー イメージで定義されたり、Kubernetes シークレットとして挿入されたりできますが、手動で作成し、割り当てる必要があります。 多くの場合、資格情報はポッド全体で再利用されます。定期的なローテーションはありません。

Azure リソース (関連付けられた AKS オープン ソース プロジェクトとして現在実装されている) 用のマネージド ID によって、Azure AD を介してサービスへのアクセスを自動的に要求できます。 ポッドの資格情報は手動定義しません。代わりに、アクセス トークンをリアルタイムで要求します。アクセス トークンを利用し、割り当てられたサービスにのみアクセスできます。 AKS では、2 つのコンポーネントがクラスター オペレーターによってデプロイされ、ポッドはマネージド ID を利用できます。

* **Node Management Identity (NMI) サーバー**は、AKS クラスターの各ノードで DaemonSet として実行されるポッドです。 NMI サーバーは、Azure サービスへのポッド要求を待ち受けます。
* **Managed Identity Controller (MIC)** は Kubernetes API サーバーにクエリを実行する許可が与えられた中央ポッドであり、ポッドに該当する Azure ID マッピングの存在を確認します。

ポッドが Azure サービスへのアクセスを要求すると、ネットワーク ルールによって Node Management Identity (NMI) サーバーにトラフィックがリダイレクトされます。 NMI サーバーはリモート アドレスに基づいて Azure サービスへのアクセスを要求するポッドであり、Managed Identity Controller (MIC) にクエリを実行します。 MIC は AKS クラスターで Azure ID マッピングの存在を確認し、NMI サーバーはポッドの ID マッピングに基づいて Azure Active Directory (AD) にアクセス トークンを要求します。 Azure AD が与える NMI サーバーへのアクセスがポッドに返されます。 ポッドはこのアクセス トークンを利用し、Azure のサービスへのアクセスを要求できます。

次の例では、開発者はマネージド ID を利用して Azure SQL Server インスタンスへのアクセスを要求するポッドを作成しています。

![ポッド ID によって、他のサービスへのアクセスをポッドは自動要求できます。](media/operator-best-practices-identity/pod-identities.png)

1. クラスター オペレーターはまず、ポッドがサービスへのアクセスを要求するとき、ID のマッピングに使用できるサービス アカウントを作成します。
1. NMI サーバーと MIC がデプロイされ、アクセス トークンのポッド要求を Azure AD に中継します。
1. 開発者は、NMI サーバー経由でアクセス トークンを要求するポッドをマネージド ID と共にデプロイします。
1. トークンがポッドに返され、Azure SQL Server インスタンスにアクセスするために使用されます。

> [!NOTE]
> マネージド ポッド ID はオープン ソース プロジェクトです。これは Azure テクニカル サポートではサポートされません。

ポッド ID を使用する方法については、[Kubernetes アプリケーションのための Azure Active Directory ID][aad-pod-identity] に関するページを参照してください。

## <a name="next-steps"></a>次の手順

このベスト プラクティスの記事では、クラスターとリソースの認証と認可について取り上げました。 これらのベスト プラクティスのいくつかを実装する場合は、次の記事を参照してください。

* [Azure Active Directory と AKS の統合][aks-aad]
* [AKS で Azure リソースのマネージド ID を使用する][aad-pod-identity]

AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナントとクラスター分離][aks-best-practices-scheduler]
* [Kubernetes スケジューラの基本的な機能][aks-best-practices-scheduler]
* [Kubernetes スケジューラの高度な機能][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
