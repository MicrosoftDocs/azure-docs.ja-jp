---
title: .NET アプリ向け Azure Application Insights スナップショット デバッガー | Microsoft Docs
description: 例外が運用 .NET アプリでスローされるときにデバッグ スナップショットが自動的に収集される
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 669b4d65798a553188a2b99080b72ffc7cd9e898
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890578"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>.NET アプリでの例外でのデバッグ スナップショット
例外が発生したとき、実行中の Web アプリケーションからデバッグ スナップショットを自動的に収集できます。 スナップショットには、例外がスローされたときのソース コードと変数の状態が表示されます。 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) のスナップショット デバッガー (プレビュー) により、Web アプリの例外テレメトリが監視されます。 運用環境の問題の診断に必要な情報を入手できるように、スローされる上位の例外に関するスナップショットが収集されます。 [スナップショット コレクター NuGet パッケージ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)をアプリケーションに含め、必要に応じて、[ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) にコレクション パラメーターを構成します。スナップショットが、Application Insights ポータルの[例外](../../azure-monitor/app/asp-net-exceptions.md)に表示されます。

ポータルで [Debug Snapshots (デバッグ スナップショット)] を表示して、コール スタックを表示し、各呼び出しスタック フレームで変数を確認できます。 ソース コードによるデバッグ エクスペリエンスをさらに向上させるには、Visual Studio 2017 Enterprise でスナップショットを開きます。 Visual Studio では、例外を待たずに[スナップポイントを設定し、対話形式でスナップショットを取得](https://aka.ms/snappoint)できます。

デバッグ スナップショットは 7 日間格納されます。 この保持ポリシーは、アプリケーションごとに設定されます。 この値を増やす必要がある場合は、Azure portal でサポート ケースを開くことによって増加を要求できます。

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>アプリケーションに対して Application Insights スナップショット デバッガーを有効にする
スナップショット コレクションは次のアプリケーションで使用できます。
* .NET Framework 4.5 以降を実行している .NET Framework アプリケーションと ASP.NET アプリケーション。
* Windows 上で動作している .NET core 2.0 アプリケーションと ASP.NET Core 2.0 アプリケーション。

次の環境がサポートされています。

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* OS ファミリ 4 以降を実行している [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 以降で稼働している [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 以降を実行している [Azure Virtual Machines および仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* Windows Server 2012 R2 以降を実行している[オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

> [!NOTE]
> クライアント アプリケーション (たとえば、WPF、Windows フォーム、または UWP) はサポートされません。

スナップショット デバッガーを有効にしたのにスナップショットが表示されない場合は、「[トラブルシューティング ガイド](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)」を参照してください。

## <a name="grant-permissions"></a>アクセス許可を付与する

スナップショットへのアクセスは、ロールベースのアクセス制御 (RBAC) によって保護されます。 スナップショットを検査するユーザーは、最初にサブスクリプション所有者によって必要なロールに追加される必要があります。

> [!NOTE]
> 所有者と共同作成者には、このロールは自動的に割り当てられません。 所有者や共同作成者がスナップショットを見る場合は、自分自身をロールに追加する必要があります。

サブスクリプション所有者は、スナップショットを検査する予定のユーザーに `Application Insights Snapshot Debugger` のロールを割り当てる必要があります。 このロールは、Application Insights のターゲット リソース、リソース グループ、またはサブスクリプションに関するサブスクリプション所有者が、個々のユーザーまたはグループに割り当てることができます。

1. Azure Portal で Application Insights のリソースを参照します。
1. **[アクセス制御 (IAM)]** をクリックします。
1. **[+ ロールの割り当ての追加]** ボタンをクリックします。
1. **[ロール]** ボックスの一覧の **[Application Insights スナップショット デバッガー]** を選択します。
1. 追加するユーザーの名前を探して入力します。
1. **[保存]** ボタンをクリックして、ユーザーをロールに追加します。


> [!IMPORTANT]
> スナップショットは、変数とパラメーターの値に個人情報などの機密情報を含んでいる可能性があります。

## <a name="view-snapshots-in-the-portal"></a>Portal でスナップショットを表示する

アプリケーションで例外が発生してスナップショットが作成されたら、スナップショットを表示することができます。 例外が発生してから、スナップショットの準備ができて Portal から表示できるまでには、5 分から 10 分かかります。 スナップショットを表示するには、**[失敗]** ウィンドウで、**[操作]** タブを表示しているときは **[操作]** ボタンを選択し、**[例外]** タブを表示しているときには **[例外]** ボタンを選択します。

![[失敗] ページ](./media/snapshot-debugger/failures-page.png)

右側のウィンドウで操作または例外を選択して、**[エンドツーエンド トランザクション詳細]** ウィンドウを開き、次に例外イベントを選択します。 特定の例外のスナップショットが使用可能な場合、[例外](../../azure-monitor/app/asp-net-exceptions.md)の詳細と共に右側のウィンドウに **[デバッグ スナップショットを開く]** ボタンが表示されます。

![例外の [Debug Snapshot (デバッグ スナップショット)] ボタンを開く](./media/snapshot-debugger/e2e-transaction-page.png)

デバッグ スナップショット ビューには、コール スタックと変数ウィンドウが表示されます。 コール スタック ウィンドウでコール スタックのフレームを選択すると、変数ウィンドウでその関数呼び出しのローカル変数とパラメーターを表示できます。

![ポータルでのデバッグ スナップショットの表示](./media/snapshot-debugger/open-snapshot-portal.png)

スナップショットには機密情報が含まれている可能性があるため、既定では非表示になっています。 スナップショットを表示するには、`Application Insights Snapshot Debugger` のロールを割り当てられている必要があります。

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visual Studio 2017 Enterprise 以上でスナップショットを表示する
1. **[Download Snapshot]\(スナップショットのダウンロード\)** をクリックして `.diagsession` ファイルをダウンロードします。このファイルは Visual Studio 2017 Enterprise で開くことができます。

2. `.diagsession` ファイルを開くには、スナップショット デバッガーの VS コンポーネントがインストールされている必要があります。 スナップショット デバッガー コンポーネントは、VS の ASP.net ワークロードに必要なコンポーネントです。これは、VS インストーラーの個々のコンポーネント リストから選択することができます。 15.5 より前のバージョンの Visual Studio 2017 を使用している場合、拡張機能は [VS Marketplace](https://aka.ms/snapshotdebugger) からインストールする必要があります。

3. スナップショット ファイルを開くと、Visual Studio の[ミニダンプ デバッグ] ページが表示されます。 **[Debug Managed Code]\(マネージド コードをデバッグする\)** をクリックして、スナップショットのデバッグを開始します。 例外がスローされたコード行がスナップショットに表示され、プロセスの現在の状態をデバッグできます。

    ![Visual Studio でのデバッグ スナップショットの表示](./media/snapshot-debugger/open-snapshot-visualstudio.png)

ダウンロードしたスナップショットには、Web アプリケーション サーバーで検出されたすべてのシンボル ファイルが含まれます。 これらのシンボル ファイルは、スナップショット データをソース コードと関連付けるために必要です。 App Service アプリの場合は、Web アプリを公開するときにシンボル デプロイを有効にしてください。

## <a name="how-snapshots-work"></a>スナップショットのしくみ

Snapshot Collector は、[Application Insights Telemetry Processor](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet) として実装されています。 アプリケーションが実行されると、Snapshot Collector Telemetry Processor がアプリケーションのテレメトリ パイプラインに追加されます。
アプリケーションが [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions) を呼び出すたびに、Snapshot Collector はスローされる例外の種類とスロー方法から問題 ID を計算します。
アプリケーションが TrackException を呼び出すたびに、該当する問題 ID のカウンターが増分されます。 カウンターが `ThresholdForSnapshotting` 値に達すると、問題 ID が収集計画に追加されます。

[AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception) イベントにサブスクライブすることで例外がスローされるので、Snapshot Collector は例外も監視します。 そのイベントが発生すると、例外の問題 ID が計算され、収集計画の問題 ID と比較されます。
一致する ID があれば、実行中のプロセスのスナップショットが作成されます。 スナップショットには一意の識別子が割り当てられ、例外にはその識別子を使用してスタンプされます。 FirstChanceException ハンドラーが戻った後、スローされた例外は通常どおり処理されます。 最終的に、例外は TrackException メソッドに再び到達し、スナップショット識別子と共に Application Insights に報告されます。

メイン プロセスは引き続き実行され、ユーザーへのトラフィックが処理されます。中断をほとんど発生しません。 その間、スナップショットは Snapshot Uploader プロセスに渡されます。 Snapshot Uploader からミニダンプが作成され、関連するシンボル (.pdb) ファイルと共に Application Insights にアップロードされます。

> [!TIP]
> - プロセスのスナップショットは、実行中のプロセスの一時停止された複製です。
> - スナップショットの作成には約 10 から 20 ミリ秒かかります。
> - `ThresholdForSnapshotting` の既定値は 1 です。 これは最小値でもあります。 そのため、スナップショットが作成される前に、アプリは同じ例外を  **2 回**トリガーする必要があります。
> - Visual Studio でデバッグ中にスナップショットを生成する場合は、`IsEnabledInDeveloperMode` を true に設定します。
> - スナップショットの作成速度は `SnapshotsPerTenMinutesLimit` 設定によって制限されます。 既定では、10 分ごとに 1 つのスナップショットが上限です。
> - 1 日あたり 50 枚を超えるスナップショットをアップロードすることはできません。

## <a name="limitations"></a>制限事項

既定のデータ保有期間は 7 日間です。 Application Insights のインスタンスごとに、1 日あたり最大 50 個のスナップショットが許可されます。

### <a name="publish-symbols"></a>シンボルの公開
スナップショット デバッガーでは、Visual Studio で変数をデコードし、デバッグ エクスペリエンスを提供するために、運用サーバーにシンボル ファイルが必要です。
Visual Studio 2017 のバージョン 15.2 (またはそれ以上) では、App Service に公開する際に、既定でリリース ビルドのシンボルを公開します。 以前のバージョンでは、シンボルがリリース モードで公開されるように、発行プロファイルの `.pubxml` ファイルに次の行を追加する必要があります。

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Azure Compute や他の種類の場合、シンボル ファイルがメイン アプリケーション .dll (通常は `wwwroot/bin`) の同じフォルダーにあるか、現在のパスで使用できることを確認してください。

### <a name="optimized-builds"></a>最適化されたビルド
場合によっては、JIT コンパイラによって適用される最適化のために、リリース ビルドでローカル変数を表示できないことがあります。
ただし、Azure App Services では、Snapshot Collector は収集計画の一部であるスロー方法を非最適化する可能性があります。

> [!TIP]
> Application Insights サイト拡張機能を App Service にインストールして、非最適化のサポートを得ます。

## <a name="next-steps"></a>次の手順
次のアプリケーションに対して Application Insights スナップショット デバッガーを有効にします。

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights スナップショット デバッガーを有効にした後:
 
* [コードでスナップポイントを設定](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications)し、例外を待たずにスナップショットを取得します。
* [Web アプリの例外の診断](../../azure-monitor/app/asp-net-exceptions.md)に関する記事では、Application Insights に表示される例外を増やす方法を説明しています。
* [スマート検出](../../azure-monitor/app/proactive-diagnostics.md)は、パフォーマンスの異常を自動的に検出します。
