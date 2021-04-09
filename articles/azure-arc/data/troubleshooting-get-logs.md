---
title: ログを取得して Azure Arc 対応データ サービスのトラブルシューティングを行う
description: データ コントローラーからログ ファイルを取得して、Azure Arc 対応データ サービスのトラブルシューティングを行う方法について説明します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93234047"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>ログを取得して Azure Arc 対応データ サービスのトラブルシューティングを行う

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

作業を進めるには、以下が必要です。

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. 詳細については、[Azure Arc データ サービスをデプロイおよび管理するためのクライアント ツールをインストールする](./install-client-tools.md)方法に関するページを参照してください。
* Azure Arc 対応データ コントローラーにサインインするための管理者アカウント。

## <a name="get-log-files"></a>ログ ファイルの取得

トラブルシューティングの目的で、すべてのポッドまたは特定のポッドでサービスのログを取得できます。 1 つは、`kubectl logs` コマンドなどの標準の Kubernetes ツールを使用する方法です。 この記事では、[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] ツールを使用します。これにより、一度にすべてのログを簡単に取得できます。

1. 管理者アカウントでデータ コントローラーにサインインします。

   ```console
   azdata login
   ```

2. 次のコマンドを実行して、ログをダンプします。

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   次に例を示します。

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

データ コントローラーによって、現在の作業ディレクトリの `logs` という名前のサブディレクトリにログ ファイルが作成されます。 

## <a name="options"></a>オプション

`azdata arc dc debug copy-logs` コマンドには、出力を管理するための次のオプションが用意されています。

* `--target-folder` パラメーターを使用して、ログ ファイルを別のディレクトリに出力します。
* `--skip-compress` パラメーターを省略することで、ファイルを圧縮します。
* `--exclude-dumps` を省略することで、メモリ ダンプをトリガーして含めます。 Microsoft サポートがメモリ ダンプを要求していない限り、この方法は推奨されません。 メモリ ダンプを取得するには、データ コントローラーの作成時に、データ コントローラーの `allowDumps` が `true` に設定されている必要があります。
* 名前によって特定のポッド (`--pod`) またはコンテナー (`--container`) のログのみを収集するフィルター処理を行います。
* `--resource-kind` と `--resource-name` パラメーターを渡すことによって、特定のカスタム リソースのログを収集するフィルター処理を行います。 `resource-kind` パラメーターの値は、カスタム リソース定義名のいずれかにする必要があります。 これらの名前を取得するには、コマンド `kubectl get customresourcedefinition` を使用します。

これらのパラメーターを使用して、次の例の `<parameters>` を置き換えることができます。 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

次に例を示します。

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

次に、フォルダー階層の例を示します。 これは、順番にポッド名、コンテナー、コンテナー内のディレクトリ階層ごとに整理されています。

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>次の手順

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
