---
title: Azure Monitor で Nagios と Zabbix のアラートを収集する | Microsoft Docs
description: Nagios と Zabbix は、オープン ソースの監視ツールです。 他のソースからのアラートと共に分析するために、これらのツールからのアラートを Azure Monitor 内に収集できます。  この記事では、これらのシステムからのアラートを収集するように Linux 用 Log Analytics エージェントを構成する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 0ed6747573edf4c059eb29d28107a22706c52856
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426191"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Linux 用 Log Analytics エージェントから Azure Monitor 内に Nagios と Zabbix からのアラートを収集する 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) と [Zabbix](https://www.zabbix.com/) は、オープン ソースの監視ツールです。 他のソースからのログ データと共に分析するために、これらのツールからのアラートを Azure Monitor 内に収集できます。  この記事では、これらのシステムからのアラートを収集するように Linux 用 Log Analytics エージェントを構成する方法について説明します。


> [!NOTE]
> [Azure Monitor によって作成されたアラート](alerts-overview.md)はログ データとは別に格納され、ログ クエリからアクセスすることはできません。

 
## <a name="prerequisites"></a>前提条件
Linux 用 Log Analytics エージェントは、Nagios バージョン 4.2.x までと Zabbix バージョン 2.x までのアラートの収集をサポートします。

## <a name="configure-alert-collection"></a>アラート収集を構成する

### <a name="configuring-nagios-alert-collection"></a>Nagios のアラート収集の構成
アラートを収集するには、Nagios サーバー上で次の手順を実行します。

1. ユーザー **omsagent** に Nagios ログ ファイル (`/var/log/nagios/nagios.log`) への読み取りアクセス権を付与します。 nagios.log ファイルがグループ `nagios` によって所有されていると仮定すると、ユーザー **omsagent** を **nagios** グループに追加できます。 

    sudo usermod -a -G nagios omsagent

2.  `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` にある構成ファイルを変更します。 次のエントリが存在し、コメント アウトされていないことを確認します。  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. omsagent デーモンを再起動します

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Zabbix のアラート収集の構成
Zabbix サーバーからのアラートを収集するには、ユーザーとパスワードを*クリア テキスト*で指定する必要があります。  理想的ではありませんが、読み取り専用のアクセス許可が付与されている Zabbix ユーザーを作成し、関連するアラームをキャッチできるようにすることをお勧めします。

アラートを収集するには、Nagios サーバー上で次の手順を実行します。

1. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` にある構成ファイルを変更します。 次のエントリが存在し、コメント アウトされていないことを確認します。ユーザー名とパスワードを Zabbix 環境の値に変更します。

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. omsagent デーモンを再起動します

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>アラート レコード
Azure Monitor で[ログ検索](../log-query/log-query-overview.md)を使用して、Nagios と Zabbix からアラート レコードを取得できます。

### <a name="nagios-alert-records"></a>Nagios のアラート レコード

Nagios によって収集されたアラート レコードには、**アラート**の**種類**と **Nagios** の **SourceSystem** があります。  これらのレコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| `Type` |*アラート:* |
| `SourceSystem` |*Nagios* |
| `AlertName` |アラートの名前。 |
| `AlertDescription` | アラートの説明。 |
| `AlertState` | サービスまたはホストの状態。<br><br>OK<br>WARNING<br>UP<br>DOWN |
| `HostName` | アラートを作成したホストの名前。 |
| `PriorityNumber` | アラートの優先度。 |
| `StateType` | アラートの状態の種類。<br><br>SOFT - 再確認されていない問題。<br>HARD - 指定された回数だけ再確認された問題。  |
| `TimeGenerated` |アラートが作成された日付と時刻。 |


### <a name="zabbix-alert-records"></a>Zabbix のアラート レコード
Zabbix によって収集されたアラート レコードには、**アラート**の**種類**と **Zabbix** の **SourceSystem** があります。  これらのレコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| `Type` |*アラート:* |
| `SourceSystem` |*Zabbix* |
| `AlertName` | アラートの名前。 |
| `AlertPriority` | アラートの重大度。<br><br>未分類<br>情報<br>warning<br>average<br>高<br>障害  |
| `AlertState` | アラートの状態。<br><br>0 - 状態は最新です。<br>1 - 状態は不明です。  |
| `AlertTypeNumber` | アラートによって複数の問題イベントが生成される場合があるかどうかを指定します。<br><br>0 - 状態は最新です。<br>1 - 状態は不明です。    |
| `Comments` | アラートのその他のコメント。 |
| `HostName` | アラートを作成したホストの名前。 |
| `PriorityNumber` | アラートの重大度を示す値。<br><br>0 - 未分類<br>1 - 情報<br>2 - 警告<br>3 - 平均<br>4 - 高<br>5 - 障害 |
| `TimeGenerated` |アラートが作成された日付と時刻。 |
| `TimeLastModified` |アラートの状態が最後に変更された日付と時刻。 |


## <a name="next-steps"></a>次の手順
* Azure Monitor の[アラート](alerts-overview.md)について確認します。
* [ログ クエリ](../log-query/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。 
