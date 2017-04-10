---
title: "Azure Application Insights の価格とデータ ボリュームの管理 | Microsoft Docs"
description: "Application Insights でテレメトリの量を管理し、コストを監視します。"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 3f0c890056c2ee00151ebc4cc74106368a56ba2f
ms.lasthandoff: 03/18/2017


---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Application Insights での価格とデータ ボリュームの管理


[Azure Application Insights][start] の価格は、アプリケーションごとのデータ量に基づきます。 開発時または小規模なアプリで使用頻度が低い場合は、無料になる可能性があります。これは、毎月 1 GB のテレメトリ データ許容量が設けられているためです。

Application Insights の各リソースは個々のサービスとして課金され、Azure のサブスクリプションの課金内容に加えられます。

2 つの価格プランがあります。 既定のプランは、Basic と呼ばれます。 Enterprise プランを選択できます。ここでは日単位で料金が発生しますが、[連続エクスポート](app-insights-export-telemetry.md)などの特定の追加の機能を有効にすることができます。

Application Insights の課金の仕組みについてご質問があれば、[フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights)に気軽に投稿してください。 

## <a name="the-price-plans"></a>価格プラン

お客様の通貨での現在の料金については、[Application Insights の価格ページ][pricing]を参照してください。

### <a name="basic-plan"></a>Basic プラン

Basic プランは新しい Application Insights リソースを作成する場合の既定のプランであり、ほとんどのお客様は Basic プランで十分です。

* Basic プランでは、データ量、つまり Application Insights で受信したテレメトリのバイト数に応じて課金されます。 データ量は、Application Insights がアプリケーションから受信した圧縮されていない JSON データ パッケージのサイズとして測定されます。
* いずれのアプリについても、最初の 1 GB 無料です。そのため、試験や開発の段階で換金されることはあまりありません。
* [ライブ メトリック ストリーム](app-insights-live-stream.md) データは、課金対象としてカウントされません。
* Basic プランでは、[連続エクスポート](app-insights-export-telemetry.md)を 1 GB ごとの追加料金で利用できます (2017 年 3 月上旬まで無料)。

### <a name="enterprise-plan"></a>Enterprise プラン

* Enterprise プランでは、Application Insights のすべての機能をアプリで使用できます。 Enterprise プランでは、[連続エクスポート](app-insights-export-telemetry.md)と [Log Analytics コネクタ](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409)を追加料金なしでご利用いただけます。
* Enterprise プランでは、アプリに対してテレメトリを送信しているノードごとに料金が課金されます。 
 * *ノード*とは、アプリをホストする物理または仮想サーバー マシン (または Platform-as-a-Service (PaaS) ロール インスタンス) のことです。
 * 開発マシン、クライアントのブラウザー、およびモバイル デバイスはノードとしてカウントされません。
 * テレメトリを送信するコンポーネント (Web サービスやバックエンド ワーカーなど) がアプリに複数ある場合、それらは個別にカウントされます。
 * [ライブ メトリック ストリーム](app-insights-live-stream.md) データは、課金対象としてカウントされません。
* サブスクリプション全体を通じて、料金はノード単位で計算されます (アプリ単位ではありません)。 12 のアプリに対して 5 つのノードがテレメトリを送信している場合、料金は 5 ノード分になります。
* 料金の見積りは月単位で計算されますが、実際の料金は、ノードがアプリからテレメトリを送信した時間分しか課金されません。 1 時間あたりの料金は、1 か月あたりの見積り額/744 です (1 か月 31 日の時間数)。
* 検出された各ノードに、1 日あたり 200 MB のデータ量の割り当てが (時間単位の精度で) 与えられます。 未使用のデータ割り当て分が日をまたいで繰り越されることはありません。
 * Enterprise の価格オプションを選んだお客様には、テレメトリをサブスクリプション内の Application Insights リソースに送信するノードの数に基づいて、日単位の無料データ利用分が提供されます。 したがって、終日データを送信する 5 つのノードがある場合、サブスクリプション内のすべての Application Insights リソースに 1 GB の許容量が適用されます。 無料データ利用分はすべてのノードで共有されるため、一部のノードのデータ送信量が他のノードより多くても問題はありません。 特定の日に、Application Insights リソースがサブスクリプションの日単位の無料データ割り当て分を超えるデータを受信した場合は、1 GB ごとに超過データ料金が適用されます。 
 * 1 日あたりの無料データ利用分は、各ノードが 1 日にテレメトリを送信する時間数 (UTC を使用) を 24 で割った値に、200 MB を掛け合わせて計算します。 つまり、4 つのノードが、1 日 24 時間のうち 15 時間テレメトリを送信した場合、その日のデータ量は ((4 x 15) / 24) x 200 MB = 500 MB になります。 データ超過分 1 GB あたり 2.30 米国ドルの料金ですので、その日ノードが 1 GB のデータを送信した場合、超過データ料金は 1.15 米国ドルになります。
 * Enterprise プランの日単位の無料利用分は、Basic オプションを選択したアプリケーションとは共有されず、未使用の無料利用分は日をまたいで繰り越されないことに注意してください。 
