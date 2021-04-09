---
title: Azure Web アプリケーション ファイアウォール (WAF) データを Azure Sentinel に接続する
description: Azure Sentinel に Azure WAF データを接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655903"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Azure Web アプリケーション ファイアウォール (WAF) からデータを接続する

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Azure Web アプリケーション ファイアウォール (WAF) には、コード インジェクションやクロスサイトスクリプティングなどの一般的な悪用や脅威から Web アプリケーションを一元的に保護する機能があります。 Azure WAF は、[Azure Content Delivery Network (CDN)](../web-application-firewall/cdn/cdn-overview.md) WAF ポリシーを介して、[Azure Application Gateway](../web-application-firewall/ag/ag-overview.md) サービスと [Azure Front Door](../web-application-firewall/afds/afds-overview.md) サービスにデプロイできます (後者は現在パブリック プレビュー段階です)。
Azure WAF ログを Azure Sentinel に接続すると、ブック内でログ データを表示し、それを使用してカスタム アラートを作成し、組み込んで、調査を改善することができます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

## <a name="connect-to-azure-waf"></a>Azure WAF に接続する

### <a name="instructions-tab"></a>[手順] タブ

1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure web application firewall (WAF)]\(Azure Web アプリケーション ファイアウォール (WAF)\)** を選択し、プレビュー ペインで **[Open Connector Page]\(コネクタ ページを開く\)** を選択します。

1. **[Open Application Gateway resource]\(Application Gateway リソースを開く\)** 、 **[Open Front Door resource]\(Front Door リソースを開く\)** 、または **[Open Content Delivery Network (CDN) WAF policy]\(Content Delivery Network (CDN) WAF ポリシーを開く\)** を選択してログを接続する WAF リソースの種類のリンクを選択し、リソース一覧の画面で一覧から WAF リソースを選択します。

    1. WAF リソースのナビゲーション メニューから **[診断設定]** を選択します。

    1. 一覧の下部にある **[+ 診断設定を追加する]** を選択します。

    1. **[診断設定]** 画面で、 **[診断設定の名前]** フィールドに名前を入力します。

    1. **[Log Analytics への送信]** チェック ボックスをオンにします。 2 つの新しいフィールドが下に表示されます。 関連する **[サブスクリプション]** と **[Log Analytics ワークスペース]** (Azure Sentinel が存在する場所) を選択します。

    1. ログを取り込むルールの種類のチェック ボックスをオンにします。 各リソースの種類に関する推奨事項は次のとおりです。

        | リソース | インジェストのために選択するログ |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF ポリシー      | WebApplicationFirewallLogs |
        |

    1. **[保存]** を選択します。

### <a name="next-steps-tab"></a>[次のステップ] タブ

- **Azure Web アプリケーション ファイアウォール** データ コネクタにバンドルされている推奨ブック、クエリ サンプル、分析ルール テンプレートを参照して、Azure WAF ログ データに関する分析情報を取得します。

- **[ログ]** の Azure WAF データのクエリを実行するには、クエリ ウィンドウに「**AzureDiagnostics**」と入力します。

> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 2 週間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 2 週間が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure WAF ログを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。