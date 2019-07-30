---
title: Azure Sentinel プレビューに Syslog データを接続する | Microsoft Docs
description: Azure Sentinel に Syslog データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: fef9fa128d2ebb84fb82579f254735fdb9aa7ee2
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881066"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog を使用して、ご利用の外部ソリューションを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Syslog をサポートする任意のオンプレミスのアプライアンスを Azure Sentinel に接続することができます。 これを行うには、アプライアンスと Azure Sentinel 間に Linux マシンに基づくエージェントを使用します。 ご利用の Linux マシンが Azure 内にある場合は、ご自分のアプライアンスまたはアプリケーションから、Azure 内で作成した専用のワークスペースへと、ログをストリーミングし、それを接続することができます。 ご利用の Linux マシンが Azure 内にない場合は、ご自分のアプライアンスから、Linux 用エージェントのインストール先である専用のオンプレミスの VM またはマシンへと、ログをストリーミングすることができます。 

> [!NOTE]
> ご利用のアプライアンスで Syslog CEF がサポートされている場合、接続はより詳細になるので、このオプションを選択し、[CEF からのデータの接続](connect-common-event-format.md)に関するページに記載の手順に従う必要があります。

## <a name="how-it-works"></a>動作のしくみ

Syslog は、Linux に共通のイベント ログ プロトコルです。 アプリケーションは、ローカル コンピューターへの保存または Syslog コレクターへの配信が可能なメッセージを送信します。 Linux 用 Log Analytics エージェントがインストールされている場合は、エージェントにメッセージを転送するローカル Syslog デーモンが構成されます。 エージェントは Azure Monitor にメッセージを送信し、そこで対応するレコードが作成されます。

詳細については、「[Azure Monitor の Syslog データ ソース](../azure-monitor/platform/data-sources-syslog.md)」を参照してください。

> [!NOTE]
> エージェントは、複数のソースからログを収集できますが、専用のプロキシ コンピューターにインストールする必要があります。

## <a name="connect-your-syslog-appliance"></a>Syslog アプライアンスを接続する

1. Azure Sentinel ポータルで、 **[データ コネクタ]** を選択し、テーブルで **[Syslog]** 行を選択し、右側の [Syslog] ウィンドウで **[Open connector page]\(コネクタ ページを開く\)** をクリックします。
2. Linux マシンが Azure 内にある場合、 **[Download and install agent on Azure Linux virtual machine]\(Azure Linux 仮想マシン上でエージェントをダウンロードしてインストールする\)** を選択します。 [仮想マシン] ウィンドウで、エージェントをインストールするマシンを選択して、上部にある **[接続]** をクリックします。
1. Linux マシンが Azure 内にない場合、 **[Download and install agent on Linux non-Azure machine]\(Azure 外の Linux マシン上でエージェントをダウンロードしてインストールする\)** を選択します。 **[Direct agent]\(直接エージェント\)** ウィンドウで、 **[Download and onboard agent for Linux]\(Linux 用エージェントのダウンロードとオンボーディング\)** の下にあるコマンドをコピーし、対象のマシン上で実行します。 
1. Syslog コネクタ設定ウィンドウ内の **[Configure the logs to be connected]\(接続するログを構成する\)** で、次の指示に従います。
    1. **[Open your workspace advanced settings configuration]\(ワークスペースの詳細設定の構成を開く\)** リンクをクリックします。 
    1. **[データ]** 、 **[Syslog]** の順に選択します。
    1. 次に、テーブルで、どの機能を Syslog で収集するかを設定します。 Syslog アプライアンスがそのログ ヘッダーでインクルードする機能を追加または選択する必要があります。 この構成は、Syslog アプライアンスの /etc/rsyslog.d/security-config-omsagent.conf フォルダーにある Syslog-d と、/etc/syslog-ng/security-config-omsagent.conf にある r-Syslog で確認できます。 
       > [!NOTE]
       > **[Apply below configuration to my machines]\(下の構成をマシン群に適用する\)** チェック ボックスをオンにすると、このワークスペースに接続しているすべての Linux マシンにこの構成が適用されます。 Syslog マシンでこの構成を確認できる場所は 
1. **[Press here to open the configuration blade]\(構成ブレードを開くにはここを押してください\)** をクリックします。
1. **[データ]** 、 **[Syslog]** の順に選択します。
   - Syslog によって送信する各ファシリティがテーブル内にあることを確認します。 ファシリティごとに、監視を実行し、重大度レベルを設定します。 **[Apply]** をクリックします。
1. ご利用の Syslog マシン上で、それらのファシリティを送信していることを確認します。 

1. Log Analytics で Syslog ログに関連するスキーマを使用するために、**Syslog** を検索します。
1. 「[Azure Monitor ログ クエリでの関数の使用](../azure-monitor/log-query/functions.md)」で説明されている Kusto 関数を使用して、Syslog メッセージを解析し、新しい Log Analytics 関数としてそれらを保存し、その関数を新しいデータ型として使用することができます。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Syslog オンプレミス アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
