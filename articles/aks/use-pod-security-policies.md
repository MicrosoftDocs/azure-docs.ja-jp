---
title: Azure Kubernetes Service (AKS) でポッド セキュリティ ポリシーを使用する
description: Azure Kubernetes Service (AKS) で PodSecurityPolicy を使用してポッドのアドミッションを制御する方法について学習する
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: c398567dd3383f4b0b4fd2eaa4b474d1e95b7575
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613887"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) でポッド セキュリティ ポリシーを使用してクラスターのセキュリティを保護する

AKS クラスターのセキュリティを向上させるには、どのポッドをスケジュールできるかを制限することができます。 許可しないリソースを要求するポッドは、AKS クラスターで実行できません。 ポッド セキュリティ ポリシーを使用してこのアクセスを定義します。 この記事では、ポッド セキュリティ ポリシーを使用して AKS でのポッドのデプロイを制限する方法について説明します。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービス、オプトインです。 これらは、コミュニティからフィードバックやバグを収集するために提供されています。 これらの機能はプレビュー段階であり、運用環境での使用を意図していません。 パブリック プレビュー段階の機能は、"ベスト エフォート" のサポートに該当します。 AKS テクニカル サポート チームによるサポートは、太平洋タイム ゾーン (PST) での営業時間内のみで利用できます。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]して、AKS のクイックスタートを参照してください。

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、 `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「 [Azure CLI のインストール][install-azure-cli]」を参照してください。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

ポッド セキュリティ ポリシーを使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.1 以降が必要です。 [az extension add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールします。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>ポッド セキュリティ ポリシー機能プロバイダーを登録する

ポッド セキュリティ ポリシーを使用するために AKS クラスターを作成または更新するには、まず自分のサブスクリプションで機能フラグを有効にします。 *PodSecurityPolicyPreview* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

> [!CAUTION]
> サブスクリプションで機能を登録する場合、現時点ではその機能の登録解除を行うことができません。 一部のプレビュー機能を有効にした後、すべての AKS クラスターに対して既定値が使用され、サブスクリプション内に作成されます。 運用サブスクリプションではプレビュー機能を有効にしないでください。 プレビュー機能をテストし、フィードバックを集めるには、別のサブスクリプションを使用してください。

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

Kubernetes クラスターでは、リソースが作成されるときに API サーバーへの要求をインターセプトするためにアドミッション コントローラーが使用されます。 次にアドミッション コントローラーは、一連のルールに対してリソース要求を*検証*したり、デプロイ パラメーターを変更するようにリソースを*変化*させたりすることができます。

*PodSecurityPolicy* は、ポッド仕様が定義されている要件を満たしていることを検証するアドミッション コント ローラーです。 これらの要件により、特権コンテナーの使用、特定の種類のストレージへのアクセス、またはコンテナーを実行できるユーザーやグループが制限される可能性があります。 ポッド セキュリティ ポリシーに概説されている要件をポッド仕様が満たしていない場合にリソースをデプロイしようとすると、要求は拒否されます。 AKS クラスターにどのポッドをスケジュールできるかを制御するこの機能により、いくつかの潜在的なセキュリティの脆弱性や特権エスカレーションを防ぐことができます。

AKS クラスターでポッド セキュリティ ポリシーを有効にすると、いくつかの既定のポリシーが適用されます。 これらの既定のポリシーには、どのようなポッドをスケジュールできるかを定義するための、すぐに使えるエクスペリエンスが用意されています。 ただし、独自のポリシーを定義するまでは、クラスター ユーザーはポッドのデプロイで問題に遭遇する可能性があります。 推奨されるアプローチは次のとおりです。

* AKS クラスターの作成
* 独自のポッド セキュリティ ポリシーを定義する
* ポッド セキュリティ ポリシー機能を有効にする

既定のポリシーでポッドのデプロイがどのように制限されるかを示すために、この記事では、最初にポッド セキュリティ ポリシー機能を有効にしてから、カスタム ポリシーを作成します。

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

ポッド セキュリティ ポリシーを有効にすると、AKS は、*privileged* と *restricted* という名前の 2 つの既定のポリシーを作成します。 これらの既定のポリシーを編集または削除しないでください。 代わりに、自分が制御したい設定を定義する、独自のポリシーを作成します。 最初に、これらの既定のポリシーがどのようなものか、そしてそれらがどのようにポッドのデプロイに影響を与えるかについて見てみましょう。

使用可能なポリシーを表示するには、次の例に示すように [kubectl get psp][kubectl-get] コマンドを使用します。 既定の *restricted* ポリシーの一部として、ユーザーは特権ポッド エスカレーションに対する *PRIV* 使用を拒否され、ユーザーは *MustRunAsNonRoot* になります。

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*restricted* ポッド セキュリティ ポリシーは、AKS クラスター内のすべての認証済みユーザーに適用されます。 この割り当ては、ClusterRole と ClusterRoleBinding によって制御されます。 [kubectl get clusterrolebindings][kubectl-get] コマンドを使用して *default:restricted:* バインディングを検索します。

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

次の縮約された出力に示されているように、*psp:restricted* ClusterRole は、すべての *system:authenticated* ユーザーに割り当てられます。 この機能により、独自のポリシーが定義されていなくても基本レベルの制限が提供されます。

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

独自のセキュリティ ポリシーの作成を開始する前に、これらの既定のポリシーがポッドをスケジュールするためにどのようにユーザー要求とやり取りしているかを理解することが重要です。 次のいくつかのセクションでは、これらの既定のポリシーの動作を確認するために、いくつかポッドをスケジュールしてみましょう。

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS クラスターでテスト ユーザーを作成する

既定で、[az aks get-credentials][az-aks-get-credentials] コマンドを使用すると、AKS クラスターの "*管理者*" 資格情報が `kubectl` config. に追加されます。管理者ユーザーは、ポッド セキュリティ ポリシーの適用をバイパスします。 AKS クラスターに Azure Active Directory 統合を使用する場合は、管理者以外のユーザーの資格情報でサインインして、ポリシーの適用の動作を確認することができます。 この記事では、自分が使用できる AKS クラスターにテスト ユーザー アカウントを作成しましょう。

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
* **Kubectl nonadminuser** エイリアスは、前の手順で作成した*管理者以外のユーザー*用で、スコープは *psp aks* 名前空間です。

次のコマンドで示すように、これらの 2 つのエイリアスを作成します。

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>特権のあるポッドの作成をテストする

まず、`privileged: true` のセキュリティ コンテキストを持つポッドをスケジュールしたときに何が起こるかをテストしてみましょう。 このセキュリティ コンテキストは、ポッドの特権をエスカレートします。 既定の AKS ポッド セキュリティ ポリシーを示した前のセクションでは、*restricted* ポリシーがこの要求を拒否するはずです。

`nginx-privileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
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

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

