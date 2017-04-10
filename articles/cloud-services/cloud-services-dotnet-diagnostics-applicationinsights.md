---
title: "Application Insights を使用したクラウド サービスのトラブルシューティング | Microsoft Docs"
description: "Application Insights を使用して Azure 診断データを処理することで、クラウド サービスの問題をトラブルシューティングする方法について説明します。"
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2015
ms.author: saurabh
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 78d8908a144dadb5fe9d4c48491abf153defe118
ms.lasthandoff: 03/23/2017


---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Application Insights を使用したクラウド サービスのトラブルシューティング
[Azure SDK 2.8](https://azure.microsoft.com/downloads/) と Azure 診断の拡張機能 1.5 を使用して、クラウド サービスの Azure 診断データを Application Insights に直接送信できるようになりました。 アプリケーション ログ、Windows イベント ログ、ETW ログ、パフォーマンス カウンターなど、Azure 診断によって収集されるさまざまな種類のログを Application Insights に送信し、Application Insights ポータル UI で視覚化できます。 Application Insights SDK と共に使用すると、Azure 診断のシステムやインフラストラクチャ レベルのデータだけでなくアプリケーションから取得したメトリックとログの洞察を得ることができます。

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Application Insights にデータを送信するための Azure 診断の構成
Azure 診断データを Application Insights に送信するには、次の手順に従ってクラウド サービス プロジェクトをセットアップします。

1) Visual Studio のソリューション エクスプローラーで、ロールを右クリックし、**[プロパティ]** を選択して、ロール デザイナーを開きます。

![Solution Explorer Role Properties][1]

2) ロール デザイナーの [診断] セクションで、**[診断データを Application Insights に送信する]** チェック ボックスをオンにします。

![ロール デザイナーによる Application Insights への診断データの送信][2]

3) ポップアップ表示されたダイアログ ボックスで、Azure 診断データの送信先となる Application Insights リソースを選択します。 このダイアログ ボックスでは、サブスクリプションから既存の Application Insights リソースを選択するか、Application Insights リソースのインストルメンテーション キーを手動で指定することができます。 既存の Application Insights リソースがない場合は、**[新しいリソースを作成する]** リンクをクリックして、リソースを作成できます。このリンクをクリックすると、ブラウザー ウィンドウが開いて Azure クラシック ポータルが表示され、そこで Application Insights リソースを作成できます。 Application Insights リソースの作成方法の詳細については、「[新しい Application Insights リソースの作成](../application-insights/app-insights-create-new-resource.md)」を参照してください。

![Application Insights リソースの選択][3]

4) Application Insights リソースを追加すると、そのリソースのインストルメンテーション キーが **APPINSIGHTS_INSTRUMENTATIONKEY** という名前のサービス構成設定として保存されます。 サービス構成ごとまたは環境ごとにこの構成設定を変更するには、[サービス構成] ボックスから別の構成を選択し、その構成の新しいインストルメンテーション キーを指定します。

![サービス構成の選択][4]

**APPINSIGHTS_INSTRUMENTATIONKEY** 構成設定は、発行時に適切な Application Insights リソース情報で診断の拡張機能を構成するために、Visual Studio によって使用されます。 この構成設定は、さまざまなサービス構成に異なるインストルメンテーション キーを定義するときに便利な方法です。 この設定は、発行時に Visual Studio によって変換され、診断の拡張機能のパブリック構成に挿入されます。 PowerShell を使用して診断の拡張機能を構成するプロセスを単純化するために、Visual Studio から出力されたパッケージには、該当する Application Insights インストルメンテーション キーを格納したパブリック構成 XML も含まれます。 このパブリック構成ファイルは、PaaSDiagnostics<RoleName>.PubConfig.xml という名前で拡張機能フォルダーに作成されます。 このファイルを PowerShell ベースのデプロイで使用し、各構成をロールにマップすることができます。

5) **[診断データを Application Insights に送信する]** チェック ボックスをオンにすると、Azure 診断エージェントによって収集されるすべてのパフォーマンス カウンターとエラー レベルのログを Application Insights に送信するように、Azure 診断が自動的に構成されます。 Application Insights に送信されるデータを細かく構成する場合は、ロールごとに *diagnostics.wadcfgx* ファイルを手動で編集する必要があります。 構成を手動で更新する方法の詳細については、「 [Application Insights にデータを送信するための Azure Diagnostics の構成](#configure-azure-diagnostics-to-send-data-to-application-insights) 」を参照してください。

Azure 診断データを Application Insights に送信するようにクラウド サービスを構成したら、通常と同じように Azure にデプロイし、Azure 診断の拡張機能が有効なことを確認できます。 詳細については、 [Visual Studio を使用したクラウド サービスの発行](../vs-azure-tools-publishing-a-cloud-service.md)に関するトピックを参照してください。  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Application Insights での Azure 診断データの表示
Azure 診断テレメトリは、クラウド サービス用に構成された Application Insights リソースに表示されます。

Azure 診断のさまざまなログの種類と Application Insights の概念は次のように対応しています。  

* パフォーマンス カウンターは、Application Insights でカスタム メトリックとして表示されます。
* Windows イベント ログは、Application Insights でトレースとカスタム イベントとして表示されます。
* アプリケーション ログ、ETW ログ、診断インフラストラクチャ ログは、Application Insights でトレースとして表示されます。

Application Insights で Azure 診断データを表示するには:

* [メトリックス エクスプローラー](../application-insights/app-insights-metrics-explorer.md) を使用して、任意のカスタム パフォーマンス カウンターや、さまざまな種類の Windows イベント ログのイベントの数を視覚化します。

![メトリックス エクスプローラーのカスタム メトリックス][5]

* [検索](../application-insights/app-insights-diagnostic-search.md) を使用して、Azure 診断によって送信されるさまざまなトレース ログを検索します。 たとえば、ロールでハンドルされない例外が発生して、ロールがクラッシュし、リサイクルされた場合、*Windows イベント ログ*の*アプリケーション* チャネルにその情報が表示されます。 検索機能を使用して、Windows イベント ログのエラーを確認し、例外のスタック トレース全体を取得することで、問題の根本原因を発見できます。

![トレースの選択][6]

## <a name="next-steps"></a>次のステップ
* [クラウド サービスに Application Insights SDK を追加](../application-insights/app-insights-cloudservices.md) して、アプリケーションから要求、例外、依存関係、および任意のカスタム テレメトリに関するデータを送信します。 Azure 診断データと組み合わせることで、同じ Application Insight リソース内のすべてのアプリケーションとシステムを包括的に確認できます。  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

