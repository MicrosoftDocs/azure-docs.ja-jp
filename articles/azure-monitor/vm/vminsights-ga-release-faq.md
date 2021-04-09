---
title: VM Insights (GA) についてよく寄せられる質問 | Microsoft Docs
description: VM Insights は、Azure VM オペレーティング システムの正常性およびパフォーマンスの監視と、アプリケーション コンポーネントおよび他のリソースとの依存関係の自動検出を組み合わせ、それらの間の通信をマップする Azure のソリューションです。 この記事では、GA リリースに関する一般的な質問への回答を示します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: fbef73bfe8058110277b200b8c4091fcde110c04
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031878"
---
# <a name="vm-insights-generally-available-ga-frequently-asked-questions"></a>VM Insights の一般提供 (GA) についてよく寄せられる質問
この一般提供に関する FAQ では、GA に向けた準備の過程で 2019 年第 4 四半期から 2020 年第 1 四半期までの間に行われた変更について説明しています。

## <a name="updates-for-vm-insights"></a>VM Insights の更新プログラム
GA の発表に先立ち、VM Insights の新しいバージョンが 2020 年 1 月にリリースされました。 VM Insights を有効にしたお客様は、GA バージョンを自動的に受信しますが、2019 年第 4 四半期以前のバージョンの VM Insights を使用している既存のお客様はアップグレードするように求められます。 この FAQ では、複数のワークスペースとの間で大規模なデプロイを行っている場合に、アップグレードを広範囲に実行するためのガイダンスを提供します。


このアップグレードを使用すると、VM Insights のパフォーマンス データは、[Container Insights](../containers/container-insights-overview.md) と同じ *InsightsMetrics* テーブルに格納されるため、2 つのデータ セットのクエリを簡単に実行することができます。 また、以前に使用したテーブルには格納できなかったより多様なデータ セットを格納することもできます。 

パフォーマンス ビューでは、*InsightsMetrics* テーブルに格納しているデータが使用されています。  ワークスペースで最新の VMInsights ソリューションを使用するようにアップグレードしていない場合は、グラフに情報が表示されなくなります。  次に示すように、 **[Get Started]\(開始\)** ページからアップグレードすることができます。


## <a name="what-is-changing"></a>何が変わるのですか?
VMInsights という名前の新しいソリューションがリリースされました。これには、データ収集のための追加機能と、ご利用の Log Analytics ワークスペースにこのデータを格納するための新しい場所が含まれています。 

以前は、ワークスペースで ServiceMap ソリューションを有効にし、Log Analytics ワークスペースでパフォーマンス　カウンターを設定して、*Perf* テーブルにデータを送信していました。 この新しいソリューションでは、Container Insights によっても使用される *InsightsMetrics* という名前のテーブルにデータが送信されます。 このテーブル スキーマを使用すると、*Perf* テーブル形式と互換性のない追加のメトリックとサービス データ セットを格納できます。

パフォーマンス グラフは、*InsightsMetrics* テーブルに格納されているデータを使用するように更新されました。 次に示すように、 **[Get Started]\(開始\)** ページから *InsightsMetrics* テーブルを使用するようにアップグレードできます。


## <a name="how-do-i-upgrade"></a>アップグレード方法を教えてください。
Log Analytics ワークスペースが Azure Monitor for VMs の最新バージョンにアップグレードされると、そのワークスペースに接続されている各 VM の依存関係エージェントがアップグレードされます。 アップグレードが必要な各 VM は、Azure portal の VM Insights の **[はじめに]** タブで確認できます。 VM のアップグレードを選択した場合、その VM 用のワークスペースと、そのワークスペースに接続されている他のすべての VM がアップグレードされます。 1 つの VM、複数の VM、リソース グループまたはサブスクリプションを選択することが可能です。 

PowerShell を使用してワークスペースをアップグレードするには、次のコマンドを使用します。

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>VMInsights ソリューションをインストールした場合、自分のワークスペース内のパフォーマンス カウンターには何が必要ですか?

VM Insights を有効にする以前のメソッドでは、ご利用のワークスペースでパフォーマンス カウンターを使用していました。 現在のバージョンでは、このデータは `InsightsMetrics` という名前のテーブルに格納されます。 これらのパフォーマンス カウンターを使用する必要がなくなった場合は、ご利用のワークスペースで無効にすることができます。 

>[!NOTE]
>`Perf` テーブル内にこれらのカウンターを参照するアラート ルールがある場合は、`InsightsMetrics` テーブルに格納された新しいデータを参照するようにそれらのルールを更新する必要があります。 このテーブルを参照する使用可能なログ クエリの例については、Microsoft のドキュメントを参照してください。
>

パフォーマンス カウンターを有効にしたままにする場合は、[Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/) に基づいて `Perf` テーブルに取り込まれ格納されたデータに対して課金されます。

## <a name="how-will-this-change-affect-my-alert-rules"></a>この変更はアラート ルールにどのように影響しますか? 

ワークスペース内で有効にされているパフォーマンス カウンターをターゲットにする `Perf` テーブルをクエリする[ログ アラート](../alerts/alerts-unified-log.md)を作成した場合、代わりに `InsightsMetrics` テーブルを参照するように、これらのルールを更新する必要があります。 これらのデータ セットは `VMComputer` テーブルと `VMProcess` テーブルに移動するため、このガイダンスは `ServiceMapComputer_CL` と `ServiceMapProcess_CL` を使用したログ検索規則にも適用されます。

この FAQ とドキュメントを更新して、収集するデータ セットに対するログ検索のアラート ルールの例を含めます。

