---
title: Azure Policy を使用してクラスターをセキュリティで保護する
description: Azure Policy を使用して、Azure Kubernetes Service (AKS) クラスターをセキュリティで保護します。
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193337"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Azure Policy でクラスターをセキュリティで保護する

Azure Kubernetes Service (AKS) クラスターのセキュリティを強化するために、Azure Policy を使用して、クラスターに組み込みのセキュリティ ポリシーを適用することができます。 [Azure Policy][azure-policy] は、組織の標準を適用し、コンプライアンスを大規模に評価するのに役立ちます。 [AKS 用の Azure Policy アドオン][kubernetes-policy-reference]をインストールした後、個々のポリシー定義またはイニシアチブ (policysets と呼ばれることもあります) と呼ばれるポリシー定義のグループをクラスターに適用できます。 AKS ポリシーの完全な一覧とイニシアチブの定義については、[AKS 用の Azure Policy 組み込み定義][aks-policies]に関するページを参照してください。

この記事では、ポリシー定義をクラスターに適用し、それらの割り当てが適用されていることを確認する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 既存の AKS クラスター。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。
- AKS クラスターにインストール済みの AKS 用の Azure Policy アドオン。 [こちら][azure-policy-addon]の手順に従って、Azure Policy アドオンをインストールします。

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>組み込みポリシー定義またはイニシアチブの割り当て

ポリシー定義またはイニシアチブを適用するには、Azure portal を使用します。

1. Azure portal で Azure Policy サービスに移動します。
1. Azure Policy ページの左側のウィンドウで、 **[定義]** を選択します。
1. **[カテゴリ]** で `Kubernetes` を選択します。
1. 適用するポリシー定義またはイニシアチブを選択します。 この例では `Kubernetes cluster pod security baseline standards for Linux-based workloads` イニシアチブを選択します。
1. **[割り当て]** を選択します。
1. **[スコープ]** には、Azure Policy アドオンが有効な AKS クラスターのリソース グループを設定します。
1. **[パラメーター]** ページを選択し、 **[効果]** を `audit` から `deny` に更新して、ベースライン イニシアチブに違反している新しいデプロイをブロックします。 評価から除外する名前空間を追加することもできます。 この例では、規定値のままにしておきます。
1. **[確認と作成]** 、 **[作成]** の順に選択して、ポリシーの割り当てを送信します。

## <a name="validate-a-azure-policy-is-running"></a>Azure Policy が実行されていることを検証する

次を実行して、ポリシーの割り当てがクラスターに適用されていることを確認します。

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> ポリシーの割り当てが各クラスターに[同期されるまでに最大 20 分][azure-policy-assign-policy]かかる場合があります。

出力は次のようになります。

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>特権のあるポッドの拒否を検証する

まず、`privileged: true` のセキュリティ コンテキストを持つポッドをスケジュールしたときに何が起こるかをテストしてみましょう。 このセキュリティ コンテキストは、ポッドの特権をエスカレートします。 イニシアチブでは特権のあるポッドが禁止されているため、要求は拒否されます。その結果、デプロイが拒否されます。

`nginx-privileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

### <a name="test-creation-of-an-unprivileged-pod"></a>特権のないポッドの作成をテストする

前の例では、コンテナー イメージは、自動的に root を使用して NGINX をポート 80 にバインドしようとしました。 この要求はポリシー イニシアチブによって拒否されたため、ポッドを開始できません。 次に、特権アクセスなしで同じ NGINX ポッドを実行してみましょう。

`nginx-unprivileged.yaml` という名前のファイルを作成し、次の YAML マニフェストを貼り付けます。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

## <a name="disable-a-policy-or-initiative"></a>ポリシーまたはイニシアチブを無効にする

ベースライン イニシアチブを削除するには

1. Azure portal のポリシー ウィンドウに移動します。
1. 左側のウィンドウの **[割り当て]** を選択します。
1. `Kubernetes cluster pod security baseline standards for Linux-based workloads` イニシアチブの横にある **[...]** ボタンをクリックします。
1. **[割り当ての削除]** を選択します。

## <a name="next-steps"></a>次のステップ

Azure Policy のしくみの詳細については、次を参照してください。

- [Azure Policy の概要][azure-policy]
- [AKS 用の Azure Policy イニシアチブとポリシー][aks-policies]
- [Azure Policy アドオン][azure-policy-addon-remove]を削除する。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
