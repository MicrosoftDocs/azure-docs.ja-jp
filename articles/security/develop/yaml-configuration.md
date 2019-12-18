---
title: Microsoft Azure Security Code Analysis タスク カスタマイズ ガイド
description: この記事では、Microsoft Security Code Analysis 拡張機能のすべてのタスクをカスタマイズするための YAML 構成オプションについて説明します。
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 11/29/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: d8ffc5d8e2f594cbf608f7943e48417b172522c4
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852001"
---
# <a name="yaml-configuration-options-to-customize-the-build-tasks"></a>ビルド タスクをカスタマイズするための YAML 構成オプション

この記事では、各ビルド タスクで使用できるすべての YAML 構成オプションの一覧を示します。 この記事では、最初にセキュリティ コード分析ツールのタスクについて説明します。 最後に、後処理タスクについて説明します。

## <a name="anti-malware-scanner-task"></a>Anti-Malware Scanner タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | pickList | 常時 | True | Basic | Basic、Custom | 
| ScanType | pickList | InputType = Basic | True | CustomScan | CustomScan、FullSystemScan、QuickScan、YourConfiguredScan | マルウェア対策スキャンに使用するスキャンの種類。
| FileDirPath | filePath | ScanType = CustomScan | True | $(Build.StagingDirectory) |  | スキャン対象のファイルまたはディレクトリを示します。
| DisableRemediation | ブール値 | ScanType = CustomScan | False | true |  | オンの場合:1) ファイルの除外は無視されます。 2) アーカイブ ファイルはスキャンされます。 3) 検出後にアクションは適用されません。 4) イベント ログ エントリは検出後に書き込まれません。 5) カスタム スキャンからの検出はユーザー インターフェイスに表示されません。 6) コンソールの出力には、カスタム スキャンからの検出の一覧が表示されます。
| BootSectorScan | ブール値 | ScanType = CustomScan | False | false |  | オンにすると、ブート セクター スキャンが有効になります。
| 引数 | string | InputType = Custom | True | -Scan -ScanType 3 -DisableRemediation -File $(Build.StagingDirectory) |  | コマンド ライン引数。-File の引数は絶対パス、またはビルド エージェントで事前に定義されている $(Build.StagingDirectory) の相対パスです。 注:最後の引数として -File の引数を指定しない場合、既定値の $(Build.StagingDirectory) になります。 MpCmdRun.exe ツールで使用できる独自の引数を指定することもできます。<br/><br/>このツールのコマンド ライン引数の詳細を確認するには、「<strong>-h</strong>」または「<strong>-?</strong>」と [引数] フィールドに入力し、ビルド タスクを実行します。
| EnableServices | ブール値 | 常時 | True | false |  | オンにすると、Windows Update に必要なサービスが無効な場合に、有効化が試みられます。<br/>**注**: グループ ポリシーでこのサービスが無効にされていないことと、このビルドを実行しているアカウントに管理者特権があることを確認してください。
| SupportLogOnError | ブール値 | 常時 | True | false |  | オンにすると、エラーが発生したときに診断用のサポート ファイルが収集されます。 この処理には数分かかることがあります。<br/>**注**: このビルドを実行しているアカウントに管理者特権があることを確認してください。
| TreatSignatureUpdateFailureAs | pickList | 常時 | True | 警告 | Error、Standard、Warning | 実行時に署名を更新できない場合に使用されるログ レベルです。 **Error** に設定した場合、署名の更新に失敗すると、ビルド タスクは失敗します。 署名が比較的新しい (3 時間未満) 場合でも、ホストされているビルド エージェント上で署名の更新が失敗することがよくあるので、注意してください。
| SignatureFreshness | pickList | 常時 | True | UpToDate | OneDay、ThreeDays、TwoDays、UpToDate | マルウェア対策署名に許容される最長経過日数。 署名を更新できず、この値よりも古い場合、ビルド タスクは **[Validate Signatures Age As]\(署名の経過日数の検証\)** フィールドで選択した値に従って動作します。 注: **[Up-To-Date]\(最新\)** を選択した場合、署名は最大 3 時間まで許容されます。
| TreatStaleSignatureAs | pickList | 常時 | True | Error | Error、Standard、Warning | 署名の経過日数が、選択した **[AntiMalware Signature Age]\(マルウェア対策署名の経過日数\)** よりも古い場合に使用されるログ レベルです。 古い署名は **Warning** または **Informational** として扱われ、マルウェア対策スキャンを続行できる場合がありますが、これは推奨されません。

