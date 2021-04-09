---
title: Azure Monitor で Log Analytics エージェントを使用して Syslog データ ソースを収集する
description: Syslog は、Linux に共通のイベント ログ プロトコルです。 この記事では、Log Analytics の Syslog メッセージの収集を構成する方法と作成されるレコードの詳細について説明します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: e82e74f4cd325444221bbd2e1c060b7cd2f5c6c7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036735"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Log Analytics エージェントを使用して Syslog データ ソースを収集する
Syslog は、Linux に共通のイベント ログ プロトコルです。 アプリケーションは、ローカル コンピューターへの保存または Syslog コレクターへの配信が可能なメッセージを送信します。 Linux 用 Log Analytics エージェントがインストールされている場合は、エージェントにメッセージを転送するローカル Syslog デーモンが構成されます。 エージェントは Azure Monitor にメッセージを送信し、そこで対応するレコードが作成されます。  

> [!IMPORTANT]
> この記事では、Azure Monitor で使用されるエージェントの 1 つである [Log Analytics エージェント](./log-analytics-agent.md)を使用して Syslog イベントを収集する方法について説明します。 他のエージェントは異なるデータを収集し、異なる方法で構成されます。 使用可能なエージェントとそれらが収集できるデータの一覧については、「[Azure Monitor エージェントの概要](../agents/agents-overview.md)」を参照してください。

