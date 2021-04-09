---
title: Azure Kubernetes Service (AKS) でポッド セキュリティ ポリシーを使用する
description: Azure Kubernetes Service (AKS) で PodSecurityPolicy を使用してポッドのアドミッションを制御する方法について学習する
services: container-service
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: cb317e5e0d1f558121e675f569bad37811768ca6
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180311"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) でポッド セキュリティ ポリシーを使用してクラスターのセキュリティを保護する

> [!WARNING]
> **このドキュメントに記載したポッド セキュリティ ポリシー (プレビュー) 機能は非推奨となる予定です。2021 年 6 月 30 日を過ぎると使用できなくなるため**、[AKS 用の Azure Policy](use-azure-policy.md) が推奨されます。 非推奨となる日は、以前に記載した日付の 2020 年 10 月 15 日から延長されています。
>
> ポッド セキュリティ ポリシー (プレビュー) が非推奨となった後、今後のクラスター アップグレードを実行し、Azure サポート内に留まるには、非推奨の機能を使用する既存のクラスターでその機能を無効にする必要があります。
>
> AKS 用の Azure Policy を使用したテスト シナリオを開始することを強くお勧めします。これには、ポッドをセキュリティで保護するための組み込みのポリシーと、ポッド セキュリティ ポリシーにマップする組み込みのイニシアチブが用意されています。 ポッドのセキュリティ ポリシーから移行するには、クラスターで次の操作を行う必要があります。
> 
> 1. クラスターで[ポッドのセキュリティ ポリシーを無効にします](#clean-up-resources)。
> 1. [Azure Policy アドオン][kubernetes-policy-reference]を有効にします。
> 1. [使用可能な組み込みポリシー][policy-samples]から目的の Azure Policy を有効にします。
> 1. [ポッドのセキュリティ ポリシーと Azure Policy の動作変更](#behavior-changes-between-pod-security-policy-and-azure-policy)を確認します。

AKS クラスターのセキュリティを向上させるには、どのポッドをスケジュールできるかを制限することができます。 許可しないリソースを要求するポッドは、AKS クラスターで実行できません。 ポッド セキュリティ ポリシーを使用してこのアクセスを定義します。 この記事では、ポッド セキュリティ ポリシーを使用して AKS でのポッドのデプロイを制限する方法について説明します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

ポッド セキュリティ ポリシーを使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.1 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールし、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>ポッド セキュリティ ポリシー機能プロバイダーを登録する

**このドキュメントと機能は、2020 年 10 月 15 日に非推奨となる予定です。**

ポッド セキュリティ ポリシーを使用するために AKS クラスターを作成または更新するには、まず自分のサブスクリプションで機能フラグを有効にします。 *PodSecurityPolicyPreview* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録状態を確認するには、[az feature list][az-feature-list] コマンドを使用します。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>ポッド セキュリティ ポリシーの概要

Kubernetes クラスターでは、リソースが作成されるときに API サーバーへの要求をインターセプトするためにアドミッション コントローラーが使用されます。 次にアドミッション コントローラーは、一連のルールに対してリソース要求を *検証* したり、デプロイ パラメーターを変更するようにリソースを *変化* させたりすることができます。

*PodSecurityPolicy* は、ポッド仕様が定義されている要件を満たしていることを検証するアドミッション コント ローラーです。 これらの要件により、特権コンテナーの使用、特定の種類のストレージへのアクセス、またはコンテナーを実行できるユーザーやグループが制限される可能性があります。 ポッド セキュリティ ポリシーに概説されている要件をポッド仕様が満たしていない場合にリソースをデプロイしようとすると、要求は拒否されます。 AKS クラスターにどのポッドをスケジュールできるかを制御するこの機能により、いくつかの潜在的なセキュリティの脆弱性や特権エスカレーションを防ぐことができます。

AKS クラスターでポッド セキュリティ ポリシーを有効にすると、いくつかの既定のポリシーが適用されます。 これらの既定のポリシーには、どのようなポッドをスケジュールできるかを定義するための、すぐに使えるエクスペリエンスが用意されています。 ただし、独自のポリシーを定義するまでは、クラスター ユーザーはポッドのデプロイで問題に遭遇する可能性があります。 推奨される方法:

* AKS クラスターを作成する
* 独自のポッド セキュリティ ポリシーを定義する
* ポッド セキュリティ ポリシー機能を有効にする

既定のポリシーでポッドのデプロイがどのように制限されるかを示すために、この記事では、最初にポッド セキュリティ ポリシー機能を有効にしてから、カスタム ポリシーを作成します。

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>ポッドのセキュリティ ポリシーと Azure Policy の動作変更

ポッドのセキュリティポリシーと Azure Policy の動作変更の概要を次に示します。

|シナリオ| ポッドのセキュリティ ポリシー | Azure Policy |
|---|---|---|
|インストール|ポッドのセキュリティ ポリシー機能を有効にします |Azure Policy アドオンを有効にします
|ポリシーのデプロイ| ポッドのセキュリティ ポリシーのリソースをデプロイします| サブスクリプションまたはリソース グループのスコープに Azure Policy を割り当てます。 Kubernetes リソース アプリケーションには Azure Policy アドオンが必要です。
| 既定のポリシー | AKS でポッドのセキュリティ ポリシーが有効になっている場合、既定の特権ポリシーおよび無制限のポリシーが適用されます。 | Azure Policy アドオンを有効にしても、既定のポリシーは適用されません。 Azure Policy では、ポリシーを明示的に有効にする必要があります。
| ポリシーの作成と割り当てが可能なユーザー | クラスター管理者がポッドのセキュリティ ポリシー リソースを作成します。 | ユーザーには、少なくとも AKS クラスター リソース グループに対する "所有者" または "リソース ポリシーの共同作成者" 権限を含むロールが必要です。 - ユーザーは、API を通じて、AKS クラスター リソース スコープでポリシーを割り当てることができます。 ユーザーには、少なくとも AKS クラスター リソースに対する "所有者" または "リソース ポリシーの共同作成者" 権限が必要です。 - Azure portal では、ポリシーを管理グループ、サブスクリプション、またはリソース グループ レベルで割り当てることができます。
| ポリシーの承認| ユーザーおよびサービス アカウントには、ポッドのセキュリティ ポリシーを使用するための明示的な権限が必要です。 | ポリシーを承認するために、追加の割り当ては必要ありません。 Azure でポリシーが割り当てられると、すべてのクラスター ユーザーがこれらのポリシーを使用できるようになります。
| ポリシーの適用性 | 管理者ユーザーは、ポッド セキュリティ ポリシーの適用をバイパスします。 | すべてのユーザー (管理者および管理者以外) は同じポリシーを認識します。 ユーザーに基づく特殊な文字種はありません。 ポリシーの適用は、名前空間レベルで除外できます。
| ポリシーのスコープ | ポッドのセキュリティ ポリシーには、名前空間が指定されていません。 | Azure Policy によって使用される制約テンプレートには、名前空間が指定されていません。
| 拒否/監査/変異アクション | ポッドのセキュリティ ポリシーでは、拒否アクションのみがサポートされます。 作成要求では、既定値を使用して変異を実行できます。 更新要求中に検証を行うことができます。| Azure Policy では、監査アクションと拒否アクションの両方がサポートされています。 変異はまだサポートされていませんが、計画中です。
| ポッドのセキュリティ ポリシーへの準拠 | ポッドのセキュリティ ポリシーを有効にする前に存在していたポッドが、それに準拠しているかどうかを可視化することはできません。 ポッドのセキュリティ ポリシーを有効にした後に作成された非準拠ポッドは拒否されます。 | Azure ポリシーを適用する前に存在していた非準拠ポッドは、ポリシー違反として表示されます。 ポリシーに deny 効果が設定されている場合、Azure ポリシーを有効にした後に作成された非準拠ポッドは拒否されます。
| クラスターでポリシーを表示する方法 | `kubectl get psp` | `kubectl get constrainttemplate` - すべてのポリシーが返されます。
| ポッドのセキュリティ ポリシーの標準 - 特権 | この機能を有効にすると、特権ポッド セキュリティ ポリシー リソースが既定で作成されます。 | 特権モードは、制限がないことを意味します。したがって、Azure Policy が割り当てられていないことと同じです。
| [ポッドのセキュリティ ポリシーの標準 - ベースライン/既定](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | ユーザーは、ポッドのセキュリティ ポリシーのベースライン リソースをインストールします。 | Azure Policy には、ベースライン ポッド セキュリティ ポリシーにマップされる[組み込みベースライン イニシアチブ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d)が用意されています。
| [ポッドのセキュリティ ポリシーの標準 - 制限付き](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | ユーザーは、ポッドのセキュリティ ポリシーの制限付きリソースをインストールします。 | Azure Policy には、制限付きポッド セキュリティ ポリシーにマップされる[組み込みの制限付きイニシアチブ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)が用意されています。

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS クラスターでポッド セキュリティ ポリシーを有効にする

[az aks update][az-aks-update] コマンドを使用して、ポッド セキュリティ ポリシーを有効または無効にすることができます。 次の例は、*myResourceGroup* という名前のリソース グループ内のクラスター名 *myAKSCluster* に対してポッド セキュリティ ポリシーを有効にします。

> [!NOTE]
> 実際の使用では、独自のカスタム ポリシーを定義するまではポッド セキュリティ ポリシーを有効にしないでください。 この記事では、既定のポリシーでポッドのデプロイがどのように制限されるかを確認するために、最初の手順としてポッド セキュリティ ポリシーを有効にします。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>既定の AKS ポリシー

ポッド セキュリティ ポリシーを有効にすると、AKS によって、*privileged* という名前の既定ポリシーが 1 つ作成されます。 既定のポリシーを編集または削除しないでください。 代わりに、自分が制御したい設定を定義する、独自のポリシーを作成します。 最初に、これらの既定のポリシーがどのようなものか、そしてそれらがどのようにポッドのデプロイに影響を与えるかについて見てみましょう。

使用可能なポリシーを表示するには、次の例に示すように [kubectl get psp][kubectl-get] コマンドを使用します。

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*privileged* ポッド セキュリティ ポリシーは、AKS クラスター内のすべての認証済みユーザーに適用されます。 この割り当ては、ClusterRole と ClusterRoleBinding によって制御されます。 [kubectl get clusterrolebindings][kubectl-get] コマンドを使用して、*kube-system* 名前空間で *default:privileged:* バインドを検索します。

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

次の縮約された出力に示されているように、*psp:privileged* ClusterRole は、すべての *system:authenticated* ユーザーに割り当てられます。 この機能により、独自のポリシーが定義されていなくても基本レベルの特権が提供されます。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

独自のセキュリティ ポリシーの作成を開始する前に、これらの既定のポリシーがポッドをスケジュールするためにどのようにユーザー要求に作用するかを理解することが重要です。 次のいくつかのセクションでは、これらの既定のポリシーの動作を確認するために、いくつかポッドをスケジュールしてみましょう。

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS クラスターでテスト ユーザーを作成する

既定により、[az aks get-credentials][az-aks-get-credentials] コマンドを使用すると、AKS クラスターの "*管理者*" 資格情報が `kubectl` config. に追加されます。管理者ユーザーは、ポッド セキュリティ ポリシーの適用をバイパスします。 AKS クラスターに Azure Active Directory 統合を使用する場合は、管理者以外のユーザーの資格情報でサインインして、ポリシーの適用の動作を確認することができます。 この記事では、自分が使用できる AKS クラスターにテスト ユーザー アカウントを作成しましょう。

[kubectl create namespace][kubectl-create] コマンドを使用して、テスト リソース用に *psp-aks* という名前のサンプル名前空間を作成します。 そして、[kubectl create serviceaccount][kubectl-create] コマンドを使用して、*nonadmin-user* という名前のサービス アカウントを作成します。

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

次に、[kubectl create rolebinding][kubectl-create] コマンドを使用して、名前空間で基本的な操作を行うために "*管理者以外のユーザー*" の RoleBinding を作成します。

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>管理者と管理者以外のユーザーのエイリアス コマンドを作成する

`kubectl` を使用する際の通常の管理者ユーザーと、前の手順で作成した管理者以外のユーザーの違いを明らかにするために、次のように 2 つのコマンドライン エイリアスを作成します。

* **kubectl-admin** エイリアスは通常の管理者ユーザー用で、スコープは *psp-aks* 名前空間です。
* **Kubectl nonadminuser** エイリアスは、前の手順で作成した *管理者以外のユーザー* 用で、スコープは *psp aks* 名前空間です。

次のコマンドで示すように、これらの 2 つのエイリアスを作成します。

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>特権のあるポッドの作成をテストする

まず、`privileged: true` のセキュリティ コンテキストを持つポッドをスケジュールしたときに何が起こるかをテストしてみましょう。 このセキュリティ コンテキストは、ポッドの特権をエスカレートします。 既定の AKS ポッド セキュリティ ポリシーを示した前のセクションでは、*privilege* ポリシーがこの要求を拒否するはずです。

`nginx-privileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

次の出力例に示すように、このポッドはスケジュールできません。

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

ポッドはスケジューリング段階に達しないため、続行する前に削除するリソースはありません。

## <a name="test-creation-of-an-unprivileged-pod"></a>特権のないポッドの作成をテストする

前の例で、ポッド仕様は特権のエスカレーションを要求しました。 この要求は既定の *privilege* ポッド セキュリティ ポリシーによって拒否されるため、ポッドはスケジュールできません。 次に、特権エスカレーション要求なしでその同じ NGINX ポッドを実行してみましょう。

`nginx-unprivileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

次の出力例に示すように、このポッドはスケジュールできません。

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

ポッドはスケジューリング段階に達しないため、続行する前に削除するリソースはありません。

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>特定のユーザー コンテキストでのポッドの作成をテストする

前の例では、コンテナー イメージは、自動的に root を使用して NGINX をポート 80 にバインドしようとしました。 この要求は既定の *privilege* ポッド セキュリティ ポリシーによって拒否されたため、ポッドは開始できません。 次に、`runAsUser: 2000` などの特定のユーザー コンテキストを使用して、その同じ NGINX ポッドを実行してみましょう。

`nginx-unprivileged-nonroot.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

次の出力例に示すように、このポッドはスケジュールできません。

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

ポッドはスケジューリング段階に達しないため、続行する前に削除するリソースはありません。

## <a name="create-a-custom-pod-security-policy"></a>カスタム ポッド セキュリティ ポリシーを作成する

既定のポッド セキュリティ ポリシーのビヘイビアーを確認したので、*管理者以外のユーザー* が正常にポッドをスケジュールする方法を提供しましょう。

特権アクセスを要求するポッドを拒否するポリシーを作成しましょう。 *runAsUser* や許可される *ボリューム* などのその他のオプションは、明示的に制限されません。 この種類のポリシーは特権アクセスの要求を拒否しますが、それ以外の場合は、要求されたポッドをクラスターに実行させます。

`psp-deny-privileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

[kubectl apply][kubectl-apply] コマンドを使用してポリシーを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f psp-deny-privileged.yaml
```

使用可能なポリシーを表示するには、次の例に示すように [kubectl get psp][kubectl-get] コマンドを使用します。 *psp-deny-privileged* ポリシーを、前述のポッドを作成する例で適用されていた既定の *privilege* ポリシーと比較します。 *PRIV* エスカレーションの使用のみがポリシーによって拒否されます。 *psp-deny-privileged* ポリシーには、ユーザーまたはグループの制限はありません。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>ユーザー アカウントでのカスタム ポッド ポリシーの使用を許可する

前の手順では、特権アクセスを要求するポッドを拒否するポッド セキュリティ ポリシーを作成しました。 このポリシーの使用を許可するには、*Role* または *ClusterRole* を作成します。 次に、*RoleBinding* または *ClusterRoleBinding* を使用してこれらのロールのいずれかを関連付けます。

この例では、前の手順で作成した *psp-deny-privileged* ポリシーの *使用* を可能にする ClusterRole を作成します。 `psp-deny-privileged-clusterrole.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

[kubectl apply][kubectl-apply] コマンドを使用して ClusterRole を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

次に、前の手順で作成した ClusterRole を使用するために ClusterRoleBinding を作成します。 `psp-deny-privileged-clusterrolebinding.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

[kubectl apply][kubectl-apply] コマンドを使用して ClusterRoleBinding を作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> この記事の最初の手順で、ポッド セキュリティ ポリシー機能が AKS クラスターで有効にされました。 推奨プラクティスは、独自のポリシーを定義した後でのみポッド セキュリティ ポリシーを有効にするというものでした。 これが、ポッド セキュリティ ポリシー機能を有効にする段階です。 1 つ以上のカスタム ポリシーが定義されており、ユーザー アカウントがそれらのポリシーに関連付けられています。 これで、ポッド セキュリティ ポリシー機能を安全に有効にして、既定のポリシーによって引き起こされる問題を最小限に抑えることができます。

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>特権のないポッドの作成をもう一度テストする

カスタム ポッド セキュリティ ポリシーが適用され、そのポリシーを使用するためのユーザー アカウントのバインディングが作成されたので、特権のないポッドをもう一度作成してみましょう。 [kubectl apply][kubectl-apply] コマンドを使って、同じ `nginx-privileged.yaml` マニフェストを使用してポッドを作成します。

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

ポッドが正常にスケジュールされます。 [kubectl get pods][kubectl-get] コマンドを使用してポッドの状態を確認すると、ポッドは *Running* 状態です。

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

この例は、カスタム ポッド セキュリティ ポリシーを作成して、さまざまなユーザーまたはグループのための AKS クラスターへのアクセス権を定義する方法を示しています。 既定の AKS ポリシーでは、ポッドが実行できることに対して厳格な管理が提供されているので、独自のカスタム ポリシーを作成して、自分に必要な制限を正しく定義します。

[kubectl delete][kubectl-delete] コマンドを使用して、特権のない NGINX ポッドを削除し、YAML マニフェストの名前を指定します。

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ポッド セキュリティ ポリシーを無効にするには、再び [az aks update][az-aks-update] コマンドを使用します。 次の例は、*myResourceGroup* という名前のリソース グループ内のクラスター名 *myAKSCluster* でポッド セキュリティ ポリシーを無効にします。

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

次に、ClusterRole と ClusterRoleBinding を削除します。

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

[kubectl delete][kubectl-delete] コマンドを使用してセキュリティ ポリシーを削除し、YAML マニフェストの名前を指定します。

```console
kubectl delete -f psp-deny-privileged.yaml
```

最後に、次のように *psp-aks* 名前空間を削除します。

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>次のステップ

この記事では、特権アクセスの使用を防止するためのポッド セキュリティ ポリシーを作成する方法を示しました。 ボリュームの種類や RunAs ユーザーなど、ポリシーが適用できるたくさんの機能があります。 利用可能なオプションの詳細については、[Kubernetes ポッド セキュリティ ポリシーの参照ドキュメント][kubernetes-policy-reference]に関するページを参照してください。

ポッド ネットワーク トラフィックの制限の詳細については、「[Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][network-policies]」を参照してください。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
