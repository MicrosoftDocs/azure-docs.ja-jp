---
title: Container insights エージェントのデータ収集を構成する | Microsoft Docs
description: この記事では、Container insights エージェントによる stdout/stderr および環境変数のログ収集の制御を構成する方法について説明します。
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: d866fec2013daf9b8edfdbfd703c7b1098ae91bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708392"
---
# <a name="configure-agent-data-collection-for-container-insights"></a>Container insights のエージェント データ収集を構成する

Container insights により、コンテナー化されたエージェントから、マネージド Kubernetes クラスターにデプロイされたコンテナー ワークロードの stdout、stderr、および環境変数が収集されます。 このエクスペリエンスを制御するためのカスタム Kubernetes ConfigMaps を作成することにより、エージェントのデータ収集の設定を構成できます。 

この記事では、ConfigMap を作成し、要件に基づいてデータの収集を構成する方法を示します。

>[!NOTE]
>Azure Red Hat OpenShift では、テンプレートの ConfigMap ファイルが *openshift-azure-loggin* 名前空間に作成されます。 
>

## <a name="configmap-file-settings-overview"></a>ConfigMap ファイル設定の概要

最初から作成する必要がなく、ご自分のカスタマイズで簡単に編集できる、テンプレート ConfigMap ファイルが提供されています。 始める前に、[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) についての Kubernetes のドキュメントを確認し、ConfigMaps を作成、構成、デプロイする方法をよく理解しておく必要があります。 そうすることで、名前空間ごとまたはクラスター全体の stderr と stdout、およびクラスター内のすべてのポッド/ノードで実行されている任意のコンテナーに対する環境変数を、フィルター処理できます。

>[!IMPORTANT]
>コンテナー ワークロードからの stdout、stderr、環境変数の収集をサポートされているエージェントの最小バージョンは、ciprod06142019 以降です。 お使いのエージェントのバージョンを確認するには、 **[ノード]** タブでノードを選択し、プロパティ ウィンドウで **[エージェント イメージ タグ]** プロパティの値に注目します。 エージェント バージョンおよび各リリースに含まれている機能の追加情報については、[エージェントのリリース ノート](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)を参照してください。

### <a name="data-collection-settings"></a>データ収集の設定

次の表は、データ収集を制御するために構成できる設定を示しています。