ポッドはスケジューリング段階に達しないため、続行する前に削除するリソースはありません。

## <a name="test-creation-of-an-unprivileged-pod"></a>特権のないポッドの作成をテストする

前の例で、ポッド仕様は特権のエスカレーションを要求しました。 この要求は既定の *restricted* ポッド セキュリティ ポリシーによって拒否されるため、ポッドはスケジュールできません。 次に、特権エスカレーション要求なしでその同じ NGINX ポッドを実行してみましょう。

`nginx-unprivileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes スケジューラーは、ポッド要求を受け入れます。 ただし、次のように `kubectl get pods` を使用してポッドの状態を確認すると、エラーがあります。

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

[kubectl describe pod][kubectl-describe] コマンドを使用して、ポッドのイベントを確認します。 次の縮約された例は、要求していなかったのにもかかわらず、コンテナーとイメージでルート アクセス許可が要求されていることを示しています。

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

特権アクセス許可を要求していなかったのもかかわらず、NGINX のコンテナー イメージはポート *80* のバインディングを作成する必要があります。 *1024* 以下のポートをバインドするには、*root* ユーザーが必要です。 ポッドが開始しようとすると、*restricted* ポッド セキュリティ ポリシーがこの要求を拒否します。

この例は、AKS によって作成された既定のポッド セキュリティ ポリシーが有効であり、ユーザーが実行できるアクションを制限していることを示しています。 基本の NGINX ポッドが拒否されることを予期していない可能性があるので、これらの既定のポリシーのビヘイビアーを理解しておくことが重要です。

次の手順に進む前に、[kubectl delete pod][kubectl-delete] コマンドを使用してこのテスト ポッドを削除します。

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>特定のユーザー コンテキストでのポッドの作成をテストする

前の例では、コンテナー イメージは、自動的に root を使用して NGINX をポート 80 にバインドしようとしました。 この要求は既定の *restricted* ポッド セキュリティ ポリシーによって拒否されたため、ポッドは開始できません。 次に、`runAsUser: 2000` などの特定のユーザー コンテキストを使用して、その同じ NGINX ポッドを実行してみましょう。

`nginx-unprivileged-nonroot.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes スケジューラーは、ポッド要求を受け入れます。 ただし、次のように、`kubectl get pods` を使用してポッドの状態を確認すると、前の例とは異なるエラーがあります。

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

