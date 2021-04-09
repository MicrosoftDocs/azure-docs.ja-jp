---
title: Azure Application Insights データを利用したカスタム レポートの自動化
description: Azure Application Insights データを利用して 日/週/月 1 回のレポートを自動化します
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.openlocfilehash: 39970227fc94d3073688b23cc530462f368a6ecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590041"
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

* [Power Automate を利用してレポートを自動化する](../logs/logicapp-flow-connector.md)
* [Logic Apps を利用してレポートを自動化する](automate-with-logic-apps.md)
* Monitoring シナリオで "Application Insights scheduled digest" [Azure 関数](../../azure-functions/functions-get-started.md)テンプレートを利用する。 この関数は SendGrid を使って電子メールを配信します。 

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

1. Azure Function App を作成します。(Application Insights で新しい Function App を監視する必要がある場合にのみ、Application Insights を _[オン]_ にする必要があります)

   [関数アプリの作成](../../azure-functions/functions-get-started.md)方法については、Azure Functions のドキュメントを参照してください。

2. 新しい Function App のデプロイが完了したら、 **[リソースに移動]** を選択します。

3. **[新しい関数]** を選択します。

   ![新しい関数を作成する画面のスクリーンショット](./media/automate-custom-reports/new-function.png)

4. **_Application Insights スケジュールされたダイジェスト テンプレート_** を選択します。

     > [!NOTE]
     > 既定では、関数アプリはランタイム バージョン 3.x で作成されます。 Application Insights のスケジュール済みダイジェスト テンプレートを使用するには、[Azure Functions ランタイム バージョン **1.x** をターゲットにする](../../azure-functions/set-runtime-version.md)必要があります。 [構成]、[関数のランタイム設定] の順に移動して、ランタイム バージョンを変更します。 ![ランタイムのスクリーンショット](./media/automate-custom-reports/change-runtime-v.png)

   ![新しい関数の Application Insights テンプレートのスクリーン ショット](./media/automate-custom-reports/function-app-04.png)

5. レポートの適切な受信者の電子メール アドレスを入力し、 **[作成]** を選択します。

   ![関数の設定のスクリーンショット](./media/automate-custom-reports/scheduled-digest.png)

6. 自分の **Function App** >  **[プラットフォーム機能]**  >  **[構成]** を選択します。

    ![Azure 関数のアプリケーション設定のスクリーンショット](./media/automate-custom-reports/config.png)

7. 適切な値 (``AI_APP_ID``、 ``AI_APP_KEY``、および ``SendGridAPI``) を使用して、3 つの新しいアプリケーション設定を作成します。 **[保存]** を選択します。

     ![関数の統合インターフェイスのスクリーンショット](./media/automate-custom-reports/app-settings.png)
    
    (AI_ の値は、レポート対象となる Application Insights リソースの [API アクセス] で確認できます。 Application Insights API キーがない場合は、 **[API キーの作成]** を使って作成できます。)
    
   * AI_APP_ID = アプリケーション ID
   * AI_APP_KEY = API キー
   * SendGridAPI =SendGrid API キー

     > [!NOTE]
     > SendGrid アカウントがない場合は、 作成できます。 Azure Functions での SendGrid の使用に関するドキュメントは、[こちら](../../azure-functions/functions-bindings-sendgrid.md)で参照できます。 SendGrid の設定方法と API キーの生成方法に関する簡単な説明は、この記事の最後に記載されています。 

8. **[統合]** を選択し、[出力] の下で **[SendGrid ($return)]** をクリックします。

     ![出力のスクリーンショット](./media/automate-custom-reports/integrate.png)

9. **[SendGrid API キーのアプリ設定]** で、**SendGridAPI** 用に新しく作成したアプリ設定を選択します。

     ![Function App を実行する画面のスクリーンショット](./media/automate-custom-reports/sendgrid-output.png)

10. Function App を実行し、テストします。

     ![テストのスクリーンショット](./media/automate-custom-reports/function-app-11.png)

11. 電子メールをチェックして、メッセージが正常に送受信されたことを確認します。

     ![電子メールの件名行のスクリーンショット](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>Azure での SendGrid の設定

以下の手順は、SendGrid アカウントをまだ構成していない場合にのみ行います。

1. Azure portal から **[リソースの作成]** を選択し、**SendGrid Email Delivery** を検索して **[作成]** をクリックし、SendGrid 固有の作成指示を入力します。

     ![SendGrid リソースを作成する画面のスクリーンショット](./media/automate-custom-reports/sendgrid.png)

2. 作成されたら、[SendGrid アカウント] で **[管理]** を選択します。

     ![API キーを設定する画面のスクリーンショット](./media/automate-custom-reports/sendgrid-manage.png)

3. これにより、SendGrid のサイトが起動します。 **[設定]**  >  **[API キー]** を選択します。

     ![API キー アプリを作成して表示する画面のスクリーンショット](./media/automate-custom-reports/function-app-15.png)

4. API キーを作成し、 **[Create & View]\(作成して表示\)** を選択します。 (SendGrid のドキュメントでアクセス制限について確認し、API キーに対してどのレベルのアクセス許可が適切かを判断してください。 ここでは、あくまでも例としてフル アクセスを選択しています。)

   ![フル アクセスのスクリーンショット](./media/automate-custom-reports/function-app-16.png)

5. キー全体をコピーします。この値は、Function App の設定で SendGridAPI の値として必要になります

   ![API キーをコピーする画面のスクリーンショット](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>次のステップ

* [Analytics クエリ](../logs/get-started-queries.md)の作成についての詳細を見る
* [Application Insights データに対してプログラムからクエリを実行する](https://dev.applicationinsights.io/)の詳細を見る
* [Logic Apps](../../logic-apps/logic-apps-overview.md) の詳細を見る
* [Microsoft Power Automate](https://ms.flow.microsoft.com) についての詳細を見る。
