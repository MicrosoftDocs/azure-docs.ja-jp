---
title: Azure Sentinel への接続を検証する | Microsoft Docs
description: セキュリティ ソリューションの接続を検証して、CEF メッセージが Azure Sentinel に転送されていることを確認します。
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
ms.openlocfilehash: f6892f4ebb250290a0faad546fd000530baf4479
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038173"
---
# <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

ログ フォワーダーをデプロイし (手順 1)、CEF メッセージを送信するようにセキュリティ ソリューションを構成したら (手順 2)、以下の手順に従って、セキュリティ ソリューションと Azure Sentinel との間の接続を検証します。 

## <a name="prerequisites"></a>前提条件

- ログ フォワーダー マシンに対する昇格されたアクセス許可 (sudo) が必要です。

- ログ フォワーダー マシンに Python がインストールされている必要があります。<br>
`python –version` コマンドを使用して確認してください。

## <a name="how-to-validate-connectivity"></a>接続を検証する方法

1. Azure Sentinel ナビゲーション メニューから **[ログ]** を開きます。 **CommonSecurityLog** スキーマを使用して、セキュリティ ソリューションからログが届いているかどうかを確認するクエリを実行します。<br>
ログが **Log Analytics** に表示され始めるまで、約 20 分かかる場合があります。 

1. クエリの結果がまったく表示されない場合は、セキュリティ ソリューションからイベントが生成されていることを確認するか、なんらかのイベントを生成してみてください。それらのイベントが、指定した Syslog フォワーダー マシンに転送されていることを確認します。 

1. ログ フォワーダーで次のスクリプトを実行し、セキュリティ ソリューションとログ フォワーダー、Azure Sentinel 間の接続を確認します。 デーモンが適切なポートでリッスンしていること、転送が適切に構成されていること、デーモンと Log Analytics エージェントとの間の通信がブロックされていないことが、このスクリプトによって確認されます。 また、モック メッセージ "TestCommonEventFormat" を送信することで、エンドツーエンドの接続が確認されます。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>検証スクリプトの説明

この検証スクリプトでは、次のチェックが実行されます。

# <a name="rsyslog-daemon"></a>[rsyslog デーモン](#tab/rsyslog)

1. ファイル <br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    が存在し、有効であることを確認します。

1. このファイルに次のテキストが含まれていることを確認します。

    ```console
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. マシンのセキュリティにネットワーク トラフィックをブロックするような改良 (ホスト ファイアウォールなど) がなされているかどうかを確認します。

1. CEF として識別 (正規表現を使用) されたメッセージを TCP ポート 25226 で Log Analytics エージェントに送信するよう、syslog デーモン (rsyslog) が適切に構成されていることを確認します。

    - 構成ファイル: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```console
        :rawmsg, regex, "CEF"|"ASA"
        *.* @@127.0.0.1:25226
        ```
  
1. syslog デーモンがデータをポート 514 で受信していることを確認します。

1. 必要な接続が確立されていることを確認します。データの受信には TCP 514 が、また syslog デーモンと Log Analytics エージェントとの間の内部通信には TCP 25226 が使用されます。

1. MOCK データを localhost のポート 514 に送信します。 このデータは、Azure Sentinel ワークスペースから次のクエリを実行することによって観察できます。

    ```console
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng デーモン](#tab/syslogng)

1. ファイル <br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    が存在し、有効であることを確認します。

1. このファイルに次のテキストが含まれていることを確認します。

    ```console
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. マシンのセキュリティにネットワーク トラフィックをブロックするような改良 (ホスト ファイアウォールなど) がなされているかどうかを確認します。

1. CEF として識別 (正規表現を使用) されたメッセージを TCP ポート 25226 で Log Analytics エージェントに送信するよう、syslog デーモン (syslog-ng) が適切に構成されていることを確認します。

    - 構成ファイル: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```console
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. syslog デーモンがデータをポート 514 で受信していることを確認します。

1. 必要な接続が確立されていることを確認します。データの受信には TCP 514 が、また syslog デーモンと Log Analytics エージェントとの間の内部通信には TCP 25226 が使用されます。

1. MOCK データを localhost のポート 514 に送信します。 このデータは、Azure Sentinel ワークスペースから次のクエリを実行することによって観察できます。

    ```console
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>次のステップ
このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

