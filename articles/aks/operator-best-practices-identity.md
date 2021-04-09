---
title: ID 管理のベスト プラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) でクラスターの認証と認可を管理する方法に関するクラスター オペレーター ベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 8e0c7324f5b73b3a2ac5e5fd6fa256202035077a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790971"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) の認証と認可のベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターをデプロイし、保守管理するとき、リソースやサービスへのアクセスを管理する方法を実装する必要があります。 このような制御がなければ、必要としないリソースやサービスへのアクセスがアカウントに与えられることがあります。 また、変更を行う際に使用された資格情報セットを追跡することが難しくなる場合があります。

このベスト プラクティス記事では、クラスター オペレーターが AKS クラスターのアクセスと ID を管理する方法について取り上げます。 この記事では、次のことについて説明します。

> [!div class="checklist"]
>
> * Azure Active Directory (AAD) を利用して AKS クラスター ユーザーを認証する
> * Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用してリソースへのアクセスを制御する
> * Azure RBAC を使用して、規模に応じた AKS リソースと Kubernetes API へのアクセス、および kubeconfig へのアクセスを細かく制御する。
> * マネージド ID を使用し、他のサービスでポッド自体を認証する

## <a name="use-azure-active-directory"></a>Azure Active Directory を使用する

**ベスト プラクティス ガイダンス** - Azure AD 統合で AKS クラスターをデプロイします。 Azure AD を使用することで、ID 管理コンポーネントが一元化されます。 ユーザー アカウントまたはグループの状態が変わると、AKS クラスターにアクセスしたとき、その変更内容が自動的に更新されます。 ユーザーまたはグループに必要最低限のアクセス許可を与える場合、次のセクションで説明する Roles または ClusterRoles と Bindings を使用します。

お使いの Kubernetes クラスターの開発者とアプリケーション所有者はさまざまなリソースへのアクセスを必要とします。 Kubernetes からは、リソースを使用するユーザーとユーザーに使用されるリソースを制御する ID 管理ソリューションが提供されません。 代わりに、一般的には自分のクラスターと既存の ID ソリューションを統合します。 Azure Active Directory (AD) では企業対応の ID 管理ソリューションが提供されます。また、AD は AKS クラスターと統合できます。

Azure AD 統合クラスターを AKS を使用するとき、リソースへのアクセス許可を定義する *Roles* または *ClusterRoles* を作成します。 作成後、Azure AD のユーザーまたはグループにロールを *バインド* します。 このような Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) については、次のセクションで説明します。 Azure AD の統合とリソースへのアクセスの制御方法をまとめたのが次の図です。

![Azure Active Directory と AKS の統合のためのクラスターレベルの認証](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 開発者が Azure AD を利用して認証します。
1. Azure AD トークン発行エンドポイントがアクセス トークンを発行します。
1. 開発者は、`kubectl create pod` など、Azure AD トークンを使用してアクションを実行します。
1. Kubernetes では Azure Active Directory を利用してトークンの有効性が検証され、開発者のグループ メンバーシップが取得されます。
1. Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) とクラスター ポリシーが適用されます。
1. 先の Azure AD グループ メンバーシップの検証、Kubernetes RBAC、ポリシーに基づき、開発者の要求が通過するか、却下されます。

Azure AD を使用する AKS クラスターを作成する方法については、「[Azure Active Directory と Azure Kubernetes Service を統合する][aks-aad]」を参照してください。

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用する

**ベスト プラクティス ガイダンス** - Kubernetes RBAC を使用し、クラスターのリソースに対するユーザーまたはグループのアクセス許可を定義します。 必要最低限のアクセス許可を割り当てるロールとバインディングを作成します。 ユーザーの状態やグループ メンバーシップが変わったとき、それが自動的に更新されるように、また、クラスター リソースへのアクセスが最新の状態になるように、Azure AD と統合します。

Kubernetes では、クラスターのリソースへのアクセスを細かく制御できます。 アクセス許可は、クラスター レベルで、または特定の名前空間に対して定義されます。 管理できるリソースとアクセス許可の種類を定義できます。 このようなロールはその後、バインディングが与えられているユーザーまたはグループに適用されます。 *Roles*、*ClusterRoles*、*Bindings* に関する詳細については、「[Azure Kubernetes Service (AKS) でのアクセスと ID オプション][aks-concepts-identity]」を参照してください。

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

Kubernetes RBAC と Azure AD グループを使用して Kubernetes クラスター リソースへのアクセスを制御するには、[AKS でロールベースのアクセス制御と Azure Active Directory の ID を使用してクラスター リソースへのアクセスを制御する][azure-ad-rbac]方法に関するページを参照してください。

## <a name="use-azure-rbac"></a>Azure RBAC を使用する 
**ベスト プラクティス ガイダンス** - Azure RBAC を使用して、ユーザーまたはグループが 1 つ以上のサブスクリプションの AKS リソースに対して持つ必要な最小限のアクセス許可を定義します。

