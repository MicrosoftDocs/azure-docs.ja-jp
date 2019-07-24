---
title: Azure Sentinel Preview に CEF データを接続する | Microsoft Docs
description: Azure Sentinel に CEF データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: rkarlin
ms.openlocfilehash: 18eb305beb79913713898b939ef840ca9ffab014
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489401"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>共通イベント形式を使用して外部ソリューションを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel と外部ソリューションを接続できます。これにより、ログ ファイルを Syslog に保存できるようになります。 ご利用のアプライアンスでログを Syslog 共通イベント形式 (CEF) として保存できる場合、Azure Sentinel との統合によってデータ全体で簡単に分析とクエリを実行できます。

> [!NOTE] 
> データは、Azure Sentinel を実行しているワークスペースの地域に保存されます。

## <a name="how-it-works"></a>動作のしくみ

Azure Sentinel とご利用の CEF アプライアンスとの間の接続では、次の 3 つの手順が行われます。

1. アプライアンスによって必要な形式で必要なログを Azure Sentinel Syslog エージェントに送信できるように、アプライアンスで次の値を設定する必要があります。 Azure Sentinel エージェントの Syslog デーモンでこれらのパラメーターを変更できれば、ご利用のアプライアンスでもこれらのパラメーターを変更することができます。
    - プロトコル = UDP
    - ポート = 514
    - ファシリティ = Local-4
    - 形式 = CEF
2. Syslog エージェントでは、データを収集して Log Analytics に安全に送信します。ここでデータが解析および改良されます。
3. エージェントによってデータが Log Analytics ワークスペースに保存されるため、分析、相関ルール、ダッシュボードを使用して、必要に応じてクエリできます。


## <a name="step-1-connect-to-your-cef-appliance-via-dedicated-azure-vm"></a>手順 1:専用の Azure VM を介して CEF アプライアンスに接続する

エージェントを専用の Linux マシン (VM またはオンプレミス) に展開して、アプライアンスと Azure Sentinel の間の通信をサポートする必要があります。 エージェントの展開は、自動または手動で行うことができます。 自動展開は、Resource Manager テンプレートに基づいており、ご利用の専用の Linux マシンが Azure で作成している新しい VM である場合にのみ使用できます。

 ![Azure での CEF](./media/connect-cef/cef-syslog-azure.png)

これ以外の場合は、既存の Azure VM、別のクラウド内の VM、またはオンプレミスのコンピューターに、手動でエージェントを展開します。 

 ![オンプレミスの CEF](./media/connect-cef/cef-syslog-onprem.png)

### <a name="deploy-the-agent-in-azure"></a>Azure でエージェントを展開する


1. Azure Sentinel portal で、**[Data connectors]\(データ コネクタ\)** をクリックして、お使いのアプライアンスの種類を選択します。 

1. **[Linux Syslog agent configuration]** (Linux Syslog エージェント構成) の下で:
   - Azure Sentinel エージェントが事前インストールされた、すべての必要な構成が含まれた新しいマシンを作成するには、前述の **[Automatic deployment]\(自動展開\)** を選択します。 **[Automatic deployment]\(自動展開\)** を選択して、**[Automatic agent deployment]\(エージェントの自動展開\)** をクリックします。 これにより、お使いのワークスペースに自動的に接続される専用の Linux VM の購入ページに移動します。 VM は**標準 D2s v3 (2 vCPU、8 GB メモリ)** であり、パブリック IP アドレスを持っています。
      1. **[カスタム デプロイ]** ページで詳細を入力し、ユーザー名とパスワードを選択し、使用条件に同意する場合は、VM を購入します。
      1. 接続ページに一覧表示されている設定を使用して、ログを送信するようにアプライアンスを構成します。 標準の共通イベント形式コネクタの場合、以下の設定を使用します。
         - プロトコル = UDP
         - ポート = 514
         - ファシリティ = Local-4
         - 形式 = CEF
   - 既存の VM を Azure Sentinel エージェントをインストールする専用の Linux マシンとして使用する場合、**[Manual deployment]\(手動配置\)** を選択します。 
      1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、**[Azure Linux virtual machine]\(Azure Linux 仮想マシン\)** を選択します。 
      1. 開いた **[仮想マシン]** 画面で、使用するマシンを選択し、**[接続]** をクリックします。
      1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
      1. これらのコマンドをコピーし、アプライアンス上で実行します。
          - rsyslog.d を選択した場合:
              
            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            
            1. 次の syslog デーモンを再起動します:  `sudo service rsyslog restart`<br> 詳細については、[rsyslog に関するドキュメント](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)を参照してください。
           
          - syslog-ng を選択した場合:

              1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。

              3. 次の syslog デーモンを再起動します:  `sudo service syslog-ng restart` <br>詳細については、[syslog-ng に関するドキュメント](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2)を参照してください。
      2. 次のコマンドを使用して、Syslog エージェントを再起動します:  `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します:  `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>オンプレミス Linux サーバーにエージェントをデプロイする