## <a name="binskim-task"></a>BinSkim タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | pickList | 常時 | True | Basic | Basic、CommandLine | 
| arguments | string | InputType = CommandLine | True |  |  | 実行する標準の Binskim コマンド ライン引数。 出力パスが削除され、置き換えられます。<br>このツールのコマンド ライン引数の詳細を確認するには、[引数] フィールドに「**help**」と入力し、ビルド タスクを実行してください。
| Function | pickList | InputType = Basic | True | analyze | analyze、dump、exportConfig、exportRules | 
| AnalyzeTarget | filePath | InputType = Basic && Function = analyze | True | $(Build.ArtifactStagingDirectory)\*.dll;<br>$(Build.ArtifactStagingDirectory)\*.exe |  | 分析対象として、特定のファイルまたはディレクトリの指定子を入力するか、1 つ以上のバイナリに解決されるフィルター パターンの指定子を入力します。指定子は複数入力することもできます。 (';' 区切りの一覧)
| AnalyzeSymPath | string | InputType = Basic && Function = analyze | False |  |  | ターゲットのシンボル ファイルのパス。
| AnalyzeConfigPath | string | InputType = Basic && Function = analyze | False | default |  | 分析の構成に使用されるポリシー ファイルのパス。 組み込みの設定を使用するには、'default' の値を渡します。
| AnalyzePluginPath | string | InputType = Basic && Function = analyze | False |  |  | 分析セット内のすべてのターゲットに対して呼び出されるプラグインのパス。
| AnalyzeRecurse | ブール値 | InputType = Basic && Function = analyze | False | true |  | ファイル指定子引数を評価するときにサブディレクトリに再帰します。
| AnalyzeVerbose | ブール値 | InputType = Basic && Function = analyze | False | false |  | 詳細出力を生成します。 結果の包括的なレポートは、コンプライアンス シナリオに適切な証拠が提供されるように設計されています。
| AnalyzeHashes | ブール値 | InputType = Basic && Function = analyze | False | false |  | SARIF レポートを生成するときに、分析ターゲットの SHA-256 ハッシュを出力します。
| AnalyzeStatistics | ブール値 | InputType = Basic && Function = analyze | False | false |  | 分析セッションのタイミングとその他の統計情報を生成します。
| AnalyzeEnvironment | ブール値 | InputType = Basic && Function = analyze | False | false |  | 実行のマシン環境の詳細を出力ファイルに記録します。 警告:このオプションを選択すると、機密性が高い可能性がある情報 (すべての環境変数値など) が生成されるログに記録されます。
| ExportRulesOutputType | pickList | InputType = Basic && Function = exportRules | False | SARIF | SARIF、SonarQube | 出力する規則記述子ファイルの種類。 これは、[Publish Security Analysis Logs]\(セキュリティ分析ログの発行\) ビルド タスクによって発行された BinSkim ログ フォルダーに含まれます。
| DumpTarget | filePath | InputType = Basic && Function = dump | True | $(Build.ArtifactStagingDirectory) |  | 分析対象として、特定のファイルまたはディレクトリの指定子を入力するか、1 つ以上のバイナリに解決されるフィルター パターンの指定子を入力します。指定子は複数入力することもできます。 (';' 区切りの一覧)
| DumpRecurse | ブール値 | InputType = Basic && Function = dump | False | true |  | ファイル指定子引数を評価するときにサブディレクトリに再帰します。
| DumpVerbose | ブール値 | InputType = Basic && Function = dump | False | true |  | 詳細出力を生成します。 結果の包括的なレポートは、コンプライアンス シナリオに適切な証拠が提供されるように設計されています。
| toolVersion | pickList | 常時 | False | 最新 | 1.5.0、Latest、LatestPreRelease | 実行するツールのバージョン。

