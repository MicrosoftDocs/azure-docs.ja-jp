---
title: Azure Web アプリ拡張機能のリリース ノート - Application Insights
description: Application Insights を使用した実行時インストルメンテーションのための Azure Web アプリ拡張機能のリリース ノートです。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 07ba61f630b849a377f1c7ba881f95518eb73606
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042608"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Application Insights 用 Azure Web アプリ拡張機能のリリース ノート

この記事には、Application Insights を使用した実行時インストルメンテーションのための Azure Web アプリ拡張機能のリリース ノートが含まれています。 これは、プレインストールされている拡張機能にのみ適用されます。

Application Insights 用 Azure Web アプリ拡張機能の詳細については、[こちら](azure-web-apps.md)を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

- 現在使用している拡張機能のバージョンは、どうすれば確認できますか?
    - `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights` にアクセスします。 詳細については、[拡張機能またはエージェント ベースの監視に関するステップ バイ ステップ トラブルシューティング ガイド](./azure-web-apps.md?tabs=net#troubleshooting)を参照してください。

- プライベート拡張機能を使用している場合はどうすればよいですか?
    - プライベート サイト拡張機能は現在サポートされていないため、アンインストールしてください。

## <a name="release-notes"></a>リリース ノート

### <a name="2838"></a>2.8.38

- JAVA 拡張機能: 2.5.1 から [Java Agent 3.0.2 (GA)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) へのアップグレード。
- Node.js 拡張機能: AI SDK を 1.8.7 から [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) に更新。
- .NET Core: サポート対象外のバージョン (2.0、2.2、3.0) の削除。 サポートされているバージョンは 2.1 と 3.1 です。

### <a name="2837"></a>2.8.37

- AppSvc Windows 拡張機能: .Net Core を System.Diagnostics.DiagnosticSource.dll のすべてのバージョンで動作するようにしました。

### <a name="2836"></a>2.8.36

- AppSvc Windows 拡張機能: .NET Core での AI SDK との相互操作を有効化。

### <a name="2835"></a>2.8.35

- AppSvc Windows 拡張機能: .NET Core 3.1 サポートの追加。

### <a name="2833"></a>2.8.33

- .NET、.NET Core、Java、Node.js エージェント、および Windows 拡張機能:ソブリン クラウドのサポート。 接続文字列を使用して、ソブリン クラウドにデータを送信できます。

### <a name="2831"></a>2.8.31

- ASP.NET Core エージェント:更新された Application Insights SDK の参照の 1 つに関する問題の修正 (2.8.26 の既知の問題を参照)。 正しくないバージョンの `System.Diagnostics.DiagnosticSource.dll` がランタイムによって既に読み込まれている場合、コードレス拡張機能はアプリケーションをクラッシュさせず、バックオフします。 この問題の影響を受けているお客様については、bin フォルダーから `System.Diagnostics.DiagnosticSource.dll` を削除するか、"ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24" を設定して、以前のバージョンの拡張機能を使用することをお勧めします。そのようにしないと、アプリケーションの監視は有効になりません。

### <a name="2826"></a>2.8.26

- ASP.NET Core エージェント:更新された Application Insights SDK に関連する問題の修正。 ApplicationInsights.dll が bin フォルダーに既に存在する場合、エージェントは `AiHostingStartup` を読み込もうとしません。 これにより、Assembly\<AiHostingStartup\>.GetTypes() によるリフレクションに関連する問題が解決されます。
- 既知の問題:別のバージョンの `DiagnosticSource` dll が読み込まれると、例外 `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` がスローされることがあります。 これは、`System.Diagnostics.DiagnosticSource.dll` が発行フォルダーに存在する場合などに発生することがあります。 軽減策として、App Services で次のようにアプリ設定を行い、以前のバージョンの拡張機能を使用します。ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24

### <a name="2824"></a>2.8.24

- 2\.8.21 の再パッケージ化バージョン。

### <a name="2823"></a>2.8.23

- ASP.NET Core 3.0 のコード不要の監視がサポート対象に追加されました。
- ランタイム バージョン 2.1、2.2、3.0 に関して、ASP.NET Core SDK が [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) に更新されました。 .NET Core 2.0 をターゲットとするアプリでは、引き続き 2.1.1 の SDK が使用されます。

### <a name="2814"></a>2.8.14

- .NET Core 2.1 と 2.2 をターゲットとするアプリに関して、ASP.NET Core SDK のバージョンが 2.3.0 から最新 (2.6.1) に更新されました。 .NET Core 2.0 をターゲットとするアプリでは、引き続き 2.1.1 の SDK が使用されます。

### <a name="2812"></a>2.8.12

- ASP.NET Core 2.2 アプリのサポート。
- アプリケーションが既に SDK を使用してインストルメント化されている場合でも SDK のインジェクションが発生する原因となっていた ASP.NET Core 拡張機能のバグを修正。 2\.1 および 2.2 アプリの場合、アプリケーション フォルダーに ApplicationInsights.dll が存在すると、拡張機能がバックオフするようになりました。 2\.0 アプリの場合は、ApplicationInsights が `UseApplicationInsights()` 呼び出しで有効になっている場合にのみ、拡張機能がバックオフします。

- ASP.NET Core アプリの不完全な HTML 応答の完全な修正。 この修正は、.NET Core 2.2 アプリでも機能するように拡張されました。

- ASP.NET Core アプリの JavaScript インジェクションを無効にするためのサポートを追加 (`APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting`)。 ASP.NET Core の場合、明示的に無効にしない限り、JavaScript インジェクションは既定で "オプトアウト" モードになっています。 (既定の設定は、現在の動作を保持するためのものです。)

- ikey が存在しない場合でもインジェクションが発生する原因となっていた ASP.NET Core 拡張機能のバグを修正。
- テレメトリで正しくない SDK バージョンの原因となっていた SDK バージョン プレフィックス ロジックのバグを修正。

- テレメトリの収集方法を識別するため、ASP.NET Core アプリの SDK バージョン プレフィックスを追加。
- プレインストールされている拡張機能のバージョンを正しく表示するため、SCM- ApplicationInsights ページを修正。

### <a name="2810"></a>2.8.10

- ASP.NET Core アプリの不完全な HTML 応答の修正。

## <a name="next-steps"></a>次のステップ

- Azure App Service の監視を構成する方法の詳細については、[Azure App Service のドキュメント](azure-web-apps.md)を参照してください。 
