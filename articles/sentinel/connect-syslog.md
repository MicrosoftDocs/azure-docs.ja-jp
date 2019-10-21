---
title: Azure Sentinel に Syslog データを接続する | Microsoft Docs
description: Azure Sentinel に Syslog データを接続する方法について説明します。
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
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: b2be563efa3c09cffaf14dec2b871f3881af1a7a
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240050"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog を使用して、ご利用の外部ソリューションを接続する

Syslog をサポートする任意のオンプレミスのアプライアンスを Azure Sentinel に接続することができます。 これを行うには、アプライアンスと Azure Sentinel 間に Linux マシンに基づくエージェントを使用します。 ご利用の Linux マシンが Azure 内にある場合は、ご自分のアプライアンスまたはアプリケーションから、Azure 内で作成した専用のワークスペースへと、ログをストリーミングし、それを接続することができます。 ご利用の Linux マシンが Azure 内にない場合は、ご自分のアプライアンスから、Linux 用エージェントのインストール先である専用のオンプレミスの VM またはマシンへと、ログをストリーミングすることができます。 

> [!NOTE]
> ご利用のアプライアンスで Syslog CEF がサポートされている場合、接続はより詳細になるので、このオプションを選択し、[CEF からのデータの接続](connect-common-event-format.md)に関するページに記載の手順に従う必要があります。

## <a name="how-it-works"></a>動作のしくみ

Syslog は、Linux に共通のイベント ログ プロトコルです。 アプリケーションは、ローカル コンピューターへの保存または Syslog コレクターへの配信が可能なメッセージを送信します。 Linux 用 Log Analytics エージェントがインストールされている場合は、エージェントにメッセージを転送するローカル Syslog デーモンが構成されます。 エージェントは Azure Monitor にメッセージを送信し、そこで対応するレコードが作成されます。

詳細については、「[Azure Monitor の Syslog データ ソース](../azure-monitor/platform/data-sources-syslog.md)」を参照してください。

> [!NOTE]
> エージェントは、複数のソースからログを収集できますが、専用のプロキシ コンピューターにインストールする必要があります。

## <a name="connect-your-syslog-appliance"></a>Syslog アプライアンスを接続する

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Syslog]** コネクタを選択します。

2. **[Syslog]** ブレードで、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

3. Linux エージェントをインストールします。
    
    - Linux 仮想マシンが Azure 内にある場合、 **[Download and install agent on Azure Linux virtual machine]\(Azure Linux 仮想マシン上でエージェントをダウンロードしてインストールする\)** を選択します。 **[仮想マシン]** ブレードでエージェントをインストールする仮想マシンを選択し、 **[接続]** をクリックします。
    - Linux マシンが Azure 内にない場合、 **[Download and install agent on Linux non-Azure machine]\(Azure 以外の Linux マシン上でエージェントをダウンロードしてインストールする\)** を選択します。 **[Direct agent]\(直接エージェント\)** ブレードで、 **[DOWNLOAD AND ONBOARD AGENT FOR LINUX]\(Linux 用エージェントのダウンロードとオンボーディング\)** の下にあるコマンドをコピーし、対象のコンピューター上で実行します。 
    
   > [!NOTE]
   > 組織のセキュリティ ポリシーに従って、これらのコンピューターのセキュリティ設定を構成してください。 たとえば、組織のネットワーク セキュリティ ポリシーに合わせてネットワーク設定を構成し、デーモンのポートとプロトコルをセキュリティ要件に合わせて変更できます。

4. **[Open your workspace advanced settings configuration]\(ワークスペースの詳細設定の構成を開く\)** を選択します。

5. **[詳細設定]** ブレードで、 **[データ]**  >  **[Syslog]** の順に選択します。 次に、収集するコネクタのファシリティを追加します。
    
    Syslog アプライアンスがそのログ ヘッダーに含めるファシリティを追加します。 この構成は、Syslog アプライアンスの `/etc/rsyslog.d/security-config-omsagent.conf`フォルダーにある **Syslog-d**と、 `/etc/syslog-ng/security-config-omsagent.conf`にある **r-Syslog** で確認できます。
    
    収集するデータで異常な SSH ログイン検出を使用する場合は、 **[auth]** と **[authpriv]** を追加します。 追加情報については、[次のセクション](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)を参照してください。

6. 監視するファシリティをすべて追加し、それぞれの重大度オプションを調整したら、 **[Apply below configuration to my machines]\(下の構成をコンピューターに適用する\)** チェックボックスを選択します。

7. **[保存]** を選択します。 

8. Syslog アプライアンスで、指定したファシリティが送信されていることを確認します。

9. Azure Monitor で Syslog ログに関連するスキーマを使用するために、**Syslog** を検索します。

10. 「[Azure Monitor ログ クエリでの関数の使用](../azure-monitor/log-query/functions.md)」で説明されている Kusto 関数を使用して、Syslog メッセージを解析できます。 その後、新しい Log Analytics 関数としてそれらを保存し、新しいデータ型として使用できます。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>異常な SSH ログイン検出用に Syslog コネクタを構成する

> [!IMPORTANT]
> 異常な SSH ログイン検出は現在、パブリック プレビュー段階です。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel は Syslog データに機械学習 (ML) を適用して、異常な Secure Shell (SSH) ログイン アクティビティを識別できます。 シナリオには以下が含まれます。

- あり得ない移動 - 2 つの成功したログイン イベントが、この 2 つのログイン イベントの期間内に到達できない 2 つの場所で発生した場合。
- 予期しない場所 - 成功したログイン イベントの発生した場所が疑わしい場合。 たとえば、最近検出されることのなかった場所などです。
 
この検出には、Syslog データ コネクタの特定の構成が必要です。 

1. 前の手順のステップ 5 で、監視するファシリティとして **[auth]** と **[authpriv]** の両方が選択されていることを確認してください。 重大度オプションは既定の設定のままにし、すべてが選択されるようにします。 例:
    
    > [!div class="mx-imgBorder"]
    > ![異常な SSH ログインの検出に必要なファシリティ](./media/connect-syslog/facilities-ssh-detection.png)

2. Syslog 情報が収集されるまで、十分な時間をかけます。 その後、 **[Azure Sentinel - Logs]\(Azure Sentinel - ログ\)** に移動して、次のクエリをコピーして貼り付けます。
    
        Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    
    必要に応じて **[Time range]\(時間の範囲\)** を変更し、 **[実行]** を選択します。
    
    結果の数が 0 の場合は、コネクタの構成を確認し、クエリに指定した期間内に監視対象のコンピュータで成功したログイン アクティビティがあったことを確認します。
    
    結果の数が 0 より大きい場合、Syslog データは、異常な SSH ログインの検出用に適しています。 この検出は、 **[分析]**  >   **[Rule templates]\(ルール テンプレート\)**  >  **[(Preview) Anomalous SSH Login Detection]\((プレビュー) 異常な SSH ログインの検出\)** で有効にします。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Syslog オンプレミス アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
