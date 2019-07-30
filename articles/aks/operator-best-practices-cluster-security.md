---
title: オペレーターのベスト プラクティス - Azure Kubernetes Service (AKS) でのクラスターのセキュリティ
description: Azure Kubernetes Service (AKS) でクラスターのセキュリティとアップグレードを管理する方法に関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: d4a77fc1756b0fa9decb6d3a84760beb1e700863
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614890"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのクラスターのセキュリティとアップグレードに関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際には、ワークロードとデータのセキュリティが重要な考慮事項になります。 特に、論理的な分離を使用してマルチテナント クラスターを実行する場合は、リソースとワークロードへのアクセスをセキュリティで保護する必要があります。 攻撃のリスクを最小限に抑えるには、最新の Kubernetes とノード OS のセキュリティ更新プログラムも適用する必要があります。

この記事では、AKS クラスターをセキュリティで保護する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory とロールベースのアクセス制御を使用して API サーバー アクセスをセキュリティで保護する
> * ノード リソースへのコンテナー アクセスをセキュリティで保護する
> * AKS クラスターを最新の Kubernetes バージョンにアップグレードする
> * ノードを最新の状態に保ち、セキュリティ パッチを自動的に適用する

また、[コンテナー イメージの管理][best-practices-container-image-management]と and for [pod security][best-practices-pod-security]に関するベスト プラクティスを参照できます。

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>API サーバーとクラスター ノードへのアクセスをセキュリティで保護する

**ベスト プラクティス ガイダンス** - Kubernetes API-Server へのアクセスをセキュリティで保護することは、クラスターをセキュリティで保護するためにできる最も重要な方法の 1 つです。 Kubernetes のロールベース アクセス制御 (RBAC) を Azure Active Directory と統合して、API サーバーへのアクセスを制御します。 このようなコントロールを使用すると、Azure サブスクリプションへのアクセスをセキュリティで保護する場合と同じ方法で AKS をセキュリティで保護できます。

Kubernetes API サーバーには、クラスター内でアクションを実行する要求向けに単一の接続ポイントが用意されています。 API サーバーへのアクセスをセキュリティで保護および監査するには、アクセスを制限し、特権を必要最小限に抑えたアクセス許可を付与します。 このアプローチは Kubernetes に固有のものではありませんが、マルチテナントに使用するために AKS クラスターが論理的に分離されている場合は特に重要です。

Azure Active Directory (AD) には、AKS クラスターと統合される企業対応の ID 管理ソリューションが用意されています。 Kubernetes には ID 管理ソリューションが用意されていないので、本来は API サーバーへのアクセスを細かく制限することが困難な可能性があります。 AKS の Azure AD と統合されたクラスターでは、既存のユーザー アカウントとグループ アカウントを使用して API サーバーに対してユーザーを認証します。

![AKS クラスター用の Azure Active Directory 統合](media/operator-best-practices-cluster-security/aad-integration.png)

Kubernetes RBAC と Azure AD 統合を使用して API サーバーをセキュリティで保護し、単一の名前空間など、範囲を指定した一連のリソースに必要な最小限のアクセス許可を付与します。 Azure AD のさまざまなユーザーまたはグループに、さまざまな RBAC のロールを付与することができます。 このような細かいアクセス許可を使用することで、API サーバーへのアクセスを制限し、実行されたアクションの明確な監査証跡を提供することができます。

推奨されるベスト プラクティスとして、個々の ID ではなく、グループを使用してファイルとフォルダーへのアクセス権を付与し、個々の "*ユーザー*" ではなく Azure AD の "*グループ*" メンバーシップを使用してユーザーを RBAC ロールにバインドします。 ユーザーのグループ メンバーシップが変わると、それに応じて AKS クラスターに対するアクセス許可も変わります。 ユーザーをロールに直接バインドすると、その職務が変わる可能性があります。 Azure AD グループのメンバーシップが更新されても、AKS クラスターに対するアクセス許可にはそれが反映されません。 このシナリオでは、最終的に、ユーザーに必要なアクセス許可よりも多くのアクセス許可が付与されることになります。

Azure AD 統合と RBAC の詳細については、[AKS の認証と承認のベスト プラクティス][aks-best-practices-identity]に関する記事を参照してください。

## <a name="secure-container-access-to-resources"></a>リソースへのコンテナー アクセスをセキュリティで保護する

**ベスト プラクティス ガイダンス** - コンテナーで実行できるアクションへのアクセスを制限します。 最小限のアクセス許可を付与し、ルート/特権エスカレーションの使用を避けます。

ユーザーまたはグループに必要最小限の特権を付与する場合と同様に、コンテナーも必要なアクションとプロセスのみに制限するようにします。 攻撃のリスクを最小限に抑えるには、上位の特権やルート アクセスが必要なアプリケーションやコンテナーを構成しないでください。 たとえば、ポッドのマニフェストには `allowPrivilegeEscalation: false` を設定します。 このような "*ポッドのセキュリティ コンテキスト*" は Kubernetes に組み込まれているので、実行するユーザーやグループ、公開する Linux 機能など、追加のアクセス許可を定義できます。 その他のベスト プラクティスについては、[リソースへのポッドのアクセスをセキュリティで保護する方法][pod-security-contexts]に関する記事を参照してください。

