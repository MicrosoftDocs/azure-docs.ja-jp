---
title: "Application Insights での HockeyApp データの探索 | Microsoft Docs"
description: "Application Insights を使用して Azure アプリの使用状況とパフォーマンスを分析します。"
services: application-insights
documentationcenter: windows
author: alancameronwills
manager: douge
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: b83f9c59e44348cb23a4786da907f3c6d8530683


---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Application Insights での HockeyApp データの探索
[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) は、稼働中のデスクトップ アプリとモバイル アプリの監視に推奨されているプラットフォームです。 HockeyApp から、(クラッシュ データの取得だけでなく) カスタム テレメトリやトレース テレメトリを送信して、使用状況を監視し、診断に役立てることができます。 このテレメトリのストリームは、[Azure Application Insights](app-insights-overview.md) の強力な [Analytics](app-insights-analytics.md) 機能を使用して照会できます。 さらに、 [カスタム テレメトリとトレース テレメトリをエクスポートする](app-insights-export-telemetry.md)こともできます。 これらの機能を有効にするには、HockeyApp データを Application Insights を中継するブリッジを設定します。

## <a name="the-hockeyapp-bridge-app"></a>HockeyApp ブリッジ アプリ
HockeyApp ブリッジ アプリは、Application Insights 内の HockeyApp データに Analytics 機能や連続エクスポート機能を使用してアクセスできるようにする核となる機能です。 HockeyApp ブリッジ アプリの作成後に HockeyApp で収集されたすべてのデータは、これらの機能からアクセスできるようになります。 このようなブリッジ アプリの 1 つを設定する方法を確認しましょう。

HockeyApp で [Account Settings (アカウント設定)]、 [[API Tokens (API トークン)]](https://rink.hockeyapp.net/manage/auth_tokens)の順に開きます。 新しいトークンを作成するか、既存のトークンを再利用します。 少なくとも "読み取り専用" の権限が必要です。 API トークンの控えを取ります。

![Get a HockeyApp API token](./media/app-insights-hockeyapp-bridge-app/01.png)

Microsoft Azure Portal を開いて、 [Application Insights のリソースを作成](app-insights-create-new-resource.md)します。 [アプリケーションの種類] を [HockeyApp bridge application (HockeyApp ブリッジ アプリケーション)] に設定します。

![New Application Insights resource](./media/app-insights-hockeyapp-bridge-app/02.png)

名前を設定する必要はありません。HockeyApp 名から自動的に設定されません。

HockeyApp ブリッジのフィールドが表示されます。 

![Enter bridge fields](./media/app-insights-hockeyapp-bridge-app/03.png)

前の手順で控えを取った HockeyApp トークンを入力します。 この操作により、[HockeyApp Application (HockeyApp アプリケーション)] ドロップダウン メニューにすべての HockeyApp アプリケーションが設定されます。 使用するアプリケーションを 1 つ選択すると、残りのフィールドにも値が設定されます。 

新しいリソースを開きます。 

データの送信が開始されるまで、しばらく時間がかかります。

![Application Insights resource waiting for data](./media/app-insights-hockeyapp-bridge-app/04.png)

これで終了です。 これ以降、HockeyApp でインストルメント化されたアプリで収集されたデータはすべて、Application Insights の Analytics 機能と連続エクスポート機能でも利用できるようになります。

利用可能になったこれらの機能それぞれについて簡単に確認しましょう。

## <a name="analytics"></a>分析
Analytics はデータのアドホック クエリのための強力なツールです。使用すると、テレメトリを診断、分析し、根本原因とパターンを迅速に突き止めることができます。

![[分析]](./media/app-insights-hockeyapp-bridge-app/05.png)

* [Analytics の詳細](app-insights-analytics-tour.md)
* [紹介ビデオ](https://channel9.msdn.com/events/Build/2016/T666)
* [高度な概念に関するビデオ](https://channel9.msdn.com/Events/Build/2016/P591)

## <a name="continuous-export"></a>連続エクスポート
連続エクスポートを使用すると、Azure Blob Storage コンテナーにデータをエクスポートできます。 これは、Application Insights で現在提供されている保有期間よりも長くデータを保持する必要がある場合に非常に便利です。 データは、Blob Storage で保持し、SQL データベースやお好みのデータ ウェアハウス ソリューションに加工処理することができます。

[連続エクスポートの詳細](app-insights-export-telemetry.md)

## <a name="next-steps"></a>次のステップ
* [Analytics をデータに適用する](app-insights-analytics-tour.md)




<!--HONumber=Nov16_HO3-->


