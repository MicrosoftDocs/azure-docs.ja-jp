---
title: Azure App Service 診断の概要 | Microsoft Docs
description: App Service 診断を使用してアプリに関する問題をトラブルシューティングする方法について説明します。
keywords: アプリ サービス, azure app service, 診断, サポート, web アプリ, トラブルシューティング, セルフヘルプ
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: f2f798be85e9c3aeb8d4b54cba89d8be059427e0
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147333"
---
# <a name="azure-app-service-diagnostics-overview"></a>Azure App Service 診断の概要

Web アプリケーションを実行するに当たっては、500 エラーが発生したり、サイトがダウンした場合に向けての準備体制が重要になります。 App Service 診断は、アプリのトラブルシューティングを支援するインテリジェントな対話型エクスペリエンスを備え、構成は不要です。 アプリに問題が発生した場合、App Service 診断は問題点を指摘し、その問題をすばやく簡単にトラブルシューティングして解決するための適切な情報へとユーザーを導きます。

このエクスペリエンスはアプリの問題発生時刻が過去 24 時間以内である場合に最も役立ちますが、すべての診断グラフを常に分析に利用できます。

App Service 診断は、Windows 上のアプリだけでなく、[Linux/コンテナー](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)、[App Service 環境](https://docs.microsoft.com/azure/app-service/environment/intro)、および [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 上のアプリに対しても機能します。

## <a name="open-app-service-diagnostics"></a>App Service 診断を開く

App Service 診断にアクセスするには、[Azure portal](https://portal.azure.com) 上の App Service Web アプリまたは App Service 環境に移動します。 左側のナビゲーションで、 **[問題の診断と解決]** をクリックします。

Azure Functions の場合、関数アプリに移動し、上部ナビゲーションにある **[プラットフォーム機能]** をクリックし、 **[リソース管理]** セクションの **[問題の診断と解決]** を選択します。

App Service 診断のホームページ上で、各ホームページ タイルにあるキーワードを使用して、アプリでの問題を最もよく表しているカテゴリを選択できます。 また、このページでは、Windows アプリ用の**診断ツール**を見つけることができます。 「[Diagnostic tools (only for Windows app) (診断ツール (Windows アプリのみ))](#diagnostic-tools-only-for-windows-app)」をご覧ください。

![ホームページ](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>対話型インターフェイス

アプリの問題に応じた最適なホームページ カテゴリを選択すると、App Service 診断の対話型インターフェイス Genie によって、アプリでの問題を診断して解決する手順へと誘導されます。 Genie で提供されているタイル ショートカットを使用して、関心のある問題カテゴリの診断レポート全体を閲覧できます。 タイル ショートカットでは、診断メトリックにアクセスするための直接的な方法を提供します。

![タイル ショートカット](./media/app-service-diagnostics/tile-shortcuts-2.png)

これらのタイルをクリックすると、タイルに示されている問題に関連するトピックの一覧を表示できます。 これらのトピックでは、レポート全体から注目すべき情報のスニペットを提示します。 これらのトピックのいずれかをクリックして、問題をさらに詳しく調査することができます。 また、 **[レポート全体の表示]** をクリックして、単一ページ上ですべてのトピックを参照することも可能です。

![トピック](./media/app-service-diagnostics/application-logs-insights-3.png)

![レポート全体の表示](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>診断レポート

問題をさらに詳しく調べるためにトピックをクリックすると、多くの場合はグラフとマーダウンが提供され、トピックに関する詳細をさらに閲覧できます。 診断レポートは、アプリでの問題を正確に特定するための優れたツールになる場合があります。

![診断レポート](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>正常性検査

アプリのどこに問題があるのかわからない場合や、問題のトラブルシューティングをどこから始めればよいかわからない場合には、正常性検査から始めることをお勧めします。 正常性検査では、アプリケーションを分析し、何が正常で何が異常かを指摘する簡潔な対話型の概要によって、問題の調査が必要な箇所をユーザーに示します。 また、インテリジェントな対話型のインターフェイスが、トラブルシューティングのプロセスを通じてユーザーをガイドします。 正常性検査は、Windows アプリの場合は Genie エクスペリエンスと、Linux アプリの場合は Web アプリ停止の診断レポートと統合されます。

### <a name="health-checkup-graphs"></a>正常性検査のグラフ

正常性検査には、4 つの異なるグラフがあります。

- **要求およびエラー:** HTTP サーバー エラーと共に過去 24 時間に行われた要求の数を示すグラフ。
- **アプリのパフォーマンス:** さまざまなパーセンタイル グループの過去 24 時間にわたる応答時間を示したグラフ。
- **CPU 使用率:** 過去 24 時間のインスタンスごとの CPU 使用率全体を割合で示したグラフ。  
- **メモリ使用量:** 過去 24 時間のインスタンスごとの物理メモリ使用量全体を割合で示したグラフ。

![正常性検査](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>アプリケーション コードの問題を調査する (Windows アプリのみ)

多くのアプリの問題は、アプリケーション コードの問題に関連しているため、App Service 診断は、[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) と統合して、選択されたダウンタイムと相関する例外と依存関係の問題を明らかにします。 Application Insights は、個別に有効化する必要があります。

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Application Insights の例外と依存関係を表示するには、 **[Web App Down]\(Web アプリ停止\)** または **[Web App Slow]\(Web アプリの低速化\)** タイル ショートカットを選択します。

### <a name="troubleshooting-steps-only-for-windows-app"></a>トラブルシューティングの手順 (Windows アプリのみ)

過去 24 時間以内に特定の問題カテゴリでの問題が検出された場合は、全体の診断レポートを表示することができます。また、トラブルシューティングの詳しいアドバイスや、ガイド付きのエクスペリエンスへさらに進むために、App Service 診断によって次のステップへと誘導される場合もあります。

![Application Insights およびトラブルシューティングと次のステップ](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>診断ツール (Windows アプリのみ)

診断ツールには、アプリケーション コードの問題、低速化、接続文字列などを調査できるより高度な診断ツールに加えて、 CPU 使用量、要求、およびメモリの問題を緩和できるプロアクティブ ツールが用意されています。

### <a name="proactive-cpu-monitoring"></a>プロアクティブ CPU 監視

プロアクティブ CPU 監視では、お使いのアプリやアプリの子プロセスにおいて多くの CPU リソースを費やしている場合に対策を取るための、簡単かつ事前対応型の方法を提供しています。 高い CPU 負荷の問題を一時的に緩和するために、予期しない問題の実質的な原因が検出されるまで、独自の CPU しきい値ツールを設定できます。

![プロアクティブ CPU 監視](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>プロアクティブ自動復旧

プロアクティブ CPU 監視と同様に、プロアクティブ自動復旧では、アプリの予期しない動作を緩和するための簡単かつ事前対応型の手法を提供しています。 要求数、低速の要求、メモリ制限、および HTTP ステータス コードに基づいて独自のルールを設定し、軽減のアクションをトリガーできます。 このツールは、問題の実質的な原因が検出されるまで、予期しない動作を一時的に軽減するために使用できます。 プロアクティブ自動復旧について詳しくは、「[Announcing the new auto healing experience in app service diagnostics (アプリ サービス診断における新しい自動復旧エクスペリエンスの告知)](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)」をご覧ください。

![プロアクティブ自動復旧](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis-only-for-windows-app"></a>変更分析 (Windows アプリのみ)

ペースの速い開発環境では、アプリに対して行われたすべての変更を継続的に追跡して、異常な動作の原因となった変更を単独で特定することは難しい場合があります。 変更分析を利用すると、アプリに対して行われた変更を絞り込み、トラブルシューティングのエクスペリエンスを円滑に進めることができます。 変更分析は **[Application Changes]\(アプリケーションの変更\)** にあり、 **[アプリケーションのクラッシュ]** などの診断レポートにも埋め込まれるため、他のメトリックと同時に使用できます。

変更分析は、機能を使用する前に、有効にする必要があります。 変更分析について詳しくは、「[Announcing the new change analysis experience in App Service Diagnostics (アプリ サービス診断における新しい変更分析エクスペリエンスの告知)](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)」をご覧ください。

![変更分析の既定のページ](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![差分ビュー](./media/app-service-diagnostics/diff-view-12.png)