---
title: Azure Sentinel プレビューに Syslog データを接続する | Microsoft Docs
description: Azure Sentinel に Syslog データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 9144f20eae5a925ad88054e972c99ae024bcefe3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795731"
---
# <a name="connect-your-external-solution-using-syslog"></a>Syslog を使用して、ご利用の外部ソリューションを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Syslog をサポートする任意のオンプレミスのアプライアンスを Azure Sentinel に接続することができます。 これを行うには、アプライアンスと Azure Sentinel 間に Linux マシンに基づくエージェントを使用します。 ご利用の Linux マシンが Azure 内にある場合は、ご自分のアプライアンスまたはアプリケーションから、Azure 内で作成した専用のワークスペースへと、ログをストリーミングし、それを接続することができます。 ご利用の Linux マシンが Azure 内にない場合は、ご自分のアプライアンスから、Linux 用エージェントのインストール先である専用のオンプレミスの VM またはマシンへと、ログをストリーミングすることができます。 

> [!NOTE]
> ご利用のアプライアンスで Syslog CEF がサポートされている場合、接続はより詳細になるので、このオプションを選択し、[CEF からのデータの接続](connect-common-event-format.md)に関するページに記載の手順に従う必要があります。

## <a name="how-it-works"></a>動作のしくみ

Syslog 接続は、Linux 用エージェントを使用して実現されます。 既定では、Linux 用エージェントは Syslog デーモンからのイベントを UDP 経由で受信しますが、Linux マシンで大量の Syslog イベントが収集されると予想される場合 (Linux エージェントが他のデバイスからイベントを受信している場合など)、Syslog デーモンとエージェントの間で TCP トランスポートを使用するように構成が変更されます。

## <a name="connect-your-syslog-appliance"></a>Syslog アプライアンスを接続する

1. Azure Sentinel ポータルで、**[データ コネクタ]** を選択し、**[Syslog]** タイルを選択します。
2. ご利用の Linux マシンが Azure 内にない場合は、Azure Sentinel **エージェント for Linux** をダウンロードしてご利用のアプライアンス上にインストールします。 
1. Azure 内で作業している場合は、Syslog メッセージの受信専用である Azure Sentinel ワークスペース内で VM を選択または作成します。 Azure Sentinel ワークスペース内の VM を選択し、左側のウィンドウの上部にある **[接続]** をクリックします。
3. Syslog コネクタの設定に戻り、**[Configure the logs to be connected]\(接続するログの構成\)** をクリックします。 
4. **[Press here to open the configuration blade]\(構成ブレードを開くにはここを押してください\)** をクリックします。
1. **[データ]**、**[Syslog]** の順に選択します。
   - Syslog によって送信する各ファシリティがテーブル内にあることを確認します。 ファシリティごとに、監視を実行し、重大度レベルを設定します。 **[Apply]** をクリックします。
1. ご利用の Syslog マシン上で、それらのファシリティを送信していることを確認します。 

3. Log Analytics で Syslog ログに関連するスキーマを使用するために、**Syslog** を検索します。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Syslog オンプレミス アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
