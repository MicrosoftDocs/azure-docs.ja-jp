---
title: Azure Sentinel プレビューに Fortinet データを接続する | Microsoft Docs
description: Azure Sentinel に Fortinet データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: f3ab4861e874074e7de059c7c50064d53749748c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611297"
---
# <a name="connect-your-fortinet-appliance"></a>お使いの Fortinet アプライアンスを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 運用環境のワークロード用にはお勧めしません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

ログ ファイルを Syslog 共通イベント形式 (CEF) として保存することで、Azure Sentinel を任意の Fortinet アプライアンスに接続できます。 Azure Sentinel との統合により、Fortinet のログ ファイル データ全体にわたる分析とクエリの発行を容易に実行することができます。 Azure Sentinel が CEF データを取り込む方法の詳細については、[CEF アプライアンスの接続](connect-common-event-format.md)に関する記事をご覧ください。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>手順 1:エージェントを使用して Fortinet アプライアンスを接続する

Fortinet アプライアンスを Azure Sentinel に接続するには、エージェントを、アプライアンスと Azure Sentinel の間の通信をサポートするための専用の VM またはオンプレミス マシンにデプロイします。 エージェントのデプロイは、自動または手動で行うことができます。 自動デプロイは、専用マシンが Azure 内に作成する新しい VM である場合にのみ使用できます。

既存の Azure VM、別のクラウド内の VM、またはオンプレミスのマシンに手動でエージェントをデプロイすることもできます。

