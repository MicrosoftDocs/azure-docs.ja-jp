---
title: Application Insights Profiler を使用した Azure のプロファイル運用アプリ
description: フットプリントの小さいプロファイラーを使用して Web サーバー コードのホット パスを特定できます。
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d3074d58560df5cb5bd6bdc2c0437a4be828918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86499394"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Application Insights を使用した Azure のプロファイル運用アプリケーション
## <a name="enable-application-insights-profiler-for-your-application"></a>アプリケーションに対して Application Insights Profiler を有効にする

Azure Application Insights Profiler は、Azure の運用環境で稼働しているアプリケーションのパフォーマンス トレースを提供します。 Profiler は、ユーザーに悪影響を及ぼすことなく、自動的に大量のデータをキャプチャします。 Profiler を使用すると、特定の Web 要求の処理時に最も時間が長くなる "ホットな" コード パスを特定できます。 

Profiler は、以下の Azure サービスにデプロイされた .NET アプリケーションと連携します。 各種サービスに Profiler を有効にする具体的な手順については、以下のリンクを参照してください。

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [ASP.NET Core Azure Linux Web アプリ](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json)を **プレビュー** する 

Profiler を有効にして、トレースが表示されない場合は、[トラブルシューティング ガイド](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)を参照してください。

## <a name="view-profiler-data"></a>Profiler データを表示する

Profiler がトレースをアップロードするには、アプリケーションが要求をアクティブに処理する必要があります。 実験を行っている場合は、[Application Insights パフォーマンス テスト](/vsts/load-test/app-service-web-app-performance-test)を使用して Web アプリへの要求を生成できます。 Profiler を新しく有効にした場合は、短い読み込みテストを実行することができます。 読み込みテストの実行中に、[ **[Profiler Settings]\(Profiler の設定\)** ウィンドウ](profiler-settings.md)の **[Profile Now]\(今すぐプロファイル\)** ボタンを選択します。 Profiler が実行されると、1 時間に約 1 回、2 分間、ランダムにプロファイリングされます。 アプリケーションが要求の安定したストリームを処理している場合、Profiler から 1 時間ごとにトレースがアップロードされます。

アプリケーションでトラフィックを受け取り、Profiler にトレースをアップロードする時間があった場合、表示するトレースがあります。 このプロセスには 5 分から 10 分かかる可能性があります。 トレースを表示するには、 **[パフォーマンス]** ウィンドウで **[Take Actions]\(アクションの実行\)** を選択して、 **[Profiler トレース]** ボタンを選択します。

![Application Insights の [パフォーマンス] ウィンドウの Profiler トレースのプレビュー][performance-blade]

要求の実行に費やされた時間のコード レベルの内訳を表示するためのサンプルを選択します。

![Application Insights トレース エクスプローラー][trace-explorer]

トレース エクスプローラーは、次の情報を表示します。

* **ホット パスの表示**:最大リーフ ノード、またはそれに近いノードが表示されます。 ほとんどの場合、このようなノードはパフォーマンスのボトルネックの近くにあります。
* **ラベル**:関数またはイベントの名前。 このツリーには、コードと発生したイベント (SQL イベントや HTTP イベント) が混在して表示されます。 最上位のイベントは要求時間全体に相当します。
* **経過時間**:操作の開始から終了までの期間。
* **時間**:関数またはイベントが他の関数と関連して実行されていた時間。

## <a name="how-to-read-performance-data"></a>パフォーマンス データの解釈方法

Microsoft サービス プロファイラーでは、アプリのパフォーマンス分析に、サンプリング メソッドとインストルメンテーションを組み合わせて使用します。 詳細な収集が進行中のとき、サービス プロファイラーは 1 ミリ秒ごとに各マシン CPU の命令ポインターをサンプリングします。 各サンプルでは、現在実行中のスレッドの呼び出し履歴全体がキャプチャされます。 これにより、そのスレッドが実行した内容に関する詳細情報が、高い抽象化レベルと低い抽象化レベルの両方で取得されます。 また、サービス プロファイラーでは、アクティビティの相関関係と因果関係を追跡するために、コンテキスト切り替えイベント、タスク並列ライブラリ (TPL) イベント、スレッドプール イベントなど、他のイベントも収集されます。

タイムライン ビューに表示される呼び出し履歴は、サンプリングとインストルメンテーションの結果です。 各サンプルはスレッドの完全な呼び出し履歴をキャプチャするため、これには Microsoft .NET Framework や、参照しているその他のフレームワークのコードが含まれています。

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>オブジェクトの割り当て (clr!JIT\_New または clr!JIT\_Newarr1)

**clr!JIT\_New** と **clr!JIT\_Newarr1** は、マネージド ヒープからメモリを割り当てる .NET Framework のヘルパー関数です。 **clr!JIT\_New** は、オブジェクトが割り当てられるときに呼び出されます。 **clr!JIT\_Newarr1** は、オブジェクト配列が割り当てられるときに呼び出されます。 通常、これらの 2 つの関数は高速であり、比較的短時間で完了します。 タイムラインで **clr!JIT\_New** または **clr!JIT\_Newarr1** がかなり長時間かかっている場合は、そのコードが多数のオブジェクトを割り当て、大量のメモリを消費する可能性があることを示しています。

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>コードの読み込み (clr!ThePreStub)

**clr!ThePreStub** は、初めて実行するコードを準備する .NET Framework のヘルパー関数です。 この実行には通常、Just-In-Time (JIT) コンパイルが含まれます (ただし、これに限定されません)。 C# の各メソッドでは、1 回の処理中に **clr!ThePreStub** が最大 1 回呼び出されます。

