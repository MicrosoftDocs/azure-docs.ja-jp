---
title: Illusive 攻撃管理システム データを Azure Sentinel に接続する| Microsoft Docs
description: Illusive 攻撃管理システム データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038119"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>Illusive 攻撃管理システムを Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Illusive 攻撃管理システム データ コネクタは、現在、パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、[Illusive 攻撃管理システム](https://www.illusivenetworks.com/technology/platform/attack-detection-system)を Azure Sentinel に接続する方法について説明します。 Illusive 攻撃管理システム データ コネクタを使用すると、Illusive の攻撃対象領域分析データとインシデント ログを Azure Sentinel と共有し、この情報を専用のダッシュボードに表示できます。組織の攻撃対象領域リスクに対する分析情報の入手 (ASM ダッシュボード) や、組織のネットワークにおける承認されていない横移動の追跡 (ADS ダッシュボード) を行えます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Illusive 攻撃管理システム ログを Syslog エージェントに転送する  

Syslog エージェントを介して、CEF 形式で Syslog メッセージを Azure ワークスペースに転送するように攻撃管理システムを構成します。

1. Illusive Console にログオンし、[Settings]\(設定\) -> [Reporting]\(レポート\) に移動します。

1. Syslog サーバーを見つけます。

1. 次の情報を指定します。
   - ホスト名:Linux Syslog エージェントの IP アドレスまたは FQDN ホスト名
   - ポート: 514
   - プロトコル:TCP
   - 監査メッセージ:サーバーに監査メッセージを送信する

1. Syslog サーバーを追加するには、[Add]\(追加\) をクリックします。

1. **ログ** で Illusive 攻撃管理システムに関連するスキーマを使用するために、**CommonSecurityLog** を検索します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Illusive 攻撃管理システムを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
