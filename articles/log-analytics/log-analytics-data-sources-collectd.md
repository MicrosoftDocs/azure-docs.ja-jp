---
title: "OMS Log Analytics での CollectD からのデータの収集 | Microsoft Docs"
description: "CollectD は、アプリケーションおよびシステム レベルの情報から定期的にデータを収集するオープン ソースの Linux デーモンです。  この記事では、Log Analytics での CollectD からのデータの収集に関する情報を提供します。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a63b15ca5126b45451f0694c9ee75d7b67b1ceaf
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Log Analytics で Linux エージェント上の CollectD からデータを収集する
[CollectD](https://collectd.org/) は、アプリケーションおよびシステム レベルの情報から定期的にパフォーマンス メトリックを収集するオープン ソースの Linux デーモンです。 アプリケーションの例には、Java 仮想マシン (JVM)、MySQL Server、および Nginx が含まれます。 この記事では、Log Analytics での CollectD からのパフォーマンス データの収集に関する情報を提供します。

使用可能なプラグインの完全な一覧は、「[プラグインの表](https://collectd.org/wiki/index.php/Table_of_Plugins)」にあります。

![CollectD の概要](media/log-analytics-data-sources-collectd/overview.png)

次の CollectD 構成は、CollectD データを OMS Agent for Linux にルーティングするために OMS Agent for Linux に含まれています。

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

さらに、5.5 より前の CollectD のバージョンを使用している場合は、代わりに次の構成を使用します。

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

CollectD 構成では、既定の `write_http` プラグインを使用して、パフォーマンス メトリック データをポート 26000 経由で OMS Agent for Linux に送信します。 

> [!NOTE]
> このポートは、必要に応じてカスタム定義のポートに構成できます。

OMS Agent for Linux はまた、CollectD メトリックのためにポート 26000 をリッスンし、それらを OMS スキーマ メトリックに変換します。 OMS Agent for Linux の構成 `collectd.conf` を次に示します。

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>サポートされているバージョン
- Log Analytics は現在、CollectD バージョン 4.8 以降をサポートしています。
- CollectD メトリックの収集には OMS Agent for Linux v1.1.0-217 以降が必要です。


## <a name="configuration"></a>構成
Log Analytics での CollectD データの収集を構成するための基本的な手順を次に示します。

1. write_http プラグインを使用してデータを OMS Agent for Linux に送信するように CollectD を構成します。  
2. 適切なポート上で CollectD データをリッスンするように OMS Agent for Linux を構成します。
3. CollectD と OMS Agent for Linux を再起動します。

### <a name="configure-collectd-to-forward-data"></a>データを転送するように CollectD を構成する 

1. CollectD データを OMS Agent for Linux にルーティングするには、CollectD の構成ディレクトリに `oms.conf` を追加する必要があります。 このファイルの移動先は、使用しているマシンの Linux ディストリビューションによって異なります。

    CollectD config ディレクトリが /etc/collectd.d/ に配置されている場合:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    CollectD config ディレクトリが /etc/collectd/collectd.conf.d/ に配置されている場合:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >5.5 より前の CollectD バージョンでは、上に示すように `oms.conf` 内のタグを変更する必要があります。
    >

2. collectd.conf を目的のワークスペースの omsagent 構成ディレクトリにコピーします。

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. 次のコマンドを使用して、CollectD と OMS Agent for Linux を再起動します。

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>CollectD メトリックから Log Analytics スキーマへの変換
既に OMS Agent for Linux によって収集されたインフラストラクチャ メトリックと、CollectD によって収集された新しいメトリックの間で使い慣れたモデルを維持するために、次のスキーマ マッピングが使用されます。

| CollectD メトリックのフィールド | Log Analytics のフィールド |
|:--|:--|
| host | コンピューター |
| プラグイン | なし |
| plugin_instance | インスタンス名<br>**plugin_instance** が *null* である場合、InstanceName="*_Total*" |
| type | ObjectName |
| type_instance | CounterName<br>**type_instance** が *null* である場合、CounterName=**blank** |
| dsnames[] | CounterName |
| dstypes | なし |
| values[] | CounterValue |

## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。 
* [カスタム フィールド](log-analytics-custom-fields.md) を使用して、syslog レコードのデータを個別のフィールドに解析します。