コンテナー アクションをより細かく制御するには、*AppArmor* や *seccomp* など、組み込みの Linux セキュリティ機能を使用することもできます。 このような機能はノード レベルで定義されてから、ポッド マニフェストを介して実装されます。 組み込みの Linux セキュリティ機能は、Linux ノードとポッドに対してのみ使用できます。

> [!NOTE]
> AKS などでは、Kubernetes 環境は、悪意のあるマルチテナント使用に対しては完全に安全ではありません。 *AppArmor*、*seccomp*、*Pod Security Policy* などの他のセキュリティ機能やノードに対するきめの細かいロールベースのアクセス制御 (RBAC) によって、悪用しにくくします。 ただし、悪意のあるマルチテナント ワークロードの実行に対して真のセキュリティを実現するために信頼できる唯一のセキュリティ レベルはハイパーバイザーです。 Kubernetes 用のセキュリティ ドメインは、個々のノードではなく、クラスター全体になります。 この種の悪意のあるマルチテナント ワークロードでは、物理的に分離されたクラスターを使用する必要があります。

### <a name="app-armor"></a>App Armor

コンテナーが実行できるアクションを制限するには、[AppArmor][k8s-apparmor] Linux カーネル セキュリティ モジュールを使用できます。 AppArmor は基となる AKS ノード OS に含まれており、既定で有効です。 読み取り、書き込み、実行などのアクション、またはファイルシステムのマウントなどのシステム機能を制限する AppArmor プロファイルを作成します。 既定の AppArmor プロファイルでは、さまざまな `/proc` と `/sys` の場所へのアクセスが制限されており、基となるノードからコンテナーを論理的に分離する手段が用意されています。 AppArmor は、Kubernetes ポッドだけでなく、Linux 上で動作するあらゆるアプリケーションに対応しています。

![コンテナーの動作を制限するために AKS クラスターで使用されている AppArmor プロファイル](media/operator-best-practices-container-security/apparmor.png)

AppArmor の動作を確認するために、次の例ではファイルへの書き込みを防止するプロファイルを作成します。 [SSH][aks-ssh] で AKS ノードに接続し、*deny-write.profile* という名前のファイルを作成し、次の内容を貼り付けます。

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

`apparmor_parser` コマンドを使用して AppArmor プロファイルが追加されます。 プロファイルを AppArmor に追加し、前の手順で作成したプロファイルの名前を指定します。

```console
sudo apparmor_parser deny-write.profile
```

プロファイルが正しく解析され、AppArmor に適用されている場合、出力は返されません。 コマンド プロンプトに戻ります。

ローカル コンピューターから、*aks-apparmor.yaml* という名前のポッド マニフェストを作成し、次の内容を貼り付けます。 このマニフェストでは、`container.apparmor.security.beta.kubernetes` の注釈が定義され、前の手順で作成した *deny-write* プロファイルが参照されています。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

[kubectl apply][kubectl-apply] コマンドを使用してサンプル ポッドを展開します。

```console
kubectl apply -f aks-apparmor.yaml
```

ポッドが展開されたら、[kubectl exec][kubectl-exec] コマンドを使用してファイルに書き込みます。 次の出力例に示すように、このコマンドは実行できません。

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

AppArmor の詳細については、[Kubernetes の AppArmor プロファイル][k8s-apparmor]に関する記事を参照してください。

### <a name="secure-computing"></a>セキュア コンピューティング

AppArmor は任意の Linux アプリケーションで機能しますが、[seccomp (*sec*ure *comp*uting)][seccomp] はプロセス レベルで機能します。 seccomp は Linux カーネル セキュリティ モジュールでもあり、AKS ノードで使用される Docker ランタイムでネイティブにサポートされています。 seccomp では、コンテナーが実行できるプロセス呼び出しは制限されています。 許可または拒否するアクションを定義するフィルターを作成してから、ポッド YAML マニフェスト内の注釈を使用して seccomp フィルターに関連付けます。 これは、実行に必要な最小限のアクセス許可のみをコンテナーに付与するというベスト プラクティスと合っています。

seccomp の動作を確認するには、ファイルに対するアクセス許可の変更を防止するフィルターを作成します。 [SSH][aks-ssh] で AKS ノードに接続し、 */var/lib/kubelet/seccomp/prevent-chmod* という名前の seccomp フィルターを作成し、次の内容を貼り付けます。

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

ローカル コンピューターから、*aks-seccomp.yaml* という名前のポッド マニフェストを作成し、次の内容を貼り付けます。 このマニフェストでは、`seccomp.security.alpha.kubernetes.io` の注釈が定義され、前の手順で作成した *prevent-chmod* フィルターが参照されています。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

[kubectl apply][kubectl-apply] コマンドを使用してサンプル ポッドを展開します。

```console
kubectl apply -f ./aks-seccomp.yaml
```

