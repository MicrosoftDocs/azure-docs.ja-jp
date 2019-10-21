---
title: Azure Sentinel に Fortinet データを接続する | Microsoft Docs
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
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 20079fd0c95da3e3aec9518f194ea39561a5e662
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240696"
---
# <a name="connect-your-fortinet-appliance"></a>お使いの Fortinet アプライアンスを接続する



ログ ファイルを Syslog 共通イベント形式 (CEF) として保存することで、Azure Sentinel を任意の Fortinet アプライアンスに接続できます。 Azure Sentinel との統合により、Fortinet のログ ファイル データ全体にわたる分析とクエリの発行を容易に実行することができます。 Azure Sentinel が CEF データを取り込む方法の詳細については、[CEF アプライアンスの接続](connect-common-event-format.md)に関する記事をご覧ください。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>手順 1:エージェントを使用して Fortinet アプライアンスを接続する

Fortinet アプライアンスを Azure Sentinel に接続するには、エージェントを、アプライアンスと Azure Sentinel の間の通信をサポートするための専用の VM またはオンプレミス マシンにデプロイします。 

既存の Azure VM、別のクラウド内の VM、またはオンプレミスのマシンに手動でエージェントをデプロイすることもできます。

> [!NOTE]
> 組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 

両方のオプションのネットワーク図を表示するには、「[データ ソースの接続](connect-data-sources.md#agent-options)」を参照してください。

### <a name="deploy-the-agent"></a>エージェントをデプロイする

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Fortinet]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** で、マシンの種類として Azure またはオンプレミスを選択します。 
1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、 **[接続]** をクリックします。
1. **[Download and install agent for Azure Linux virtual machines]\(Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、マシンを選択し、 **[接続]** をクリックします。 **[Download and install agent for non-Azure Linux virtual machines]\(Azure Linux 以外の仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、 **[ダイレクトエージェント]** 画面で、 **[Linux 用エージェントのダウンロードとオンボード]** の下にあるスクリプトを実行します。
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

3. 送信するログが [RFC 3164](https://tools.ietf.org/html/rfc3164) に準拠していることを確認します。

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
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