要求に対して **clr!ThePreStub** が長時間を要している場合は、その要求はそのメソッドを実行する最初の要求です。 .NET Framework ランタイムが最初のメソッドを読み込むには、かなりの時間がかかります。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用するか、アセンブリで Native Image Generator (ngen.exe) を実行することを検討してください。

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>ロックの競合 (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** または **clr!JITutil\_MonEnterWorker** は、現在のスレッドが、ロックが解放されるのを待っていることを示します。 このテキストは多くの場合、C# の **LOCK** ステートメントを実行するとき、**Monitor.Enter** メソッドを呼び出すとき、または **MethodImplOptions.Synchronized** 属性を持つメソッドを呼び出すときに表示されます。 ロックの競合は通常、スレッド _A_ がロックを取得し、スレッド _A_ がそれを解放する前にスレッド _B_ が同じロックを取得しようとしたときに発生します。

### <a name="loading-code-cold"></a><a id="ngencold"></a>コードの読み込み ([COLD])

**mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined** のように、メソッド名に **[COLD]** が含まれる場合、.NET Framework ランタイムによって、[Profile がガイドする最適化](/cpp/build/profile-guided-optimizations)によって最適化されていないコードが初めて実行されます。 これは、メソッドごとに、その処理中に最大 1 回表示されます。

要求に対してコードの読み込みにかなり時間がかかっている場合、その要求は、そのメソッドの最適化されていない部分を実行する最初の要求です。 コードのその部分を、ユーザーがアクセスする前に実行するウォーミングアップ プロセスを使用することを検討してください。

### <a name="send-http-request"></a><a id="httpclientsend"></a>HTTP 要求の送信

**HttpClient.Send** などのメソッドは、コードが HTTP 要求の完了を待機していることを示します。

### <a name="database-operation"></a><a id="sqlcommand"></a>データベース操作

**SqlCommand.Execute** などのメソッドは、データベース操作が終了するのをコードが待機していることを示します。

### <a name="waiting-await_time"></a><a id="await"></a>待機 (AWAIT\_TIME)

**AWAIT\_TIME** は、別のタスクが終了するのをコードが待機していることを示します。 通常、この遅延は、C# の **AWAIT** ステートメントで発生します。 コードで C# の **AWAIT** が実行されると、スレッドはアンワインドして、スレッド プールの制御を戻します。**AWAIT** が終了するのを待機している間にスレッドがブロックされることはありません。 ただし、論理的に見れば、**AWAIT** を実行したスレッドは、操作の終了を待機している間 "ブロック" されます。 **AWAIT\_TIME** ステートメントは、タスクが終了するのを待機しているブロック時間を示します。

### <a name="blocked-time"></a><a id="block"></a>ブロック時間

**BLOCKED_TIME** は、別のリソースが使用可能になるのをコードが待機していることを示します。 たとえば、同期オブジェクトやスレッドが使用可能になるのを待っているか、要求が終了するのを待機している可能性があります。

### <a name="unmanaged-async"></a>アンマネージド非同期

.NET フレームワークでは、ETW イベントを出力し、スレッド間で非同期呼び出しを追跡できるように、スレッド間でアクティビティ ID を渡します。 アンマネージド コード (ネイティブ コード) と非同期コードの一部の古いスタイルがこれらのイベントとアクティビティの ID にはないので、プロファイラーは、どのスレッドと、どの関数がそのスレッドで実行しているかを判別できません。 これは、コール スタックで「Unmanaged Async」とラベルが付けられます。 ETW ファイルをダウンロードする場合は、[PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) を使用して、何が起きているかさらに洞察を得ることができます。

### <a name="cpu-time"></a><a id="cpu"></a>CPU 時間

命令の実行中のため、CPU がビジー状態です。

### <a name="disk-time"></a><a id="disk"></a>ディスク時間

アプリケーションがディスク操作を実行しています。

### <a name="network-time"></a><a id="network"></a>ネットワーク時間

アプリケーションがネットワーク操作を実行しています。

### <a name="when-column"></a><a id="when"></a>[When (実行期間)] 列

**[When]\(実行期間\)** 列は、ノードについて収集された包括的なサンプルが時間の経過と共にどのように変化するかを視覚化したものです。 要求の範囲全体は 32 個のタイム バケットに分割されます。 そのノードの包括的なサンプルは、その 32 個のバケットに蓄積されます。 各バケットはバーとして表されます。 バーの長さは、調整された値を表します。 **CPU_TIME** または **BLOCKED_TIME** とマークされているか、リソース (CPU、ディスク、スレッドなど) の消費との明らかな関係が存在するノードの場合、このバーは、そのバケット中のいずれかのリソースの消費を表します。 これらのメトリックでは、複数のリソースを消費することによって 100 % を超える値になることがあります。 たとえば、ある間隔の間に平均して 2 つの CPU を使用した場合は、200 % になります。

## <a name="limitations"></a>制限事項

既定のデータ保有期間は 5 日です。 1 日あたりに取り込まれる最大データは 10 GB です。

Profiler サービスの使用に料金は発生しません。 使用するには、Web アプリは、少なくとも Azure App Service の Web Apps の機能の Basic レベルでホストされる必要があります。

## <a name="overhead-and-sampling-algorithm"></a>オーバーヘッドとサンプリング アルゴリズム

Profiler は、トレースをキャプチャするために Profiler が有効になっているアプリケーションをホストする各仮想マシン上で 1 時間に 2 分間ランダムに実行されます。 Profiler の実行中は、サーバーに 5 - 15 % の CPU オーバーヘッドが追加されます。

## <a name="next-steps"></a>次のステップ
Azure アプリケーションに対して Application Insights Profiler を有効にします。 こちらもご覧ください。
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
