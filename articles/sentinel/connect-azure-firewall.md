---
title: Azure Firewall データを Azure Sentinel に接続する
description: Azure Sentinel に Azure Firewall データを接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621297"
---
# <a name="connect-data-from-azure-firewall"></a>Azure Firewall からデータを接続する

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 完全にステートフルなサービスとしてのファイアウォールで、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えています。 

Azure Firewall ログを Azure Sentinel に接続すると、ブック内でログ データを表示し、それを使用してカスタム アラートを作成し、組み込んで、調査を改善することができます。

[Azure Firewall ログの監視](../firewall/firewall-diagnostics.md)について説明します。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

## <a name="connect-to-azure-firewall"></a>Azure Firewall に接続する
    
1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure Firewall]** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. 接続するログが存在するすべてのファイアウォールで **診断ログ** を有効にします。

    1. **[Azure Firewall を開く >]** リンクを選択します。

    1. **[ファイアウォール]** ナビゲーション メニューから **[診断設定]** を選択します。

    1. 一覧の下部にある **[+ 診断設定を追加する]** を選択します。

    1. **[診断設定]** 画面で、 **[診断設定の名前]** フィールドに名前を入力します。
    
    1. **[Log Analytics への送信]** チェック ボックスにマークを付けます。 2 つの新しいフィールドが下に表示されます。 関連する **[サブスクリプション]** と **[Log Analytics ワークスペース]** (Azure Sentinel が存在する場所) を選択します。

    1. ログを取り込むルールの種類のチェック ボックスにマークを付けます。 **AzureFirewallApplicationRule** と **AzureFirewallNetworkRule** を設定することをお勧めします。

    1. ページの最上部で **[保存]** を選択します。

1. Azure Firewall アラートのために Log Analytics 内で関連するスキーマを使用するには、**AzureDiagnostics** を検索します。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 2 週間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 2 週間が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Firewall ログを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。