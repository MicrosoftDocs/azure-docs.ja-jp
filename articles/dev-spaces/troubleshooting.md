---
title: トラブルシューティング
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: troubleshooting
description: Azure Dev Spaces を有効にして使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 14ced0c66b42b6f18c946d0c75091be1af5598f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102197794"
---
# <a name="azure-dev-spaces-troubleshooting"></a>Azure Dev Spaces のトラブルシューティング

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

このガイドでは、Azure Dev Spaces 使用時の一般的な問題についての情報を示します。

Azure Dev Spaces の使用時に問題が発生した場合は、[Azure Dev Spaces GitHub リポジトリに問題](https://github.com/Azure/dev-spaces/issues)を作成します。

## <a name="before-you-begin"></a>開始する前に

問題のトラブルシューティングをより効果的に行うには、レビュー用のより詳細なログを作成することが役に立つ可能性があります。

Visual Studio の場合、`MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` 環境変数を 1 に設定します。 環境変数を有効にするために Visual Studio を再起動してください。 有効になると、詳細なログが `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` ディレクトリに書き込まれます。

CLI では、`--verbose` スイッチを使用してコマンド実行中に詳細な情報を出力できます。 `%TEMP%\Azure Dev Spaces`でより詳しいログを参照することもできます。 Mac では、ターミナル ウィンドウから `echo $TMPDIR` を実行することで、*TEMP* ディレクトリを見つけることができます。 Linux コンピューターでは、通常、*TEMP* ディレクトリは `/tmp` です。 また、[Azure CLI 構成ファイル](/cli/azure/azure-cli-configuration#cli-configuration-values-and-environment-variables)でログ記録が有効になっていることを確認します。

Azure Dev Spaces は、単一のインスタンス (ポッド) のデバッグでもきわめて効果的に機能します。 `azds.yaml` ファイルには、Kubernetes がサービスに対して実行するポッドの数を示す設定である *replicaCount* が含まれています。 *replicaCount* を変更して特定のサービスに対して複数のポッドを実行するようにアプリを設定すると、デバッガは (アルファベット順にリストされている場合) 最初のポッドに接続されます。 元のポッドがリサイクルされた場合、デバッガは別のポッドに接続されます。これにより、予期しない動作が発生する可能性があります。

## <a name="common-issues-when-enabling-azure-dev-spaces"></a>Azure Dev Spaces を有効にするときに発生する一般的な問題

### <a name="error-failed-to-create-azure-dev-spaces-controller"></a>エラー "Azure Dev Spaces コントローラーを作成できませんでした"

コントローラーの作成で問題が発生した場合に、このようなエラーが表示されることがあります。 これが一時的なエラーの場合は、コントローラーを削除して再作成することで修正されます。

コントローラーの削除を試みることもできます。

```bash
azds remove -g <resource group name> -n <cluster name>
```

コントローラーを削除するには、Azure Dev Spaces CLI を使用します。 Visual Studio からコントローラーを削除することはできません。 また、Azure Cloud Shell に Azure Dev Spaces CLI をインストールすることもできないため、Azure Cloud Shell からコントローラーを削除することはできません。

Azure Dev Spaces CLI がインストールされていない場合は、まず次のコマンドを使用してインストールしてからコントローラーを削除できます。

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

### <a name="controller-create-failing-because-of-controller-name-length"></a>コントローラー名の文字数が原因でコントローラーの作成に失敗する

Azure Dev Spaces コントローラー名の文字数は 31 文字までです。 AKS クラスターの Dev Spaces を有効にするとき、またはコントローラーを作成するときに、コントローラー名が 31 文字を超えているとエラーが発生します。 次に例を示します。

```console
Failed to create a Dev Spaces controller for cluster 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev Spaces Controller name 'a-controller-name-that-is-way-too-long-aks-east-us' is invalid. Constraint(s) violated: Azure Dev Spaces Controller names can only be at most 31 characters long*
```

この問題を解決するには、別の名前でコントローラーを作成します。 次に例を示します。

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

### <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>AKS クラスターに Windows ノード プールが追加されていると、Dev Spaces を有効にすることができません

現在のところ、Azure Dev Spaces は Linux のポッドとノードのみで実行するものです。 Windows ノード プールのある AKS クラスターがある場合は、Azure Dev Spaces ポッドが Linux ノードでのみスケジュールされていることを確認する必要があります。 Azure Dev Spaces ポッドが Windows ノードで実行されるようにスケジュールされている場合、そのポッドは開始されず、Dev Spaces の有効化は失敗します。

この問題を解決するには、AKS クラスターに[テイントを追加](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)して、Linux ポッドが Windows ノードで実行されるようにスケジュールされていないことを確認します。

### <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>エラー "クラスター上で準備完了状態のテイントされていない Linux ノードが見つかりませんでした。 "azds" 名前空間にポッドをデプロイするには、準備完了状態でテイントされていない Linux ノードが少なくとも 1 つ必要です。"

ポッドをスケジュールする *準備完了* 状態のテイントされていないノードが見つからなかったため、Azure Dev Spaces で AKS クラスターにコントローラーを作成できませんでした。 Azure Dev Spaces では、容認を指定せずにポッドをスケジュールするための、少なくとも 1 つの *準備完了* 状態の Linux ノードが必要です。

この問題を解決するには、少なくとも 1 つの Linux ノードで容認を指定せずに確実にポッドをスケジュールできるように、AKS クラスターで[テイント構成を更新](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations)します。 また、容認を指定せずポッドをスケジュールできる少なくとも 1 つの Linux ノードが、*準備完了* 状態であることも確認します。 ノードが *準備完了* 状態に達するのに時間がかかっている場合は、ノードの再起動を試みることができます。

### <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>az aks use-dev-spaces の実行時のエラー "Azure Dev Spaces CLI not installed properly (Azure Dev Spaces CLI が正しくインストールされていません)"

Azure Dev Spaces CLI に対する更新により、そのインストール パスが変更されました。 2\.0.63 より前のバージョンの Azure CLI を使用している場合は、このエラーが表示されることがあります。 Azure CLI のバージョンを表示するには、`az --version` を使用します。

```azurecli
az --version
```

```output
azure-cli                         2.0.60 *
...
```

2\.0.63 の前のバージョンの Azure CLI で `az aks use-dev-spaces` を実行している場合は、エラー メッセージに関係なくインストールは成功します。 引き続き問題なく `azds` を使用できます。

この問題を解決するには、[Azure CLI](/cli/azure/install-azure-cli) のインストールを 2.0.63 以降に更新します。 この更新により、`az aks use-dev-spaces` の実行中に受け取るエラー メッセージが解決されます。 また、引き続き現在のバージョンの Azure CLI と Azure Dev Spaces CLI を使用することもできます。

### <a name="error-unable-to-reach-kube-apiserver"></a>エラー"Unable to reach kube-apiserver (kube-apiserver に到達できません)"

Azure Dev Spaces が AKS クラスターの API サーバーに接続できない場合に、このエラーが表示されることがあります。

AKS クラスターの API サーバーへのアクセスがロック ダウンされている場合、または AKS クラスターに対して [API サーバーの許可された IP アドレス範囲](../aks/api-server-authorized-ip-ranges.md)が有効になっている場合は、[ご利用のリージョンに基づいて追加の範囲を許可する](configure-networking.md#aks-cluster-network-requirements)ために、クラスターを[作成](../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled)または[更新](../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges)する必要もあります。

kubectl コマンドを実行して、API サーバーが使用可能であることを確認してください。 API サーバーが使用できない場合は、AKS サポートに連絡し、API サーバーが動作しているときにもう一度試してください。

## <a name="common-issues-when-preparing-your-project-for-azure-dev-spaces"></a>Azure Dev Spaces のプロジェクトを準備するときに発生する一般的な問題

### <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>警告 "Dockerfile could not be generated due to unsupported language\(サポートされていない言語が原因で Dockerfile を生成できませんでした\)"
Azure Dev Spaces では、C# と Node.js がネイティブでサポートされます。 これらのいずれかの言語で記述されたコードが含まれているディレクトリで `azds prep` を実行すると、Azure Dev Spaces によって適切な Dockerfile が自動的に作成されます。

その他の言語で記述されたコードを Azure Dev Spaces で使用できますが、`azds up` の初回の実行前に、Dockerfile を手動で作成しておく必要があります。

Azure Dev Spaces でネイティブにサポートされる言語でアプリケーションが記述されていない場合、コードが実行されるコンテナー イメージをビルドするために適切な Dockerfile を設定する必要があります。 Docker では、[Dockerfile の記述に関するベスト プラクティスの一覧](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)と [Dockerfile リファレンス](https://docs.docker.com/engine/reference/builder/)を提供しています。これはご自身のニーズに合った Dockerfile を記述するために役立ちます。

適切な Dockerfile を用意できたら、`azds up` を実行して、Azure Dev Spaces でアプリケーションを実行します。

## <a name="common-issues-when-starting-or-stopping-services-with-azure-dev-spaces"></a>Azure Dev Spaces でサービスを開始または停止するときに発生する一般的な問題

### <a name="error-config-file-not-found"></a>エラー "構成ファイルが見つかりません:"

`azds up` を実行しているときに、このエラーが表示されることがあります。 `azds up` と `azds prep` はどちらも、Dev Spaces で実行するプロジェクトのルート ディレクトリから実行する必要があります。

この問題を解決するには、次の手順に従います。
1. サービス コードが含まれているルート フォルダーにカレント ディレクトリを変更します。 
1. _azds.yaml_ ファイルがコード フォルダーにない場合、`azds prep` を実行して Docker、Kubernetes、および Azure Dev Spaces アセットを生成します。

### <a name="timeout-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>AKS 仮想ノードの "Waiting for container image build... (コンテナー イメージがビルドされるのを待っています...)" 手順でタイムアウトが発生する

このタイムアウトは、Dev Spaces を使用して、[AKS 仮想ノード](../aks/virtual-nodes-portal.md)上で実行するように構成されているサービスを実行しようとした場合に発生します。 Dev Spaces では、現在、仮想ノード上でのサービスのビルドもデバッグもサポートされていません。

`azds up` を `--verbose` スイッチを指定して実行するか、Visual Studio で詳細ログを有効にした場合、次の詳細が表示されます。

```cmd
azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

上のコマンドは、サービスのポッドが、仮想ノードである *virtual-node-aci-linux* に割り当てられたことを示しています。

この問題を解決するには、サービス用の Helm チャートで、仮想ノード上でのサービスの実行を許可する *nodeSelector* 値と *tolerations* 値を削除して、チャートを更新します。 通常、これらの値は、チャートの `values.yaml` ファイルに定義されます。

Dev Spaces を介してビルドまたはデバッグするサービスを VM ノード上で実行したい場合は、仮想ノード機能が有効な AKS クラスターを引き続き使用できます。 VM ノード上で Dev Spaces を使用してサービスを実行するのが既定の構成です。

### <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>Dev Spaces 起動時の "Error: could not find a ready tiller pod" (エラー: 準備のできた Tiller ポッドが見つかりませんでした)

このエラーは、クラスターで実行されている Tiller ポッドに Helm クライアントが通信できなくなった場合に発生します。

この問題を解決するには、クラスター内のエージェント ノードを再起動します。

### <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>エラー: "リリース azds-\<identifier\>-\<spacename\>-\<servicename\> が失敗しました: サービス '\<servicename\>' は既に存在します" または "\<servicename\> に対するプル アクセスが拒否されました。リポジトリが存在しないか、"docker login" が必要な場合があります"

これらのエラーは、ダイレクト Helm コマンド (`helm install`、`helm upgrade`、`helm delete` など) と、Dev Spaces コマンド (`azds up`、`azds down` など) を同じ開発スペース内に混在させて実行した場合に、発生することがあります。 原因は、Dev Spaces に独自の Tiller インスタンスがあり、これが、同じ開発スペースで実行されているご自身の Tiller インスタンスと競合していることです。

Helm コマンドと Dev Spaces コマンドは両方とも同じ AKS クラスターに対して使用しても問題ありませんが、それぞれの Dev Spaces 対応名前空間で、いずれかが使用されていなければなりません。

たとえば、Helm コマンドを使用して、親開発スペースでご自身のアプリケーション全体を実行するとします。 その親から離れた場所に子開発スペースを作成し、Dev Spaces を使って子開発スペース内で個別のサービスを実行した後、そのサービスをまとめてテストできます。 ご自身の変更をチェックインする準備ができたら、Helm コマンドを使用して、更新されたコードを親開発スペースにデプロイします。 更新されたサービスを親開発スペースで実行するときに `azds up` は使用しないでください。これは、Helm を使用して最初にサービスで実行されたサービスと競合するためです。

### <a name="existing-dockerfile-not-used-to-build-a-container"></a>既存の Dockerfile がコンテナーのビルドに使用されない

プロジェクト内の特定の _Dockerfile_ を指示するように Azure Dev Spaces を構成できます。 Azure Dev Spaces で、コンテナーをビルドするために想定した _Dockerfile_ が使用されていないと思われる場合は、どの Dockerfile ファイルを使用するかを Azure Dev Spaces に明示的に通知する必要があります。 

この問題を解決するには、Azure Dev Spaces によってプロジェクト内に生成された _azds.yaml_ ファイルを開きます。 *configurations: develop: build: dockerfile* を更新して、使用する Dockerfile を指示します。 次に例を示します。

```yaml
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

### <a name="error-unauthorized-authentication-required-when-trying-to-use-a-docker-image-from-a-private-registry"></a>プライベート レジストリから Docker イメージを使用しようとすると、"unauthorized: authentication required" (未承認: 認証が必要) というエラーが発生する

認証を必要とするプライベート レジストリから Docker イメージを使用しています。

この問題を解決するには、[imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets) を使用して、このプライベート レジストリからイメージを認証およびプルすることを Dev Spaces に許可することができます。 imagePullSecrets を使用するには、イメージを使用する名前空間に [Kubernetes シークレットを作成](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)します。 次に、`azds.yaml` で imagePullSecret としてシークレットを指定します。

`azds.yaml` で imagePullSecrets を指定する例を次に示します。

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: $BUILD_CONTEXT$
  dockerfile: Dockerfile
install:
  chart: $CHART_DIR$
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    # Optional, specify an array of imagePullSecrets. These secrets must be manually created in the namespace.
    # This will override the imagePullSecrets array in values.yaml file.
    # If the dockerfile specifies any private registry, the imagePullSecret for the registry must be added here.
    # ref: https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod
    #
    # This uses credentials from secret "myRegistryKeySecretName".
    imagePullSecrets:
      - name: myRegistryKeySecretName
```

> [!IMPORTANT]
> `azds.yaml` で imagePullSecrets を設定すると、`values.yaml` で指定された imagePullSecrets はオーバーライドされます。

### <a name="error-service-cannot-be-started"></a>エラー "サービスを開始できません。"

サービス コードを起動できない場合、このエラーが表示されることがあります。 通常、原因はユーザー コードです。 より詳しい診断情報を入手するには、サービスを開始するときに、詳細なログを有効にします。

詳細なログを有効にするには、コマンド ラインから `--verbose` を使用します。 `--output` を使用して出力形式を指定することもできます。 次に例を示します。

```cmd
azds up --verbose --output json
```

Visual Studio で次の操作を行います。

1. **[ツール] > [オプション]** を開き、 **[プロジェクトとソリューション]** で、 **[ビルドおよび実行]** を選択します。
2. **[MSBuild プロジェクト ビルドの出力の詳細]** の設定を **[詳細]** または **[診断]** に変更します。

    ![ツール オプション ダイアログのスクリーンショット](media/common/VerbositySetting.PNG)

### <a name="rerunning-a-service-after-controller-re-creation"></a>コントローラーの再作成後のサービスの再実行

このクラスターに関連付けられた Azure Dev Spaces を削除してから再作成した後、サービスを再実行しようとすると、"*Service cannot be started (サービスを開始できません)* " エラーが発生します。 このような状況では、詳細出力に次のテキストが含まれます。

```output
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

このエラーは、Dev Spaces コントローラーを削除しても、そのコントローラーによって以前にインストールされたサービスが削除されないという理由で発生します。 コントローラーを再作成してから、新しいコントローラーを使用してサービスを実行しようとしても、古いサービスがまだ残っているため失敗します。

この問題に対処するには、`kubectl delete` コマンドを使用して古いサービスをクラスターから手動で削除した後、Dev Spaces を再実行して新しいサービスをインストールします。

### <a name="error-service-cannot-be-started-when-using-multi-stage-dockerfiles"></a>エラー "サービスを開始できません。" (マルチステージの Dockerfile の使用時)

マルチステージの Dockerfile を使用すると、"*Service cannot be started (サービスを開始できません)* " エラーが発生します。 このような状況では、詳細出力に次のテキストが含まれます。

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

このエラーが発生するのは、Azure Dev Spaces が現在マルチステージ ビルドをサポートしていないためです。 マルチステージ ビルドを回避するには、Dockerfile を書き直します。

### <a name="network-traffic-is-not-forwarded-to-your-aks-cluster-when-connecting-your-development-machine"></a>開発マシンに接続するときに、ネットワーク トラフィックが AKS クラスターに転送されない

[Azure Dev Spaces を使用して AKS クラスターを開発マシンに接続する](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes)と、開発マシンと AKS クラスターの間でネットワーク トラフィックが転送されないという問題が発生する可能性があります。

開発マシンを AKS クラスターに接続すると、Azure Dev Spaces は開発マシンの `hosts` ファイルを変更することによって、お使いの AKS クラスターと開発マシンの間でネットワーク トラフィックを転送します。 Azure Dev Spaces は、ホスト名として置き換える Kubernetes サービスのアドレスを使用して、`hosts` にエントリを作成します。 このエントリは、開発マシンと AKS クラスターの間でネットワーク トラフィックを転送するために、ポート フォワーディングと共に使用されます。 開発マシン上のサービスが、置き換える Kubernetes サービスのポートと競合する場合、Azure Dev Spaces は Kubernetes サービスのネットワーク トラフィックを転送できません。 たとえば、*Windows BranchCache* サービスは通常 *0.0.0.0:80* にバインドされます。これにより、すべてのローカル IP でポート 80 の競合が発生します。

この問題を解決するには、置き換えようとしている Kubernetes サービスのポートと競合する、あらゆるサービスやプロセスを停止する必要があります。 *netstat* などのツールを使用して、開発マシン上のどのサービスまたはプロセスが競合しているかを調べることができます。

たとえば、*Windows BranchCache* サービスを停止および無効にするには、次のようにします。
* コマンド プロンプトで `services.msc` を実行します。
* *[BranchCache]* を右クリックし、 *[プロパティ]* を選択します。
* *[停止]* をクリックします。
* 必要に応じて、 *[スタートアップの種類]* を *[無効]* に設定して無効にすることができます。
* [*OK*] をクリックします。

### <a name="error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state"></a>エラー "割り当てられた状態で pod:azds/azds-webhook-deployment-\<id\>の AzureAssignedIdentity が見つかりませんでした"

[マネージド ID](../aks/use-managed-identity.md) および [ポッド マネージ ID](../aks/developer-best-practices-pod-security.md#use-pod-managed-identities) がインストールされている AKS クラスターで、Azure Dev Spaces を使用してサービスを実行すると、"*グラフのインストール*" ステップ後にプロセスが応答しなくなることがあります。 *azds* 名前空間で *azds-injector-webhook* を調べると、このエラーが表示されることがあります。

Azure Dev Spaces がクラスターで実行するサービスは、クラスターのマネージド ID を利用して、クラスター外の Azure Dev Spaces バックエンド サービスと通信します。 ポッド マネージド ID がインストールされている場合、ネットワーク ルールはクラスターのノードで構成され、マネージド ID 資格情報の呼び出しはすべて、[クラスターにインストールされた Node Managed Identity (NMI) デーモンセット](https://github.com/Azure/aad-pod-identity#node-managed-identity)にリダイレクトされます。 この NMI デーモンセットは呼び出し元のポッドを識別し、要求されたマネージド ID にアクセスできるようポッドに適切なラベルが付けられていることを確認します。 Azure Dev Spaces は、クラスターにポッド マネージド ID がインストールされているかどうか検出できず、Azure Dev Spaces サービスがクラスターのマネージド ID にアクセスするために必要な構成を実行できません。 Azure Dev Spaces サービスはクラスターのマネージド ID にアクセスするように構成されていないため、NMI デーモンセットではマネージド ID の Azure AD トークンを取得できず、Azure Dev Spaces バックエンド サービスと通信できません。

この問題を修正するには、*azds-injector-webhook* で [AzurePodIdentityException](https://azure.github.io/aad-pod-identity/docs/configure/application_exception) を適用し、Azure Dev Spaces でインストルメント化されたポッドを更新してマネージド ID にアクセスできるようにします。

*webhookException.yaml* という名前のファイルを作成し、以下の YAML 定義をコピーします。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: azds
spec:
  PodLabels:
    azds.io/uses-cluster-identity: "true"
```

上記のファイルによって、*azds-injector-webhook* の *AzurePodIdentityException* オブジェクトが作成されます。 このオブジェクトをデプロイするには、`kubectl` を使用します。

```cmd
kubectl apply -f webhookException.yaml
```

マネージド ID にアクセスするために Azure Dev Spaces によってインストルメント化されたポッドを更新するには、以下の YAML 定義で *名前空間* を更新し、`kubectl` を使用して各開発領域に適用します。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: azds-infrastructure-exception
  namespace: myNamespace
spec:
  PodLabels:
    azds.io/instrumented: "true"
```

また、*AzureIdentity* および *AzureIdentityBinding* オブジェクトを作成し、Azure Dev Spaces によってインストルメント化されたスペースで実行中のワークロードのポッド ラベルを更新して、AKS クラスターによって作成されたマネージド ID にアクセスできるようにすることもできます。

マネージド ID の詳細を一覧表示するには、AKS クラスターで以下のコマンドを実行してください。

```azurecli
az aks show -g <resourcegroup> -n <cluster> -o json --query "{clientId: identityProfile.kubeletidentity.clientId, resourceId: identityProfile.kubeletidentity.resourceId}"
```

上記のコマンドはマネージド ID の *clientId* と *resourceId* を出力します。 次に例を示します。

```json
{
  "clientId": "<clientId>",
  "resourceId": "/subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>"
}
```

*AzureIdentity* オブジェクトを作成するには、*clusteridentity.yaml* という名前のファイルを作成し、前のコマンドで得たマネージド ID の詳細で更新された以下の YAML 定義を使用します。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentity
metadata:
  name: my-cluster-mi
spec:
  type: 0
  ResourceID: /subscriptions/<subid>/resourcegroups/<resourcegroup>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<name>
  ClientID: <clientId>
```

*AzureIdentityBinding* オブジェクトを作成するには、*clusteridentitybinding.yaml* という名前のファイルを作成し、以下の YAML 定義を使用します。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: my-cluster-mi-binding
spec:
  AzureIdentity: my-cluster-mi
  Selector: my-label-value
```

*AzureIdentity* および *AzureIdentityBinding* オブジェクトをデプロイするには、`kubectl` を使用します。

```cmd
kubectl apply -f clusteridentity.yaml
kubectl apply -f clusteridentitybinding.yaml
```

*AzureIdentity* および *AzureIdentityBinding* オブジェクトのデプロイ後、*aadpodidbinding: my-label-value* ラベルを持つワークロードはクラスターのマネージド ID にアクセスできるようになります。 このラベルを追加し、開発領域で実行中のワークロードをすべて再度デプロイします。 次に例を示します。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: sample
        aadpodidbinding: my-label-value
    spec:
      [...]
```

### <a name="error-cannot-get-connection-details-for-azure-dev-spaces-controller-abc-because-it-is-in-the-failed-state-something-wrong-might-have-happened-with-your-controller"></a>エラー "Azure Dev Spaces コントローラー ' ABC ' は 'Failed' 状態であるため、この接続の詳細を取得できません。 コントローラーで何らかの問題が発生した可能性があります。"

この問題を解決するには、Azure Dev Spaces コントローラーをクラスターから削除してから再インストールすることを試してください。

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

また、Azure Dev Spaces は廃止される予定なので、[Bridge to Kubernetes への移行](migrate-to-bridge-to-kubernetes.md)を検討してください。これによりエクスペリエンスが向上します。

## <a name="common-issues-using-visual-studio-and-visual-studio-code-with-azure-dev-spaces"></a>Visual Studio と Visual Studio Code を Azure Dev Spaces で使用するときに発生する一般的な問題

### <a name="error-required-tools-and-configurations-are-missing"></a>エラー "必要なツールと構成が見つからない"

このエラーは、VS Code を起動するときに発生する場合があります。"[[Azure Dev Spaces] Required tools and configurations to build and debug '[project name]' are missing.]\([Azure Dev Spaces] '[プロジェクト名]' をビルドして出バックするために必要なツールと構成がありません。\)"
エラーは、VS Code に示されるように、azds.exe が PATH 環境変数に無いことを意味します。

PATH 環境変数が正しく設定されているコマンド プロンプトから VS Code を起動してみてください。

### <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>エラー "Required tools to build and debug 'projectname' are out of date." ('projectname' をビルドおよびデバッグするために必要なツールの期限が切れています。)

このエラーは、Azure Dev Spaces 用の VS Code 拡張機能のバージョンは新しいが、Azure Dev Spaces CLI のバージョンが古い場合に、Visual Studio Code に表示されます。

最新バージョンの Azure Dev Spaces CLI をダウンロードしてインストールしてみてください。

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

### <a name="error-failed-to-find-debugger-extension-for-typecoreclr"></a>エラー:"Failed to find debugger extension for type:coreclr (type:coreclr のデバッガー拡張機能が見つかりませんでした)"

Visual Studio Code デバッガーを実行しているときに、このエラーが表示されることがあります。 C# 用の VS Code 拡張機能が開発マシンにインストールされていない可能性があります。 C# 拡張機能には、.NET Core (CoreCLR) 用のデバッグ サポートが含まれています。

この問題を解決するには、[C# 用 VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)をインストールします。

### <a name="error-configured-debug-type-coreclr-is-not-supported"></a>エラー "Configured debug type 'coreclr' is not supported (構成されているデバッグの種類 'coreclr' はサポートされていません)"

Visual Studio Code デバッガーを実行しているときに、このエラーが表示されることがあります。 Azure Dev Spaces 用の VS Code 拡張機能が開発マシンにインストールされていない可能性があります。

この問題を解決するには、Azure Dev Spaces 用の VS Code 拡張機能をインストールします。

### <a name="error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>エラー "Invalid 'cwd' value '/src'. The system cannot find the file specified." ('cwd' の値 '/src' が無効です。指定されたファイルがシステムに見つかりません。) または "launch: program '/src/[path to project binary]' does not exist" (launch: program '/src/[プロジェクト バイナリのパス]' が存在しません)

Visual Studio Code デバッガーを実行しているときに、このエラーが表示されることがあります。 既定では、VS Code 拡張機能はコンテナー上のプロジェクト用の作業ディレクトリとして `src` を使用します。 `Dockerfile` を更新して別の作業ディレクトリを指定した場合、このエラーが表示されることがあります。

この問題を解決するには、プロジェクト フォルダーの `.vscode` サブディレクトリ下の `launch.json` ファイルを更新します。 プロジェクトの `Dockerfile` で定義されている `WORKDIR` と同じディレクトリを指すように、`configurations->cwd` ディレクティブを変更します。 `configurations->program` ディレクティブも更新することが必要な場合もあります。

### <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>エラー "The pipe program 'azds' exited unexpectedly with code 126. (パイプ プログラム 'azds' が、コード 126 により予期せず終了しました。)"

Visual Studio Code デバッガーを実行しているときに、このエラーが表示されることがあります。

この問題を解決するには、Visual Studio Code を閉じて、もう一度開きます。 デバッガーを再起動してください。

### <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Node.js アプリケーションにデバッグを接続するとエラー "Internal watch failed: watch ENOSPC" が発生する

デバッガーで接続しようとしている Node.js アプリケーションでポッドを実行しているノードが *fs.inotify.max_user_watches* 値を超えるとこのエラーが発生します。 場合によっては、[*fs.inotify.max_user_watches* の既定値が小さすぎて、デバッガーのポッドへの直接の接続を処理できない可能性があります](https://github.com/Azure/AKS/issues/772)。

この問題の一時的な対処法として、クラスター内の各ノード上で *fs.inotify.max_user_watches* の値を増やし、そのノードを再起動して変更を有効にしてください。

## <a name="other-common-issues"></a>その他の一般的な問題

### <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>エラー "azds" が、内部または外部コマンド、操作可能プログラム、またはバッチ ファイルとして認識されません

`azds.exe` がインストールされていないか、正しく構成されていない場合に、このエラーが発生することがあります。

この問題を解決するには、次の手順に従います。

1. %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev Spaces CLI で `azds.exe` を探してください。 見つかった場合は、その場所を PATH 環境変数に追加します。
2. `azds.exe` がインストールされていない場合は、次のコマンドを実行します。

    ```azurecli
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

### <a name="authorization-error-microsoftdevspacesregisteraction"></a>承認エラー "Microsoft.DevSpaces/register/action"

Azure Dev Spaces を管理するには、お使いの Azure サブスクリプション内に "*所有者*" または "*共同作成者*" アクセス権が必要です。 Dev Spaces を管理しようとしたが、関連付けられている Azure サブスクリプションに "*所有者*" アクセス権も "*共同作成者*" アクセス権もない場合、承認エラーが表示されることがあります。 次に例を示します。

```output
The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.
```

この問題を解決するには、Azure サブスクリプションの *所有者* または *共同作成者* のアクセス権を持つアカウントを使用して、`Microsoft.DevSpaces` 名前空間を手動で登録します。

```azurecli
az provider register --namespace Microsoft.DevSpaces
```

### <a name="new-pods-arent-starting"></a>新しいポッドが開始しない

Kubernetes の初期化子は、クラスター内の *cluster-admin* ロールに対する Kubernetes RBAC 権限の変更により、新しいポッドに PodSpec を適用できません。 ポッドに関連付けられているサービス アカウントが存在しないなど、新しいポッドに無効な PodSpec が含まれている可能性もあります。 初期化子の問題により *[保留中]* の状態になっているポッドを確認するには、`kubectl get pods` コマンドを使用します。

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

この問題は、クラスター内の *すべての名前空間* (Azure Dev Spaces が無効な名前空間を含む) のポッドに影響を与える可能性があります。

この問題を解決するには、[Dev Spaces CLI を最新バージョンに更新](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools)して、Azure Dev Spaces コントローラーから *azds InitializerConfiguration* を削除します。

```azurecli
az aks get-credentials --resource-group <resource group name> --name <cluster name>
```

```bash
kubectl delete InitializerConfiguration azds
```

Azure Dev Spaces コントローラーから *azds InitializerConfiguration* を削除したら、`kubectl delete` を使用して *[保留中]* 状態のポッドをすべて削除します。 保留中のポッドをすべて削除したら、ポッドを再デプロイします。

再デプロイ後も新しいポッドが *[保留中]* 状態のままになっている場合は、`kubectl delete` を使用して *[保留中]* 状態のポッドをすべて削除してください。 保留中のポッドをすべて削除したら、コントローラーをクラスターから削除して再インストールします。

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

コントローラーを再インストールしたら、ポッドを再デプロイします。

### <a name="incorrect-azure-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Dev Spaces コントローラーと API を呼び出すための Azure RBAC 権限が正しくない

Azure Dev Spaces コントローラーにアクセスするユーザーは、AKS クラスター上の管理者用の *kubeconfig* を読み取るためのアクセス権を持っている必要があります。 たとえば、この権限は、[組み込みの Azure Kubernetes Service クラスター管理者ロール](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions)で利用できます。 また、Azure Dev Spaces コントローラーにアクセスするユーザーには、コントローラーの "*共同作成者*" または "*所有者*" の Azure ロールも設定されている必要があります。 AKS クラスターに対するユーザーの権限の更新に関する詳細は、[こちら](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group)で確認できます。

コントローラーに対するユーザーの Azure ロールを更新するには、次のことを行います。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. コントローラーを含むリソース グループに移動します。これは通常、お使いの AKS クラスターと同じです。
1. *[非表示の型の表示]* チェック ボックスをオンにします。
1. コントローラーをクリックします。
1. *[アクセス制御] (IAM)* ウィンドウを開きます。
1. *[ロールの割り当て]* タブをクリックします。
1. *[追加]* をクリックして、 *[ロールの割り当ての追加]* をクリックします。
    * *[ロール]* で、 *[共同作成者]* または *[所有者]* を選択します。
    * *[アクセスの割り当て先]* で *[Azure AD のユーザー、グループ、サービス プリンシパル]* を選択します。
    * *[選択]* で、権限を付与するユーザーを検索します。
1. [*保存*] をクリックします。

### <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Dev Spaces サービスに関連付けられたパブリック URL で DNS の名前解決が失敗します。

`azds prep` コマンドで `--enable-ingress` スイッチを指定するか、Visual Studio で `Publicly Accessible` チェック ボックスをオンにすることで、サービスのパブリック URL エンドポイントを構成できます。 Dev Spaces でサービスを実行すると、パブリック DNS 名が自動的に登録されます。 この DNS 名が登録されていない場合は、パブリック URL に接続するときに、Web ブラウザーに "*Page cannot be displayed (ページを表示できません)* " または "*Site cannot be reached (サイトに到達できません)* " エラーが表示されます。

この問題を解決するには、次の手順に従います。

* お使いの Dev Spaces サービスに関連付けられたすべての URL の状態をチェックします。

  ```console
  azds list-uris
  ```

* URL が "*保留中*" の状態にある場合、まだ Dev Spaces が DNS の登録が完了するのを待っている状態です。 登録が完了するまでに数分かかることがあります。 また、Dev Spaces は各サービスの localhost トンネルを開き、これを DNS の登録を待っている間に使用できます。
* URL の "*保留中*" 状態が 5 分を超える場合、パブリック エンドポイントを作成する外部 DNS ポッド、またはパブリック エンドポイントを取得する nginx イングレス コントローラー ポッドに問題がある可能性があります。 次のコマンドを使用して、それらのポッドを削除し、AKS で自動的に再作成してください。
  ```console
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
  kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
  ```

### <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>エラー "upstream connect error or disconnect/reset before headers (ヘッダーの前にアップストリームの接続エラーあるいは切断またはリセットが発生しました)"

このエラーは、サービスへのアクセスを試みたときに発生する場合があります。 たとえば、ブラウザーでサービスの URL にアクセスしたときです。 このエラーは、コンテナーのポートが使用できないことを意味します。 その理由としては次のことが考えられます。

* コンテナーがまだビルドとデプロイの処理中です。 `azds up` を実行またはデバッガーを起動した後、コンテナーが正常にデプロイされる前にコンテナーにアクセスしようとした場合、この問題が発生する可能性があります。
* _Dockerfile_、Helm チャート、およびポートを開くサーバー コード間でポート構成が整合していません。

この問題を解決するには、次の手順に従います。

1. コンテナーがビルド/デプロイ処理中の場合、2 ～ 3 秒待ってからサービスへのアクセスを再試行できます。 
1. 次の資産のポート構成を確認します。
    * **[Helm チャート](https://docs.helm.sh):** `azds prep` コマンドでの values.yaml scaffolded 内に `service.port` と `deployment.containerPort` によって指定されています。
    * Node.js などのアプリケーション コードで開かれているポートがあります: `var server = app.listen(80, function () {...}`

### <a name="the-type-or-namespace-name-mylibrary-couldnt-be-found"></a>型または名前空間名 "MyLibrary" が見つかりませんでした

ご使用のライブラリ プロジェクトが見つかりません。 Dev Spaces では、ビルド コンテキストが既定でプロジェクトまたはサービス レベルになっています。  

この問題を解決するには、次の手順に従います。

1. `azds.yaml` ファイルを変更して、ビルド コンテキストをソリューション レベルに設定します。
2. `Dockerfile` および `Dockerfile.develop` ファイルを変更して、新しいビルド コンテキストからの相対位置でプロジェクト (`.csproj` など) ファイルを正しく参照します。
3. `.sln` ファイルと同じディレクトリに `.dockerignore` を追加します。
4. 必要に応じて追加のエントリで `.dockerignore` を更新します。

[こちら](https://github.com/sgreenmsft/buildcontextsample)に例があります。

### <a name="horizontal-pod-autoscaling-not-working-in-a-dev-space"></a>開発スペースでポッドの水平オートスケール機能が動作しない

開発スペースでサービスを実行すると、そのサービスのポッドが[インストルメンテーション用の追加のコンテナーと共に挿入されます](how-dev-spaces-works-cluster-setup.md#prepare-your-aks-cluster)。また、ポッド内のすべてのコンテナーでは、ポッドの水平オートスケール機能に対してリソース制限と要求を設定する必要があります。

この問題を解決するには、挿入された Dev Spaces コンテナーにリソースの要求と制限を適用します。 ポッド仕様に `azds.io/proxy-resources` 注釈を追加することで、挿入されたコンテナー (devspaces-proxy) にリソースの要求と制限を適用できます。この値は、プロキシのコンテナー仕様の resources セクションを表す JSON オブジェクトに設定する必要があります。

ポッド仕様に適用される proxy-resources 注釈の例を次に示します。
```
azds.io/proxy-resources: "{\"Limits\": {\"cpu\": \"300m\",\"memory\": \"400Mi\"},\"Requests\": {\"cpu\": \"150m\",\"memory\": \"200Mi\"}}"
```

### <a name="enable-azure-dev-spaces-on-an-existing-namespace-with-running-pods"></a>ポッドが実行されている既存の名前空間で Azure Dev Spaces を有効にする

既存の AKS クラスターとポッドが実行されている名前空間で Azure Dev Spaces を有効にしたい場合があります。

AKS クラスター内の既存の名前空間で Azure Dev Spaces を有効にするには、`use-dev-spaces` を実行し、`kubectl` を使用して、その名前空間内のすべてのポッドを再起動します。

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space my-namespace --yes
```

```console
kubectl -n my-namespace delete pod --all
```

ポッドの再起動後、Azure Dev Spaces で既存の名前空間を使用できるようになります。

### <a name="enable-azure-dev-spaces-on-aks-cluster-with-restricted-egress-traffic-for-cluster-nodes"></a>クラスター ノードのエグレス トラフィックが制限されている AKS クラスターで Azure Dev Spaces を有効にする

クラスターノードからのエグレス トラフィックが制限されている AKS クラスターで Azure Dev Spaces を有効にするには、次の FQDN を許可する必要があります。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS: 443 | Linux Alpine とその他の Azure Dev Spaces イメージをプルします |
| gcr.io | HTTP:443 | helm/tiller イメージをプルします|
| storage.googleapis.com | HTTP:443 | helm/tiller イメージをプルします|

上のすべての FQDN と [Azure Dev Spaces インフラストラクチャ サービス](../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations)との間のネットワーク トラフィックを許可するようにファイアウォールまたはセキュリティ構成を更新してください。

### <a name="error-could-not-find-the-cluster-cluster-in-subscription-subscriptionid"></a>エラー "Could not find the cluster \<cluster\> in subscription \<subscriptionId\>" (サブスクリプション subscriptionId にクラスター cluster が見つかりませんでした)

kubeconfig ファイルが、Azure Dev Spaces クライアント側ツールで使用しようとしているものとは異なるクラスターまたはサブスクリプションを対象としている場合に、このエラーが表示されることがあります。 Azure Dev Spaces クライアント側ツールは、*kubectl* の動作をレプリケートし、[1 つ以上の kubeconfig ファイル](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)を使用して、クラスターを選択して通信します。

この問題を解決するには、次の手順に従います。

* `az aks use-dev-spaces -g <resource group name> -n <cluster name>` を使用して、現在のコンテキストを更新します。 このコマンドによって、AKS クラスターで Azure Dev Spaces も有効になります (まだ有効になっていない場合)。 または、`kubectl config use-context <cluster name>` を使用して現在のコンテキストを更新することもできます。
* `az account show` を使用して、対象となっている現在の Azure サブスクリプションを表示し、これが正しいことを確認します。 `az account set` を使用して、対象とするサブスクリプションを変更できます。

### <a name="error-using-dev-spaces-after-rotating-aks-certificates"></a>AKS 証明書のローテーション後に Dev Spaces を使用するエラー

[AKS クラスターで証明書をローテーションした](../aks/certificate-rotation.md)後に、`azds space list` や `azds up` などの特定の操作が失敗します。 また、クラスター上で証明書をローテーションした後に、Azure Dev Spaces コントローラー上で証明書を更新する必要があります。

この問題を解決するには、`az aks get-credentials` を使用して *kubeconfig* に更新された証明書がある状態を確保してから、`azds controller refresh-credentials` コマンドを実行します。 次に例を示します。

```azurecli
az aks get-credentials -g <resource group name> -n <cluster name>
```

```console
azds controller refresh-credentials -g <resource group name> -n <cluster name>
```