両方のオプションのネットワーク図を表示するには、「[データ ソースの接続](connect-data-sources.md#agent-options)」を参照してください。

### <a name="deploy-the-agent-in-azure"></a>Azure でエージェントを展開する

1. Azure Sentinel portal で、 **[Data connectors]\(データ コネクタ\)** を選択して、お使いのアプライアンスの種類を選択します。

1. **[Linux Syslog agent configuration]** (Linux Syslog エージェント構成) の下で:
   - Azure Sentinel エージェントが事前にインストールされ、必要な構成がすべて含まれる新しいマシンを作成する場合は、前に説明したように、 **[Automatic deployment]\(自動展開\)** を選択します。 **[自動展開]**  >  **[Automatic agent deployment]\(エージェントの自動展開\)** を選択します。 ワークスペースに自動的に接続される専用 VM の購入ページが表示されます。 VM は**標準 D2s v3 (2 vCPU、8 GB メモリ)** であり、パブリック IP アドレスを持っています。
      1. **[カスタム デプロイ]** ページで詳細を指定し、ユーザー名とパスワードを入力して、使用条件に同意する場合は、VM を購入します。
      1. 接続ページに一覧表示されている設定を使用して、ログを送信するようにアプライアンスを構成します。 標準の共通イベント形式コネクタの場合、以下の設定を使用します。
         - プロトコル = UDP
         - ポート = 514
         - ファシリティ = Local-4
         - 形式 = CEF
   - 既存の VM を、Azure Sentinel エージェントをインストールする専用の Linux マシンとして使用する場合、 **[Manual deployment]\(手動配置\)** を選択します。 
      1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、 **[Azure Linux virtual machine]\(Azure Linux 仮想マシン\)** を選択します。 
      1. **[仮想マシン]** 画面で、使用するマシンを選択し、 **[接続]** を選択します。
      1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
      1. これらのコマンドをコピーし、アプライアンス上で実行します。
          - rsyslog.d を選択した場合:
              
            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに指示します。 次のコマンドを使用します。`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 次のコマンドを実行します。`sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`: {0} はワークスペース GUID に置き換える必要があります。
            1. 次のコマンドを使用して syslog デーモンを再起動します。`sudo service rsyslog restart`
             
          - syslog-ng を選択した場合:

              1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに指示します。 次のコマンドを使用します。`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 次のコマンドを実行します。`sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`: {0} はワークスペース GUID に置き換える必要があります。
              1. 次のコマンドを使用して syslog デーモンを再起動します。`sudo service syslog-ng restart`
      1. 次のコマンドを使用して Syslog エージェントを再起動します。`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します。`tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>オンプレミス Linux サーバーにエージェントをデプロイする

Azure を使用していない場合は、専用の Linux サーバーで実行するように Azure Sentinel エージェントを手動でデプロイします。

1. Azure Sentinel portal で、 **[Data connectors]\(データ コネクタ\)** を選択して、お使いのアプライアンスの種類を選択します。
1. 専用の Linux VM を作成するには、 **[Linux Syslog agent configuration]** \(Linux Syslog エージェント構成\) の下で **[Manual deployment]\(手動配置\)** を選択します。

    1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、 **[Non-Azure Linux machine]\(Azure ではない Linux マシン\)** を選択します。
    1. 開いた **[ダイレクト エージェント]** 画面で、 **[Linux 用エージェント]** を選択して、エージェントをダウンロードするか、次のコマンドを実行してエージェントを Linux マシンにダウンロードします。`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。
       1. これらのコマンドをコピーし、お使いのアプライアンス上で実行します。

          - rsyslog を選択した場合:

            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 次のコマンドを使用します。`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 次のコマンドを実行します。`sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`: {0} はワークスペース GUID に置き換える必要があります。
            1. 次のコマンドを使用して syslog デーモンを再起動します。`sudo service rsyslog restart`

          - syslog-ng を選択した場合:

            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに指示します。 次のコマンドを使用します。`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 次のコマンドを実行します。`sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`: {0} はワークスペース GUID に置き換える必要があります。
            1. 次のコマンドを使用して syslog デーモンを再起動します。`sudo service syslog-ng restart`

      1. 次のコマンドを使用して Syslog エージェントを再起動します。`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します。`tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>手順 2:Fortinet のログを Syslog エージェントに転送する

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように Fortinet を構成します。

1. お使いの Fortinet アプライアンスで CLI を開き、次のコマンドを実行します。

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - サーバーの **IP アドレス**を、エージェントの IP アドレスに置き換えます。
    - **facility_name** を、エージェントで構成したファシリティを使用するよう設定します。 既定で、エージェントはこれを local4 に設定します。
    - **syslog のポート**を **514** に設定するか、エージェントに設定されているポートに設定します。
    - 初期の FortiOS のバージョンで CEF 形式を有効にするため、コマンド セット **csv disable** を実行する必要が生じる場合があります。
 
   > [!NOTE] 
   > 詳細については、[Fortinet のドキュメント ライブラリ](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)を参照してください。 バージョンを選択してから、**Handbook** (ハンドブック) と **Log Message Reference** (ログ メッセージ リファレンス) を利用します。

 Azure Monitor Log Analytics で Fortinet イベントに関連するスキーマを使用する場合は、`CommonSecurityLog` を検索します。


## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 

1. 必ず正しいファシリティを使用してください。 ファシリティは、アプライアンスと Azure Sentinel で同じである必要があります。 どのファシリティ ファイルを Azure Sentinel で使用しているかをチェックし、ファイル `security-config-omsagent.conf` でそれを修正することができます。 

2. Syslog エージェントで、自分のログが正しいポートに到達していることを確認します。 Syslog エージェント マシンで次のコマンドを実行します: `tcpdump -A -ni any  port 514 -vv`。 このコマンドは、デバイスから Syslog マシンにストリーミングするログを表示します。 ソース アプライアンスからのログを、正しいポートと正しいファシリティで受信していることを確認します。

3. 送信するログが [RFC 5424](https://tools.ietf.org/html/rfc542) に準拠していることを確認します。

4. Syslog エージェントを実行しているコンピューターで、コマンド `netstat -a -n:` を使用して、ポート 514 および 25226 が開き、リッスン中であることを確認します。 このコマンドの詳しい使用方法については、[netstat(8) - Linux man ページ](https://linux.die.net/man/8/netstat)を参照してください。 正しくリッスンしている場合、次のように表示されます。

   ![Azure Sentinel ポート](./media/connect-cef/ports.png) 

5. ログを送信しているポート 514 をリッスンするように、デーモンが設定されていることを確認します。
    - rsyslog の場合:<br>ファイル `/etc/rsyslog.conf` に次の構成が含まれていることを確認します。

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      詳細については、[imudp:UDP Syslog の入力モジュール](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module)および [imtcp:TCP Syslog の入力モジュール](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)に関するページを参照してください。

   - syslog-ng の場合:<br>ファイル `/etc/syslog-ng/syslog-ng.conf` に次の構成が含まれていることを確認します。

           # source s_network {
            network( transport(UDP) port(514));
             };
     詳細については、[imudp:UDP Syslog の入力モジュール](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html)および [syslog-ng オープンソース エディション 3.16 - 管理者ガイド](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)に関するページを参照してください。

1. Syslog デーモンとエージェント間で通信が行われていることを確認します。 Syslog エージェント マシンで次のコマンドを実行します: `tcpdump -A -ni any  port 25226 -vv`。 このコマンドは、デバイスから Syslog マシンにストリーミングするログを表示します。 エージェントでログが受信されていることを確認します。

6. これらの両方のコマンドで正常な結果が表示された場合は、Log Analytics を調べて自分のログが到着しているかどうかを確認してください。 これらのアプライアンスからストリーミングされるすべてのイベントは、Log Analytics で `CommonSecurityLog` 型の下に未加工の形式で表示されます。

7. エラーがあるかどうか、またはログが到着しているかどうかを確認するには、`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log` を調べます。 ログ形式が一致していないというエラーがある場合は、`/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` に移動し、`security_events.conf` ファイルを調べます。 ログの形式が、このファイルで確認できる正規表現の形式と一致していることを確認します。

8. Syslog メッセージの既定のサイズが 2,048 バイト (2 KB) に制限されていることを確認します。 ログが長すぎる場合は、次のコマンドを使用して security_events.conf を更新します。`message_length_limit 4096`

10. Fortinet のログがエージェントで受信されていない場合は、使用している Syslog デーモンの種類に応じて次のコマンドを実行し、ファシリティを設定して、ログ内で Fortinet という語を検索するようログを設定します。
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     次のコマンドを使用して Syslog デーモンを再起動します。`sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     次のコマンドを使用して Syslog デーモンを再起動します。`sudo service syslog-ng restart`

## <a name="next-steps"></a>次の手順
この記事では、Fortinet アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

