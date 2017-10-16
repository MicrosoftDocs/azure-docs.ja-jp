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
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Application Insights を使用したクラウド サービスのトラブルシューティング
[Azure SDK 2.8](https://azure.microsoft.com/downloads/) と Azure 診断の拡張機能 1.5 を使用すると、クラウド サービスの Azure 診断データを Application Insights に直接送信できます。 Azure 診断によって収集されたログ (アプリケーション ログ、Windows イベント ログ、ETW ログ、パフォーマンス カウンターなど) を Application Insights に送信できます。 この情報は、Application Insights ポータルの UI で視覚化できます。 その後、Application Insights SDK を使用して、アプリケーションから取得したメトリックとログだけでなく、Azure 診断から取得したシステムやインフラストラクチャ レベルのデータから洞察を得ることができます。

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Application Insights にデータを送信するための Azure 診断の構成
Azure 診断データを Application Insights に送信するには、次の手順に従ってクラウド サービス プロジェクトをセットアップします。

1. Visual Studio のソリューション エクスプローラーで、ロールを右クリックし、**[プロパティ]** を選択して、ロール デザイナーを開きます。

    ![Solution Explorer Role Properties][1]

2. ロール デザイナーの **[診断]** セクションで、**[診断データを Application Insights に送信する]** チェック ボックスをオンにします。

    ![ロール デザイナーによる Application Insights への診断データの送信][2]

3. ポップアップ表示されたダイアログ ボックスで、Azure 診断データの送信先となる Application Insights リソースを選択します。 このダイアログ ボックスでは、サブスクリプションから既存の Application Insights リソースを選択するか、Application Insights リソースのインストルメンテーション キーを手動で指定することができます。 Application Insights リソースの作成方法の詳細については、「[Application Insights リソースの作成](../application-insights/app-insights-create-new-resource.md)」を参照してください。

    ![Application Insights リソースの選択][3]

    Application Insights リソースを追加すると、そのリソースのインストルメンテーション キーが **APPINSIGHTS_INSTRUMENTATIONKEY** という名前のサービス構成設定として保存されます。 サービスの構成または環境ごとに、この構成設定を変えることができます。 これを行うには、**[サービス構成]** の一覧からさまざまな構成を選び、その構成に対して新しいインストルメンテーション キーを指定します。

    ![サービス構成の選択][4]

    **APPINSIGHTS_INSTRUMENTATIONKEY** 構成設定は、発行時に適切な Application Insights リソース情報で診断の拡張機能を構成するために、Visual Studio によって使用されます。 この構成設定は、さまざまなサービス構成に異なるインストルメンテーション キーを定義するときに便利な方法です。 この設定は、発行プロセスの間に Visual Studio によって変換され、診断の拡張機能のパブリック構成に挿入されます。 診断の拡張機能を構成するプロセスを PowerShell で単純化するために、Visual Studio から出力されるパッケージには、該当する Application Insights インストルメンテーション キーを持つパブリック構成 XML も含まれています。 このパブリック構成ファイルは、*PaaSDiagnostics.&lt;RoleName&gt;.PubConfig.xml* というパターンの名前で、Extensions フォルダーに作成されます。 このパターンを PowerShell ベースのデプロイで使用すれば、各構成をロールにマップすることができます。

4) Azure 診断エージェントによって収集されるすべてのパフォーマンス カウンターとエラー レベルのログを Application Insights に送信するように Azure 診断を構成するには、**[診断データを Application Insights に送信する]** オプションを有効にします。 

    Application Insights に送信されるデータを細かく構成する場合は、ロールごとに *diagnostics.wadcfgx* ファイルを手動で編集する必要があります。 構成を手動で更新する方法の詳細については、「 [Application Insights にデータを送信するための Azure Diagnostics の構成](#configure-azure-diagnostics-to-send-data-to-application-insights) 」を参照してください。

Azure 診断データを Application Insights に送信するようにクラウド サービスを構成する場合は、そのクラウド サービスを通常と同じように Azure にデプロイし、Azure 診断の拡張機能が有効になっていることを確認します。 詳細については、「[Visual Studio を使用した Azure クラウド サービスの発行](../vs-azure-tools-publishing-a-cloud-service.md)」を参照してください。  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Application Insights での Azure 診断データの表示
Azure 診断テレメトリは、クラウド サービス用に構成された Application Insights リソースに表示されます。

Azure の診断ログの種類と Application Insights の概念は次のように対応しています。

* パフォーマンス カウンターは、Application Insights でカスタム メトリックとして表示されます。
* Windows イベント ログは、Application Insights でトレースとカスタム イベントとして表示されます。
* アプリケーション ログ、ETW ログ、診断インフラストラクチャ ログは、Application Insights でトレースとして表示されます。

Application Insights で Azure 診断データを表示するには、次のいずれかの操作をします。

* [メトリックス エクスプローラー](../application-insights/app-insights-metrics-explorer.md) を使用して、任意のカスタム パフォーマンス カウンターや、さまざまな種類の Windows イベント ログのイベント数を視覚化します。

    ![メトリックス エクスプローラーのカスタム メトリックス][5]

* [検索](../application-insights/app-insights-diagnostic-search.md)を使用して、Azure 診断によって送信されるトレース ログを検索します。 たとえば、ロールでハンドルされない例外が発生して、ロールがクラッシュし、リサイクルされた場合、*Windows イベント ログ*の *[アプリケーション]* チャネルにその例外に関する情報が表示されます。 ユーザーは、検索を使用して Windows イベント ログのエラーを確認し、例外の完全なスタック トレースを取得できるので、問題の原因を突き止めるために役立ちます。

    ![トレースの選択][6]

## <a name="next-steps"></a>次のステップ
* [クラウド サービスに Application Insights SDK を追加](../application-insights/app-insights-cloudservices.md) して、アプリケーションから要求、例外、依存関係、および任意のカスタム テレメトリに関するデータを送信します。 この情報は、Azure 診断データと組み合わせることで、アプリケーションとシステムを、すべて同じ Application Insight リソースとして包括的に確認できます。  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
