---
title: Azure Application Insights データを利用したカスタム レポートの自動化
description: Azure Application Insights データを利用して 日/週/月 1 回のレポートを自動化します
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 37de2de18656f0f8ba85495b3fd72315e0bd885b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113080"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Azure Application Insights データを利用したカスタム レポートの自動化

定期レポートは､ビジネスに重要なサービスの実施状況についてチームが常に情報を入手するのに役立ちます｡ 開発者や DevOps/SRE チーム､彼らのマネージャーは､誰もポータルにサインインしなくても､自動化されたレポートによって信頼できる形で知見が提供されることで生産的であることができます。 そうしたレポートによって､アラート ルールをトリガーすることのない待ち時間や負荷､故障発生率の漸増を発見することもできます｡

エンタープライズはそれぞれに次のような独自のレポート ニーズがあります｡ 

* メトリックの特定の百分率集計またはカスタム メトリックのレポート
* 報告対象者に応じたデータの 日､ 週､および月 1 回のロールアップ レポート
* リージョンや環境などのカスタム 属性別に区分けしたレポート 
* いくつかの AI リソースを 1 つのグループにまとめたレポート (リソースの属するサブスクリプションあるいはリソース グループが異なっていてもよい)
* 報告対象者別の送信した重要なメトリックからなるレポート
* ポータル リソースへのアクセス権を持たない利害関係者向けレポート

> [!NOTE] 
> 週 1 回の Application Insights ダイジェスト 電子メールはカスタマイズができず、以下のカスタム オプションで代替できることから廃止されます。 週 1 回の最後のダイジェスト電子メールは 2018 年 6 月 11 日に送信されます。 次のオプションのいずれかを構成して、類似のカスタム レポートを取得するようにしてください （以下で提案するクエリを利用）。

## <a name="to-automate-custom-report-emails"></a>カスタム レポート電子メールを自動化する

[Application Insights データに対してプログラムからクエリを実行する](https://dev.applicationinsights.io/) ことで、スケジュールに従ってカスタム レポートを作成できます。 次のオプションを利用して、すぐに始めることができます。

* [Microsoft Flow を利用してレポートを自動化する](automate-with-flow.md)
* [Logic Apps を利用してレポートを自動化する](automate-with-logic-apps.md)
* Monitoring シナリオで "Application Insights scheduled digest" [Azure 関数](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function)テンプレートを利用する。 この関数は SendGrid を使って電子メールを配信します。 

    ![Azure 関数テンプレート](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>月 1 回のレポート用のクエリ例
次のクエリは、レポートのような月 1 回のダイジェスト電子メール用に複数のデータセットにまたがって結合を行う例です。 必要に応じてカスタマイズし、上記のオプションと組み合わせることで週 1 回のレポートを自動化することができます。   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Application Insights スケジュールされたダイジェスト レポート

1. Azure Portal から、**[リソースの作成]** > **[コンピューティング]** > **[Function App]** の順に選択します。

   ![Azure リソース Function App を作成する画面のスクリーンショット](./media/automate-custom-reports/function-app-01.png)

2. アプリに関する適切な情報を入力し、_[作成]_ を選択します。 ([Application Insights] は、新しい Function App を Application Insights で監視する必要がある場合にのみ、_[オン]_ にします)

   ![Azure リソース Function App の設定を作成する画面のスクリーンショット](./media/automate-custom-reports/function-app-02.png)

3. 新しい Function App のデプロイが完了したら、**[リソースに移動]** を選択します。

4. **[新しい関数]** を選択します。

   ![新しい関数を作成する画面のスクリーンショット](./media/automate-custom-reports/function-app-03.png)

5. **_Application Insights スケジュールされたダイジェスト テンプレート_** を選択します。

   ![新しい関数の Application Insights テンプレートのスクリーン ショット](./media/automate-custom-reports/function-app-04.png)

6. レポートの適切な受信者の電子メール アドレスを入力し、**[作成]** を選択します。

   ![関数の設定のスクリーンショット](./media/automate-custom-reports/function-app-05.png)

7. 作成した **Function App** > **[プラットフォーム機能]** > **[アプリケーション設定]** を選択します。

    ![Azure 関数のアプリケーション設定のスクリーンショット](./media/automate-custom-reports/function-app-07.png)

8. 適切な値 (``AI_APP_ID``、 ``AI_APP_KEY``、および ``SendGridAPI``) を使用して、3 つの新しいアプリケーション設定を作成します。 **[保存]** を選択します。

     ![関数の統合インターフェイスのスクリーンショット](./media/automate-custom-reports/function-app-08.png)
    
    (AI_ の値は、レポート対象となる Application Insights リソースの [API アクセス] で確認できます。 Application Insights API キーがない場合は、**[API キーの作成]** を使って作成できます。)
    
   * AI_APP_ID = アプリケーション ID
   * AI_APP_KEY = API キー
   * SendGridAPI =SendGrid API キー

     > [!NOTE]
     > SendGrid アカウントがない場合は、 作成できます。 Azure Functions での SendGrid の使用に関するドキュメントは、[こちら](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid)で参照できます。 SendGrid の設定方法と API キーの生成方法に関する簡単な説明は、この記事の最後に記載されています。 

9. **[統合]** を選択し、[出力] の下で **[SendGrid ($return)]** をクリックします。

     ![出力のスクリーンショット](./media/automate-custom-reports/function-app-09.png)

10. **[SendGrid API キーのアプリ設定]** で、**SendGridAPI** 用に新しく作成したアプリ設定を選択します。

     ![Function App を実行する画面のスクリーンショット](./media/automate-custom-reports/function-app-010.png)

11. Function App を実行し、テストします。

     ![テストのスクリーンショット](./media/automate-custom-reports/function-app-11.png)

12. 電子メールをチェックして、メッセージが正常に送受信されたことを確認します。

     ![電子メールの件名行のスクリーンショット](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Azure での SendGrid の設定

以下の手順は、SendGrid アカウントをまだ構成していない場合にのみ行います。

1. Azure Portalから **[Create a resource]\(リソースの作成\)** を選択し、**[SendGrid Email Delivery]\(SendGrid 電子メール配信\)** を検索して **[作成]** をクリックし、SendGrid 固有の作成指示を入力します。 

     ![SendGrid リソースを作成する画面のスクリーンショット](./media/automate-custom-reports/function-app-13.png)

2. 作成されたら、[SendGrid アカウント] で **[管理]** を選択します。

     ![API キーを設定する画面のスクリーンショット](./media/automate-custom-reports/function-app-14.png)

3. これにより、SendGrid のサイトが起動します。 **[設定]** > **[API キー]** を選択します。

     ![API キー アプリを作成して表示する画面のスクリーンショット](./media/automate-custom-reports/function-app-15.png)

4. API キーを作成し、**[Create & View]\(作成して表示\)** を選択します (SendGrid のドキュメントでアクセス制限に関する説明を参照し、API キーに対してどのレベルのアクセス許可が適切かを確認してください。 ここでは、あくまでも例としてフル アクセスを選択しています。)

   ![フル アクセスのスクリーンショット](./media/automate-custom-reports/function-app-16.png)

5. キー全体をコピーします。この値は、Function App の設定で SendGridAPI の値として必要になります

   ![API キーをコピーする画面のスクリーンショット](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>次の手順

* [Analytics クエリ](../../azure-monitor/log-query/get-started-queries.md)の作成についての詳細を見る
* [Application Insights データに対してプログラムからクエリを実行する](https://dev.applicationinsights.io/)の詳細を見る
* [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps) の詳細を見る
* [Microsoft Flow](https://ms.flow.microsoft.com)についての詳細を見る