[kubectl get pods][kubectl-get] コマンドを使用して、ポッドの状態を確認します。 ポッドからはエラーが報告されています。 次の出力例に示すように、`chmod` コマンドは seccomp フィルターによって実行されません。

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

使用できるフィルターの詳細については、「[Seccomp security profiles for Docker (Docker の Seccomp セキュリティ プロファイル)][seccomp]」を参照してください。

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>最新版の Kubernetes に定期的に更新する

**ベスト プラクティス ガイダンス** - 新機能とバグ修正を常に最新の状態に保つには、AKS クラスターの Kubernetes バージョンを定期的にアップグレードしてください。

Kubernetes は、従来のインフラストラクチャ プラットフォームよりも速いペースで新機能をリリースしています。 Kubernetes の更新プログラムには、新機能、バグやセキュリティの修正が含まれています。 通常、新機能は "*アルファ版*"、"*ベータ版*" の状態を経てから、"*安定版*" になり、一般公開され、運用環境での使用が推奨されるようになります。 このリリース サイクルであれば、定期的に重大な変更が発生したり、展開やテンプレートを調整したりすることなく、Kubernetes を更新できます。

AKS では、Kubernetes の 4 つのマイナー バージョンがサポートされています。 つまり、パッチの新しいマイナー バージョンが導入されると、サポートされている最も古いマイナー バージョンとパッチのリリースは、提供終了となります。 Kubernetes のマイナー更新は定期的に行われています。 サポート対象外にならないように、必要に応じて確認してアップグレードするガバナンス プロセスを用意してください。 詳細については、[AKS でサポートされる Kubernetes のバージョン][aks-supported-versions]に関する記事を参照してください。

実際のクラスターに使用できるバージョンを確認するには、次の例に示すように [az aks get-upgrades][az-aks-get-upgrades] コマンドを使用します。

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

次に、[az aks upgrade][az-aks-upgrade] コマンドを使用して AKS クラスターをアップグレードすることができます。 このアップグレード プロセスでは、ノードの遮断と解放を一度に 1 つずつ安全に実行し、残りのノード上のポッドをスケジュールに設定してから、最新の OS および Kubernetes バージョンを実行している新しいノードを展開します。

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.8
```

AKS のアップグレードの詳細については、[AKS でサポートされる Kubernetes のバージョン][aks-supported-versions]と and [Upgrade an AKS cluster][aks-upgrade]に関する記事を参照してください。

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>kured を使用して Linux ノードの更新と再起動を処理する

**ベスト プラクティス ガイダンス** - AKS では、各 Linux ノードへのセキュリティ パッチのダウンロードとインストールが自動的に行われますが、必要な場合に再起動が自動的に実行されることはありません。 `kured` を使用して保留中の再起動を確認してから、ノードの遮断と解放を安全に実行し、ノードを再起動し、更新プログラムを適用して、OS に関してできる限りセキュリティで保護されるようにします。 Windows Server ノード (現在 AKS でプレビュー段階) では、ポッドを安全に切断およびドレインし、更新されたノードをデプロイするために、AKS のアップグレード操作を定期的に実行します。

AKS の Linux ノードでは、毎晩、ディストリビューション更新チャネルを介してセキュリティ修正プログラムを入手できます。 この動作は、ノードが AKS クラスターに展開されるときに自動的に構成されます。 中断や実行中のワークロードへの潜在的な影響を最小限に抑えるために、セキュリティ修正プログラムまたはカーネルの更新プログラムに必要な場合でも、ノードは自動的に再起動されません。

Weaveworks による [kured (KUbernetes REboot Daemon)][kured] プロジェクトでは、保留中のノードの再起動が監視されます。 再起動が必要な更新プログラムが Linux ノードに適用されると、そのノードの遮断と解放が安全に実行され、クラスター内の他のノード上のポッドの移動とスケジュール設定が行われます。 ノードが再起動されると、そのノードはクラスターに追加され、Kubernetes によってそのノード上でのポッドのスケジュール設定が再開されます。 中断を最小限に抑えるために、`kured` では一度に 1 つのノードのみを再起動できます。

![kured を使用した AKS ノードの再起動プロセス](media/operator-best-practices-cluster-security/node-reboot-process.png)

再起動が発生するタイミングを細かく制御する場合は、他のメンテナンス イベントやクラスターの問題が発生しているときに再起動しないように、`kured` を Prometheus と統合する方法があります。 この統合により、他の問題を解決している間にノードを再起動することで増える複雑さを最小限に抑えることができます。

ノードの再起動を処理する方法については、[AKS のノードにセキュリティとカーネルの更新プログラムを適用する方法][aks-kured]に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、AKS クラスターをセキュリティで保護する方法について説明しました。 これらの領域のいくつかを実装する場合は、次の記事を参照してください。

* [Azure Active Directory と AKS の統合][aks-aad]
* [AKS クラスターを最新版の Kubernetes にアップグレードする][aks-upgrade]
* [kured を使用してセキュリティ更新プログラムとノードの再起動を処理する][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