* 個別のノード数を決定するいくつかの例を次に示します。
| シナリオ                               | 日単位の合計ノード数 |
|:---------------------------------------|:----------------:|
| 1 つのアプリケーションで 3つの Azure App Service インスタンスと 1 つの仮想サーバーが使用されている。 | 4 |
| 3 つのアプリケーションが 2 つの VM で実行され、これらのアプリケーションの Application Insights リソースが、同じサブスクリプションの Enterprise プランにある。 | 2 | 
| Applications Insights リソースが同じサブスクリプションにある 4 つのアプリケーション。 各アプリケーションが 2 つのインスタンスをピーク時以外の 16 時間実行し、4 つのインスタンスをピーク時の 8 時間実行している。 | 13.33 | 
| Cloud Services に 1 つの Worker ロールと 1 つの Web ロールが含まれ、それぞれ 2 つのインスタンスを実行している。 | 4 | 
| 5 つのノードがある Service Fabric クラスターが 50 のマイクロサービスを実行し、各マイクロサービスが 3 つのインスタンスを実行している。 | 5|

* 正確なノード カウントの動作は、アプリケーションで使用している Application Insights SDK によって異なります。 
  * SDK バージョン 2.2 以降では、Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) と [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) の両方で各アプリケーション ホストがノードとして報告されます (例: 物理サーバーと VM ホストのコンピューター名、または Cloud Services の場合はインスタンス名)。  唯一の例外は [.NET Core](https://dotnet.github.io/) と Application Insights Core SDK のみを使用するアプリケーションです。この場合はホスト名が使用できないため、すべてのホストについて報告されるのは 1 つのノードのみです。 
  * 以前のバージョンの SDK である [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) は、新しいバージョンの SDK と同じように動作しますが、[Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) は実際のアプリケーション ホストの数に関係なく 1 つのノードのみ報告します。 
  * アプリケーションで SDK を使用してロール インスタンスをカスタム値に設定すると、既定でノード数の決定に同じ値が使用されることに注意してください。 
  * クライアント コンピューターやモバイル デバイスから実行されているアプリで新しいバージョンの SDK を使用している場合は、ノードのカウントが (多数のクライアント コンピューターやモバイル デバイスから) 非常に大きい数値を返す可能性があります。 

### <a name="multi-step-web-tests"></a>複数手順の Web テスト

[複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)に対しては、追加料金が発生します。 これは、一連のアクションを実行する Web テストのことです。 

単一ページの ping テストについては、個別の料金はかかりません。 Ping テストと複数ステップ テストの両方からのテレメトリについては、アプリの他のテレメトリと一緒に課金されます。

## <a name="review-pricing-plan-and-estimate-costs-for-your-application-insights-resource"></a>Application Insights リソースの価格プランの確認とコストの見積もり
お使いのアプリケーションの Application Insights リソースの [Features + Pricing (機能と価格設定)] ブレードを開きます。

![[Pricing (価格設定)] を選択します。](./media/app-insights-pricing/01-pricing.png)

**a.** 該当の月のデータ量を確認します。 これには、(サーバーおよびクライアント アプリからの、また可用性テストからの[サンプリング](app-insights-sampling.md)の後) 受信され保持されたすべてのデータが含まれます。

**b.** [複数ステップ Web テスト](app-insights-monitor-web-app-availability.md#multi-step-web-tests)で別料金が発生しています。 (これには、データ ボリューム料金に含まれるシンプルな可用性テストは含まれません。)

**c.** Enterprise プランを有効にします。

**d.** データ管理オプションをクリックして先月のデータ量を表示し、1 日あたりの上限を設定するか、インジェスト サンプリングを設定します。

Application Insights の課金は Azure の課金内容に加えられます。 Azure ポータルの [課金] か [Azure Billing Portal](https://account.windowsazure.com/Subscriptions)で、Azure の課金内容の詳細を確認できます。 

![サイド メニューで、[課金] を選択します。](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>データ速度
送信するデータ ボリュームを制限するには、3 つの方法があります。

* **1 日あたりの上限**。 最大容量は 500 GB/日です。 Visual Studio から Application Insights リソースを作成する場合の既定値は小 (32.3 MB/日) です。 Azure Portal から Application Insights リソースを作成する場合は、最大容量に設定されます。 上限に達すると、その日の残りのデータが失われるため、この値を変更する場合は注意が必要です。 値を変更するには、[データ管理] ブレードからリンクされている [日次ボリューム上限] ブレードを使用します。
* **[サンプリング](app-insights-sampling.md)。** このメカニズムでは、メトリックのひずみを最小に抑えて、サーバーおよびクライアント アプリから送信されるテレメトリの量を減らすことができます。
* **スロットル**により、データ速度は 1 分間で平均して 1 秒あたり 32000 イベントに制限されます。 


*アプリがスロットル レートを超えるとどうなりますか。*

* アプリから送信されるデータ量は分単位で評価されます。 1 分間で平均して 1 秒あたりのレートを超える場合、一部の要求がサーバーから拒否されます。 SDK はデータをバッファー処理し、再送信を試みるので、数分間にわたってデータ量が増加します。 アプリが常にスロットル レートを超えてデータを送信した場合、一部のデータが破棄されます  (ASP.NET、Java、JavaScript SDK はこの方法で再送信を試みますが、その他の SDK は調整されたデータを単に破棄します)。

スロットルが発生した場合、この状況が発生したことを通知する警告が表示されます。

*アプリがどれだけのデータ ポイントを送信しているかを知る方法はありますか。*

* [価格] ブレードを開いて、[データ ボリューム] グラフを表示します。
* または、メトリックス エクスプローラーで、新しいグラフを追加し、 **[データ ポイントの量]** メトリックを選択します。 [グループ化] を有効にし、 **[データの種類]**を選択してグループ化します。

## <a name="to-reduce-your-data-rate"></a>データ レートを削減するには
データ ボリュームを減らすために、以下のことを実行できます。

* [サンプリング](app-insights-sampling.md)の使用。 このテクノロジは、メトリックや、検索で関連するアイテム間を移動する機能を損なうことなく、データ レートを削減します。 サーバー アプリケーションで自動的に操作されます。
* [報告できる AJAX 呼び出しの数を制限する](app-insights-javascript.md#detailed-configuration) か、AJAX レポートを無効にします。
* [ApplicationInsights.config を編集](app-insights-configuration-with-applicationinsights-config.md)し、不要なコレクション モジュールを無効にします。 たとえば、パフォーマンス カウンターや依存関係のデータが重要ではないと判断した場合などに検討します。
* テレメトリを分割して、インストルメンテーション キーを分割します。 
* 事前集計メトリック。 TrackMetric への呼び出しをアプリに配置した場合、平均計算と測定のバッチの標準偏差を受け入れるオーバーロードを使用して、トラフィックを減らすことができます。 また、 [事前集計パッケージ](https://www.myget.org/gallery/applicationinsights-sdk-labs)を使用することもできます。
* 最後に、日次ボリューム上限を減らして収集するデータを制限できますが、そうするとその日の残りのデータが失われます。 上限を変更するには、**[機能と価格設定]**、**[データ管理]** の順に開きます。

    ![テレメトリの日次ボリューム上限の調整](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>サンプリング
[サンプリング](app-insights-sampling.md) は、テレメトリがアプリに送信される速度を低下させる一方で、診断検索中に関連イベントを見つける機能を保持すると共に、正しいイベント数を保持する方法です。 

サンプリングは、料金を削減し、月間クォータ内で維持する効果的な方法です。 サンプリング アルゴリズムは、たとえば、検索を使用する場合は、特定の例外に関連する要求を検出できるようにする、テレメトリの関連項目を保持します。 アルゴリズムは、要求レート、例外レート、およびその他のカウントについてメトリックス エクスプ ローラーに正しい値が表示されるように正しいカウントも保持します。

サンプリングの形式にはいくつかあります。

* [アダプティブ サンプリング](app-insights-sampling.md) は ASP.NET SDK の既定値で、アプリが送信するテレメトリの量を自動的に調整します。 Web アプリの SDK で自動的に動作して、ネットワーク上のテレメトリのトラフィックを削減します。 
* *インジェスト サンプリング* " は、アプリからのテレメトリが Application Insights サービスに入る時点で動作します。 アプリから送信されるテレメトリの量には影響しませんが、サービスによって保持される量が削減されます。 これを使用すると、ブラウザーや他の SDK からのテレメトリによって使用されるクォータを削減できます。

インジェスト サンプリングを設定するには、[価格] ブレードでコントロールを設定します。

![クォータと価格のブレードから、[サンプル] タイルをクリックして、サンプリングの割合を選択します。](./media/app-insights-pricing/04.png)

> [!WARNING]
> [サンプル保持の割合] タイルに表示される値は、インジェスト サンプリングに設定した値のみを示します。 アプリの SDK で動作するサンプリング レートは反映されません。 
> 
> 受信テレメトリが既に SDK でサンプリングされている場合、インジェスト サンプリングは適用されません。
> 
> 

適用されている場所に関係なく、実際のサンプリング レートを検出するには、次のように [Analytics クエリ](app-insights-analytics.md) を使用します。

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

保持されている各レコードで、 `itemCount` は、それが表す元のレコードの数 (1 + 以前に破棄されたレコードの数と同じ) を示します。 


## <a name="automation"></a>Automation

Azure Resource 管理を使用して、価格プランを設定するスクリプトを記述することができます。 方法については、[こちら](app-insights-powershell.md#price)をご覧ください。

## <a name="limits-summary"></a>制限の概要
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>次のステップ

* [サンプリング](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/