AKS クラスターを完全に運用するには、次の 2 つのレベルのアクセスが必要です。 
1. Azure サブスクリプションの AKS リソースへのアクセス。 このアクセス レベルでは、AKS API を使用してクラスターのスケーリングやアップグレードを制御したり、kubeconfig をプルしたりすることができます。
AKS リソースと kubeconfig へのアクセスを制御する方法については、[クラスター構成ファイルへのアクセスの制限](control-kubeconfig-access.md)に関する記事を参照してください。

2. Kubernetes API へのアクセス。 このアクセス レベルは、[Kubernetes RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (従来)、または Kubernetes 認可のための Azure RBAC と AKS の統合によって制御されます。
Azure RBAC を使用して Kubernetes API にアクセス許可を細かく付与する方法については、[Kubernetes 認可に対する Azure RBAC の使用](manage-azure-rbac.md)に関する記事を参照してください。

## <a name="use-pod-managed-identities"></a>ポッドマネージド ID を使用する

**ベスト プラクティス ガイダンス** - ポッドやコンテナー イメージ内で固定の資格情報を使用しないでください。開示や悪用の危険にさらされます。 代わりに、ポッド ID を使用し、中央の Azure AD ID ソリューションによるアクセスを自動要求してください。 ポッド ID は、Linux のポッドおよびコンテナー イメージのみでの使用を目的としています。

> [!NOTE]
> Windows コンテナーに対するポッドマネージド ID のサポートは近日対応予定です。

Cosmos DB、Key Vault、Blob Storage など、他の Azure サービスへのアクセスをポッドが必要とするとき、ポッドはアクセス資格情報を必要とします。 このような資格情報はコンテナー イメージで定義されたり、Kubernetes シークレットとして挿入されたりできますが、手動で作成し、割り当てる必要があります。 多くの場合、資格情報はポッド全体で再利用されます。定期的なローテーションはありません。

Azure リソースのポッドマネージド ID を利用すると、Azure AD 経由でサービスにアクセスすることを自動要求できます。 ポッドマネージド ID は、Azure Kubernetes Service では現在プレビュー段階です。 使用を開始するには、ドキュメント「[Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)」を参照してください。 ポッドマネージド ID を使用する場合、ポッドの資格情報は手動定義しません。代わりに、アクセス トークンをリアルタイムで要求します。アクセス トークンを使用し、割り当てられたサービスにのみアクセスできます。 AKS には、ポッドでマネージド ID を使用できるようにする操作を処理するコンポーネントが 2 つあります。

* **Node Management Identity (NMI) サーバー** は、AKS クラスターの各ノードで DaemonSet として実行されるポッドです。 NMI サーバーは、Azure サービスへのポッド要求を待ち受けます。
* **Azure リソース プロバイダー** は、Kubernetes API サーバーに対してクエリを行い、ポッドに対応する Azure ID マッピングを確認します。

ポッドが Azure サービスへのアクセスを要求すると、ネットワーク ルールによって Node Management Identity (NMI) サーバーにトラフィックがリダイレクトされます。 NMI サーバーは、リモート アドレスに基づいて Azure サービスへのアクセスを要求するポッドを識別し、Azure リソース プロバイダーに対してクエリを行います。 Azure リソース プロバイダーは、AKS クラスターで Azure ID マッピングを確認します。その後、NMI サーバーは、ポッドの ID マッピングに基づいて Azure Active Directory (AD) にアクセス トークンを要求します。 Azure AD が与える NMI サーバーへのアクセスがポッドに返されます。 ポッドはこのアクセス トークンを利用し、Azure のサービスへのアクセスを要求できます。

次の例では、開発者はマネージド ID を利用して Azure SQL Database へのアクセスを要求するポッドを作成しています。

![ポッド ID によって、他のサービスへのアクセスをポッドは自動要求できます。](media/operator-best-practices-identity/pod-identities.png)

1. クラスター オペレーターはまず、ポッドがサービスへのアクセスを要求するとき、ID のマッピングに使用できるサービス アカウントを作成します。
1. NMI サーバーは、アクセス トークンのためのポッド要求を、Azure リソース プロバイダーと共に Azure AD に中継するためにデプロイされます。
1. 開発者は、NMI サーバー経由でアクセス トークンを要求するポッドをマネージド ID と共にデプロイします。
1. トークンがポッドに返され、Azure SQL Database にアクセスするために使用されます

> [!NOTE]
> ポッドマネージド ID は、現在プレビュー状態です。

ポッドマネージド ID を使用するには、「[Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)」を参照してください。

## <a name="next-steps"></a>次のステップ

このベスト プラクティスの記事では、クラスターとリソースの認証と認可について取り上げました。 これらのベスト プラクティスのいくつかを実装する場合は、次の記事を参照してください。

* [Azure Active Directory と AKS の統合][aks-aad]
* [Azure Kubernetes Service で Azure Active Directory ポッドマネージド ID を使用する (プレビュー)]( https://docs.microsoft.com/azure/aks/use-azure-ad-pod-identity)

AKS でのクラスター操作の詳細については、次のベスト プラクティスを参照してください。

* [マルチ テナントとクラスター分離][aks-best-practices-cluster-isolation]
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