## <a name="credential-scanner-task"></a>Credential Scanner タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| outputFormat | pickList | 常時 | False | pre | csv、pre、tsv | Credential Scanner の結果ファイルの出力形式。
| toolVersion | pickList | 常時 | False | 最新 | 1.27.7、Latest、LatestPreRelease | 実行するツールのバージョン。
| scanFolder | filePath | 常時 | False | $(Build.SourcesDirectory) |  | 資格情報をスキャンするリポジトリ内のフォルダー。
| searchersFileType | pickList | 常時 | False | Default | Custom、Default、DefaultAndCustom | スキャンに使用されるサーチャー ファイルを検索するオプション。
| searchersFile | filePath | searchersFileType == Custom OR searchersFileType == DefaultAndCustom | False |  |  | 実行するチェックの Credential Scanner サーチャーの構成ファイル。 複数の値を含めて使用するには、Credential Scanner サーチャー ファイルのパスのコンマ区切りリストを指定します。
| suppressionsFile | filePath | 常時 | False |  |  | 出力ログの問題を抑制するために使用する Credential Scanner の抑制ファイル。
| suppressAsError | ブール値 | 常時 | False | false |  | 抑制された一致は、既定の抑制された出力ファイル [-O]-suppressed.[-f] ではなく、出力ファイル [-O]-matches.[-f] に出力されます。 既定値は False です。
| verboseOutput | ブール値 | 常時 | False | false |  | 詳細情報を出力します。
| batchSize | string | 常時 | False |  |  | 資格情報スキャナーの並列実行に使用するコンカレント スレッドの数。 (既定値は 20)<br/>値は 1-2147483647 の範囲内である必要があります。
| regexMatchTimeoutInSeconds | string | 常時 | False |  |  | サーチャーがチェックを中止する前に照合に費やす時間です (秒単位)。<br/>コマンド ラインに ``-Co RegexMatchTimeoutInSeconds=<Value>`` を追加します。
| fileScanReadBufferSize | string | 常時 | False |  |  | コンテンツの読み取り中のバッファー サイズ (バイト単位)。 既定値は 524288 です。<br/>コマンド ラインに ``-Co FileScanReadBufferSize=<Value>`` を追加します。
| maxFileScanReadBytes | string | 常時 | False |  |  | コンテンツの分析中に指定したファイルから読み取る最大バイト数。 既定値は 104,857,600 です。<br/>コマンド ラインに ``-Co MaxFileScanReadBytes=<Value>`` を追加します。

