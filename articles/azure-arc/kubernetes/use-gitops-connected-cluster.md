---
title: Arc 対応 Kubernetes クラスターに対して GitOps を使用して構成をデプロイする (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 対応のクラスター構成に対して GitOps を使用する (プレビュー)
keywords: GitOps、Kubernetes、K8s、Azure、Arc、Azure Kubernetes Service、コンテナー
ms.openlocfilehash: e25fdf3a51b3e9264c85707df31d3a4d107b25ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87049965"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Arc 対応 Kubernetes クラスターに対して GitOps を使用して構成をデプロイする (プレビュー)

GitOps では、Git リポジトリ内で Kubernetes 構成 (デプロイ、名前空間など) の望ましい状態を宣言した後、ポーリングしたり、演算子を使用してこれらの構成をクラスターにプルベースで展開したりします。 このドキュメントでは、Azure Arc 対応 Kubernetes クラスターでのこのようなワークフローのセットアップについて説明します。

クラスターと 1 つ以上の Git リポジトリの間の接続は、Azure Resource Manager で `sourceControlConfiguration` 拡張リソースとして追跡されます。 `sourceControlConfiguration` リソースのプロパティは、Kubernetes リソースが Git からお客様のクラスターへとフローする場所と方法を表します。 `sourceControlConfiguration` データの保存中は Azure Cosmos DB データベースに暗号化された状態で格納されるので、データの機密性が確保されます。

クラスターで実行されている `config-agent` は、Azure Arc 対応 Kubernetes リソースで新規または更新された `sourceControlConfiguration` の拡張リソースを監視し、Git リポジトリを監視するための Flux 演算子をデプロイし、`sourceControlConfiguration` に加えられた更新を伝達する役割を担います。 複数の `sourceControlConfiguration` リソースを同じ Azure Arc 対応 Kubernetes クラスター上の `namespace` スコープで作成し、マルチテナントを実現することもできます。 このような場合、各演算子はそれぞれの名前空間にのみ構成をデプロイできます。

この Git リポジトリには、Namespace、ConfigMap、Deployment、DaemonSet などの有効な Kubernetes リソースを含めることができます。また、アプリケーションをデプロイするための Helm グラフを含めることもできます。 一般的なシナリオ セットとして、組織のベースライン構成の定義が含まれます。これには、一般的な RBAC ロールとバインド、監視エージェントまたはログ エージェント、またはクラスター全体のサービスが含まれる場合があります。

同じパターンを使用して、異機種混合の環境全体にデプロイされているような、より大規模なクラスターのコレクションを管理できます。 たとえば、組織のベースライン構成を定義するリポジトリが 1 つあり、それを一度に数十の Kubernetes クラスターに適用しているとします。 [Azure Policy では、スコープ (サブスクリプションまたはリソース グループ) にあるすべての Azure Arc 対応 Kubernetes リソースで、特定のパラメーター セットを使用して `sourceControlConfiguration` の作成を自動化](use-azure-policy.md)できます。

このファースト ステップ ガイドでは、クラスターと管理者のスコープを使用して一連の構成を適用する方法について説明します。

## <a name="create-a-configuration"></a>構成を作成する

- リポジトリの例: <https://github.com/Azure/arc-k8s-demo>

このリポジトリの例は、いくつかの名前空間をプロビジョニングし、共通のワークロードをデプロイし、チーム固有の構成を提供するクラスター オペレーターのペルソナを中心に構成されています。 このリポジトリを使用すると、クラスターに次のリソースが作成されます。

**Namespace:** `cluster-config`、`team-a`、`team-b`
**Deployment:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

`config-agent` は、新規または更新された `sourceControlConfiguration` を 30 秒ごと (`config-agent` が新規または更新された構成を取得するためにかかる最大時間) に Azure でポーリングします。
`sourceControlConfiguration` を持つプライベート リポジトリを関連付ける場合は、[プライベート git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)手順も完了するようにします。

### <a name="using-azure-cli"></a>Azure CLI の使用

`k8sconfiguration` の Azure CLI 拡張機能を使用して、接続されたクラスターを [Git リポジトリの例](https://github.com/Azure/arc-k8s-demo)にリンクしましょう。 この構成に `cluster-config` と名前を付け、`cluster-config` 名前空間にオペレーターを配置するようにエージェントに指示し、オペレーターに `cluster-admin` アクセス許可を与えます。

```console
az k8sconfiguration create \
    --name cluster-config \
    --cluster-name AzureArcTest1 --resource-group AzureArcTest \
    --operator-instance-name cluster-config --operator-namespace cluster-config \
    --repository-url https://github.com/Azure/arc-k8s-demo \
    --scope cluster --cluster-type connectedClusters
```

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>repository-url パラメーター

次に --repository-url パラメーターの値についてサポートされているシナリオを示します。

| シナリオ | Format | 説明 |
| ------------- | ------------- | ------------- |
| プライベート GitHub リポジトリ - SSH | git@github.com:username/repo | Flux によって生成される SSH キーペア。  ユーザーは、公開キーをデプロイ キーとして GitHub アカウントに追加する必要があります。 |
| パブリック GitHub リポジトリ | `http://github.com/username/repo` または git://github.com/username/repo   | パブリック Git リポジトリ  |

これらのシナリオは Flux でサポートされていますが、sourceControlConfiguration ではまだサポートされていません。 

| シナリオ | Format | 説明 |
| ------------- | ------------- | ------------- |
| プライベート GitHub リポジトリ - HTTPS | `https://github.com/username/repo` | Flux では SSH キーペアが生成されません。  [手順](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| プライベート Git ホスト | user@githost:path/to/repo | [手順](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| プライベート GitHub リポジトリ -SSH (Bring Your Own Key) | git@github.com:username/repo | [自分の SSH キーペアを使用する](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>追加のパラメーター

構成の作成をカスタマイズするために、いくつかの追加パラメーターを次に示します。

`--enable-helm-operator`:*省略可能*: Helm グラフの配置のサポートを有効にするように切り替えます。

`--helm-operator-chart-values`:*省略可能*: Helm 演算子 (有効な場合) のグラフの値。  たとえば、'--set helm.versions=v3' です。

`--helm-operator-chart-version`:*省略可能*: Helm 演算子 (有効な場合) のグラフのバージョン。 既定値は'0.6.0'。

`--operator-namespace`:*省略可能*: オペレーターの名前空間の名前。 既定値: 'default'

`--operator-params`:*省略可能*: オペレーターのパラメーター。 単一引用符で囲む必要があります。 たとえば、```--operator-params='--git-readonly --git-path=releases' ``` のように指定します。

--operator-params でサポートされているオプション

| オプション | 説明 |
| ------------- | ------------- |
| --git-branch  | Kubernetes マニフェストに使用する git リポジトリのブランチ。 既定値は 'master' です。 |
| --git-path  | Flux から Kubernetes マニフェストを特定するための Git リポジトリ内の相対パス。 |
| --git-readonly | Git リポジトリは読み取り専用と見なされます。Flux では書き込みが試行されなくなります。 |
| --manifest-generation  | 有効にすると、Flux によって .flux.yaml が検索され、Kustomize または他のマニフェスト ジェネレーターが実行されます。 |
| --git-poll-interval  | 新しいコミットに対して Git リポジトリをポーリングする期間。 既定は '5m' (5 分) です。 |
| --sync-garbage-collection  | 有効にすると、Flux によって作成されたリソースは削除されますが、Git には存在しなくなります。 |
| --git-label  | 同期の進行状況を追跡するためのラベル。Git ブランチのタグ付けに使用されます。  既定値は 'flux-sync' です。 |
| --git-user  | git コミットのユーザー名。 |
| --git-email  | git コミットに使用するメール アドレス。 |

* '--git-user' または '--git-email' が設定されていない場合 (つまり、Flux を使用してリポジトリに書き込みたくない場合)、--git-readonly が自動的に設定されます (まだ設定していない場合)。

* enableHelmOperator が true の場合、operatorInstanceName + operatorNamespace の文字列の合計を 47 文字以下にする必要があります。  この制限に従わないと、次のエラーが発生します。

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

詳細については、[Flux のドキュメント](https://aka.ms/FluxcdReadme)を参照してください。

> [!TIP]
> sourceControlConfiguration は、Azure portal の Azure Arc 対応 Kubernetes リソース ブレードの **[構成]** タブで作成することもできます。

## <a name="validate-the-sourcecontrolconfiguration"></a>sourceControlConfiguration を確認する

Azure CLI を使用して、`sourceControlConfiguration` の作成が成功したことを確認します。

```console
az k8sconfiguration show --resource-group AzureArcTest --name cluster-config --cluster-name AzureArcTest1 --cluster-type connectedClusters
```

`sourceControlConfiguration` リソースは、コンプライアンスの状態、メッセージ、デバッグの情報によって更新されることにご注意ください。

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

`sourceControlConfiguration` が作成されると、内部でいくつかの処理が行われます。

1. Azure Arc `config-agent` では、Azure Resource Manager の新規または更新された構成 (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`) が監視されます
1. `config-agent` を使用すると、新しい`Pending` 構成が通知されます
1. `config-agent` を使用すると、構成プロパティが読み取られ、`flux` のマネージド インスタンスのデプロイが準備されます
    * `config-agent` を使用すると、ターゲットの名前空間が作成されます
    * `config-agent` を使用すると、適切なアクセス許可 (`cluster` または `namespace` スコープ) を持つ Kubernetes サービス アカウントが準備されます
    * `config-agent` を使用すると、`flux` のインスタンスがデプロイされます
    * `flux` を使用すると、SSH キーが生成され、公開キーがログに記録されます
1. `config-agent` を使用すると、状態が `sourceControlConfiguration` に報告されます

プロビジョニング プロセスが実行されている間、`sourceControlConfiguration` はいくつかの状態に遷移します。 上記の `az k8sconfiguration show ...` のコマンドを使用して進行状況を監視します。

1. `complianceStatus` -> `Pending`: 初期状態と進行中の状態を表します。
1. `complianceStatus` -> `Installed`: `config-agent` によってクラスターが正常に構成され、エラーなしで `flux` がデプロイされました
1. `complianceStatus` -> `Failed`: `config-agent` で `flux` のデプロイ中にエラーが発生しました。詳細は `complianceStatus.message` 応答本文で確認できます

## <a name="apply-configuration-from-a-private-git-repository"></a>プライベート git リポジトリから構成を適用する

プライベート git リポジトリを使用している場合、ループを閉じるためにもう 1 つのタスクを実行する必要があります。リポジトリで、`flux` によって生成された公開キーを**デプロイ キー**として追加します。

**Azure CLI を使用して公開キーを取得する**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Azure portal から公開キーを取得する**

1. Azure portal で、接続されているクラスター リソースに移動します。
2. [リソース] ページで [構成] を選択し、このクラスターの構成の一覧を表示します。
3. プライベート Git リポジトリを使用する構成を選択します。
4. 開いたコンテキスト ウィンドウで、ウィンドウの下部にある **[リポジトリの公開キー]** をコピーします。

**公開キーをデプロイ キーとして git リポジトリに追加する**

1. GitHub を開き、フォークに移動して、 **[設定]** 、 **[Deploy keys]\(デプロイ キー\)** の順に移動します
2. **[Add deploy key]\(デプロイ キーの追加\)** をクリックします
3. [タイトル] を入力します
4. **[Allow write access]\(書き込みアクセスを許可する\)** をオンにします
5. 公開キーを貼り付けます (囲んでいる引用符は除きます)
6. **[Add key]\(キーの追加\)** をクリックします

これらのキーを管理する方法の詳細については、GitHub のドキュメントを参照してください。

**Azure DevOps リポジトリを使用している場合は SSH キーにキーを追加する**

1. 右上の (プロファイル画像の横にある) **[ユーザー設定]** で、 **[SSH public keys]\(SSH 公開キー\)** をクリックします
1. **[+ New Key]\(+ 新しいキー\)** を選択します
1. 名前を入力します
1. 囲んでいる引用符を含めずに公開キーを貼り付けます
1. **[追加]** をクリックします。

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes の構成を確認する

`config-agent` で `flux` インスタンスがインストールされた後、git リポジトリに保持されているリソースがクラスターにフローし始めます。 名前空間、デプロイ、およびリソースが作成されていることを確認します。

```console
kubectl get ns --show-labels
```

**出力:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

`team-a`、`team-b`、`itops`、および`cluster-config` の名前空間が作成されていることがわかります。

`sourceControlConfig` の指示に従って、`flux` 演算子が `cluster-config` 名前空間にデプロイされました。

```console
kubectl -n cluster-config get deploy  -o wide
```

**出力:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>さらに探索する

構成リポジトリの一部としてデプロイされた他のリソースを探索することができます。

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>構成を削除する

`sourceControlConfiguration` を削除するには、Azure CLI または Azure portal を使用します。  delete コマンドを開始すると、Azure の `sourceControlConfiguration` リソースはすぐに削除されますが、クラスターから関連オブジェクトが完全に削除されるまでに最大 1 時間かかることがあります (このタイムラグを短縮するためにバックログ項目が用意されています)。

> [!NOTE]
> 名前空間スコープを持つ sourceControlConfiguration を作成した後、名前空間で `edit` ロールがバインドされているユーザーは、この名前空間にワークロードをデプロイできます。 名前空間スコープを持つこの `sourceControlConfiguration` が削除されると、その名前空間はそのまま残り、他のワークロードが中断されることを防ぐために削除されません。
> `sourceControlConfiguration` が削除されても、追跡対象の git リポジトリからのデプロイの結果としてクラスターに加えられた変更は削除されません。

```console
az k8sconfiguration delete --name '<config name>' -g '<resource group name>' --cluster-name '<cluster name>' --cluster-type connectedClusters
```

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>次のステップ

- [ソース管理の構成で Helm を使用する](./use-gitops-with-helm.md)
- [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)
