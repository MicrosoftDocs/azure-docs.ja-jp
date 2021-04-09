---
title: ログ フォワーダーをデプロイして CEF データを Azure Sentinel に接続する | Microsoft Docs
description: Azure Sentinel に CEF データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: ee28837d3e687d78b645a1ab18a9add1f8e57fcc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771262"
---
# <a name="step-1-deploy-the-log-forwarder"></a>手順 1:ログ フォワーダーをデプロイする


この手順では、ご利用のセキュリティ ソリューションから Azure Sentinel ワークスペースにログを転送する Linux マシンを指定して構成します。 このマシンは、オンプレミス環境の物理マシンや仮想マシン、Azure VM のほか、別のクラウドの VM でもかまいません。 用意されているリンクを使用し、指定したマシンで、次のタスクを実行するスクリプトを実行します。

- Linux 用 Log Analytics エージェント ("OMS エージェント" ともいいます) をインストールし、次の用途に構成します。
    - 組み込みの Linux Syslog デーモンからの CEF メッセージを TCP ポート 25226 でリッスンする
    - メッセージの解析とエンリッチが行われる Azure Sentinel ワークスペースに対し、TLS で安全にメッセージを送信する

- 組み込みの Linux Syslog デーモン (rsyslog.d/syslog-ng) を次の用途に構成します。
    - セキュリティ ソリューションからの Syslog メッセージを TCP ポート 514 でリッスンする
    - CEF として識別したメッセージだけを localhost の Log Analytics エージェントに TCP ポート 25226 を使用して転送する
 
## <a name="prerequisites"></a>前提条件

- 指定した Linux マシンに対する昇格されたアクセス許可 (sudo) が必要です。

- Linux マシンに **python 2.7** または **3** がインストールされている必要があります。<br>`python -version` コマンドを使用して確認してください。

- Log Analytics エージェントをインストールする前に Linux マシンを Azure ワークスペースに接続することは避けてください。

- Linux マシンには、少なくとも **4 つの CPU コアと 8 GB の RAM** が必要です。

    > [!NOTE]
    > - **rsyslog** デーモンを使用している 1 つのログ フォワーダー マシンには、収集される **最大 8500 EPS (1 秒あたりのイベント数)** のサポートされる容量があります。

- このプロセスのある時点で、ワークスペース ID とワークスペースの主キーが必要になる場合があります。 それらはワークスペース リソースの **[エージェント管理]** で確認できます。

## <a name="run-the-deployment-script"></a>展開スクリプトを実行する
 
1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** をクリックします。 コネクタの一覧で **[共通イベント形式 (CEF)]** タイルをクリックし、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。 

