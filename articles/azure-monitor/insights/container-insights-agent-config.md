---
title: コンテナーの Azure Monitor エージェントのデータ収集を構成する | Microsoft Docs
description: この記事では、コンテナーの Azure Monitor エージェントによる stdout/stderr および環境変数のログ収集の制御を構成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341661"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>コンテナーの Azure Monitor に対するエージェントのデータ収集を構成する

コンテナーの Azure Monitor では、コンテナー化されたエージェントにより、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスターにデプロイされたコンテナー ワークロードからの stdout、stderr、および環境変数が収集されます。 このエクスペリエンスを制御するためのカスタム Kubernetes ConfigMaps を作成することにより、エージェントのデータ収集の設定を構成できます。 この記事では、ConfigMap を作成し、要件に基づいてデータの収集を構成する方法を示します。

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>カスタム データ収集の設定でクラスターを構成する

最初から作成する必要がなく、ご自分のカスタマイズで簡単に編集できる、テンプレート ConfigMap ファイルが提供されています。 始める前に、[ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) についての Kubernetes のドキュメントを確認し、ConfigMaps を作成、構成、デプロイする方法をよく理解しておく必要があります。 そうすることで、名前空間ごとまたはクラスター全体の stderr と stdout、およびクラスター内のすべてのポッド/ノードで実行されている任意のコンテナーに対する環境変数を、フィルター処理できます。

>[!IMPORTANT]
>この機能でサポートされているエージェントの最小バージョンは、microsoft/oms:ciprod06142019 またはそれ以降です。 

### <a name="overview-of-configurable-data-collection-settings"></a>構成可能なデータ収集設定の概要

データ収集を制御するために構成できる設定を次に示します。

|キー |データ型 |値 |説明 |
|----|----------|------|------------|
|`schema-version` |String (大文字と小文字が区別されます) |v1 |これは、この ConfigMap を解析するときにエージェントで使われるスキーマのバージョンです。 現在サポートされているスキーマのバージョンは、v1 です。 この値に対する変更はサポートされておらず、ConfigMap の評価時に拒否されます。|
|`config-version` |string | | ソース管理システム/リポジトリでこの構成ファイルのバージョンを追跡する機能をサポートします。 許容される最大文字数は 10 で、他のすべての文字は切り捨てられます。 |
|`[log_collection_settings.stdout] enabled =` |Boolean | true または false | stdout コンテナーのログ収集が有効かどうかを制御します。 `true` に設定した場合、stdout のログ収集に対して名前空間を除外しないと (後の `log_collection_settings.stdout.exclude_namespaces` 設定)、クラスター内のすべてのポッド/ノードのすべてのコンテナーから、stdout ログが収集されます。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |
|`[log_collection_settings.stdout] exclude_namespaces =`|string | コンマ区切りの配列 |stdout のログを収集しない Kubernetes 名前空間の配列。 この設定は、`log_collection_settings.stdout.enabled` を `true` に設定した場合にのみ有効です。 ConfigMap で指定しない場合、既定値は `exclude_namespaces = ["kube-system"]` です。|
|`[log_collection_settings.stderr] enabled =` |Boolean | true または false |stderr コンテナーのログ収集が有効かどうかを制御します。 `true` に設定した場合、stdout のログ収集に対して名前空間を除外しないと (`log_collection_settings.stderr.exclude_namespaces` 設定)、クラスター内のすべてのポッド/ノードのすべてのコンテナーから、stderr ログが収集されます。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |
|`[log_collection_settings.stderr] exclude_namespaces =` |string |コンマ区切りの配列 |stderr のログを収集しない Kubernetes 名前空間の配列。 この設定は、`log_collection_settings.stdout.enabled` を `true` に設定した場合にのみ有効です。 ConfigMap で指定しない場合、既定値は `exclude_namespaces = ["kube-system"]` です。 |
| `[log_collection_settings.env_var] enabled =` |Boolean | true または false | これは、環境変数の収集が有効かどうかを制御します。 `false` に設定すると、クラスター内のすべてのポッド/ノードで実行されているすべてのコンテナーの環境変数が収集されません。 ConfigMap で指定しない場合、既定値は `enabled = true` です。 |

### <a name="configure-and-deploy-configmaps"></a>ConfigMap を構成してデプロイする

ConfigMap 構成ファイルを構成してクラスターにデプロイするには、次の手順のようにします。

1. テンプレート ConfigMap の yaml ファイルを[ダウンロード](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml)し、container-azm-ms-agentconfig.yaml として保存します。  
1. ご自分のカスタマイズで ConfigMap yaml ファイルを編集します。 

    - stdout のログ収集から特定の名前空間を除外するには、次の例を使ってキー/値を構成します: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`。
    - 特定のコンテナーからの環境変数の収集を無効にするには、キー/値 `[log_collection_settings.env_var] enabled = true` を設定して変数の収集をグローバルに有効にした後、[こちら](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container)の手順に従って特定のコンテナーの構成を完了します。
    - クラスター全体で stderr のログ収集を無効にするには、次の例を使ってキー/値を構成します: `[log_collection_settings.stderr] enabled = false`。

1. 次の kubectl コマンドを実行して、ConfigMap を作成します: `kubectl apply -f <configmap_yaml_file.yaml>`。
    
    例: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    構成の変更が有効になるまでに数分かかる場合があり、クラスター内のすべての omsagent ポッドが再起動されます。 すべての omsagent ポッドが同時に再起動されるのではなく、ローリング再起動で行われます。 再起動が完了すると、次のような結果を含むメッセージが表示されます: `configmap "container-azm-ms-agentconfig" created`。

構成が正常に適用されたことを検証するには、次のコマンドを使って、エージェント ポッドからのログを確認します: `kubectl logs omsagent-fdf58 -n=kube-system`。 osmagent ポッドからの構成エラーがある場合は、出力で次のようなエラーが示されます。

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

エラーがあると omsagent でファイルを解析できず、再起動されて、既定の構成が使用されます。 ConfigMap でエラーを修正した後、yaml ファイルを保存し、次のコマンドを実行して更新された ConfigMap を適用します: `kubectl apply -f <configmap_yaml_file.yaml`。

## <a name="applying-updated-configmap"></a>更新された ConfigMap を適用する

クラスターに ConfigMap を既に展開してあり、それを新しい構成で更新したい場合は、前に使用した ConfigMap ファイルを単に編集した後、同じコマンド `kubectl apply -f <configmap_yaml_file.yaml` を使って適用できます。

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

## <a name="next-steps"></a>次の手順

- Azure Monitor を使用して、AKS クラスターの他の側面を監視する方法を引き続き学習するには、[Azure Kubernetes Service の正常性の表示](container-insights-analyze.md)に関するページをご覧ください。
- [ログ クエリの例](container-insights-log-search.md#search-logs-to-analyze-data)を表示して、事前定義されたクエリや例を確認し、クラスターのアラート、視覚化、または分析のために評価やカスタマイズを行います。