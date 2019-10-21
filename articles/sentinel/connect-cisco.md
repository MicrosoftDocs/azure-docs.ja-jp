---
title: Azure Sentinel に Cisco データを接続する | Microsoft Docs
description: Azure Sentinel に Cisco データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6218d3a53df38e938b7d7805c45facd30f850105
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240156"
---
# <a name="connect-your-cisco-asa-appliance"></a>Cisco ASA アプライアンスを接続する 



Azure Sentinel はあらゆる Cisco ASA アプライアンスに接続できます。 Cisco ASA は、データ インジェストのために Azure Sentinel とネイティブ統合されます。そのため、ご利用の Cisco アプライアンスでログが CEF として保存されなくても、Azure Sentinel で、CEF ログと同じ方法でログが取り込まれます。 Azure Sentinel との統合により、Cisco ASA のすべてのログ ファイル データを簡単に分析および照会できます。 

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地域に格納されます。

## <a name="step-1-connect-your-cisco-asa-appliance-using-an-agent"></a>手順 1:エージェントを使用してお使いの Cisco ASA アプライアンスを接続する

お使いの Cisco ASA アプライアンスを Azure Sentinel に接続するには、エージェントを専用のマシン (VM またはオンプレミス) に展開して、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 

または、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントをデプロイすることもできます。

> [!NOTE]
> 組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 

両方のオプションのネットワーク図を表示するには、「[データ ソースの接続](connect-data-sources.md)」を参照してください。

### <a name="deploy-the-agent-on-your-machine"></a>マシンにエージェントをデプロイする

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Cisco ASA]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。 

1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** で、マシンの種類として Azure またはオンプレミスを選択します。 
1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、 **[接続]** をクリックします。
1. **[Download and install agent for Azure Linux virtual machines]\(Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、マシンを選択し、 **[接続]** をクリックします。 **[Download and install agent for non-Azure Linux virtual machines]\(Azure Linux 以外の仮想マシン用のエージェントをダウンロードしてインストールする\)** を選択した場合は、 **[ダイレクトエージェント]** 画面で、 **[Linux 用エージェントのダウンロードとオンボード]** の下にあるスクリプトを実行します。
1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
1. これらのコマンドをコピーし、アプライアンス上で実行します。
    - rsyslog.d を選択した場合:
              
       1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            
      1. syslog デーモン `sudo service rsyslog restart` を再起動します。
             
    - syslog-ng を選択した場合:

        1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
        2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。

        3. syslog デーモン `sudo service syslog-ng restart` を再起動します。
1. 次のコマンドを使用して、Syslog エージェントを再起動します: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します。`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-cisco-asa-logs-to-the-syslog-agent"></a>手順 2:Cisco ASA のログを Syslog エージェントに転送する

Cisco ASA は CEF をサポートしていないため、ログは Syslog として送信され、Azure Sentinel エージェントはそれらを CEF ログのように解析する方法を認識しています。 Syslog のエージェントを介して Azure ワークスペースに Syslog メッセージを転送するように Cisco ASA を構成します。

[外部 Syslog サーバーに Syslog メッセージを送信する](https://aka.ms/asi-syslog-cisco-forwarding)方法に関するページに進み、指示に従って接続を設定します。 入力を求められたら、次のパラメーターを使用します。
- **port** を 514 またはエージェントで設定したポートに設定します。
- **syslog_ip** をエージェントの IP アドレスに設定します。
- **logging facility** をエージェントで設定した機能に設定します。 既定で、エージェントによって機能が 4 に設定されます。

Log Analytics で Cisco イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

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




## <a name="next-steps"></a>次の手順
このドキュメントでは、Cisco ASA アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