| Key | データ型 | 値 | 説明 |
|--|--|--|--|
| `schema-version` | String (大文字と小文字が区別されます) | v1 | これは、この ConfigMap を解析するときにエージェント<br> で使われるスキーマのバージョンです。<br> 現在サポートされているスキーマのバージョンは、v1 です。<br> この値の変更はサポートされておらず、<br> ConfigMap の評価時に拒否されます。 |
| `config-version` | String |  | ソース管理システム/リポジトリでこの構成ファイルのバージョンを追跡する機能をサポートします。<br> 許容される最大文字数は 10 で、他のすべての文字は切り捨てられます。 |
| `[log_collection_settings.stdout] enabled =` | Boolean | true または false | stdout コンテナーのログ収集が有効かどうかを制御します。 `true` に設定し、stdout のログ収集に対して名前空間の除外を行っていない場合<br> (以下の `log_collection_settings.stdout.exclude_namespaces` 設定)、クラスター内のすべてのポッドまたはノードのすべてのコンテナーから stdout ログが収集されます。 ConfigMaps で指定していない場合、<br> 既定値は `enabled = true` です。 |
| `[log_collection_settings.stdout] exclude_namespaces =` | String | コンマ区切りの配列 | stdout のログを収集しない Kubernetes 名前空間の配列。 この設定は、<br> `log_collection_settings.stdout.enabled`<br> を `true` に設定した場合のみ有効になります。<br> ConfigMap で指定しない場合、既定値は<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Boolean | true または false | stderr コンテナーのログ収集が有効かどうかを制御します。<br> `true` に設定し、stdout のログ収集に対して名前空間の除外を行っていない場合<br> (`log_collection_settings.stderr.exclude_namespaces` 設定)、クラスター内のすべてのポッドまたはノードのすべてのコンテナーから stderr ログが収集されます。<br> ConfigMap で指定しない場合、既定値は<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | String | コンマ区切りの配列 | stderr のログを収集しない Kubernetes 名前空間の配列。<br> この設定は、<br> `log_collection_settings.stdout.enabled` は `true` に設定されます。<br> ConfigMap で指定しない場合、既定値は<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Boolean | true または false | この設定は、クラスター内のすべてのポッドまたはノードにわたる<br> 環境変数の収集を制御します。<br> ConfigMaps で指定していない場合、既定は `enabled = true`<br> です。<br> 環境変数の収集がグローバルに有効になっている場合は、次の環境変数を設定して、特定のコンテナーに対してそれを<br> 無効にすることができます。<br> `AZMON_COLLECT_ENV` を Dockerfile 設定または [ポッドの構成ファイル](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/)の **env:** セクションの下で **False** に設定します。<br> 環境変数のコレクションがグローバルに無効になっている場合、特定のコンテナーに対してコレクションを有効にすることはできません (つまり、コンテナー レベルで適用できる唯一のオーバーライドは、既にグローバルに有効になっている場合にコレクションを無効にすることです)。 |
| `[log_collection_settings.enrich_container_logs] enabled =` | Boolean | true または false | この設定は、クラスター内のすべてのコンテナー ログの ContainerLog テーブルに書き込まれる<br> 各ログ記録の名前およびイメージ プロパティ値を設定する、コンテナー ログ強化を制御します。<br> ConfigMap に指定されていない場合、既定は `enabled = false` になります。 |
| `[log_collection_settings.collect_all_kube_events]` | Boolean | true または false | この設定では、すべての種類の Kube イベントの収集を許可します。<br> 既定では、種類が *Normal* の Kube イベントは収集されません。 この設定を `true` に設定した場合、*Normal* イベントはフィルター処理されず、すべてのイベントが収集されます。<br> 既定では、これは `false` に設定されています。 |

### <a name="metric-collection-settings"></a>メトリック収集の設定

次の表は、メトリックの収集を制御するために構成できる設定を示しています。

| Key | データ型 | 値 | 説明 |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Boolean | true または false | この設定を使用すると、kube-system 名前空間で永続ボリューム (PV) の使用状況メトリックを収集できます。 既定では、kube-system 名前空間の永続ボリューム要求を持つ永続ボリュームの使用状況メトリックは収集されません。 この設定を `true` に設定すると、すべての名前空間の PV 使用状況メトリックが収集されます。 既定では、これは `false` に設定されています。 |

ConfigMaps はグローバル リストであり、エージェントに適用できる ConfigMap は 1 つだけです。 別の ConfigMaps でコレクションを上書きすることはできません。

## <a name="configure-and-deploy-configmaps"></a>ConfigMap を構成してデプロイする

ConfigMap 構成ファイルを構成してクラスターにデプロイするには、次の手順のようにします。

