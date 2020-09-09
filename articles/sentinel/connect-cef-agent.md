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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: a7d7c7b7236841835866ccb7786e7e4eab767c1f
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565589"
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
- Linux マシンに Python がインストールされている必要があります。<br>`python -version` コマンドを使用して確認してください。
- Log Analytics エージェントをインストールする前に Linux マシンを Azure ワークスペースに接続することは避けてください。

## <a name="run-the-deployment-script"></a>展開スクリプトを実行する
 
1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** をクリックします。 コネクタの一覧で **[共通イベント形式 (CEF)]** タイルをクリックし、右下にある **[Open connector page]\(コネクタ ページを開く\)** ボタンをクリックします。 

1. **[1.2 Linux マシンへの CEF コレクターのインストール]** で、 **[Run the following script to install and apply the CEF collector]\(CEF コレクターをインストールして適用するには、次のスクリプトを実行します\)** の下にあるリンクをコピーします。または、次のテキストからコピーしてください。

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。

> [!NOTE]
> **同じマシンを使用してプレーンな Syslog *と* CEF メッセージの両方を転送する**
>
> このログ フォワーダー マシンを使用して [Syslog メッセージ](connect-syslog.md)と CEF を転送する予定であれば、Syslog および CommonSecurityLog テーブルへのイベントの重複を回避するために、以下を実行します。
>
> 1. CEF 形式でフォワーダーにログを送信する各ソース マシンで、Syslog 構成ファイルを編集し、CEF メッセージの送信に使用されているファシリティを削除する必要があります。 これで、CEF で送信されるファシリティは、Syslog で送信されません。 この方法の詳細については、「[Linux エージェントでの Syslog の構成](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent)」を参照してください。
>
> 1. これらのマシンで次のコマンドを実行して、エージェントと Azure Sentinel の Syslog 構成との同期を無効にする必要があります。 これで、前の手順で構成に加えた変更が上書きされなくなります。<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

[手順 2 に進みます:セキュリティソリューションを構成して、CEF メッセージを転送するようにします](connect-cef-solution-config.md) 。

## <a name="deployment-script-explained"></a>デプロイ スクリプトの説明

次に、デプロイ スクリプトによって実行されるアクションをコマンドごとに説明します。

syslog デーモンを選択して、適切な説明を表示してください。

# <a name="rsyslog-daemon"></a>[rsyslog デーモン](#tab/rsyslog)

1. **Log Analytics エージェントをダウンロードしてインストールする**

    - Log Analytics (OMS) Linux エージェントのインストール スクリプトをダウンロードします。<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Log Analytics エージェントをインストールします。<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Syslog デーモンを構成する**

    1. syslog 構成ファイル `/etc/rsyslog.conf` を使用して TCP 通信用のポート 514 を開きます。

    1. 特殊な構成ファイル `security-config-omsagent.conf` を syslog デーモン ディレクトリ `/etc/rsyslog.d/` に挿入して、CEF メッセージを TCP ポート 25226 で Log Analytics エージェントに転送するようにデーモンを構成します。

        `security-config-omsagent.conf` ファイルの内容は次のとおりです。

        ```console
        :rawmsg, regex, "CEF"|"ASA"
        *.* @@127.0.0.1:25226
        ```

1. **Syslog デーモンを再起動する**

    `service rsyslog restart`

1. **ポート 25226 でリッスンして、CEF メッセージを Azure Sentinel に転送するよう、Log Analytics エージェントの構成を設定する**

    1. Log Analytics エージェントの GitHub リポジトリから構成をダウンロードします。<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Log Analytics エージェントを再起動します。<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng デーモン](#tab/syslogng)

1. **Log Analytics エージェントをダウンロードしてインストールする**

    - Log Analytics (OMS) Linux エージェントのインストール スクリプトをダウンロードします。<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - Log Analytics エージェントをインストールします。<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **Syslog デーモンを構成する**

    1. syslog 構成ファイル `/etc/syslog-ng/syslog-ng.conf` を使用して TCP 通信用のポート 514 を開きます。

    1. 特殊な構成ファイル `security-config-omsagent.conf` を syslog デーモン ディレクトリ `/etc/syslog-ng/conf.d/` に挿入して、CEF メッセージを TCP ポート 25226 で Log Analytics エージェントに転送するようにデーモンを構成します。

        `security-config-omsagent.conf` ファイルの内容は次のとおりです。

        ```console
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Syslog デーモンを再起動する**

    `service syslog-ng restart`

1. **ポート 25226 でリッスンして、CEF メッセージを Azure Sentinel に転送するよう、Log Analytics エージェントの構成を設定する**

    1. Log Analytics エージェントの GitHub リポジトリから構成をダウンロードします。<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Log Analytics エージェントを再起動します。<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Log Analytics エージェントをデプロイして、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

