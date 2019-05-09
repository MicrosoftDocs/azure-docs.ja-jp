---
title: Application Insights による SCOM 統合 | Microsoft Docs
description: SCOM をご使用の場合は、Application Insights でパフォーマンスを監視して問題を診断します。 包括的なダッシュ ボード、スマート アラート、強力な診断ツール、および分析クエリです。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: a08a67a78362325e29b1002b44f390a94e78888a
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54027838"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>SCOM での Application Insights を使用したアプリケーション パフォーマンス監視
サーバーの管理に System Center Operations Manager (SCOM) を使用する場合、[Azure Application Insights](../../azure-monitor/app/asp-net.md)を利用して、パフォーマンスを監視し、パフォーマンスの問題を診断することができます。 Application Insights では、Web アプリケーションに入力される要求、出力される REST と SQL、例外、ログ トレースが監視されます。 また、メトリック グラフとスマート アラートだけでなく、このテレメトリに対する強力な診断検索と分析クエリがダッシュボードに用意されています。 

SCOM 管理パックを使用して、Application Insights の監視方法を切り替えることができます。

> [!IMPORTANT]
> 現在、この System Center Operations Manager 管理パックは**非推奨**です。 最新の Application Insights SDK をサポートしていないため、推奨されなくなりました。

## <a name="before-you-start"></a>開始する前に
次を想定しています。

* SCOM を使い慣れていて、IIS Web サーバー の管理に SCOM 2012 R2 または 2016 を使用している。
* Application Insights を使用して監視する Web アプリケーションをサーバーに既にインストールしている。
* アプリケーション フレームワークのバージョンが .NET 4.5 以降である。
* [Microsoft Azure](https://azure.com) のサブスクリプションに対するアクセス権があり、[Azure Portal](https://portal.azure.com) にサインインできる。 組織でサブスクリプションを所有でき、それにユーザーの Microsoft アカウントを追加できる。

(開発チームは [Application Insights SDK](../../azure-monitor/app/asp-net.md) を Web アプリに組み込むこともできます。 このビルド時のインストルメンテーションにより、カスタムのテレメトリをさらに柔軟に記述できます。 ただし、これは重要ではありません。SDK をビルドするかどうかに関係なく、ここで説明されている手順に従うことができます)。

## <a name="one-time-install-application-insights-management-pack"></a>(1 回) Application Insights の管理パックをインストールする
Operations Manager が実行されているコンピューターで以下の手順に従います。

1. 以前のバージョンの管理パックをアンインストールします。
   1. Operations Manager で、[管理]、[管理パック] を開きます。 
   2. 以前のバージョンを削除します。
2. カタログから管理パックをダウンロードしてインストールします。
3. Operations Manager を再起動します。

## <a name="create-a-management-pack"></a>管理パックを作成する
1. Operations Manager で **[作成]**、**[.NET...with Application Insights (Application Insights を使用した .NET アプリケーション パフォーマンス監視)]**、**[監視の追加ウィザード]** の順に開き、**[.NET...with Application Insights (Application Insights を使用した .NET アプリケーション パフォーマンス監視)]** を再度選択します。
   
    ![](./media/scom/020.png)
2. アプリにちなんで構成に名前を付けます  (1 回に 1 つのアプリケーションをインストルメント化する必要があります)。
   
    ![](./media/scom/030.png)
3. 同じウィザード ページで、新しい管理パックを作成するか、Application Insights 用に先ほど作成したパックを選択します 
   
     (Application Insights の [管理パック](https://technet.microsoft.com/library/cc974491.aspx) は、インスタンスを作成するためのテンプレートです。 このインスタンスは後で再利用できます)。

    ![[全般プロパティ] タブで、アプリの名前を入力します。 [新規] をクリックし、管理パックの名前を入力します。 [OK]、[次へ] の順にクリックします。](./media/scom/040.png)

1. 監視対象のアプリを 1 つ選択します。 検索機能では、サーバーにインストールされているアプリが検索されます。
   
    ![[What to Monitor (監視対象)] タブの [追加] をクリックして、アプリ名の一部を入力し、[検索] をクリックします。アプリを選択して [追加]、[OK] を選択します。](./media/scom/050.png)
   
    すべてのサーバー内のアプリを監視するわけではない場合は、[監視範囲] フィールドを使用してサーバーのサブセットを指定することができます (この操作は省略可能です)。
2. 次のウィザード ページで、Microsoft Azure にサインインするための資格情報をまず指定する必要があります。
   
    このページでは、テレメトリ データの分析と表示を行う Application Insights リソースをクリックします。 
   
   * アプリケーションが、開発時に Application Insights 用に構成されている場合は、既存のリソースを選択します。
   * それ以外の場合は、アプリの名前が付いた新しいリソースを作成します。 同じシステムのコンポーネントとして他にもアプリがある場合は、同じリソース グループに配置して、テレメトリへのアクセスを簡単に管理できるようにします。
     
     これらの設定は後で変更できます。
     
     ![[Application Insights settings (Application Insights の設定)] タブの [サインイン] をクリックし、Azure 用の Microsoft アカウントの資格情報を指定します。 次に、サブスクリプション、リソース グループ、リソースをクリックします。](./media/scom/060.png)
3. ウィザードを終了します。
   
    ![Create をクリックしてください。](./media/scom/070.png)

監視対象のアプリごとにこの手順を繰り返します。

後で設定を変更する必要がある場合は、[作成] ウィンドウからモニターのプロパティを再度開きます。

![[作成] で、[NET Application Performance Monitoring with Application Insights (Application Insights を使用した .NET アプリケーション パフォーマンス監視)] を選択し、モニターを選択して、プロパティをクリックします。](./media/scom/080.png)

## <a name="verify-monitoring"></a>監視を確認する
インストールしたモニターでは、すべてのサーバーのアプリが検索されます。 アプリが見つかる場所には、アプリを監視するために Application Insights Status Monitor が構成されます。 必要に応じて、最初に状態モニターがサーバーにインストールされます。

どのアプリのインスタンスが検出されたかを確認できます。

![[監視] で Application Insights を開く](./media/scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Application Insights でテレメトリを表示する
[Azure Portal](https://portal.azure.com) で、アプリのリソースを参照します。 アプリから[テレメトリを示すグラフを参照](../../azure-monitor/app/app-insights-dashboards.md)します  (メイン ページに表示されない場合は、[ライブ メトリック ストリーム] をクリックします)。

## <a name="next-steps"></a>次の手順
* [ダッシュ ボードを設定](../../azure-monitor/app/app-insights-dashboards.md) し、これと他のアプリを監視する最も重要なグラフをまとめて表示します。
* [メトリックの詳細](../../azure-monitor/app/metrics-explorer.md)
* [アラートの設定](../../azure-monitor/app/alerts.md)
* [パフォーマンスの問題の診断](../../azure-monitor/app/detect-triage-diagnose.md)
* [分析クエリ](../../azure-monitor/app/analytics.md)
* [可用性 Web テスト](../../azure-monitor/app/monitor-web-app-availability.md)

