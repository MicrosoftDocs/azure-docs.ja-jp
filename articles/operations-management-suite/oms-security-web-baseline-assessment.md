---
title: "Operations Management Suite の Security and Audit ソリューションでの Web ベースライン評価 | Microsoft Docs"
description: "このドキュメントでは、OMS の Security and Audit ソリューションでの Web ベースライン評価を使用して、コンプライアンスとセキュリティの目的で監視対象のすべての Web サーバーのベースライン評価を実行する方法を説明します。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Operations Management Suite のセキュリティと監査ソリューションでの Web ベースライン評価
このドキュメントは、OMS の Security and Audit Web ベースライン評価機能を使用して、監視対象のリソースのセキュリティで保護された状態にアクセスする際に役立ちます。

## <a name="what-is-web-baseline-assessment"></a>Web ベースライン評価とは
現在、OMS Security は、オペレーティング システムのセキュリティ ベースライン評価を提供します。 24 時間ごとにサーバーの OS の設定をスキャンし、潜在的に脆弱な設定に関する情報を提供します。 詳細については、「[Baseline Assessment in Operations Management Suite Security and Audit Solution (Operations Management Suite のセキュリティと監査ソリューションでのベースライン評価)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline)」を参照してください。

Web ベースライン評価の目的は、Web サーバーの潜在的に脆弱な設定を見つけることです。 Web ベースラインの構成の 3 つの主要ソースは、.NET、ASP.NET、および IIS の構成です。  オペレーティング システムのベースライン評価と同じように、OMS Security は、24 時間ごとに Web サーバーをスキャンし、セキュリティの状態に関する情報を提供します。  インターネット インフォメーション サービス (IIS) では、構成を高度にカスタマイズできるため、さまざまなサイトやアプリケーション レベルを上書きできます。 スキャナーは、既定のルート レベルだけでなく、各アプリケーションおよびサイト レベルでも設定を確認します。 そのため、潜在的に脆弱な設定を特定してすぐに修復し、これらの設定に対する推奨事項を適用できます。

>[!NOTE] 
>OMS Security で使われている一般的な構成識別子および基準規則は、こちらの[ページ](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0)からダウンロードできます。


## <a name="web-security-baseline-assessment"></a>Web セキュリティ ベースライン評価

このプレビューでは、OMS 検索オプションと OMS の [セキュリティおよび監査] ダッシュボードを使用してこの機能にアクセスできます。 適切なクエリを実行するには、次の手順に従います。

1. **Microsoft Operations Management Suite** のメイン ダッシュボードで、**[セキュリティおよび監査]** タイルをクリックします。
2. **[セキュリティおよび監査]** ダッシュボードの OS ベースライン パースペクティブの隣に Web ベースライン パースペクティブが表示されます。
   
    ![OMS の Security and Audit Web セキュリティ ベースライン評価](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. 左側のウィンドウには、ベースラインに対する Web サーバーの数、評価対象のすべてのサーバーでパスしたルールの平均パーセンテージ、評価されたサーバーの一覧が表示されます。
4. 右側のウィンドウには、パスしなかった固有のルールが "*重大度*" ごと、および "*ルールの種類*" ごとに表示されます。 右側のウィンドウで任意のルールをクリックすると、そのルールの詳細が表示されます。 以下に例を示します。 評価されるルールが、*[ルールの設定]* の下に表示されます。 *[AzId]* フィールドは各ルールの一意の識別子で、ベースライン ルールを追跡するために Microsoft が使用します。 その他に、*[Expected Result]\(想定される結果\)* (Microsoft の推奨値) や、ルールのセキュリティへの影響に関する他の詳細が表示されます。
    
    ![クエリ](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. 独自のクエリを作成して結果を確認できます。 

使用できる最初のクエリは、**Web ベースライン評価の概要**です。 **[Begin search here]\(ここから検索を開始\)** フィールドに、*Type=SecurityBaselineSummary BaselineType=Web* というクエリを入力します。 出力の例を以下に示します。

![クエリの結果](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>このクエリでは、各レコードが 1 つのサーバーの評価の概要を示しています。

**[ログ検索]** では、さまざまなクエリを入力して、Web ベースライン評価に関する詳細情報を入手できます。 このプレビューでは、前のクエリだけでなく、次のクエリを使用することもできます。

**Web ベースライン ルール評価**: 各レコードは、1 つのサーバーの 1 つの Web ベースライン ルール評価を表します。 パスしなかったルールのすべてのデータ、ルールが評価された "*サイト パス*"、"*想定される結果*"、"*実際の結果*" が含まれます。

クエリ: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![クエリの結果 2](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**特定のサーバーのすべての結果を表示**: このクエリは、特定のサーバーの結果を表示する方法を示します。クエリ: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![クエリの結果 3](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

これらのレコード/クエリを使用して、独自のダッシュボード、レポート、またはアラートを作成できます。 これは、ダッシュボードに追加できる UI コントロールのサンプルです。 OMS ビュー デザイナーを使用して、データを視覚化する方法については、[こちら](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/)を参照してください。 次の画面は、カスタマイズするとタイルがどのように表示されるかを示す例です。

![dashboard](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>関連項目
このドキュメントでは、OMS の Security and Audit Web ベースライン評価について説明しました。 OMS セキュリティの詳細については、次の記事を参照してください。

* [Operations Management Suite (OMS) overview (Operations Management Suite (OMS) の概要)](operations-management-suite-overview.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのセキュリティの警告に対する監視と対応](oms-security-responding-alerts.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのリソースの監視](oms-security-monitoring-resources.md)

