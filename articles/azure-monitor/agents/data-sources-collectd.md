---
title: Azure Monitor での CollectD からのデータの収集 | Microsoft Docs
description: CollectD は、アプリケーションおよびシステム レベルの情報から定期的にデータを収集するオープン ソースの Linux デーモンです。  この記事では、Azure Monitor での CollectD からのデータの収集に関する情報を提供します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: daeb587349d885b3e6820b8da2951c3790eb1ac6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039582"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Azure Monitor で Linux エージェント上の CollectD からデータを収集する
[CollectD](https://collectd.org/) は、アプリケーションおよびシステム レベルの情報から定期的にパフォーマンス メトリックを収集するオープン ソースの Linux デーモンです。 アプリケーションの例には、Java 仮想マシン (JVM)、MySQL Server、および Nginx が含まれます。 この記事では、Azure Monitor での CollectD からのパフォーマンス データの収集に関する情報を提供します。

使用可能なプラグインの完全な一覧は、「[プラグインの表](https://collectd.org/wiki/index.php/Table_of_Plugins)」にあります。

![CollectD の概要](media/data-sources-collectd/overview.png)

次の CollectD 構成は、CollectD データを Linux 用 Log Analytics エージェントにルーティングするために、Linux 用 Log Analytics エージェントに含まれています。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

さらに、5.5 より前の CollectD のバージョンを使用している場合は、代わりに次の構成を使用します。

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

CollectD 構成では、既定の `write_http` プラグインを使用して、パフォーマンス メトリック データをポート 26000 経由で Linux 用 Log Analytics エージェントに送信します。 

> [!NOTE]
> このポートは、必要に応じてカスタム定義のポートに構成できます。

また、Linux 用 Log Analytics エージェントは、CollectD メトリックのためにポート 26000 をリッスンし、それらを Azure Monitor スキーマ メトリックに変換します。 Linux 用 Log Analytics エージェントの構成 `collectd.conf` を次に示します。

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> CollectD は、既定では、10 秒[間隔](https://collectd.org/wiki/index.php/Interval)で値を読み取るように設定されています。 これは Azure Monitor ログに送信されるデータの量に直接影響するため、監視要件と関連するコストと Azure Monitor ログの使用率の適切なバランスを取るために、CollectD 構成内でこの間隔を調整することが必要になる場合があります。

## <a name="versions-supported"></a>サポートされているバージョン
- Azure Monitor は現在、CollectD バージョン 4.8 以降をサポートしています。
- CollectD メトリックの収集には Linux 用 Log Analytics エージェント v1.1.0-217 以降が必要です。


## <a name="configuration"></a>構成
Azure Monitor での CollectD データの収集を構成するための基本的な手順を次に示します。

1. write_http プラグインを使用してデータを Linux 用 Log Analytics エージェントに送信するように CollectD を構成します。  
2. 適切なポート上で CollectD データをリッスンするように Linux 用 Log Analytics エージェントを構成します。
3. CollectD と Linux 用 Log Analytics エージェントを再起動します。

### <a name="configure-collectd-to-forward-data"></a>データを転送するように CollectD を構成する 

1. CollectD データを Linux 用 Log Analytics エージェントにルーティングするには、CollectD の構成ディレクトリに `oms.conf` を追加する必要があります。 このファイルの移動先は、使用しているマシンの Linux ディストリビューションによって異なります。

    CollectD config ディレクトリが /etc/collectd.d/ に配置されている場合:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    CollectD config ディレクトリが /etc/collectd/collectd.conf.d/ に配置されている場合:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >5\.5 より前の CollectD バージョンでは、上に示すように `oms.conf` 内のタグを変更する必要があります。
    >

2. collectd.conf を目的のワークスペースの omsagent 構成ディレクトリにコピーします。

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. 次のコマンドを使用して、CollectD と Linux 用 Log Analytics エージェントを再起動します。

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>CollectD メトリックから Azure Monitor スキーマへの変換
既に Linux 用 Log Analytics エージェントによって収集されたインフラストラクチャ メトリックと、CollectD によって収集された新しいメトリックの間で使い慣れたモデルを維持するために、次のスキーマ マッピングが使用されます。

| CollectD メトリックのフィールド | Azure Monitor のフィールド |
|:--|:--|
| `host` | Computer |
| `plugin` | なし |
| `plugin_instance` | インスタンス名<br>**plugin_instance** が *null* である場合、InstanceName=" *_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>**type_instance** が *null* である場合、CounterName=**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | なし |
| `values[]` | CounterValue |

## <a name="next-steps"></a>次のステップ
* [ログ クエリ](../logs/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 
* [カスタム フィールド](../logs/custom-fields.md) を使用して、syslog レコードのデータを個別のフィールドに解析します。