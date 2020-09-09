---
title: Azure Kubernetes Service (AKS) の Azure Policy によるポッドのセキュリティ保護
description: Azure Kubernetes Service (AKS) で Azure Policy を使用してポッドをセキュリティ保護する方法について説明します。
services: container-service
ms.topic: article
ms.date: 07/06/2020
author: jluk
ms.openlocfilehash: 18947f409ebcef570998671f9f421f8228e9692d
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987360"
---
# <a name="secure-pods-with-azure-policy-preview"></a>Azure Policy を使用したポッドのセキュリティ保護 (プレビュー)

AKS クラスターのセキュリティを強化するために、ポッドに付与される機能と、会社のポリシーに対して違反となる内容を制御できます。 このアクセスは、AKS の [Azure Policy アドオン][kubernetes-policy-reference]によって提供される組み込みのポリシーを使用して定義されます。 ルート特権など、ポッドの仕様のセキュリティ面をさらに制御することにより、より厳密なセキュリティを確保し、クラスターにデプロイされている内容を確認することができます。 ポッドがポリシーで指定された条件を満たしていない場合、Azure Policy ではポッドの開始を禁止したり、違反を示すフラグを設定したりすることができます。 この記事では、Azure Policy を使用して AKS でのポッドのデプロイを制限する方法について説明します。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

### <a name="install-the-azure-policy-add-on-for-aks"></a>AKS 用の Azure Policy アドオンをインストールする

Azure Policy を使用して AKS ポッドをセキュリティ保護するには、AKS クラスターに AKS 用の Azure Policy アドオンをインストールする必要があります。 [こちら](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks)の手順に従って、Azure Policy アドオンをインストールします。

このドキュメントでは、リンク先の手順で以下がデプロイされていることを前提としています。

* `az provider register` を使用して `Microsoft.ContainerService` および `Microsoft.PolicyInsights` のリソース プロバイダーが登録されている
* `az feature register` を使用して `AKS-AzurePolicyAutoApprove` プレビュー機能フラグが登録されている
* Azure CLI と `aks-preview` 拡張機能バージョン 0.4.53 以上がインストールされている
* サポートされている 1.15 以上のバージョンの AKS クラスターと Azure Policy アドオンがインストールされている

## <a name="overview-of-securing-pods-with-azure-policy-for-aks-preview"></a>AKS 用の Azure Policy でポッドをセキュリティ保護する方法の概要 (プレビュー)

>[!NOTE]
> このドキュメントでは、Azure Policy を使用してポッドをセキュリティ保護する方法について詳しく説明します。これは、[プレビュー段階の Kubernetes ポッド セキュリティ ポリシー機能](use-pod-security-policies.md)の後継の機能です。
> **ポッドのセキュリティ ポリシー (プレビュー) と AKS 用 Azure Policy アドオンの両方を同時に有効にすることはできません。**
> 
> ポッドのセキュリティ ポリシーが有効になっているクラスターに Azure Policy アドオンをインストールする場合は、[こちら](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster)の手順に従ってポッドのセキュリティ ポリシーを無効にます。

AKS クラスターでは、リソースが作成され更新されるときに、API サーバーへの要求をインターセプトするためにアドミッション コントローラーが使用されます。 次にアドミッション コントローラーは、一連のルールに対してリソース要求を*検証*して、それを作成する必要があるかどうかを判断します。

以前は、Kubernetes プロジェクトによって[ポッドのセキュリティ ポリシー (プレビュー)](use-pod-security-policies.md) 機能が有効にされ、デプロイ可能なポッドが制限されていました。