## <a name="microsoft-security-risk-detection-task"></a>Microsoft Security Risk Detection タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                           |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceEndpointName | connectedService:Generic | 常時 | True |  |  | MSRD インスタンス URL (オンボード済み) および REST API アクセス トークン (アカウント設定ページから生成され、アカウントへの完全なアクセスが許可されるもの) が格納される VSTS プロジェクトの事前構成済みサービス エンドポイントの名前 (ジェネリック型)。
| AccountId | string | 常時 | True |  |  | アカウントを識別する GUID。 アカウントの URL から取得できます。
| BinariesURL | string | 常時 | True |  |  | ファジー マシンでバイナリのダウンロードに使用されるセミコロン区切りの URL 一覧。 URL が公開されていることを確認してください。
| SeedsURL | string | 常時 | False |  |  | ファジー マシンでシードのダウンロードに使用されるセミコロン区切りの URL 一覧。 URL が公開されていることを確認してください。
| OSPlatformType | pickList | 常時 | True | Windows | Linux、Windows | ファジー ジョブを実行するマシンの OS プラットフォームの種類。
| WindowsEdition | string | OSPlatformType = Windows | True | Server 2008 R2 |  | ファジー テスト ジョブが実行されるマシンの OS エディション。
| LinuxEdition | string | OSPlatformType = Linux | True | Redhat |  | ファジー テスト ジョブが実行されるマシンの OS エディション。
| PreSubmissionCommand | string | 常時 | False |  |  | ファジー テスト ジョブの送信前にテスト対象プログラムとその依存関係をインストールするためにテスト マシン上で実行する必要があるスクリプト。
| SeedDirectory | string | 常時 | True |  |  | シードが格納されるファジー テスト マシン上のディレクトリのパス。 詳細については、「[シード ファイル ディレクトリ](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/03-choose-seed-files#seed-file-directory)」を参照してください。
| SeedExtension | string | 常時 | True |  |  | シードのファイル拡張子。
| TestDriverExecutable | string | 常時 | True |  |  | ファジー テスト マシン上のターゲット実行可能ファイルのパス。 詳細については、「[EPE の完全なパス](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/02-choose-exe#full-path-to-the-epe)」を参照してください。
| TestDriverExeType | pickList | 常時 | True | x86 | amd64、x86 | ターゲット実行可能ファイルのアーキテクチャです。
| TestDriverParameters | string | 常時 | True | "%testfile%" |  | テスト対象の実行可能ファイルに渡されるコマンドライン引数。 **"%testfile%"** という記号 (二重引用符を含む) は、テスト ドライバーで解析することになるターゲット ファイルの完全なパスに自動的に置き換えられることに注意してください (必須)。 詳細については、「[コマンドライン引数](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/02-choose-exe#command-line-arguments)」を参照してください。
| ClosesItself | ブール値 | 常時 | True | true |  | 完了時にテスト ドライバーが自動的に終了する場合はオンにします (たとえば、テスト ドライバーで入力ファイルの解析後にすぐに終了する場合など)。テスト ドライバーを強制的に終了する必要がある場合はオフにします (たとえば、テスト ドライバーが、入力の解析後もメイン ウィンドウを開いたままにする GUI アプリケーションの場合など)。 詳細については、「[自動終了](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/05-scope-exe-lifetime#self-termination)」を参照してください。
| MaxDurationInSeconds | string | 常時 | True | 5 |  | テスト ドライバーの最大継続時間 (秒)。 ターゲット プログラムが入力ファイルを解析するのに必要であると考えられる最大推定時間を指定します。 この推定の精度が高いほど、ファジー テストの実行効率が上がります。 詳細については、「[予想される実行時間の最大値](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/05-scope-exe-lifetime#maximum-expected-execution-duration)」を参照してください。
| CanRunRepeat | ブール値 | 常時 | True | true |  | 永続化 (または共有) されたグローバルな状態に依存することなくテスト ドライバーを繰り返し実行できる場合は、チェック ボックスをオンにします。 詳細については、「[最初からの実行](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#runs-from-scratch)」を参照してください。
| CanTestDriverBeRenamed | ブール値 | 常時 | True | false |  | テスト ドライバー実行可能ファイルがその名前を変更しても正しく動作する場合は、チェック ボックスをオンにします。 詳細については、「[名前の変更と並列化](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#can-be-renamed-and-parallelized)」を参照してください。
| SingleOsProcess | ブール値 | 常時 | True | false |  | テスト ドライバーが単一の OS プロセスで動作する場合はチェック ボックスをオンにします。テスト ドライバーによって追加のプロセスが生成される場合は、チェック ボックスをオフにします。 詳細については、「[単一プロセス](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#single-process)」を参照してください。

## <a name="roslyn-analyzers-task"></a>Roslyn Analyzers タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                   |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userProvideBuildInfo | pickList | 常時 | True | 自動 | auto、msBuildInfo | Roslyn 分析用の MSBuild バージョン、MSBuild アーキテクチャ、およびビルド コマンド ラインを指定するユーザー向けのオプション。 **Auto** が選択されている場合、このタスクによって、同じパイプラインの (ビルドの) 以前の **MSBuild**、**VSBuild**、 **.NET Core** タスクからビルド情報が取得されます。
| msBuildVersion | pickList | userProvideBuildInfo == msBuildInfo | True | 16.0 | 15.0、16.0 | MSBuild のバージョン。
| msBuildArchitecture | pickList | userProvideBuildInfo == msBuildInfo | True | x86 | DotNetCore、x64、x86 | MSBuild アーキテクチャ。 注:ビルド コマンドラインから **dotnet.exe build** を呼び出す場合は、 **[Via .Net Core]\(.NET Core 経由\)** オプションを選択します。
| msBuildCommandline | string | userProvideBuildInfo == msBuildInfo | True |  |  | ソリューションまたはプロジェクトをコンパイルするための完全なビルド コマンドライン。<br/><br/>注:このコマンドラインは、**MSBuild.exe** または **dotnet.exe** の完全なパスで始める必要があります。<br/>コマンドは、作業ディレクトリとして $(Build.SourcesDirectory) を使用して実行されます。
| rulesetName | pickList | 常時 | False | 推奨 | Custom、None、Recommended、Required | 使用する名前付きルールセット。<br/><br/>`Ruleset Configured In Your Visual Studio Project File(s)` を選択した場合は、VS プロジェクト ファイルで事前に構成されたルールセットが使用されます。 `Custom` を選択した場合は、カスタム ルールセットのパス オプションを設定できます。
| rulesetVersion | pickList | rulesetName == Required OR rulesetName == Recommended | False | 最新 | 8.0、8.1、8.2、Latest、LatestPreRelease | 選択した SDL ルールセットのバージョン。
| customRuleset | string | rulesetName = Custom | False |  |  | 使用するルールセットへのアクセス可能なパス。 相対パスは、ソース リポジトリ (`$(Build.SourcesDirectory)`) のルートに正規化されます。<br/><br/>`Actions` が `Error` に設定された `Rules` をルールセットで指定すると、ビルド タスクは失敗します。 これを行うルールセットを使用するには、ビルド タスクの `Control Options` で `Continue on error` をオンにしてください。
| microsoftAnalyzersVersion | pickList | 常時 | False | 最新 | 2.9.3、2.9.4、2.9.6、Latest、LatestPreRelease | 実行する [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) パッケージのバージョン。
| suppressionFileForCompilerWarnings | filePath | 常時 | False |  |  | C# および VB コンパイラの警告を抑制する抑制ファイル。<br/><br/>各警告 ID が個別の行に記載されたプレーンテキスト ファイル。<br/>コンパイラの警告について、警告 ID の数値部分のみを指定します。 たとえば、1018 を指定すると CS1018 が抑制され、CA1501 を指定すると CA1501 が抑制されます。<br/><br/>相対ファイル パスは、ソース リポジトリ (`$(Build.SourcesDirectory)`) のルートに追加されます。

## <a name="tslint-task"></a>TSLint タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RuleLibrary | pickList | 常時 | True | tslint | custom、microsoft、tslint | すべての結果には、選択したバージョンの TSLint に付属するルールが含まれます ( **[Base Only]\(ベースのみ\)** )。<br/><br/>**[Base Only]\(ベースのみ\) -** TSLint と共にリリースされたルールのみ。<br/><br/>**[Include Microsoft Rules]\(Microsoft ルールを含める\) -** [tslint-microsoft-contrib](https://github.com/Microsoft/tslint-microsoft-contrib) をダウンロードします。TSLint の実行で使用できるルールが含まれます。 このオプションを選択すると、Microsoft のルールに必須であり、自動的に使用されるため、`Type Checking` チェックボックスが非表示になります。 また、`Microsoft Contribution Version` フィールドも非表示になり、[npm](https://www.npmjs.com/package/tslint-microsoft-contrib) から `tslint-microsoft-contrib` のバージョンを選択できるようになります。<br/><br/>**[Include Custom Rules]\(カスタム ルールを含める\) -** `Rules Directory` フィールドを再表示します。これで、TSLint の実行に使用できる TSLint ルールのディレクトリのアクセス可能なパスを受け入れられるようになります。<br/><br/>**注:** 多くのユーザーで Microsoft のルールセットの構成に問題が起こったため、既定値は tslint に変更されました。 特定のバージョンの構成については、[GitHub の tslint-microsoft-contrib](https://github.com/microsoft/tslint-microsoft-contrib) に関するページを参照してください。
| RulesDirectory | string | RuleLibrary == custom | True |  |  | TSLint の実行に使用できる追加の TSLint ルールを含むアクセス可能なディレクトリ。
| Ruleset | pickList | RuleLibrary != microsoft | True | tsrecommended | custom、tslatest、tsrecommended | TypeScript ファイルに対して実行するルールを定義します。<br/><br/>**[tslint:latest](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) -** すべての TSLint リリースの最新ルールの構成を含めるように `tslint:recommended` を拡張します。また、継続的に更新されます。 この構成を使用すると、新しいルールが有効になり、コード内で lint エラーが発生するため、マイナー リリース間で破壊的変更が生じる場合があります。 TSLint がメジャー バージョンのバンプに達すると、`tslint:recommended` は `tslint:latest` と同じになるように更新されます。<br/><br/>**[tslint:recommended](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) -** TSLint が一般的な TypeScript プログラミングに推奨している、安定した、やや独自のルール セット。 この構成は `semver` に従うため、マイナー リリースまたはパッチ リリース間に破壊的変更は "*生じません*"。
| RulesetMicrosoft | pickList | RuleLibrary == microsoft | True | mssdlrequired | custom、msrecommended、mssdlrecommended、mssdlrequired、tslatest、tsrecommended | TypeScript ファイルに対して実行するルールを定義します。<br/><br/>**[microsoft:sdl-required](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -** "*必須の*" [セキュリティ開発ライフサイクル (SDL)](https://www.microsoft.com/esdl/) ポリシーを満たす tslint および tslint-microsoft-contrib ルールによって提供される利用可能なすべてのチェックを実行します。<br/><br/>**[microsoft:sdl-recommended](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -** "*必須および推奨の*" [セキュリティ開発ライフサイクル (SDL)](https://www.microsoft.com/sdl/) ポリシーを満たす tslint および tslint-microsoft-contrib ルールによって提供される利用可能なすべてのチェックを実行します。<br/><br/>**[microsoft:recommended](https://github.com/Microsoft/tslint-microsoft-contrib/blob/master/recommended_ruleset.js) -** tslint-microsoft-contrib ルールの作成者によって推奨されるすべてのチェック。 これには、セキュリティ チェックとセキュリティ以外のチェックが含まれます。<br/><br/>**[tslint:latest](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) -** すべての TSLint リリースの最新ルールの構成を含めるように `tslint:recommended` を拡張します。また、継続的に更新されます。 この構成を使用すると、新しいルールが有効になり、コード内で lint エラーが発生するため、マイナー リリース間で破壊的変更が生じる場合があります。 TSLint がメジャー バージョンのバンプに達すると、`tslint:recommended` は `tslint:latest` と同じになるように更新されます。<br/><br/>**[tslint:recommended](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) -** TSLint が一般的な TypeScript プログラミングに推奨している、安定した、やや独自のルール セット。 この構成は `semver` に従うため、マイナー リリースまたはパッチ リリース間に破壊的変更は "*生じません*"。
| RulesetFile | string | Ruleset == custom OR RulesetMicrosoft == custom | True |  |  | 実行するルールを指定する[構成ファイル](https://palantir.github.io/tslint/usage/tslint-json/)。<br/><br/>構成のパスは、[カスタム ルール](https://palantir.github.io/tslint/develop/custom-rules/)のパスとして追加されます。
| FileSelectionType | pickList | 常時 | True | fileGlob | fileGlob、projectFile | 
| ファイル | string | FileSelectionType == fileGlob | True | **\*.ts |  | 処理するファイルを決定するファイル [glob](https://www.npmjs.com/package/glob)。 パスは、`Build.SourcesDirectory` 値に対する相対パスです。<br/><br/>Microsoft の Contribution ライブラリでは、プロジェクト ファイルを使用する必要があります。 `File Glob Pattern` オプションを指定して Microsoft の Contribution ライブラリを使用している場合は、プロジェクト ファイルが自動的に生成されます。
| ECMAScriptVersion | pickList | FileSelectionType == fileGlob && RuleLibrary == microsoft | True | ES3 | ES2015、ES2016、ES2017、ES3、ES5、ES6、ESNext | TypeScript コンパイラを使用して構成された ECMAScript のターゲット バージョン。 プロジェクト ファイルを使用する場合、これは TypeScript tsconfig.json ファイルの compilerOptions.target フィールドです。
| Project | string | FileSelectionType == projectFile | True |  |  | TSLint を実行する TypeScript ファイルを指定する [tsconfig.json](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html) ファイルのパス。 パスは、`Build.SourcesDirectory` 値に対する相対パスです。
| TypeCheck | ブール値 | RuleLibrary != microsoft && FileSelectionType == projectFile | False | true |  | lint 処理ルールを実行するときにタイプ チェッカーを有効にします。
| ExcludeFiles | string | 常時 | False |  |  | lint 処理から除外するファイルを示す [glob](https://www.npmjs.com/package/glob)。 パスは、`Build.SourcesDirectory` 値に対する相対パスです。 複数の値を指定するには、セミコロンで区切ります。
| OutputFormat | pickList | 常時 | True | json | checkstyle、codeFrame、filesList、json、msbuild、pmd、prose、stylish、verbose、vso | 出力の生成に使用する[フォーマッタ](https://palantir.github.io/tslint/formatters/)。 JSON 形式はポスト分析と互換性があることに注意してください。
| NodeMemory | string | 常時 | False |  |  | TSLint を実行するためにノードに割り当てられる、MB 単位の明示的なメモリ容量。 例:8000<br/><br/>`v8 option` であるノードの `--max_old_space=<value>` CLI オプションにマップします。
| ToolVersion | pickList | RuleLibrary != microsoft | True | latest | 4.0.0、4.0.1、4.0.2、4.1.0、4.1.1、4.2.0、4.3.0、4.3.1、4.4.0、4.4.1、4.4.2、4.5.0、4.5.1、5.0.0、5.1.0、5.2.0、5.3.0、5.3.2、5.4.0、5.4.1、5.4.2、5.4.3、5.5.0、最新 | ダウンロードして実行する TSLint の[バージョン](https://github.com/palantir/tslint/releases)。
| TypeScriptVersion | pickList | 常時 | True | latest | 0.8.0、0.8.1、0.8.2、0.8.3、0.9.0、0.9.1、0.9.5、0.9.7、1.0.0、1.0.1、1.3.0、1.4.1、1.5.3、1.6.2、1.7.3、1.7.5、1.8.0、1.8.10、1.8.2、1.8.5、1.8.6、1.8.7、1.8.9、1.9.0、2.0.0、2.0.10、2.0.2、2.0.3、2.0.6、2.0.7、2.0.8、2.0.9、2.1.1、2.1.4、2.1.5、2.1.6、2.2.0、2.2.1、カスタム、最新 | ダウンロードして使用する [typescript](https://www.npmjs.com/package/typescript) のバージョン。<br/>**注:** これは、コードのコンパイルに使用される TypeScript のバージョンと同じである必要があります。
| TypeScriptVersionCustom | string | TypeScriptVersion == custom | True | latest |  | ダウンロードして使用する [typescript](https://www.npmjs.com/package/typescript) のバージョン。<br/>**注:** これは、コードのコンパイルに使用される TypeScript のバージョンと同じである必要があります。
| MicrosoftContribVersion | pickList | RuleLibrary == microsoft |  | latest | 4.0.0、4.0.1、5.0.0、5.0.1、最新 | ダウンロードして使用する [tslint-microsoft-contrib](https://www.npmjs.com/package/tslint-microsoft-contrib) (SDL ルール) のバージョン。</br>**注:** tslint-microsoft-contrib に選択したバージョンと互換性がある [tslint](https://www.npmjs.com/package/tslint) のバージョンが選択されます。 tslint-microsoft-contrib の更新は、テストの期間が発生するまで、このビルド タスクによってゲートされます。

## <a name="publish-security-analysis-logs-task"></a>Publish Security Analysis Logs タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ArtifactName | string | 常時 | True | CodeAnalysisLogs |  | 作成する成果物の名前。
| ArtifactType | pickList | 常時 | True | コンテナー | Container、FilePath | 作成する成果物の型。
| TargetPath | string | ArtifactType = FilePath | False | \\my\share\$(Build.DefinitionName)<br>\$(Build.BuildNumber) |  | ファイルのコピー先のファイル共有
| AllTools | ブール値 | 常時 | True | true |  | すべての Secure Development Tools ビルド タスクによって生成された結果を発行します。
| AntiMalware | ブール値 | AllTools = false | True | true |  | マルウェア対策ビルド タスクによって生成された結果を発行します。
| BinSkim | ブール値 | AllTools = false | True | true |  | BinSkim ビルド タスクによって生成された結果を発行します。
| CredScan | ブール値 | AllTools = false | True | true |  | Credential Scanner ビルド タスクによって生成された結果を発行します。
| MSRD | ブール値 | AllTools = false | True | true |  | MSRD ビルド タスクによって開始された MSRD ジョブのジョブ情報とジョブの url を発行します。 MSRD ジョブは長時間実行され、個別のレポートが提供されます。
| RoslynAnalyzers | ブール値 | AllTools = false | True | false |  | Roslyn アナライザー ビルド タスクによって生成された結果を発行します。
| TSLint | ブール値 | AllTools = false | True | true |  | TSLint ビルド タスクによって生成された結果を発行します。 レポートでは、JSON 形式の TSLint ログのみがサポートされていることに注意してください。 別の形式を選択した場合は、それに応じて TSLint ビルド タスクを更新してください。
| ToolLogsNotFoundAction | picklist | 常時 | True | Standard | Error、None、Standard、Warning | 選択したツール (または [All Tools]\(すべてのツール\) がオンの場合はすべてのツール) のログが見つからない場合 (つまりツールが実行されなかった場合) に実行するアクション。<br/><br/>**オプション:**<br/>**None:** メッセージをアクセス可能な詳細な出力ストリームに書き込むには、VSTS 変数 **system.debug** を **true** に設定する必要があります。<br/>**Standard:** (既定値) ツールのログが見つからなかったことを示す標準出力メッセージを書き込みます。<br/>**警告:** ツールのログが見つからなかったことを示す黄色の警告メッセージを書き込みます。これは、ビルドの概要ページに警告として表示されます。<br/>**エラー:** 赤色のエラー メッセージを書き込み、例外をスローして、ビルドを中断します。 個々のツールを選択し、どのツールが実行されたかを確認するには、このオプションを使用します。

## <a name="security-report-task"></a>Security Report タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VstsConsole | ブール値 | 常時 | False | true |  | パイプライン コンソールに結果を書き込みます。
| TsvFile | ブール値 | 常時 | False | true |  | 見つかった結果ごとに 1 行が使用され、結果の情報がタブで区切られている tsv ファイル (タブ区切り値) を生成します。
| HtmlFile | ブール値 | 常時 | False | true |  | html レポート ファイルを生成します。
| AllTools | ブール値 | 常時 | True | false |  | すべての Secure Development Tools ビルド タスクによって生成された結果を報告します。
| BinSkim | ブール値 | AllTools = false | True | false |  | BinSkim ビルド タスクによって生成されたレポート結果。
| BinSkimBreakOn | pickList | AllTools = true OR BinSkim = true | True | Error | Error、WarningAbove | 報告する結果のレベル。
| CredScan | ブール値 | AllTools = false | True | false |  | Credential Scanner ビルド タスクによって生成された結果を報告します。
| MSRD | ブール値 | AllTools = false | True | false |  | MSRD ビルド タスクによって開始された MSRD ジョブのジョブ情報とジョブの url を報告します。 MSRD ジョブは長時間実行され、個別のレポートが提供されます。
| RoslynAnalyzers | ブール値 | AllTools = false | True | false |  | Roslyn Analyzer ビルド タスクによって生成された結果を報告します。
| RoslynAnalyzersBreakOn | pickList | AllTools = true OR RoslynAnalyzers = true | True | Error | Error、WarningAbove | 報告する結果のレベル。
| TSLint | ブール値 | AllTools = false | True | false |  | TSLint ビルド タスクによって生成された結果を報告します。 レポートでは、JSON 形式の TSLint ログのみがサポートされていることに注意してください。 別の形式を選択した場合は、それに応じて TSLint ビルド タスクを更新してください。
| TSLintBreakOn | pickList | AllTools = true OR TSLint = true | True | Error | Error、WarningAbove | 報告する結果のレベル。
| ToolLogsNotFoundAction | picklist | 常時 | True | Standard | Error、None、Standard、Warning | 選択したツール (または [All Tools]\(すべてのツール\) がオンの場合はすべてのツール) のログが見つからない場合 (つまりツールが実行されなかった場合) に実行するアクション。<br/><br/>**オプション:**<br/>**None:** メッセージをアクセス可能な詳細な出力ストリームに書き込むには、VSTS 変数 **system.debug** を **true** に設定する必要があります。<br/>**Standard:** (既定値) ツールのログが見つからなかったことを示す標準出力メッセージを書き込みます。<br/>**警告:** ツールのログが見つからなかったことを示す黄色の警告メッセージを書き込みます。これは、ビルドの概要ページに警告として表示されます。<br/>**エラー:** 赤色のエラー メッセージを書き込み、例外をスローして、ビルドを中断します。 個々のツールを選択し、どのツールが実行されたかを確認するには、このオプションを使用します。
| CustomLogsFolder | string | 常時 | False |  |  | 分析ツールのログが配置されているベース フォルダー。個々のログ ファイルは、このパス以下の各ツールに従って名付けられたサブフォルダーに配置されます。

## <a name="post-analysis-task"></a>Post-Analysis タスク

| **InputType**      | **Type**     | **適用条件**            | **必須** | **既定値**             | **オプション (候補リスト)**                                   | **説明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AllTools | ブール値 | 常時 | True | false |  | Microsoft Security Code Analysis ビルド タスクによって問題が検出された場合は、ビルドを中断します。
| BinSkim | ブール値 | AllTools = false | True | false |  | BinSkim の問題が見つかった場合、選択した [Break On]\(中断するタイミング\) オプションに従ってビルドを中断します。
| BinSkimBreakOn | pickList | AllTools = true OR BinSkim = true | True | Error | Error、WarningAbove | ビルドを中断する問題のレベル。
| CredScan | ブール値 | AllTools = false | True | false |  | Credential Scanner の問題が見つかった場合にビルドを中断します。
| RoslynAnalyzers | ブール値 | AllTools = false | True | false |  | Roslyn アナライザーの問題が見つかった場合は、ビルドを中断します。
| RoslynAnalyzersBreakOn | pickList | AllTools = true OR RoslynAnalyzers = true | True | Error | Error、WarningAbove | ビルドを中断する問題のレベル。
| TSLint | ブール値 | AllTools = false | True | false |  | TSLint の何らかの問題が見つかった場合は、ビルドを中断します。 ポスト分析では、JSON 形式の TSLint ログのみがサポートされていることに注意してください。 別の形式を選択した場合は、それに応じて TSLint ビルド タスクを更新してください。
| TSLintBreakOn | pickList | AllTools = true OR TSLint = true | True | Error | Error、WarningAbove | ビルドを中断する問題のレベル。
| VstsConsole | ブール値 | 常時 | False | true |  | パイプライン コンソールに結果を書き込みます。
| ToolLogsNotFoundAction | picklist | 常時 | True | Standard | Error、None、Standard、Warning | 選択したツール (または [All Tools]\(すべてのツール\) がオンの場合はすべてのツール) のログが見つからない場合 (つまりツールが実行されなかった場合) に実行するアクション。<br/><br/>**オプション:**<br/>**None:** メッセージをアクセス可能な詳細な出力ストリームに書き込むには、VSTS 変数 **system.debug** を **true** に設定する必要があります。<br/>**Standard:** (既定値) ツールのログが見つからなかったことを示す標準出力メッセージを書き込みます。<br/>**警告:** ツールのログが見つからなかったことを示す黄色の警告メッセージを書き込みます。これは、ビルドの概要ページに警告として表示されます。<br/>**エラー:** 赤色のエラー メッセージを書き込み、例外をスローして、ビルドを中断します。 個々のツールを選択し、どのツールが実行されたかを確認するには、このオプションを使用します。

## <a name="next-steps"></a>次の手順

Security Code Analysis 拡張機能と提供されるツールについてさらに質問がある場合は、[FAQ ページ](security-code-analysis-faq.md)を参照してください。