## <a name="how-will-this-change-affect-my-bill"></a>この変更は請求書にどのように影響しますか。

課金は引き続き、Log Analytics ワークスペースに取り込まれ保持されるデータに基づいて行われます。

Microsoft が収集するマシン レベルのパフォーマンス データは同じであり、`Perf` テーブルに格納されたデータと同様のサイズで、ほぼ同じ額のコストがかかります。

## <a name="what-if-i-only-want-to-use-service-map"></a>Service Map のみを使用する場合はどうなりますか? 

問題はありません。 VM Insights で今後の更新プログラムについて表示するときに、Azure portal にプロンプトが表示されます。 リリースされると、新しいバージョンへの更新を要求するプロンプトが表示されます。 [Maps](vminsights-maps.md) 機能のみを使用する場合は、アップグレードしないことを選択して、VM Insights の Maps 機能と、ご利用のワークスペースまたはダッシュボード タイルからアクセスする Service Map ソリューションを使用し続けることができます。

ワークスペースでパフォーマンス カウンターを手動で有効にすることを選択した場合は、Azure Monitor から表示されたパフォーマンス グラフの一部にデータが表示されることがあります。 新しいソリューションがリリースされたら、`InsightsMetrics` テーブルの格納データのクエリを実行するようにパフォーマンス グラフを更新します。 これらのグラフにそのテーブルからのデータを表示したい場合は、新しいバージョンの VM Insights にアップグレードする必要があります。

`ServiceMapComputer_CL` と `ServiceMapProcess_CL` からデータを移動するための変更は、Service Map と VM Insights の両方に影響するため、この更新について引き続き計画する必要があります。

**VMInsights** ソリューションにアップグレードしないことを選択した場合は、`Perf` テーブルのデータを参照するレガシ バージョンのパフォーマンス ブックを引き続き提供します。  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Service Map データ セットも InsightsMetrics に格納されますか? 

両方のソリューションを使用する場合、データ セットは複製されません。 両方のオファリングでは、`VMComputer` (以前の ServiceMapComputer_CL)、`VMProcess` (以前の ServiceMapProcess_CL)、`VMConnection`、`VMBoundPort` テーブルに格納されるデータ セットを共有して、収集するマップ データ セットを格納します。  

`InsightsMetrics` テーブルには、収集された VM、プロセス、およびサービス データ セットが格納されます。このテーブルは、VM Insights と VM Insights ソリューションを使用している場合にのみ設定されます。 Service Map ソリューションでは、データの収集も `InsightsMetrics` テーブルへのデータの格納も行われません。

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>ワークスペースに Service Map と VMInsights のソリューションがある場合、二重に課金されますか? 

いいえ、2 つのソリューションでは、`VMComputer` (以前の ServiceMapComputer_CL)、`VMProcess` (以前の ServiceMapProcess_CL)、`VMConnection`、`VMBoundPort` に格納されているマップ データ セットを共有しています。 ワークスペースに両方のソリューションがある場合、このデータに対して二重に課金されることはありません。

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Service Map または VMInsights ソリューションのいずれかを削除した場合、自分のデータは削除されますか。

いいえ、2 つのソリューションでは、`VMComputer` (以前の ServiceMapComputer_CL)、`VMProcess` (以前の ServiceMapProcess_CL)、`VMConnection`、`VMBoundPort` に格納されているマップ データ セットを共有しています。 ソリューションのいずれかを削除すると、これらのデータ セットでは、データを使用するソリューションが配置されたままで、Log Analytics ワークスペースに保持されていることがわかります。 ご利用のワークスペースからデータを削除する場合は、ワークスペースから両方のソリューションを削除する必要があります。

## <a name="health-feature-is-in-limited-public-preview"></a>正常性機能は、制限付きのパブリック プレビュー段階にあります。

VM 正常性の機能セットに関して、お客様から多くのすばらしいフィードバックを受け取りました。 この機能には大きな関心が寄せられており、監視ワークフローをサポートする可能性にご期待いただいています。 Microsoft では、機能を追加するための一連の変更を行い、お客様からお寄せいただいたフィードバックに対処する予定です。 

これらの変更が新規のお客様に与える影響を最小限に抑えるために、この機能を **制限付きパブリック プレビュー** に移行しました。 この更新は 2019 年 10 月に行われました。

VM Insights が GA になった後、2020 年にこの正常性機能を再開する予定です。

## <a name="how-do-existing-customers-access-the-health-feature"></a>既存のお客様が正常性機能にアクセスする方法

正常性機能を使用している既存のお客様は引き続きアクセスできますが、新しいお客様には提供されません。  

この機能にアクセスするには、次の機能フラグ `feature.vmhealth=true` を Azure portal URL [https://portal.azure.com](https://portal.azure.com) に追加します。 例: `https://portal.azure.com/?feature.vmhealth=true`。

次の短い URL を使用することもできます。これにより、機能フラグが自動的に設定されます: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)。

既存のお客様は、正常性機能を備えた既存のワークスペースのセットアップに接続されている VM 上で、正常性機能を使用し続けることができます。  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>現在、1 つの環境で VM の正常性を使用していますが、これを新しい環境にデプロイしたいと考えています

正常性機能を使用している既存のお客様であり、新しいロール アウトに使用する場合は、vminsights@microsoft.com にお問い合わせの上、指示を依頼してください。

## <a name="next-steps"></a>次のステップ

ご利用の仮想マシンを監視する上で役立つ要件と方法については、[VM Insights のデプロイ](./vminsights-enable-overview.md)に関するページをご覧ください。