Azure を使用していない場合は、専用の Linux サーバーで実行するように Azure Sentinel エージェントを手動でデプロイします。


1. Azure Sentinel portal で、**[Data connectors]\(データ コネクタ\)** をクリックして、お使いのアプライアンスの種類を選択します。
1. 専用の Linux VM を作成するには、**[Linux Syslog agent configuration]** \(Linux Syslog エージェント構成\) の下で **[Manual deployment]\(手動配置\)** を選択します。
   1. **[Download and install the Syslog agent]\(Syslog エージェントのダウンロードとインストール\)** の下で、**[Non-Azure Linux machine]\(Azure ではない Linux マシン\)** を選択します。 
   1. 開いた **[ダイレクト エージェント]** 画面で、**[Linux 用エージェント]** を選択して、エージェントをダウンロードするか、次のコマンドを実行してお使いの Linux マシンにダウンロードします。`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. コネクタ画面の **[Configure and forward Syslog]\(Syslog の構成と転送\)** の下で、お使いの Syslog デーモンが **rsyslog.d** であるか **syslog-ng** であるかを設定します。 
      1. これらのコマンドをコピーし、お使いのアプライアンス上で実行します。
         - rsyslog を選択した場合:
           1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
           3. 次の syslog デーモンを再起動します:  `sudo service rsyslog restart`
         - syslog-ng を選択した場合:
            1. ファシリティ local_4 をリッスンし、ポート 25226 を使用して Syslog メッセージを Azure Sentinel エージェントに送信するように、Syslog デーモンに伝えます。 `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. ポート 25226 でリッスンするように Syslog エージェントを構成する [security_events 構成ファイル](https://aka.ms/asi-syslog-config-file-linux)をダウンロードしてインストールします。 `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ただし、{0} はワークスペース GUID に置き換える必要があります。
            3. 次の syslog デーモンを再起動します:  `sudo service syslog-ng restart`
      1. 次のコマンドを使用して、Syslog エージェントを再起動します:  `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. 次のコマンドを実行して、エージェント ログにエラーがないことを確認します:  `tail /var/opt/microsoft/omsagent/log/omsagent.log`
  
## <a name="step-2-validate-connectivity"></a>手順 2:接続の検証

ログが Log Analytics に表示され始めるまで、20 分以上かかる場合があります。 

1. Syslog エージェントで、自分のログが正しいポートに到達していることを確認します。 Syslog エージェント マシンで次のコマンドを実行します:`tcpdump -A -ni any  port 514 -vv` このコマンドでは、デバイスから Syslog マシンにストリーミングされるログを表示します。ログが、正しいポートとファシリティでソース アプライアンスから受信されていることを確認します。
2. Syslog デーモンとエージェント間で通信が行われていることを確認します。 Syslog エージェント マシンで次のコマンドを実行します:`tcpdump -A -ni any  port 25226 -vv` このコマンドによって、デバイスから Syslog コンピューターにストリーミングされるログが表示されます。ログがエージェント上でも受信されていることを確認します。
3. これらの両方のコマンドで正常な結果が表示された場合は、Log Analytics を調べて自分のログが到着しているかどうかを確認してください。 これらのアプライアンスからストリーミングされるすべてのイベントは、Log Analytics で `CommonSecurityLog` 型の下に未加工の形式で表示されます。
1. エラーがあるかどうか、またはログが到着しているかどうかを確認するには、次のファイルを調べます:  `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. 自分の Syslog メッセージの既定のサイズが 2048 バイト (2 KB) に制限されていることを確認します。 ログが長すぎる場合は、次のコマンドを使用して security_events.conf を更新します:  `message_length_limit 4096`
6. Log Analytics で CEF イベントに関連するスキーマを使用するために、**CommonSecurityLog** を検索します。



## <a name="next-steps"></a>次の手順
このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。

