---
title: Azure Sentinel に Check Point データを接続する | Microsoft Docs
description: Azure Sentinel に Check Point データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 1bfa59e92ce2cde45b448174260396f4e93a6282
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240132"
---
# <a name="connect-your-check-point-appliance"></a>Check Point アプライアンスを接続する



ログ ファイルを Syslog CEF として保存することにより、Azure Sentinel を任意の Check Point アプライアンスに接続できます。 Azure Sentinel との統合により、Check Point から、ログ ファイル データにわたって分析およびクエリを簡単に実行できるようになります。 Azure Sentinel が CEF データを取り込む方法の詳細については、[CEF アプライアンスの接続](connect-common-event-format.md)に関する記事をご覧ください。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>手順 1:エージェントを使用した Check Point アプライアンスの接続

Check Point アプライアンスを Azure Sentinel に接続するには、エージェントを専用のマシン (VM またはオンプレミス) にデプロイして、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 

または、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントをデプロイすることもできます。

> [!NOTE]
> 組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 

両方のオプションのネットワーク図を表示するには、「[データ ソースの接続](connect-data-sources.md)」を参照してください。

### <a name="deploy-the-agent"></a>エージェントをデプロイする
1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Check Point]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** で、マシンの種類として Azure またはオンプレミスを選択します。 
1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、 **[接続]** をクリックします。
1. **[Download and install agent for Azure Linux virtual machines]\(Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、マシンを選択し、 **[接続]** をクリックします。 **[Download and install agent for non-Azure Linux virtual machines]\(Azure Linux 以外の仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、 **[ダイレクトエージェント]** 画面で、 **[Linux 用エージェントのダウンロードとオンボード]** の下にあるスクリプトを実行します。
 
1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
1. これらのコマンドをコピーし、アプライアンス上で実行します。
     - rsyslog.d を選択した場合:
            
       1. ファシリティ local_4 および "Check Point" をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
           
       2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
           
       1. syslog デーモン `sudo service rsyslog restart` を再起動します。
             
    - syslog-ng を選択した場合:

         1. ファシリティ local_4 および "Check Point" をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
         2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。

        3. syslog デーモン `sudo service syslog-ng restart` を再起動します。
2. 次のコマンドを使用して、Syslog エージェントを再起動します: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します。`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>手順 2:Syslog エージェントへの Check Point ログの転送

Syslog エージェントを介して Azure ワークスペースに CEF 形式で Syslog メッセージを転送するように Check Point アプライアンスを構成します。

1. 「[Check Point Log Export](https://aka.ms/asi-syslog-checkpoint-forwarding)」(Check Point ログのエクスポート) に移動します。
2. 下にスクロールして「**Basic Deployment**」(基本的なデプロイ) に移動し、次のガイドラインを使用して、接続を設定する指示に従います。
   - **Syslog ポート**を **514** に設定するか、エージェントに設定されているポートに設定します。
     - CLI で、**名前**と**ターゲット サーバーの IP アドレス**を、Syslog エージェント名と IP アドレスに置き換えます。
     - 形式を **CEF** に設定します。
3. R77.30 または R80.10 バージョンを使用している場合は、上にスクロールして「**Installations**」(インストール) に移動し、お使いのバージョン用の Log Exporter をインストールする指示に従います。
 
## <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. 必ず正しいファシリティを使用してください。 ファシリティは、アプライアンスと Azure Sentinel で同じである必要があります。 どのファシリティ ファイルを Azure Sentinel で使用しているかをチェックし、ファイル `security-config-omsagent.conf` でそれを修正することができます。 

2. Syslog エージェントで、自分のログが正しいポートに到達していることを確認します。 Syslog エージェント マシンで次のコマンドを実行します。`tcpdump -A -ni any  port 514 -vv` このコマンドは、デバイスから Syslog マシンにストリーミングするログを表示します。 正しいポートと正しい機能でソース アプライアンスからログを受信していることを確認してください。

3. 送信するログが [RFC 3164](https://tools.ietf.org/html/rfc3164) に準拠していることを確認します。

4. Syslog エージェントを実行しているコンピューターで、コマンド `netstat -a -n:` を使用して、これらのポート 514、25226 が開いており、リッスンしていることを確認します。 このコマンドの詳しい使用方法については、[netstat(8) - Linux man ページ](https://linux.die.net/man/8/netstat)を参照してください。 正しくリッスンしている場合、次のように表示されます。

   ![Azure Sentinel ポート](./media/connect-cef/ports.png) 

5. ログを送信しているポート 514 をリッスンするように、デーモンが設定されていることを確認します。
    - rsyslog の場合:<br>ファイル `/etc/rsyslog.conf` に次の構成が含まれていることを確認します。

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      詳細については、[imudp:UDP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) および [imtcp:TCP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module) に関するページを参照してください

   - syslog-ng の場合:<br>ファイル `/etc/syslog-ng/syslog-ng.conf` に次の構成が含まれていることを確認します。

           # source s_network {
            network( transport(UDP) port(514));
             };
    詳細については、「[syslog-ng Open Source Edition 3.16 - Administration Guide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)」 (syslog-ng オープンソース エディション 3.16 - 管理者ガイド) を参照してください。

1. Syslog デーモンとエージェント間で通信が行われていることを確認します。 Syslog エージェント マシンで次のコマンドを実行します。`tcpdump -A -ni any  port 25226 -vv` このコマンドは、デバイスから Syslog マシンにストリーミングするログを表示します。 エージェントでログが受信されていることを確認します。

6. これらの両方のコマンドで正常な結果が表示された場合は、Log Analytics を調べて自分のログが到着しているかどうかを確認してください。 これらのアプライアンスからストリーミングされるすべてのイベントは、Log Analytics で `CommonSecurityLog` 型の下に未加工の形式で表示されます。

7. エラーがあるかどうか、またはログが到着しているかどうかを確認するには、`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log` を調べます。 ログ形式の不一致エラーがある場合は、`/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` に移動してファイル `security_events.conf` を調べ、ログの正規表現形式がこのファイルのものと一致していることを確認します。

8. Syslog メッセージの既定のサイズが 2,048 バイト (2 KB) に制限されていることを確認します。 ログが長すぎる場合は、次のコマンドを使用して security_events.conf を更新します: `message_length_limit 4096`

4. 必ず次のコマンドを実行してください。
  
   - Syslog-ng を使用する場合は、これらのコマンドを実行します (これにより Syslog エージェントが再起動されることに注意してください)。

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        次のようにして Syslog デーモンを再起動します: `sudo service syslog-ng restart`

   - rsyslog を使用する場合は、これらのコマンドを実行します (これにより Syslog エージェントが再起動されることに注意してください)。 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     次のようにして Syslog デーモンを再起動します: `sudo service rsyslog restart`




## <a name="next-steps"></a>次の手順
このドキュメントでは、Check Point アプライアンスを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

