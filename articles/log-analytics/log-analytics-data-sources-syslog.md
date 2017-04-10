---
title: "OMS Log Analytics の Syslog メッセージの収集と分析 | Microsoft Docs"
description: "Syslog は、Linux に共通のイベント ログ プロトコルです。   この記事では、Log Analytics の Syslog メッセージの収集を構成する方法と OMS リポジトリに作成されるレコードの詳細について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 653696779e612726ed5b75829a5c6ed2615553d7
ms.openlocfilehash: 6e92a79c0b7ea35f110c779922255d6ddc93ed7c
ms.lasthandoff: 01/24/2017


---
# <a name="syslog-data-sources-in-log-analytics"></a>Log Analytics の Syslog データ ソース
Syslog は、Linux に共通のイベント ログ プロトコルです。  アプリケーションは、ローカル コンピューターへの保存または Syslog コレクターへの配信が可能なメッセージを送信します。  OMS Agent for Linux がインストールされている場合は、エージェントにメッセージを転送するローカル Syslog デーモンが構成されます。  エージェントは Log Analytics にメッセージを送信し、そこで対応するレコードが OMS リポジトリに作成されます。  

> [!NOTE]
> Log Analytics では、rsyslog または syslog-ng によって送信されたメッセージの収集がサポートされています。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、 [rsyslog デーモン](http://rsyslog.com) をインストールし、sysklog を置き換えるように構成する必要があります。
> 
> 

![Syslog collection](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Syslog の構成
OMS Agent for Linux は、構成で指定されているファシリティと重大度を持つイベントだけを収集します。  OMS ポータルを通じて、または Linux エージェントで構成ファイルを管理することによって、Syslog を構成できます。

### <a name="configure-syslog-in-the-oms-portal"></a>OMS ポータルでの Syslog の構成
[Log Analytics の [設定] の [データ] メニュー](log-analytics-data-sources.md#configuring-data-sources)で Syslog を構成します。  この構成は、各 Linux エージェントの構成ファイルに配信されます。

新しいファシリティを追加するには、その名前を入力して、 **+**で Syslog を構成します。  各ファシリティについて、選択した重大度のメッセージのみが収集されます。  各ファシリティで収集する重大度のチェック ボックスをオンにします。  メッセージをフィルター処理するための追加条件を指定することはできません。

![Configure Syslog](media/log-analytics-data-sources-syslog/configure.png)

既定では、すべての構成変更はすべてのエージェントに自動的にプッシュされます。  各 Linux エージェントで Syslog を手動で構成する場合は、 *[Apply below configuration to my Linux machines (Linux コンピューターに以下の構成を適用する)]*チェック ボックスをオフにします。

### <a name="configure-syslog-on-linux-agent"></a>Linux エージェントでの Syslog の構成
[Linux クライアントに OMS エージェントがインストールされている](log-analytics-linux-agents.md)場合は、収集されるメッセージのファシリティと重大度を定義する既定の syslog 構成ファイルがインストールされます。  このファイルを修正して、構成を変更することができます。  クライアントにインストールされている Syslog デーモンによって、構成ファイルは異なります。

> [!NOTE]
> syslog 構成を編集した場合、変更を有効にするには、syslog デーモンを再起動する必要があります。
> 
> 

#### <a name="rsyslog"></a>rsyslog
rsyslog の構成ファイルは、 **/etc/rsyslog.d/95-omsagent.conf**にあります。  既定の内容を以下に示します。  これは、ローカル エージェントから送信された、すべてのファシリティの警告レベル以上の syslog メッセージを収集します。

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

ファシリティを削除するには、構成ファイルの該当セクションを削除します。  ファシリティのエントリを変更することで、特定のファシリティで収集される重大度を制限することができます。  たとえば、ユーザー ファシリティを重大度がエラー以上のメッセージに制限するには、構成ファイルの該当行を次のように変更します。

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
syslog-ng の構成ファイルは、**/etc/syslog-ng/syslog-ng.conf** にあります。  既定の内容を以下に示します。  これは、ローカル エージェントから送信された、すべてのファシリティのすべての重大度の syslog メッセージを収集します。   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

ファシリティを削除するには、構成ファイルの該当セクションを削除します。  リストから重大度を削除することで、特定のファシリティで収集される重大度の制限を変更することができます。  たとえば、ユーザー ファシリティをアラートとクリティカルのメッセージだけに制限するには、構成ファイルの該当セクションを次のように変更します。

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Syslog ポートの変更
OMS エージェントは、ポート 25224 でローカル クライアント上の Syslog メッセージをリッスンします。  このポートを変更するには、 **/etc/opt/microsoft/omsagent/conf/omsagent.conf**にある OMS エージェント構成ファイルに次のセクションを追加します。  **port** エントリの 25224 を、目的のポート番号に置き換えます。  Syslog デーモンの構成ファイルも、このポートにメッセージを送信するように変更する必要があることに注意してください。

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>データ収集
OMS エージェントは、ポート 25224 でローカル クライアント上の Syslog メッセージをリッスンします。 Syslog デーモンの構成ファイルは、アプリケーションから送信された Syslog メッセージをこのポートに転送します (このポートは、Log Analytics がそれらのメッセージを収集する場所です)。

## <a name="syslog-record-properties"></a>Syslog レコードのプロパティ
Syslog レコードの型は **Syslog** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| コンピューター |イベントが収集されたコンピューター。 |
| Facility |メッセージを生成したシステムの部分を定義します。 |
| HostIP |メッセージを送信するシステムの IP アドレスです。 |
| HostName |メッセージを送信するシステムの名前です。 |
| SeverityLevel |イベントの重大度レベルです。 |
| SyslogMessage |メッセージのテキストです。 |
| ProcessID |メッセージを生成したプロセスの ID です。 |
| EventTime |イベントが生成された日時です。 |

## <a name="log-queries-with-syslog-records"></a>Syslog レコードのログ クエリ
次の表は、Syslog レコードを取得するログ クエリのさまざまな例をまとめたものです。

| クエリ | 説明 |
|:--- |:--- |
| Type=Syslog |すべての Syslog です。 |
| Type=Syslog SeverityLevel=error |重大度がエラーであるすべての Syslog レコードです。 |
| Type=Syslog &#124; measure count() by Computer |コンピューターごとの Syslog レコードの数です。 |
| Type=Syslog &#124; measure count() by Facility |ファシリティごとの Syslog レコードの数です。 |

## <a name="next-steps"></a>次のステップ
* [ログ検索](log-analytics-log-searches.md) について学習し、データ ソースとソリューションから収集されたデータを分析します。 
* [カスタム フィールド](log-analytics-custom-fields.md) を使用して、syslog レコードのデータを個別のフィールドに解析します。
* [Linux エージェントを構成](log-analytics-linux-agents.md) して、他の種類のデータを収集します。 


