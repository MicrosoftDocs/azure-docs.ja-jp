---
title: Azure Sentinel に NXLog LinuxAudit データを接続する | Microsoft Docs
description: NXLog LinuxAudit データ コネクタを使用して、LinuxAudit ログを Azure Sentinel にプルする方法について説明します。 LinuxAudit データをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743560"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>NXLog LinuxAudit を Azure Sentinel に接続する

> [!IMPORTANT]
> NXLog LinuxAudit コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) コネクタを使用すると、Linux セキュリティ イベントを Azure Sentinel にリアルタイムで簡単にエクスポートできるため、組織全体でのドメイン ネーム サーバーの利用状況に関する分析情報を得ることができます。 NXLog LinuxAudit モジュールは、カスタム監査規則をサポートし、auditd やその他のユーザー空間のソフトウェアを使用せずにログを収集します。 IP アドレスとグループ/ユーザー ID はそれぞれの名前に解決されるため、[Linux 監査](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html)ログはセキュリティ アナリストにとって、より分かりやすくなります。 NXLog LinuxAudit と Azure Sentinel の統合は、REST API を通じて容易に行うことができます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-nxlog-linuxaudit"></a>NXLog LinuxAudit を構成して接続する

JSON 形式でイベントを Azure Sentinel に直接送信するように NXLog を構成できます。

1. Azure Sentinel ポータルで、 **[データ コネクタ]** をクリックし、 **[NXLog LinuxAudit]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. 『*NXLog ユーザー ガイド*』の統合に関するトピック「[Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)」に記載されている手順に従って、REST API 経由での転送を構成します。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、*LinuxAudit_CL* テーブル内の **[カスタム ログ]** セクションの **[ログ]** にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、NXLog LinuxAudit を使用して Linux のセキュリティ イベントを Azure Sentinel に取り込む方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