Azure Policy アドオンを使用することにより、AKS クラスターで組み込みの Azure Policy を使用できるようになります。このポリシーでは、以前のポッドのセキュリティ ポリシーと同様に、ポッドとその他の Kubernetes リソースをセキュリティ保護することができます。 AKS 用の Azure Policy アドオンでは、[Gatekeeper](https://github.com/open-policy-agent/gatekeeper) のマネージド インスタンスがインストールされます。これは、検証を行うアドミッション コントローラーです。 Kubernetes 用 の Azure Policy は、[Rego ポリシー言語](../governance/policy/concepts/policy-for-kubernetes.md#policy-language)に依存するオープンソースの Open Policy Agent に基づいて構築されています。

このドキュメントでは、Azure Policy を使用して AKS クラスター内のポッドをセキュリティ保護する方法、およびポッドのセキュリティ ポリシー (プレビュー) から移行する方法について説明します。

## <a name="limitations"></a>制限事項

* プレビュー期間中、1 つのクラスターでの実行は、Kubernetes 用の Azure Policy 20 個を含む 200 のポッドに制限されます。
* AKS マネージド ポッドを含む[一部のシステム名前空間](#namespace-exclusion)は、ポリシーの評価から除外されます。
* Windows ポッドでは、[セキュリティ コンテキストがサポートされていません](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods)。そのため、ルート特権を許可しないといった多くの Azure ポリシーは Linux ポッドにのみ適用され、Windows ポッドにエスカレートすることはできません。
* ポッドのセキュリティ ポリシーと AKS 用 Azure Policy アドオンの両方を同時に有効にすることはできません。 ポッドのセキュリティ ポリシーが有効になっているクラスターに Azure Policy アドオンをインストールする場合は、[こちら](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster)の手順に従ってポッドのセキュリティ ポリシーを無効にします。

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Kubernetes ポッドをセキュリティ保護するための Azure ポリシー

Azure Policy アドオンをインストールした後、既定ではポリシーは適用されません。

11 個の組み込みの個別 Azure ポリシーと、特に AKS クラスター内のポッドをセキュリティ保護する 2 つの組み込みのイニシアチブがあります。
各ポリシーをカスタマイズして、効果を追加することができます。 AKS ポリシーとサポートされている効果のリストについては、[こちら][policy-samples]を参照してください。 Azure Policy の効果の詳細については、[こちら](../governance/policy/concepts/effects.md)をご覧ください。

Azure Policy は、管理グループ、サブスクリプション、またはリソース グループ レベルで適用できます。 リソース グループ レベルでポリシーを割り当てるときには、ポリシーの対象にターゲット AKS クラスターのリソース グループが選択されていることを確認してください。 Azure Policy アドオンがインストールされている、割り当てられたスコープ内のすべてのクラスターが、ポリシーの対象となります。

[ポッドのセキュリティ ポリシー (プレビュー)](use-pod-security-policies.md) を使用する場合は、[Azure Policy への移行方法、およびその他の動作の相違点](#migrate-from-kubernetes-pod-security-policy-to-azure-policy)について確認してください。

### <a name="built-in-policy-initiatives"></a>組み込みのポリシー イニシアチブ

Azure Policy のイニシアチブは、単一の包括的なゴールを達成することを目的として調整されたポリシー定義のコレクションです。 イニシアチブを使用すると、AKS クラスター間でのポリシーの管理と割り当てが簡単になります。 イニシアチブは単一のオブジェクトとして存在します。Azure Policy のイニシアチブについては、[こちら](../governance/policy/overview.md#initiative-definition)をご覧ください。

Kubernetes 用の Azure Policy には、ポッド、[ベースライン](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d)、および[制限付き](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)をセキュリティ保護する 2 つの組み込みイニシアチブが用意されています。

これらの組み込みイニシアチブは両方とも、[Kubernetes のポッドのセキュリティ ポリシー](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml)で使用される定義で構築されています。

|[ポッドのセキュリティ ポリシーの制御](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy の定義リンク| [ベースライン イニシアチブ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [制限付きイニシアチブ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|特権コンテナーの実行の禁止|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| はい | はい
|ホスト名前空間の共有使用の禁止|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| はい | はい
|ホスト ネットワークとポートの使用を制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| はい | はい
|ホスト ファイルシステムのあらゆる使用を制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| はい | はい
|Linux 機能を[既定のセット](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)に制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | はい | はい
|定義されたボリュームの種類の使用を制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | はい - 許可されているボリュームの種類は `configMap`、`emptyDir`、`projected`、`downwardAPI`、`persistentVolumeClaim`|
|ルートへの特権エスカレーション|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | はい |
|コンテナーのユーザー ID とグループ ID を制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | はい|
|ポッドのボリュームを所有する FSGroup の割り当てを制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | はい - 許可されている規則は `runAsUser: mustRunAsNonRoot`、`supplementalGroup: mustRunAs 1:65536`、`fsGroup: mustRunAs 1:65535`、`runAsGroup: mustRunAs 1:65535`。  |
|seccomp プロファイルを要求する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | はい、allowedProfiles は *`docker/default` または `runtime/default` |

\* docker/default は、Kubernetes では v1.11 以降非推奨

### <a name="additional-optional-policies"></a>追加のオプションのポリシー

イニシアチブの適用の外部で単独で適用できる、追加の Azure ポリシーがあります。 組み込みのイニシアチブで要件が満たされない場合は、イニシアチブに加えてこれらのポリシーを追加することを検討してください。

|[ポッドのセキュリティ ポリシーの制御](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy の定義リンク| ベースライン イニシアチブに加えて適用 | 制限付きイニシアチブに加えて適用 |
|---|---|---|---|
|コンテナーで使用される AppArmor プロファイルを定義する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | 省略可能 | 省略可能 |
|読み取り専用ではないマウントを許可する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | 省略可能 | 省略可能 |
|特定の FlexVolume ドライバーに制限する|[パブリック クラウド](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | 省略可能 - FlexVolume ドライバーを制限するだけで、"定義されたボリュームの種類の使用を制限する" で設定されているものは対象外の場合に、使用します | 適用外 - 制限付きのイニシアチブには、すべての FlexVolume ドライバーを禁止する "定義されたボリュームタイプの使用を制限する" が含まれます。 |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>マネージド AKS クラスターに対してサポートされていない組み込みポリシー

> [!NOTE]
> 次の 3 つのポリシーは、AKS によって管理サービスとして管理およびセキュリティ保護されているアスペクトのカスタマイズのため、**AKS ではサポートされていません**。 これらのポリシーは、管理されていないコントロール プレーンがある Azure Arc の接続されたクラスター専用に構築されています。

|[ポッドのセキュリティ ポリシーの制御](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|コンテナーのカスタム SELinux コンテキストを定義する|
|コンテナーで使用される sysctl プロファイルを制限する|
|既定 Proc のマウントの種類を定義して攻撃対象領域を減らす|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>名前空間の除外

> [!WARNING]
> kube-system などの管理名前空間内のポッドは、クラスターを正常な状態に維持するために実行する必要があります。既定の除外された名前空間のリストから必要な名前空間を削除すると、必須システム ポッドが原因でポリシー違反が発生する可能性があります。

AKS では、DNS 解決などの重要なサービスを提供するために、クラスターでシステム ポッドを実行する必要があります。 ポッド機能を制限するポリシーは、システムポッドの安定性に影響を与える可能性があります。 このため、次の名前空間は、**作成、更新、およびポリシー監査中の受付要求においてポリシーの評価から除外されます**。 この結果、これらの名前空間への新しいデプロイは Azure policy から強制的に除外されます。

1. kube-system
1. gatekeeper-system
1. azure-arc
1. aks-periscope

追加したカスタム名前空間は、作成、更新、および監査中に評価から除外することができます。 これは、承認された名前空間で実行され、監査違反のトリガーを避ける必要がある特殊なポッドがある場合に使用します。

## <a name="apply-the-baseline-initiative"></a>ベースライン イニシアチブの適用

> [!TIP]
> 既定では、すべてのポリシーの効果は audit になっています。 Azure Policy を使用して、効果を deny にいつでも更新できます。

ベースライン イニシアチブを適用するには、Azure portal を通じて割り当てます。
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. [このリンク](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d)から Azure portal にアクセスして、ポッドのセキュリティ ベースライン イニシアチブを確認します。
1. **[スコープ]** をサブスクリプション レベルに、または Azure Policy アドオンが有効な AKS クラスターを保持しているリソース グループのみに設定します。
1. **[パラメーター]** ページを選択し、 **[効果]** を `audit` から `deny` に更新して、ベースライン イニシアチブに違反している新しいデプロイをブロックします。
1. 作成、更新、および監査中に評価から除外する名前空間を追加します。[一部の名前空間は、ポリシーの評価から強制的に除外されます。](#namespace-exclusion)
![効果を更新](media/use-pod-security-on-azure-policy/update-effect.png)
1. **[確認と作成]** を選択して、ポリシーを送信します。

`kubectl get constrainttemplates` を実行して、ポリシーがクラスターに適用されていることを確認します。

> [!NOTE]
> ポリシーが各クラスターに[同期されるまでに最大 20 分](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition)かかる場合があります。

出力は次のようになります。

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>特権のあるポッドの拒否を検証する

まず、`privileged: true` のセキュリティ コンテキストを持つポッドをスケジュールしたときに何が起こるかをテストしてみましょう。 このセキュリティ コンテキストは、ポッドの特権をエスカレートします。 ベースライン イニシアチブでは特権のあるポッドが禁止されているため、要求は拒否されます。その結果、デプロイが拒否されます。

`nginx-privileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx
      securityContext:
        privileged: true
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストの名を指定します。

```console
kubectl apply -f nginx-privileged.yaml
```

次の出力例に示すように、このポッドは想定どおりスケジュールできません。

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

ポッドはスケジューリング段階に達しないため、続行する前に削除するリソースはありません。

## <a name="test-creation-of-an-unprivileged-pod"></a>特権のないポッドの作成をテストする

前の例では、コンテナー イメージは、自動的に root を使用して NGINX をポート 80 にバインドしようとしました。 この要求はベースライン ポリシー イニシアチブによって拒否されたため、ポッドを開始できません。 次に、特権アクセスなしで同じ NGINX ポッドを実行してみましょう。

`nginx-unprivileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx
```

[kubectl apply][kubectl-apply] コマンドを使用してポッドを作成し、YAML マニフェストのファイル名を指定します。

```console
kubectl apply -f nginx-unprivileged.yaml
```

ポッドが正常にスケジュールされます。 [kubectl get pods][kubectl-get] コマンドを使用してポッドの状態を確認すると、ポッドは *Running* 状態です。

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

この例は、コレクション内のポリシーに違反するデプロイのみに影響するベースライン イニシアチブを示しています。 許可されたデプロイは引き続き機能します。

[kubectl delete][kubectl-delete] コマンドを使用して、特権のない NGINX ポッドを削除し、YAML マニフェストの名前を指定します。

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>ポリシーと Azure Policy アドオンを無効にする

ベースライン イニシアチブを削除するには

1. Azure portal のポリシー ウィンドウに移動します。
1. 左側のウィンドウの **[割り当て]** を選択します。
1. ベースライン プロファイルの横にある [...] ボタンをクリックします。
1. [割り当ての削除] を選択します。

![割り当ての削除](media/use-pod-security-on-azure-policy/delete-assignment.png)

Azure Policy アドオンを無効にするには、[az aks disable-addons][az-aks-disable-addons] コマンドを使用します。

```azure-cli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Azure portal から Azure Policy アドオンを削除する方法については、[こちら](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks)をご覧ください。

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Kubernetes ポッド セキュリティ ポリシーから Azure Policy への移行

ポッドのセキュリティ ポリシーから移行するには、クラスターで次の操作を行う必要があります。

1. クラスターで[ポッドのセキュリティ ポリシーを無効にします](use-pod-security-policies.md#clean-up-resources)。
1. [Azure Policy アドオン][kubernetes-policy-reference]を有効にします。
1. [使用可能な組み込みポリシー][policy-samples]から目的の Azure Policy を有効にします。

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

## <a name="next-steps"></a>次のステップ

この記事では、特権アクセスが使用されないように特権ポッドのデプロイを制限する Azure ポリシーの適用方法について説明しました。 適用できるポリシーは多数あります。たとえば、ボリュームの使用を制限するポリシーなどです。 利用可能なオプションの詳細については、[Kubernetes 用 Azure Policy の参照ドキュメント][kubernetes-policy-reference]をご覧ください。

ポッド ネットワーク トラフィックの制限の詳細については、「[Azure Kubernetes Service (AKS) のネットワーク ポリシーを使用したポッド間のトラフィックの保護][network-policies]」を参照してください。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: policy-samples.md#microsoftcontainerservice
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
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete
