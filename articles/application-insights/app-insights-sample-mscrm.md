---
title: "Microsoft Dynamics CRM と Azure Application Insights | Microsoft Docs"
description: "Application Insights を使用して Microsoft Dynamics CRM Online からテレメトリを取得します。 設定、データの取得、視覚化、およびエクスポートをしてみましょう。"
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: carmonm
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 948c894cba1b8bb513a26d720cbe433b38353d03
ms.contentlocale: ja-jp
ms.lasthandoff: 04/18/2017

---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>チュートリアル: Application Insights を使用して Microsoft Dynamics CRM Online のテレメトリを有効にする
この記事では、[Azure Application Insights](https://azure.microsoft.com/services/application-insights/) を使用して [Microsoft Dynamics CRM Online](https://www.dynamics.com/) からテレメトリ データを取得する方法について説明します。 アプリケーションに Application Insights のスクリプトを追加し、データをキャプチャし、データを視覚化するすべてのプロセスを見てみましょう。

> [!NOTE]
> [サンプル ソリューションを参照します](https://dynamicsandappinsights.codeplex.com/)。
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>新規または既存の CRM Online インスタンスに Application Insights を追加する
アプリケーションを監視するには、アプリケーションに Application Insights SDK を追加します。 この SDK はテレメトリを [Application Insights ポータル](https://portal.azure.com)に送信します。そのポータルでは、強力な分析ツールおよび診断ツールを使用したり、データをストレージにエクスポートしたりできます。

### <a name="create-an-application-insights-resource-in-azure"></a>Azure で Application Insights のリソースを作成する
1. [Microsoft Azure のアカウント](http://azure.com/pricing)を取得します。 
2. [Azure Portal](https://portal.azure.com) にサインインし、Application Insights の新しいリソースを追加します。 これは、データを処理し表示する場所になります。
   
    ![[+]、[開発者向けサービス]、[Application Insights] をクリックします。](./media/app-insights-sample-mscrm/01.png)
   
    アプリケーションの種類として ASP.NET を選択します。
3. [はじめに] ページを開き、[クライアント側アプリケーションの監視と診断] を開きます。
   
    ![Web ページに挿入するためのコード スニペット](./media/app-insights-sample-mscrm/03.png)

**このコードのページは開いたまま** 、別のブラウザー ウィンドウで次の手順を行います。 このコードはすぐに必要になります。 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Microsoft Dynamics CRM で JavaScript の Web リソースを作成する
1. CRM Online インスタンスを開き、管理者権限でログインします。
2. Microsoft Dynamics CRM で、[設定]、[カスタマイズ]、[システムのカスタマイズ] の順に開きます。
   
    ![Microsoft Dynamics CRM 設定](./media/app-insights-sample-mscrm/04.png)
   
    ![[設定]、[カスタマイズ]](./media/app-insights-sample-mscrm/05.png)

    ![[システムのカスタマイズ] オプション](./media/app-insights-sample-mscrm/06.png)

1. JavaScript リソースを作成します。
   
    ![新しい [Web リソース] ダイアログ](./media/app-insights-sample-mscrm/07.png)
   
    名前を付け、[ **スクリプト (JScript)** ] を選択してテキスト エディターを開きます。
   
    ![テキスト エディターを開く](./media/app-insights-sample-mscrm/08.png)
2. Application Insights からコードをコピーします。 コピーの際はスクリプト タグを無視します。 次のスクリーン ショットを参照してください。
   
    ![インストルメンテーション キーを設定する](./media/app-insights-sample-mscrm/09.png)
   
    コードには Application Insights リソースを識別するインストルメンテーション キーが含まれています。
3. 保存して発行します。
   
    ![保存して発行する](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>フォームをインストルメント化する
1. Microsoft CRM Online で Account フォームを開きます。
   
    ![[取引先企業] フォーム](./media/app-insights-sample-mscrm/11.png)
2. フォームのプロパティを開きます。
   
    ![[フォームのプロパティ]](./media/app-insights-sample-mscrm/12.png)
3. 作成した JavaScript の Web リソースを追加します。
   
    ![[追加] メニュー](./media/app-insights-sample-mscrm/13.png)
   
    ![Web リソースを追加する](./media/app-insights-sample-mscrm/14.png)
4. 保存し、フォームのカスタマイズ内容を発行します。

## <a name="metrics-captured"></a>キャプチャされるメトリック
フォームのテレメトリ キャプチャを設定しました。 使用するたびに、データが Application Insights のリソースに送信されます。

表示されるデータのサンプルを次に示します。

#### <a name="application-health"></a>アプリケーションの健全性
![ページ読み込み時間の例](./media/app-insights-sample-mscrm/15.png)

![ページ ビュー グラフの例](./media/app-insights-sample-mscrm/16.png)

ブラウザーの例外:

![ブラウザーの例外グラフ](./media/app-insights-sample-mscrm/17.png)

詳細情報を表示するグラフをクリックします。

![例外リスト](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>使用法
![ユーザー、セッション、ページ ビュー](./media/app-insights-sample-mscrm/19.png)

![セッション グラフ](./media/app-insights-sample-mscrm/20.png)

![ブラウザーのバージョン](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>ブラウザー
![ページ読み込み時間の内訳](./media/app-insights-sample-mscrm/22.png)

![ブラウザーのバージョン別のセッション数](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>地理的位置情報
![国別のセッション数](./media/app-insights-sample-mscrm/24.png)

![国別のセッションとユーザー](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>内部ページ表示要求
![ページ ビューの概要](./media/app-insights-sample-mscrm/26.png)

![ページ ビュー イベントの検索](./media/app-insights-sample-mscrm/27.png)

![類似のページ ビュー](./media/app-insights-sample-mscrm/28.png)

![ページ ビュー プロパティ](./media/app-insights-sample-mscrm/29.png)

![セッションごとのページ数](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>サンプル コード
[サンプル コードを参照します](https://dynamicsandappinsights.codeplex.com/)。

## <a name="power-bi"></a>Power BI
[データを Microsoft Power BI にエクスポート](app-insights-export-power-bi.md)すると、より深い分析を行うことができます。

## <a name="sample-microsoft-dynamics-crm-solution"></a>Microsoft Dynamics CRM ソリューションのサンプル
[Microsoft Dynamics CRM で実装されたサンプル ソリューションを示します](https://dynamicsandappinsights.codeplex.com/)。

## <a name="learn-more"></a>詳細情報
* [Application Insights とは何か?](app-insights-overview.md)
* [Web ページ向けの Application Insights](app-insights-javascript.md)
* [その他のサンプルとチュートリアル](app-insights-code-samples.md)

