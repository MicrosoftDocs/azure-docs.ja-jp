---
title: Azure Sentinel に CyberArk EPV データを接続する | Microsoft Docs
description: CyberArk Enterprise Password Vault (EPV) データ コネクタを使用して、そのログを Azure Sentinel にプルする方法について説明します。 CyberArk EPV データをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: aa57963ce369e4c8f84f4aae5f99fe343181ff6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530503"
---
# <a name="connect-cyberark-enterprise-password-vault-epv-to-azure-sentinel"></a>CyberArk Enterprise Password Vault (EPV) を Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の CyberArk Enterprise Password Vault (EPV) データ コネクタは、現在パブリック プレビュー段階にあります。 この機能は、サービス レベル アグリーメントなしに提供されます。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

CyberArk Syslog コネクタを使用すると、Azure Sentinel にすべての CyberArk セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 CyberArk と Azure Sentinel の統合により、CEF データ コネクタを使用して、CyberArk Syslog メッセージを適切に解析して表示します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-cyberark-epv"></a>CyberArk EPV の構成と接続

CyberArk EPV ログは、コンテナーから Log Analytics エージェントがインストールされた Linux ベースのログ転送サーバー (rsyslog または syslog-ng が実行されている) に送信され、ログは Azure Sentinel にエクスポートされます。 このようなログ転送サーバーを持っていない場合は、起動させるための[手順](connect-cef-agent.md)を参照してください。

1. Azure Sentinel ポータルで、 **[データ コネクタ]** をクリックし、 **[CyberArk Enterprise Password Vault (EPV) イベント (プレビュー)]** 、次に **[コネクタ ページを開く]** を選択します。

1. [CyberArk EPV の指示](https://docs.cyberark.com/Product-Doc/OnlineHelp/PAS/Latest/en/Content/PASIMP/DV-Integrating-with-SIEM-Applications.htm)に従って、ログ転送サーバーへの syslog データの送信を構成します。

1. 接続を検証し、[こちらの手順](connect-cef-verify.md)を使用してデータ インジェストを確認します。 ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 *[CommonSecurityLog]* テーブルにある **[Azure Sentinel]** セクションの **[ログ]** にデータが表示されます。

Log Analytics で CyberArk EPV ログのクエリを実行するには、クエリ ウィンドウの上部に `CommonSecurityLog` を入力します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、CyberArk Enterprise Password Vault ログを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