1. **[1.2 Linux マシンへの CEF コレクターのインストール]** で、 **[Run the following script to install and apply the CEF collector]\(CEF コレクターをインストールして適用するには、次のスクリプトを実行します\)** の下にあるリンクをコピーします。または、次のテキストからコピーしてください (プレースホルダーをワークスペース ID と主キーに置き換えます)。

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```

1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。
    - *[コンピューター]* フィールドのマッピングに関する問題を修正するためのコマンドを実行するよう指示するメッセージが表示される場合があります。 詳細については、[デプロイ スクリプトの説明](#mapping-command)を参照してください。

1. [手順 2 に進みます:CEF メッセージを送信するように、セキュリティ ソリューションを構成する](connect-cef-solution-config.md)に進みます。


> [!NOTE]
> **同じマシンを使用してプレーンな Syslog *と* CEF メッセージの両方を転送する**
>
> このログ フォワーダー マシンを使用して [Syslog メッセージ](connect-syslog.md)と CEF を転送する予定であれば、Syslog および CommonSecurityLog テーブルへのイベントの重複を回避するために、以下を実行します。
>
> 1. CEF 形式でフォワーダーにログを送信する各ソース マシンで、Syslog 構成ファイルを編集し、CEF メッセージの送信に使用されているファシリティを削除する必要があります。 これで、CEF で送信されるファシリティは、Syslog で送信されません。 この方法の詳細については、「[Linux エージェントでの Syslog の構成](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)」を参照してください。
>
> 1. これらのマシンで次のコマンドを実行して、エージェントと Azure Sentinel の Syslog 構成との同期を無効にする必要があります。 これで、前の手順で構成に加えた変更が上書きされなくなります。<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **TimeGenerated フィールドのソースの変更**
>
> - 既定では、Log Analytics エージェントでは、エージェントが Syslog デーモンからイベントを受信した時刻がスキーマの *TimeGenerated* フィールドに設定されます。 その結果、ソース システムでイベントが生成された時刻は Azure Sentinel に記録されません。
>
> - ただし、次のコマンドを実行すると、 `TimeGenerated.py` スクリプトをダウンロードして実行できます。 このスクリプトにより、エージェントによる受信時刻ではなくソース システムの元の時刻を *TimeGenerated* フィールドに設定するように Log Analytics エージェントが構成されます。
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>デプロイ スクリプトの説明

次に、デプロイ スクリプトによって実行されるアクションをコマンドごとに説明します。

syslog デーモンを選択して、適切な説明を表示してください。

# <a name="rsyslog-daemon"></a>[rsyslog デーモン](#tab/rsyslog)

1. **Log Analytics エージェントをダウンロードしてインストールする**

    - Log Analytics (OMS) Linux エージェントのインストール スクリプトをダウンロードします。

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics エージェントをインストールします。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **ポート 25226 でリッスンして、CEF メッセージを Azure Sentinel に転送するよう、Log Analytics エージェントの構成を設定する**

    - Log Analytics エージェントの GitHub リポジトリから構成をダウンロードします。

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog デーモンを構成する**

    - syslog 構成ファイル `/etc/rsyslog.conf` を使用して TCP 通信用のポート 514 を開きます。

    - 特殊な構成ファイル `security-config-omsagent.conf` を syslog デーモン ディレクトリ `/etc/rsyslog.d/` に挿入して、CEF メッセージを TCP ポート 25226 で Log Analytics エージェントに転送するようにデーモンを構成します。

        `security-config-omsagent.conf` ファイルの内容は次のとおりです。

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Syslog デーモンと Log Analytics エージェントを再起動する**

    - rsyslog デーモンを再起動します。
    
        ```bash
        service rsyslog restart
        ```

    - Log Analytics エージェントを再起動します。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. ***Computer* フィールドのマッピングが想定どおりになっていることを確認する**

    - 次のコマンドを使用して、Syslog ソースの *[コンピューター]* フィールドが、Log Analytics エージェントで正しくマップされていることを確認します。 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>マッピングに問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドは、正しいマッピングを確認して、エージェントを再起動します。
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng デーモン](#tab/syslogng)

1. **Log Analytics エージェントをダウンロードしてインストールする**

    - Log Analytics (OMS) Linux エージェントのインストール スクリプトをダウンロードします。

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics エージェントをインストールします。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **ポート 25226 でリッスンして、CEF メッセージを Azure Sentinel に転送するよう、Log Analytics エージェントの構成を設定する**

    - Log Analytics エージェントの GitHub リポジトリから構成をダウンロードします。

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog デーモンを構成する**

    - syslog 構成ファイル `/etc/syslog-ng/syslog-ng.conf` を使用して TCP 通信用のポート 514 を開きます。

    - 特殊な構成ファイル `security-config-omsagent.conf` を syslog デーモン ディレクトリ `/etc/syslog-ng/conf.d/` に挿入して、CEF メッセージを TCP ポート 25226 で Log Analytics エージェントに転送するようにデーモンを構成します。

        `security-config-omsagent.conf` ファイルの内容は次のとおりです。

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Syslog デーモンと Log Analytics エージェントを再起動する**

    - syslog-ng デーモンを再起動します。
    
        ```bash
        service syslog-ng restart
        ```

    - Log Analytics エージェントを再起動します。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. ***Computer* フィールドのマッピングが想定どおりになっていることを確認する**

    - 次のコマンドを使用して、Syslog ソースの *[コンピューター]* フィールドが、Log Analytics エージェントで正しくマップされていることを確認します。 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>マッピングに問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドは、正しいマッピングを確認して、エージェントを再起動します。
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Log Analytics エージェントをデプロイして、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [Cef および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)についての説明。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