[kubectl describe pod][kubectl-describe] コマンドを使用して、ポッドのイベントを確認します。 次の縮約された例は、ポッドのイベントを示しています。

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

これらのイベントは、コンテナーが作成されて開始されたことを示しています。 ポッドが失敗状態である理由に関して、直ちに明らかなものはありません。 [kubectl logs][kubectl-logs] コマンドを使用してポッドのログを確認しましょう。

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

次のログ出力例は、NGINX 構成自体の中に、サービスが開始を試行するときにアクセス許可エラーがあることを示しています。 このエラーも、ポート 80 へのバインドが必要であることが原因で発生しています。 ポッド仕様には通常のユーザー アカウントが定義されていましたが、このユーザー アカウントの OS レベルは、NGINX サービスが起動して、制限されたポートにバインドするのに十分なものではありません。

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

ここでも、既定のポッド セキュリティ ポリシーのビヘイビアーを理解することが重要です。 このエラーは見つけるのが少し難しいものでした。そしてここでも、基本的な NGINX ポッドが拒否されることは予期できない可能性があります。

次の手順に進む前に、[kubectl delete pod][kubectl-delete] コマンドを使用してこのテスト ポッドを削除します。

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>カスタム ポッド セキュリティ ポリシーを作成する

既定のポッド セキュリティ ポリシーのビヘイビアーを確認したので、*管理者以外のユーザー*が正常にポッドをスケジュールする方法を提供しましょう。

特権アクセスを要求するポッドを拒否するポリシーを作成しましょう。 *runAsUser* や許可される*ボリューム*などのその他のオプションは、明示的に制限されません。 この種類のポリシーは特権アクセスの要求を拒否しますが、それ以外の場合は、要求されたポッドをクラスターに実行させます。

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

使用可能なポリシーを表示するには、次の例に示すように [kubectl get psp][kubectl-get] コマンドを使用します。 *psp-deny-privileged* ポリシーを、前述のポッドを作成する例で適用されていた既定の *restricted* ポリシーと比較します。 *PRIV* エスカレーションの使用のみがポリシーによって拒否されます。 *psp-deny-privileged* ポリシーには、ユーザーまたはグループの制限はありません。

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>ユーザー アカウントでのカスタム ポッド ポリシーの使用を許可する

前の手順では、特権アクセスを要求するポッドを拒否するポッド セキュリティ ポリシーを作成しました。 このポリシーの使用を許可するには、*Role* または *ClusterRole* を作成します。 次に、*RoleBinding* または *ClusterRoleBinding* を使用してこれらのロールのいずれかを関連付けます。

この例では、前の手順で作成した *psp-deny-privileged* ポリシーの*使用*を可能にする ClusterRole を作成します。 `psp-deny-privileged-clusterrole.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

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
apiVersion: rbac.authorization.k8s.io/v1beta1
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
> この記事の最初の手順で、ポッド セキュリティ ポリシー機能が AKS クラスターで有効にされました。 推奨プラクティスは、独自のポリシーを定義した後でのみポッド セキュリティ ポリシーを有効にするというものでした。 これが、ポッド セキュリティ ポリシー機能を有効にする段階です。 1 つ以上のカスタム ポリシーが定義されており、ユーザー アカウントがそれらのポリシーに関連付けられています。 これで、ポッド セキュリティ ポリシー機能を安全に使用して、既定のポリシーによって引き起こされる問題を最小限に抑えることができます。

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

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

[kubectl delete][kubectl-delete] コマンドを使用してネットワーク ポリシーを削除し、YAML マニフェストの名前を指定します。

```console
kubectl delete -f psp-deny-privileged.yaml
```

最後に、次のように *psp-aks* 名前空間を削除します。

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>次の手順

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