> [!NOTE]
> Azure Monitor では、rsyslog または syslog-ng によって送信されたメッセージの収集がサポートされています。rsyslog は既定のデーモンです。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、 [rsyslog デーモン](http://rsyslog.com) をインストールし、sysklog を置き換えるように構成する必要があります。
>
>

![Syslog collection](media/data-sources-syslog/overview.png)

Syslog コレクターでは、次のファシリティがサポートされています。

* kern
* user
* mail
* daemon
* auth
* syslog
* lpr
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

その他のファシリティについては、Azure Monitor で[カスタム ログ データ ソースを構成](data-sources-custom-logs.md)します。
 
## <a name="configuring-syslog"></a>Syslog の構成
Linux 用 Log Analytics エージェントは、構成で指定されているファシリティと重大度のイベントだけを収集します。 Azure Portal を通じて、または Linux エージェントで構成ファイルを管理することによって、Syslog を構成できます。

### <a name="configure-syslog-in-the-azure-portal"></a>Azure Portal での Syslog の構成
Log Analytics ワークスペースの [[エージェント構成] メニュー](../agents/agent-data-sources.md#configuring-data-sources)から Syslog を構成します。 この構成は、各 Linux エージェントの構成ファイルに配信されます。

**[Add facility]\(ファシリティの追加\)** をクリックして新しいファシリティを追加できます。 各ファシリティについて、選択した重大度のメッセージのみが収集されます。  各ファシリティで収集する重大度のチェック ボックスをオンにします。 メッセージをフィルター処理するための追加条件を指定することはできません。

[![Configure Syslog](media/data-sources-syslog/configure.png)](media/data-sources-syslog/configure.png#lightbox)

既定では、すべての構成変更はすべてのエージェントに自動的にプッシュされます。 各 Linux エージェントで Syslog を手動で構成する場合は、*[下の構成をコンピューターに適用する]* チェック ボックスをオフにします。

### <a name="configure-syslog-on-linux-agent"></a>Linux エージェントでの Syslog の構成
[Linux クライアントに Log Analytics エージェントがインストールされている](../vm/quick-collect-linux-computer.md)場合は、収集されるメッセージのファシリティと重大度を定義する既定の syslog 構成ファイルがインストールされます。 このファイルを修正して、構成を変更することができます。 クライアントにインストールされている Syslog デーモンによって、構成ファイルは異なります。

> [!NOTE]
> syslog 構成を編集した場合、変更を有効にするには、syslog デーモンを再起動する必要があります。
>
>

#### <a name="rsyslog"></a>rsyslog
rsyslog の構成ファイルは、 **/etc/rsyslog.d/95-omsagent.conf** にあります。 既定の内容を以下に示します。 これは、ローカル エージェントから送信された、すべてのファシリティの警告レベル以上の syslog メッセージを収集します。

```config
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
```

ファシリティを削除するには、構成ファイルの該当セクションを削除します。 ファシリティのエントリを変更することで、特定のファシリティで収集される重大度を制限することができます。 たとえば、ユーザー ファシリティを重大度がエラー以上のメッセージに制限するには、構成ファイルの該当行を次のように変更します。

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog-ng
syslog-ng の構成ファイルは、**/etc/syslog-ng/syslog-ng.conf** にあります。  既定の内容を以下に示します。 これは、ローカル エージェントから送信された、すべてのファシリティのすべての重大度の syslog メッセージを収集します。   

```config
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
```

ファシリティを削除するには、構成ファイルの該当セクションを削除します。 リストから重大度を削除することで、特定のファシリティで収集される重大度の制限を変更することができます。  たとえば、ユーザー ファシリティをアラートとクリティカルのメッセージだけに制限するには、構成ファイルの該当セクションを次のように変更します。

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>追加の Syslog ポートからデータを収集する
Log Analytics エージェントは、ポート 25224 でローカル クライアント上の Syslog メッセージを待ち受けます。  エージェントをインストールすると、既定の syslog 構成が適用され、次の場所で見つかります。

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

2 つの構成ファイルを作成することでポート番号を変更できます: FluentD 構成ファイルと rsyslog または syslog-ng ファイル (インストールしている Syslog デーモンにより決まります)。  

* FluentD 構成ファイルは `/etc/opt/microsoft/omsagent/conf/omsagent.d` にある新しいファイルです。**port** エントリの値をカスタム ポート番号に変更します。

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* rsyslog の場合、`/etc/rsyslog.d/` に新しい構成ファイルを作成し、値 %SYSLOG_PORT% をカスタム ポート番号に変更する必要があります。  

    > [!NOTE]
    > 構成ファイル `95-omsagent.conf` でこの値を変更すると、エージェントが既定の構成を適用したときに上書きされます。
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* syslog-ng 構成は下のサンプル構成をコピーして変更し、`/etc/syslog-ng/` にある syslog-ng.conf 構成ファイルの終わりに変更したカスタム設定を追加する必要があります。 既定のラベルである **%WORKSPACE_ID%_oms** または **%WORKSPACE_ID_OMS** は **使用しない** でください。変更を区別するために、カスタム ラベルを定義してください。  

    > [!NOTE]
    > 構成ファイルの既定値を変更すると、エージェントが既定の構成を適用したときに上書きされます。
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

変更の完了後、構成変更を適用するために Syslog と Log Analytics エージェント サービスを再起動する必要があります。   

## <a name="syslog-record-properties"></a>Syslog レコードのプロパティ
Syslog レコードの型は **Syslog** になり、次の表に示すプロパティがあります。

| プロパティ | 説明 |
|:--- |:--- |
| Computer |イベントが収集されたコンピューター。 |
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
| syslog |すべての Syslog です。 |
| Syslog &#124; where SeverityLevel == "error" |重大度がエラーであるすべての Syslog レコードです。 |
| Syslog &#124; summarize AggregatedValue = count() by Computer |コンピューターごとの Syslog レコードの数です。 |
| Syslog &#124; summarize AggregatedValue = count() by Facility |ファシリティごとの Syslog レコードの数です。 |

## <a name="next-steps"></a>次のステップ
* [ログ クエリ](../logs/log-query-overview.md)について学習し、データ ソースとソリューションから収集されたデータを分析します。
* [カスタム フィールド](../logs/custom-fields.md) を使用して、syslog レコードのデータを個別のフィールドに解析します。
* [Linux エージェントを構成](../vm/quick-collect-linux-computer.md) して、他の種類のデータを収集します。