---
title: Microsoft.ApplicationInsights.SnapshotCollector NuGet パッケージのリリース ノート - Application Insights
description: Application Insights スナップショット デバッガーで使用される Microsoft.ApplicationInsights.SnapshotCollector NuGet パッケージのリリース ノート。
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: 4a787c6e2a9b59874f965a2bbcebea9ce02d8082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97093262"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Microsoft.ApplicationInsights.SnapshotCollector のリリース ノート

この記事には、Application Insights スナップショット デバッガーで使用される、.NET アプリケーション向け Microsoft.ApplicationInsights.SnapshotCollector NuGet パッケージのリリース ノートを記載します。

.NET アプリケーション向け Application Insights スナップショット デバッガーの詳細については、[こちら](./snapshot-debugger.md)を参照してください。

バグ レポートとフィードバックについては、GitHub (https://github.com/microsoft/ApplicationInsights-SnapshotCollector ) でイシューをオープンしてください。

## <a name="release-notes"></a>リリース ノート

## <a name="1375"></a>[1.3.7.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.5)
1\.4.0-pre から修正プログラムをバックポートするポイント リリース。
### <a name="bug-fixes"></a>バグの修正
- [シャットダウン時の ObjectDisposedException](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2097) を修正。

## <a name="1374"></a>[1.3.7.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.4)
Azure App Service のコードなしアタッチ シナリオのテストで検出された問題に対処するポイント リリース。
### <a name="changes"></a>[変更点]
- netcoreapp3.0 ターゲットが、Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 (以前は >= 2.1.2) に依存するようになりました。

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
影響の大きい 2 つの問題を解決するためのポイント リリース。
### <a name="bug-fixes"></a>バグの修正
- wwroot/bin フォルダーでの PDB 検出が修正されました。これは、1.3.6 でシンボル検索アルゴリズムを変更したときに破損しました。
- テレメトリで頻繁に発生する ExtractWasCalledMultipleTimesException が修正されました。

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>[変更点]
- SnapshotCollector の netcoreapp2.0 ターゲットは、Microsoft.ApplicationInsights.AspNetCore >= 2.1.1 に (再度) 依存します。 これにより、1.3.5 前の動作に戻ります。 1\.3.6 でアップグレードしようとしましたが、一部の Azure App Service シナリオが破損しました。
### <a name="new-features"></a>新機能
- Snapshot Collector が、APPLICATIONINSIGHTS_CONNECTION_STRING 環境変数または TelemetryConfiguration から ConnectionString を読み取り、解析するようになりました。 これは主に、スナップショット サービスに接続する際にエンドポイントを設定するために使用されます。 詳細については、[接続文字列に関するドキュメント](./sdk-connection-string.md)を参照してください。
### <a name="bug-fixes"></a>バグの修正
- 互換性のない SecurityProtocol (TLS 1.2 が必要) が原因で、一部の環境で WebRequest が失敗していたため、net45 を除くすべてのターゲットに対して HttpClient を使用するように切り替えました。

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>[変更点]
- SnapshotCollector が、すべてのターゲット フレームワークに対して Microsoft.ApplicationInsights >= 2.5.1 に依存するようになりました。 アプリケーションが以前のバージョンの Microsoft.ApplicationInsights SDK に依存している場合、これは破壊的変更になる可能性があります。
- Snapshot Uploader で TLS 1.0 と1.1 のサポートが削除されました。
- PDB スキャンの既定期間が、15 分ではなく 24 時間になりました。 SnapshotCollectorConfiguration の PdbRescanInterval を使用して構成できます。
- PDB スキャンで、再帰的ではなく、最上位レベルのフォルダーのみ検索されるようになりました。 シンボルがバイナリ フォルダーのサブフォルダーにある場合、これは破壊的変更になる可能性があります。
### <a name="new-features"></a>新機能
- SnapshotUploader でのログ ローテーション。logs フォルダーに古いファイルが格納されないようにします。
- .NET Core 3.0 アプリケーションの (アタッチでの ReJIT による) 非最適化のサポート。
- NuGet パッケージへのシンボルの追加。
- ミニダンプのアップロード時の追加メタデータの設定。
- SnapshotCollectorTelemetryProcessor に Initialized プロパティを追加。 これは CancellationToken であり、Snapshot Collector が完全に初期化され、サービスエンド ポイントに接続するとキャンセルされます。
- 動的に生成されたメソッドでの例外に対してスナップショットをキャプチャできるようになりました。 たとえば、Entity Framework のクエリによって生成されるコンパイル済みの式ツリーなどです。
### <a name="bug-fixes"></a>バグの修正
- Status Monitor を原因とする Snapshot Collector 読み込み時の AmbiguousMatchException。
- GetSnapshotCollector 拡張メソッドで、すべての TelemetrySink が検索されるようになりました。
- サポートされていないプラットフォームで Snapshot Uploader を開始しない。
- 動的メソッドの非最適化時に InvalidOperationException を処理 (たとえば、Entity Framework)。

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- ソブリン クラウドのサポートが追加されました (以前のバージョンはソブリン クラウドでは動作しません)。
- AddSnapshotCollector() を使用することで、Snapshot Collector を簡単に追加できるようになりました。 詳細については、 [こちら](./snapshot-debugger-appservice.md)で確認できます。
- BLOB ブロックの確認に FISMA MD5 設定を使用。 これにより、既定の .NET MD5 暗号アルゴリズム (OS が FIPS 準拠モードに設定されている場合には使用できない) が回避されます。
- 関数呼び出しを非最適化するときに .NET Framework フレームを無視。 この動作は、DeoptimizeIgnoredModules 構成設定によって制御できます。
- 複数の関数呼び出しを非最適化できる `DeoptimizeMethodCount` 構成設定を追加。 詳細については、こちらを参照してください。

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- 構造化されたインストルメンテーション キーを許可。
- SnapshotUploader の堅牢性が向上。古いアップローダー ログを移動できない場合でも、スタートアップが続行されます。
- SnapshotUploader.exe が即座に終了したときの追加のテレメトリの報告を再有効化 (1.3.3 では無効化されました)。
- 内部テレメトリを簡素化。
- _試験的な機能_: スナップショット コレクション プラン: "snapshotOnFirstOccurence" を追加。 詳細については、[こちら](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)をご覧ください。

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- SnapshotUploader.exe が応答を停止し、.NET Core アプリのスナップショットがアップロードされないという問題の原因になっていたバグを修正しました。

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _試験的な機能_: スナップショット コレクション プラン。 詳細については、[こちら](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe)をご覧ください。
- SnapshotCollector の読み込み元の AppDomain がランタイムによってアップロードされると、処理が終了するのを待たずに、SnapshotUploader.exe が終了します。 これにより、IIS でホストされている場合のコレクターの信頼性が向上します。
- 同じインストルメンテーション キーを使用している複数の SnapshotCollector インスタンスが同じ SnapshotUploader プロセス: ShareUploaderProcess (既定値は `true`) を共有できるように構成を追加。
- SnapshotUploader.exe が即座に終了したときに、追加のテレメトリを報告。
- SnapshotUploader.exe がディスクへの書き込みを行うときに必要となるサポート ファイルの数を削減。

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- RtlCloneUserProcess API を使用してスナップショットを収集するためのサポートを削除し、PssCaptureSnapshots API のみをサポート。
- 10 分間でキャプチャできるスナップショットの既定の制限数を 1 から 3 に増加。
- SnapshotUploader.exe で TLS 1.1 および1.2 のネゴシエートが可能。
- SnapshotUploader が警告またはエラーをログに記録したときに追加のテレメトリを報告。
- バックエンド サービスが 1 日あたりのクォータに達したことを報告したときに、スナップショットの作成を停止 (1 日あたり 50 のスナップショット)。
- 2 つのインスタンスが同時に実行されないようにするために、SnapshotUploader.exe にさらにチェックを追加。

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>[変更点]
- .NET Framework をターゲットとするアプリケーションで、Snapshot Collector が Microsoft.ApplicationInsights バージョン 2.3.0 以降に依存するようになりました。
以前は2.2.0 以降が使用されていました。
これは、ほとんどのアプリケーションで問題にならないと思われますが、この変更によって最新の Snapshot Collector を使用できなくなった場合はお知らせください。
- 失敗したアップロードを再試行するときに、Snapshot Uploader で指数バックオフ遅延を使用。
- テレメトリでのレポート作成の信頼性を高めるために、ServerTelemetryChannel を使用 (使用可能な場合)。
- スナップショット デバッガー サービスへの初期接続時に依存関係の追跡で無視されるように "SdkInternalOperationsMonitor" を使用。
- スナップショット デバッガー サービスへの初期接続に関するテレメトリを改善。
- 以下に関する追加テレメトリを報告。
  - Azure App Service バージョン。
  - Azure コンピューティング インスタンス。
  - コンテナー。
  - Azure 関数アプリ。
### <a name="bug-fixes"></a>バグの修正
- 問題カウンターのリセット間隔が 24 日に設定されている場合に、24 時間と解釈される。
- スナップショットの破棄中に例外が発生した場合に、Snapshot Uploader が新しいスナップショットの処理を停止するというバグを修正。

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Snapshot Uploader バイナリでの厳密な名前の署名を修正。

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>[変更点]
- SnapshotUploader(64).exe に必要なファイルが、メイン DLL にリソースとして埋め込まれるようになりました。 これにより、SnapshotCollectorFiles フォルダーが作成されなくなり、ビルドとデプロイが簡略化され、ソリューション エクスプローラーでの煩雑さが軽減されます。 アップグレード時には、`.csproj` ファイルで変更を入念に確認してください。 `Microsoft.ApplicationInsights.SnapshotCollector.targets` ファイルは不要になりました。
- ProvideAnonymousTelemetry が false に設定されている場合でも、テレメトリが Application Insights リソースに記録されます。 これは、Azure portal に正常性チェック機能を実装できるようにするための措置です。 ProvideAnonymousTelemetry は、製品のサポートと改善のためにマイクロソフトに送信されるテレメトリにのみ影響します。
- TempFolder または ShadowCopyFolder が環境変数にリダイレクトされるとき、これらの環境変数が設定されるまでコレクターをアイドル状態のまま維持。
- プロキシ サーバー経由でインターネットに接続するアプリケーションで、Snapshot Collector はプロキシ設定を自動検出し、SnapshotUploader.exe に渡すようになりました。
- SnapshotUplaoder プロセスの優先順位を下げる (可能な場合)。 この優先順位は、IsLowPrioirtySnapshotUploader オプションを使用してオーバーライドできます。
- プログラムを使用して Snapshot Collector を構成するシナリオのために、GetSnapshotCollector 拡張メソッドを TelemetryConfiguration に追加。
- 顧客向けのテレメトリで (アプリケーションのバージョンではなく) Application Insights SDK のバージョンを設定。
- 2 分後に最初のハートビート イベントを送信。
### <a name="bug-fixes"></a>バグの修正
- 例外に null 値または不変のデータ ディクショナリが含まれている場合の NullReferenceException を修正。
- アップローダーで、共有違反が発生した場合に、PDB の照合を数回再試行。
- スタートアップ時に複数のスレッドがテレメトリ パイプラインに呼び出されたときの重複するテレメトリを修正。

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>[変更点]
- XML ドキュメント コメント ファイルが NuGet パッケージに含まれるようになりました。
- 頻繁に発生する例外があることがわかっていて、それに対してスナップショットが作成されないようにする場合のために、ExcludeFromSnapshotting 拡張メソッドを `System.Exception` に追加。
- IsEnabledWhenProfiling 構成プロパティを追加。既定値は true です。 これは、Application Insights Profiler が詳細なコレクションを実行した場合に、スナップショットの作成が一時的に無効化された以前のバージョンからの変更点です。 以前の動作を復元するには、このプロパティを false に設定します。
### <a name="bug-fixes"></a>バグの修正
- SnapshotUploader64.exe に適切に署名。
- テレメトリ プロセッサの二重の初期化を防止。
- 複数のパイプラインがあるアプリで、テレメトリの二重のログ記録を防止。
- 収集計画の期限切れ日時に関するバグ (24 時間後にスナップショットが妨げられる場合がある) を修正。

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
このバージョンでの最大の変更 (したがって、新しいマイナー バージョン番号に移行) は、スナップショットの作成および処理パイプラインの更新です。 以前のバージョンでは、この機能は、ネイティブ コード (ProductionBreakpoints *.dll および SnapshotHolder*.exe) で実装されていました。 新しい実装は、すべて P/Invoke を使用したマネージド コードです。 新しいパイプラインを使用したこの最初のバージョンでは、動作は元の動作から大幅には変わっていません。 新しい実装では、より適切なエラー報告ができるようになり、今後の改善のための用意ができました。

### <a name="other-changes-in-this-version"></a>このバージョンのその他の変更
- MinidumpUploader.exe の名前が SnapshotUploader.exe (または SnapshotUploader64.exe) に変更されました。
- DeOptimize/ReOptimize 要求にタイミング テレメトリを追加。
- ミニダンプ アップロードのための gzip 圧縮を追加。
- PDB がロックされ、サイトのアップグレードが妨げられる問題を修正。
- シャドウ コピー時に元のフォルダー名 (SnapshotCollectorFiles) をログに記録。
- OOM が原因のサイトの再起動を防止するために、64 ビット プロセスのメモリ制限を調整。
- 無効化後もスナップショットが収集される問題を修正。
- 顧客の AI リソースにハートビート イベントを記録。
- 問題 ID から "ソース" を削除することで、スナップショットの速度を向上。

## <a name="112"></a>[1.1.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>[変更点]
使用状況テレメトリを拡張。
- .NET バージョンと OS を検出して報告。
- 追加の Azure 環境 (クラウド サービス、Service Fabric) を検出して報告。
- ハートビート テレメトリでの例外メトリック (初回例外の数と TrackException 呼び出しの数) を記録して報告。
### <a name="bug-fixes"></a>バグの修正
- 内部例外 (Win32Exception) がスローされない SqlException の処理を修正。
- シンボル フォルダーの末尾スペースのトリミング。これが原因で、MinidumpUploader へのコマンドライン引数が正しく解析されませんでした。
- スナップショット デバッガー エージェントのエンドポイントへの接続失敗時に無限に再試行されるのを防止。

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>[変更点]
- ホスト メモリの保護を追加。 この機能により、ホスト コンピューターのメモリへの影響が軽減されます。
- Azure portal スナップショットの表示エクスペリエンスを改善。