1. [テンプレート ConfigMap の YAML ファイル](https://aka.ms/container-azm-ms-agentconfig)をダウンロードし、container-azm-ms-agentconfig.yaml として保存します。 

   > [!NOTE]
   > Azure Red Hat OpenShift を使用する場合は、ConfigMap テンプレートがクラスターに既に存在しているため、この手順は必要ありません。

2. カスタマイズで ConfigMap yaml ファイルを編集し、stdout、stderr、または環境変数を収集します。 Azure Red Hat OpenShift の ConfigMap yaml ファイルを編集している場合は、最初にコマンド `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` を実行して、テキスト エディターでファイルを開きます。

    - stdout のログ収集から特定の名前空間を除外するには、次の例を使ってキー/値を構成します: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    
    - 特定のコンテナーからの環境変数の収集を無効にするには、キー/値 `[log_collection_settings.env_var] enabled = true` を設定して変数の収集をグローバルに有効にした後、[こちら](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)の手順に従って特定のコンテナーの構成を完了します。
    
    - クラスター全体で stderr のログ収集を無効にするには、次の例を使ってキー/値を構成します: `[log_collection_settings.stderr] enabled = false`。

3. Azure Red Hat OpenShift 以外のクラスターの場合は、Azure Red Hat OpenShift 以外のクラスターで kubectl コマンド `kubectl apply -f <configmap_yaml_file.yaml>` を実行して、ConfigMap を作成します。 
    
    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Azure Red Hat OpenShift では、エディターに変更内容を保存します。

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

## <a name="verify-configuration"></a>構成の確認

Azure Red Hat OpenShift 以外のクラスターに構成が正常に適用されたことを確認するには、次のコマンドを使って、エージェント ポッドからのログを確認します: `kubectl logs omsagent-fdf58 -n kube-system`。 omsagent ポッドからの構成エラーがある場合は、出力で次のようなエラーが示されます。

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

構成変更の適用に関連するエラーも確認できます。 構成変更の追加のトラブルシューティングを実行するには、次のオプションを使用できます。

- エージェント ポッド ログから。同じ `kubectl logs` コマンドを使用。 

    >[!NOTE]
    >このコマンドは、Azure Red Hat OpenShift クラスターには適用できません。
    > 

- ライブ ログから。 ライブ ログでは、次のようなエラーが示されます。

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Log Analytics ワークスペースの **KubeMonAgentEvents** テーブルから。 データは、構成エラーに関する *エラー* の重大度と共に、1 時間ごとに送信されます。 エラーがない場合、テーブルのエントリには "*情報*" の重大度のデータが含まれ、エラーは報告されません。 **[タグ]** プロパティには、エラーが発生したポッドとコンテナー ID に関する詳細情報のほか、過去 1 時間の最初の発生、最後の発生、および発生回数も含まれます。

- Azure Red Hat OpenShift では、openshift-azure-logging のログ収集が有効になっているかどうかを確認するために、**ContainerLog** テーブルを検索して omsagent ログを確認します。

Azure Red Hat OpenShift 以外のクラスターで ConfigMap のエラーを修正した後、次のコマンドを実行して yaml ファイルを保存し、更新された ConfigMap を適用します: `kubectl apply -f <configmap_yaml_file.yaml`。 Azure Red Hat OpenShift の場合は、次のコマンドを実行して、更新された ConfigMap を編集して保存します。

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>更新された ConfigMap を適用する

Azure Red Hat OpenShift 以外のクラスターに ConfigMap を既にデプロイしてあり、それをより新しい構成で更新したい場合は、前に使用した ConfigMap ファイルを編集してから、前と同じコマンド `kubectl apply -f <configmap_yaml_file.yaml` を使って適用できます。 Azure Red Hat OpenShift の場合は、次のコマンドを実行して、更新された ConfigMap を編集して保存します。

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" updated`。

## <a name="verifying-schema-version"></a>スキーマのバージョンを確認する

サポートされている構成スキーマのバージョンは、omsagent ポッドのポッド注釈 (schema-versions) として利用できます。 次の kubectl コマンドでそれらを確認できます: `kubectl describe pod omsagent-fdf58 -n=kube-system`

注釈 schema-versions を含む次のような出力が表示されます。

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>次のステップ

- Container insights にはアラートの定義済みのセットは含まれていません。 [Container insights を使用したパフォーマンス アラートの作成](./container-insights-log-alerts.md)に関するページを読んで、CPU やメモリの使用率が高い場合に推奨アラートを作成し、DevOps や運用プロセスまたは手順をサポートする方法について学習します。

- AKS またはハイブリッド クラスターと実行中のワークロードの正常性とリソース使用率を収集するための監視を有効にしたうえで、Container insights を[使用する方法](container-insights-analyze.md)について学習します。

- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。