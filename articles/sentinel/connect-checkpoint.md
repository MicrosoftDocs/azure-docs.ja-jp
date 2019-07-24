---
title: Azure Sentinel プレビューに Check Point データを接続する | Microsoft Docs
description: Azure Sentinel に Check Point データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 664b09a8ad0cb7d06019281869e390a465637c00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489178"
---
# <a name="connect-your-check-point-appliance"></a>Check Point アプライアンスを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

ログ ファイルを Syslog CEF として保存することにより、Azure Sentinel を任意の Check Point アプライアンスに接続できます。 Azure Sentinel との統合により、Check Point から、ログ ファイル データにわたって分析およびクエリを簡単に実行できるようになります。 Azure Sentinel が CEF データを取り込む方法の詳細については、[CEF アプライアンスの接続](connect-common-event-format.md)に関する記事をご覧ください。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>手順 1:エージェントを使用した Check Point アプライアンスの接続

Check Point アプライアンスを Azure Sentinel に接続するには、エージェントを専用のマシン (VM またはオンプレミス) にデプロイして、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 エージェントのデプロイは、自動または手動で行うことができます。 自動デプロイは、専用マシンが Azure に作成中の新しい VM である場合にのみ使用できます。 

または、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントをデプロイすることもできます。

両方のオプションのネットワーク図を表示するには、「[データ ソースの接続](connect-data-sources.md)」を参照してください。

### <a name="deploy-the-agent-in-azure"></a>Azure でエージェントを展開する

1. Azure Sentinel portal で、**[Data connectors]\(データ コネクタ\)** をクリックして、お使いのアプライアンスの種類を選択します。 

1. **[Linux Syslog agent configuration]** (Linux Syslog エージェント構成) の下で:
   - Azure Sentinel エージェントが事前インストールされた、すべての必要な構成が含まれた新しいマシンを作成するには、前述の **[Automatic deployment]\(自動展開\)** を選択します。 **[自動デプロイ]** を選択して、**[Automatic agent deployment]** (エージェントの自動デプロイ) をクリックします。 これにより、ワークスペースに自動的に接続する専用 VM の購入ページが表示されます。 VM は**標準 D2s v3 (2 vCPU、8 GB メモリ)** であり、パブリック IP アドレスを持っています。
     1. **[カスタム デプロイ]** ページで詳細を入力し、ユーザー名とパスワードを選択し、使用条件に同意する場合は、VM を購入します。
      
        1. Syslog エージェント マシンでこれらのコマンドを実行し、Check Point のすべてのログが Azure Sentinel エージェントにマップされるようにします。
           - Syslog-ng を使用する場合は、これらのコマンドを実行します (これにより Syslog エージェントが再起動されることに注意してください)。
            
                sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"

             次のようにして Syslog デーモンを再起動します: `sudo service syslog-ng restart`
           - rsyslog を使用する場合は、これらのコマンドを実行します (これにより Syslog エージェントが再起動されることに注意してください)。
                    
                 sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
             次のようにして Syslog デーモンを再起動します: `sudo service rsyslog restart`

   - Azure Sentinel エージェントのインストール先となる専用の Linux マシンとして既存の VM を使用する場合、**[Manual deployment]** (手動デプロイ) を選択します。 
      1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、**[Azure Linux virtual machine]\(Azure Linux 仮想マシン\)** を選択します。 
      1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、**[接続]** をクリックします。
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

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>オンプレミス Linux サーバーにエージェントをデプロイする

Azure を使用していない場合は、専用の Linux サーバーで実行するように Azure Sentinel エージェントを手動でデプロイします。

1. 専用の Linux VM を作成するには、**[Linux Syslog agent configuration]** (Linux Syslog エージェント構成) の下で **[Manual deployment]** (手動デプロイ) を選択します。
   1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、**[Non-Azure Linux machine]\(Azure ではない Linux マシン\)** を選択します。 
   1. 開いた **[ダイレクト エージェント]** 画面で、**[Linux 用エージェント]** を選択して、エージェントをダウンロードするか、次のコマンドを実行してお使いの Linux マシンにダウンロードします。`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
      1. これらのコマンドをコピーし、アプライアンス上で実行します。
         - **rsyslog** を選択した場合:
           1. ファシリティ local_4 および "Check Point" をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
           3. 次のようにして Syslog デーモンを再起動します: `sudo service rsyslog restart`
         - **syslog-ng** を選択した場合:
            1. ファシリティ local_4 および "Check Point" をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            3. syslog デーモン `sudo service syslog-ng restart` を再起動します。
      1. 次のコマンドを使用して、Syslog エージェントを再起動します: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
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

1. Syslog エージェントで、自分のログが正しいポートに到達していることを確認します。 Syslog エージェント マシンで次のコマンドを実行します:`tcpdump -A -ni any  port 514 -vv` このコマンドでは、デバイスから Syslog マシンにストリーミングされるログを表示します。ログが、正しいポートとファシリティでソース アプライアンスから受信されていることを確認します。
2. Syslog デーモンとエージェント間で通信が行われていることを確認します。 Syslog エージェント マシンで次のコマンドを実行します:`tcpdump -A -ni any  port 25226 -vv` このコマンドによって、デバイスから Syslog コンピューターにストリーミングされるログが表示されます。ログがエージェント上でも受信されていることを確認します。
3. これらの両方のコマンドで正常な結果が表示された場合は、Log Analytics を調べて自分のログが到着しているかどうかを確認してください。 これらのアプライアンスからストリーミングされるすべてのイベントは、Log Analytics で `CommonSecurityLog` タイプの下に未加工の形式で表示されます。

4. 必ず次のコマンドを実行してください。
  
   - Syslog-ng を使用する場合は、これらのコマンドを実行します (これにより Syslog エージェントが再起動されることに注意してください)。

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        次のようにして Syslog デーモンを再起動します: `sudo service syslog-ng restart`

   - rsyslog を使用する場合は、これらのコマンドを実行します (これにより Syslog エージェントが再起動されることに注意してください)。 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     次のようにして Syslog デーモンを再起動します: `sudo service rsyslog restart`

1. エラーがあるかどうか、またはログが到着しているかどうかを確認するには、`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log` を調べます
4. 自分の Syslog メッセージの既定のサイズが 2048 バイト (2 KB) に制限されていることを確認します。 ログが長すぎる場合は、次のコマンドを使用して security_events.conf を更新します: `message_length_limit 4096`



## <a name="next-steps"></a>次の手順
このドキュメントでは、Check Point アプライアンスを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

