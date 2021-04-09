---
title: JavaScript Web アプリケーションの SDK 読み込みエラーのトラブルシューティング - Azure Application Insights
description: JavaScript Web アプリケーションの SDK 読み込みエラーのトラブルシューティングを行う方法
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711418"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>JavaScript Web アプリの SDK 読み込みエラーのトラブルシューティング

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

SDK 読み込みエラーの例外は、SDK スクリプトがダウンロードまたは初期化に失敗したことが JavaScript スニペット (v3 以降) によって検出されると、作成および報告されます。 つまり、エンド ユーザーのクライアント (ブラウザー) が Application Insights SDK をダウンロードできなかったか、識別されたホスト ページから初期化できなかったため、テレメトリやイベントはまったく報告されません。

![Azure portal ブラウザーのエラーの概要](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> この例外は、fetch() API や XMLHttpRequest をサポートするすべての主要なブラウザーでサポートされています。 IE 8 以下は除外されます。このため、それらのブラウザーではこの種の例外は報告されません (ご使用の環境に fetch polyfill が含まれている場合を除く)。

![ブラウザーの例外の詳細](./media/javascript-sdk-load-failure/exception.png)

スタック詳細には、エンド ユーザーが使用している URL などの基本情報が含まれます。

| 名前                      | 説明                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN&nbsp;Endpoint&gt; | SDK をダウンロードするために使用された (失敗した) URL。                                                      |
| &lt;Help&nbsp;Link&gt;    | トラブルシューティング ドキュメント (このページ) への URL リンク。                                              |
| &lt;Host&nbsp;URL&gt;     | エンド ユーザーが使用していたページの完全な URL。                                                    |
| &lt;Endpoint&nbsp;URL&gt; | 例外を報告するために使用された URL。この値は、ホスティング ページがパブリック インターネットとプライベート クラウドのどちらからアクセスされたかを特定するのに役立ちます。

この例外が発生する最も一般的な理由は次のとおりです。

- 間欠的なネットワーク接続エラー。
- Application Insights CDN の停止。
- スクリプト読み込み後の SDK の初期化エラー。
- Application Insights JavaScript CDN がブロックされている。

[間欠的なネットワーク接続エラー](#intermittent-network-connectivity-failure)は、この例外の最も一般的な理由であり、ユーザーのネットワーク接続が間欠的に失われるモバイル ローミング シナリオでは特にこれが原因となります。

以下のセクションでは、このエラーの根本原因として考えられるものそれぞれのトラブルシューティングを行う方法を説明します。

> [!NOTE]
> トラブルシューティングの手順のいくつかは、ホストしている HTML ページの一部として返されるスニペット &lt;script /&gt; タグとその構成をアプリケーションで直接制御できることを前提としています。 そうでない場合、特定されている手順はシナリオに適用されません。

## <a name="intermittent-network-connectivity-failure"></a>間欠的なネットワーク接続エラー

**間欠的なネットワーク接続エラーをユーザーが経験している場合、考えられる解決策は限られており、通常はしばらくすると自然に解決します。** たとえば、ユーザーがサイトを再度読み込むと (ページを最新の情報に更新)、ファイルは (最終的には) ダウンロードされて、更新されたバージョンがリリースされるまでの間、ローカルにキャッシュされます。

> [!NOTE]
> この例外が解決せず、ユーザーの多くにこの例外が発生しており (この例外が急速かつ持続的に報告されていることにより診断)、通常のクライアント テレメトリが減少している場合、間欠的なネットワーク接続エラーは、この問題の真の原因 _ではない可能性が高い_ ため、考えられる他の既知の問題かどうか診断を続ける必要があります。

この状況では、SDK を独自の CDN でホストしても、その CDN も同じ問題の影響を受けるため、この例外の発生は増えも減りもしません。

NPM パッケージ ソリューション経由で SDK を使用する場合にも同じことが言えます。 ただし、エンド ユーザーの観点から見ると、この問題が発生したときには、アプリケーション全体が読み込みまたは初期化に失敗している (エンド ユーザーには見えていないテレメトリ SDK "_のみ_" ではなく) ため、サイトが完全に読み込まれるまでそれを最新の情報に更新する可能性が非常に高くなります。

[NPM パッケージ](#use-npm-packages-to-embed-the-application-insight-sdk)を使用して、Application Insights SDK を埋め込むことも試すことができます。

間欠的なネットワーク接続エラーを最小限に抑えるため、すべての CDN ファイルに Cache-Control ヘッダーが実装されています。この結果、エンド ユーザーのブラウザーで SDK の現在のバージョンがダウンロードされると、再びダウンロードする必要はなくなり、ブラウザーは以前に取得したコピーを再利用します (「[キャッシュのしくみ](../../cdn/cdn-how-caching-works.md)」を参照)。 キャッシュ チェックが失敗するか新しいリリースが存在する場合は、エンド ユーザーのブラウザーは更新されたバージョンをダウンロードする必要があります。 このため、新しいリリースが作成されて一般提供 (CDN にデプロイ) されると、チェック エラー シナリオでのバックグラウンド レベルの "_ノイズ_" や、一時的なスパイクが確認される場合があります。
 
## <a name="application-insights-cdn-outage"></a>Application Insights CDN の停止

Application Insights CDN が停止しているかどうかの確認は、エンド ユーザーの場所とは異なる場所、たとえば開発用マシンなどから (組織でこのドメインがブロックされていない場合)、ブラウザー (たとえば、 https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js や https://js.monitor.azure.com/scripts/b/ai.2.min.js) ) で直接 CDN エンドポイントにアクセスしてみることで行えます。

停止が確認できたなら、[新しいサポート チケットを作成する](https://azure.microsoft.com/support/create-ticket/)か、SDK をダウンロードするために使用される URL を変更することができます。

### <a name="change-the-cdn-endpoint"></a>CDN エンドポイントを変更する
  
スニペットとその構成は、生成された各ページの一部としてアプリケーションによって返されるため、スニペットの `src` 構成を変更することで、SDK に別の URL を使用することができます。 この方法を使用すると、新しい URL はブロックされないため、CDN がブロックされる問題を回避できます。

現在の Application Insights JavaScript SDK CDN エンドポイント
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/` エンドポイントはエイリアスで、これを使用すると、構成を変更しなくても、CDN プロバイダーをおよそ 5 分以内に切り替えることができます。これにより、CDN プロバイダーで地域的な問題や世界規模の問題が発生している場合、すべてのユーザーが自分の設定を調整しなくても、検出された CDN 関連の問題をより迅速に修正できます。

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>スクリプト読み込み後の SDK の初期化エラー

SDK が初期化に失敗する場合、&lt;script /&gt; は CDN から正常にダウンロードされていますが、初期化中にエラーが発生しています。 これは、存在しない依存関係や無効な依存関係、何らかの形の JavaScript 例外が原因である可能性があります。

最初に、SDK が正常にダウンロードされたかどうかを確認します。スクリプトがダウンロードされていない場合、このシナリオは SDK 読み込みエラーの例外の原因では __ありません__。

クイック チェック:開発者ツール (F12) をサポートするブラウザーを使用して、```src``` スニペット構成で定義されているスクリプトがダウンロードされており、応答コードが 200 (成功) または 304 (変更なし) になっていることを [ネットワーク] タブで検証します。 fiddler などのツールを使用して、ネットワーク トラフィックを確認することもできます。

以下のセクションで、さまざまなレポート オプションについて説明します。サポート チケットを作成するか、GitHub で問題を報告することをお勧めします。

 レポートの基本的なルール:

- 問題が少数のユーザーや特定のブラウザー バージョンまたはそのサブセットにのみ影響している場合 (報告された例外の詳細を確認)、エンド ユーザーや環境に限定された問題であると考えられます。その場合は、アプリケーションで `polyfill` を追加実装することが必要になる可能性があります。 この場合は、[GitHub で問題を提出](https://github.com/Microsoft/ApplicationInsights-JS/issues)してください。
- この問題がアプリケーション全体に影響を与えており、すべてのユーザーが影響を受けている場合は、リリース関連の問題であると考えられます。その場合は、[新しいサポート チケットを作成する](https://azure.microsoft.com/support/create-ticket/)必要があります。

### <a name="javascript-exceptions"></a>JavaScript の例外

最初に、開発者ツール (F12) をサポートするブラウザーを使用して、JavaScript 例外のチェックでページを読み込み、何らかの例外が発生していないか確認します。

SDK スクリプト (ai.2.min.js など) で例外が報告されている場合、SDK に渡された構成に、予期しない、または必要なのに不足している構成が含まれている可能性があります。また、問題のあるリリースが CDN にデプロイされていることも考えられます。

構成が間違っていないかどうかを確認するには、スニペットに渡される構成を変更し (まだ行っていない場合)、その構成に文字列値としてインストルメンテーション キーのみが含まれるようにします。

> src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",<br />
> cfg:{<br />
> instrumentationKey:"INSTRUMENTATION_KEY"<br />
> }});<br />

この最小構成でも SDK スクリプトで JavaScript の例外が引き続き発生する場合は、新しくデプロイされたバージョンの問題である可能性があり、障害が発生したビルドをロールバックすることが必要になるため、[新しいサポート チケットを作成](https://azure.microsoft.com/support/create-ticket/)してください。

例外が発生しなくなった場合、問題の原因は型の不一致または予期しない値だったと考えられます。 構成オプションを 1 つずつ元に戻して、例外が再び発生するまでテストを繰り返します。 その後、問題の原因となっていた項目のドキュメントを確認します。 ドキュメントがわかりづらい場合やサポートが必要な場合は、[GitHub で問題を提出](https://github.com/Microsoft/ApplicationInsights-JS/issues)してください。

構成が以前にデプロイされたもので、正常に動作していたのに突然この例外が報告されるようになった場合は、新しくデプロイされたバージョンの問題であると考えられます。ユーザーやブラウザーのごく一部にのみ影響を与えているかを確認し、[GitHub で問題を提出](https://github.com/Microsoft/ApplicationInsights-JS/issues)するか、[新しいサポート チケットを作成](https://azure.microsoft.com/support/create-ticket/)してください。

### <a name="enable-console-debugging"></a>コンソールのデバッグを有効にする

例外がスローされていない場合は、次の手順として、`loggingLevelConsole` 設定を構成に追加して、コンソールのデバッグを有効にします。このようにすると、すべての初期化エラーと警告がブラウザー コンソールに送信されます (通常は開発者ツール (F12) で利用可能)。 報告されるエラーの意味はそのメッセージが示すとおりですが、さらにサポートが必要な場合は、[GitHub で問題を提出](https://github.com/Microsoft/ApplicationInsights-JS/issues)してください。

> cfg:{<br />
> instrumentationKey:"INSTRUMENTATION_KEY",<br />
> loggingLevelConsole:2<br />
> }});<br />

> [!NOTE]
> 初期化中に、SDK は既知の主要な依存関係に対していくつかの基本的なチェックを実行します。 これらが現在のランタイムによって提供されていない場合、そのエラーは警告メッセージとしてコンソールに報告されます。ただし、`loggingLevelConsole` が 0 より大きい場合に限ります。

それでも初期化に失敗する場合は、```enableDebug``` 構成設定を有効にしてみます。 このようにすると、内部エラーはすべて例外としてスローされます (この結果、テレメトリは失われます)。 これは開発者専用の設定であるため、内部チェックの一部として頻繁に例外がスローされてノイズが多くなると考えられます。どの問題が原因で SDK が失敗しているかを特定するには、それらの例外をそれぞれ確認する必要があります。 縮小バージョンではないスクリプトを使用します (下のサンプルの拡張子が ".js" になっており、".min.js" ではないことにご注意ください)。そうしないと、例外が解読できなくなります。

> [!WARNING]
> これは開発者専用の設定であり、テレメトリが失われるため、完全な運用環境では絶対に有効にしないでください。

> src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",<br />
> cfg:{<br />
> instrumentationKey:"INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

このようにしてもまだ分析情報が得られない場合は、詳細情報と、サンプル サイト (ある場合) を添えて、[GitHub で問題を提出](https://github.com/Microsoft/ApplicationInsights-JS/issues)してください。 問題を特定するため、ブラウザー バージョン、オペレーティング システム、JS フレームワークに関する詳細を含めてください。

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Application Insights JavaScript CDN がブロックされている

Application Insights JavaScript SDK CDN エンドポイントが安全ではないと報告されているか、そのように識別された場合、CDN がブロックされる可能性があります。 これが発生すると、そのエンドポイントは公式にブロック リストに追加され、これらのリストのコンシューマーはすべてのアクセスをブロックするようになります。

この問題を解決するため、その CDN エンドポイントの所有者は、そのエンドポイントに安全ではないというマークを付けたブロックリスト登録者と協力して、関連するリストからそれを削除する必要があります。

CDN エンドポイントが安全ではないと識別されているかどうかは、次で確認できます。
- [Google 透明性レポート](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

アプリケーション、ファイアウォール、環境でリストのローカル コピーが更新される頻度によっては、問題を解決するのにかなりの時間を要する場合があり、エンド ユーザーや企業の IT 部門が手動で介入して更新を強制したり、その CDN エンドポイントを明示的に許可したりすることが必要になる場合があります。

CDN エンドポイントが安全ではないと識別されている場合、問題ができるだけ早く解決されるようにするため、[サポート チケットを作成](https://azure.microsoft.com/support/create-ticket/)してください。

[SDK CDN エンドポイントを変更する](#change-the-cdn-endpoint)ことにより、この問題をより速やかに回避できる *可能性* があります。

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN がブロックされている (エンド ユーザーによる - ブラウザー、インストールされているブロック、パーソナル ファイアウォールによるブロック)

エンド ユーザーが次に該当しないかどうかを確認します。

- ブラウザー プラグインをインストールした (通常は、何らかの広告、マルウェア、またはポップアップ ブロック)。
- ブラウザーやプロキシで Application Insights CDN エンドポイントをブロックしている (または許可していない)。
- SDK の CDN ドメインをブロックする (または DNS エントリが解決されない) 原因となるファイアウォール規則を構成している。

これらのオプションのいずれかが構成されている場合、ユーザーと協力して (またはドキュメントを提供して)、CDN エンドポイントを許可する必要があります。

ユーザーがインストールしたプラグインで、パブリック ブロックリストが使用されている可能性があります。 そうでない場合は、手動で構成された他の何らかのソリューションが原因であるか、プライベート ドメイン ブロックリストを使用している可能性があります。

#### <a name="add-exceptions-for-cdn-endpoints"></a>CDN エンドポイントの例外を追加する

エンド ユーザーと協働するかドキュメントを準備して、Application Insights CDN エンドポイントからスクリプトをダウンロードできるようにするため、それをブラウザーのプラグインやファイアウォール規則の例外一覧 (エンド ユーザーの環境によって異なります) に含める必要があることを知らせます。

[Web サイトへのアクセスを許可またはブロックするように Chrome](https://support.google.com/chrome/a/answer/7532419?hl=en) を構成する方法については、このリンクを参照してください。

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN がブロックされている (企業のファイアウォールにより)

エンド ユーザーが企業のネットワークを使用している場合、ファイアウォール ソリューションが原因となっており、IT 部門が何らかの形式のインターネット フィルタリング システムを実装している可能性が高いと考えられます。 この場合、エンド ユーザーが必要とする規則が許可されるように、IT 部門と協力することが必要になります。

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>企業に関して CDN エンドポイントの例外を追加する

  [エンド ユーザーに関して例外を追加する](#add-exceptions-for-cdn-endpoints)場合と似ていますが、Application Insights CDN エンドポイントをドメイン ブロックリスト サービスや許可リスト サービスに含める (または削除する) ことにより、それらのエンドポイントをダウンロードできるよう、企業の IT 部門と協力して、構成を依頼することが必要になります。

  > [!WARNING]
  > 企業ユーザーが[プライベート クラウド](https://azure.microsoft.com/overview/what-is-a-private-cloud/)を使用しており、内部ユーザーに CDN エンドポイントへのパブリック アクセスを提供するための例外を作成することができない場合、[Application Insights NPM パッケージ](https://www.npmjs.com/package/applicationinsights)を使用するか、独自の CDN で Application Insights SDK をホストすることが必要になります。  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>ブロックされた CDN に関するその他のトラブルシューティング

> [!NOTE]
> ユーザーが[プライベート クラウド](https://azure.microsoft.com/overview/what-is-a-private-cloud/)を使用しており、パブリック インターネットにアクセスできない場合、独自の CDN で SDK をホストするか、NPM パッケージを使用する必要があります。

#### <a name="host-the-sdk-on-your-own-cdn"></a>独自の CDN で SDK をホストする

 エンド ユーザーがパブリック CDN から Application Insights SDK をダウンロードする方法の代わりに、独自の CDN エンドポイントから Application Insights SDK をホストすることができます。 使用しているバージョンを簡単に識別できるように、特定のバージョン (ai.2.#.#.min.js) を使用することをお勧めします。 また、バグ修正や使用可能になった新機能を利用できるように、最新バージョン (ai.2.min.js) に定期的に更新してください。

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>NPM パッケージを使用して Application Insights SDK を埋め込む

スニペットやパブリック CDN エンドポイントを使用する代わりに、[NPM パッケージ](https://www.npmjs.com/package/applicationinsights)を使用して、SDK を独自の JavaScript ファイルの一部として含めることができます。 この SDK は、独自のスクリプト内の別のパッケージになります。

> [!NOTE]
> NPM パッケージを使用する場合、コードを分割し、縮小化する助けとして、何らかの [JavaScript バンドラー](https://www.bing.com/search?q=javascript+bundler)を使用することをお勧めします。

スニペットの場合と同様、独自のスクリプト (SDK NPM パッケージを使用するかどうかに関係なく) も、こちらで説明したのと同じ障害となっている問題の影響を受ける可能性があります。このため、アプリケーション、ユーザー、ご使用のフレームワークに応じて、スニペットのロジックに似たものを実装して、これらの問題を検出および報告できるようにすることをご検討ください。


## <a name="next-steps"></a>次のステップ 
- [さらにサポートを得るため、GitHub で問題を提出する](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Web ページの使用状況の監視](javascript.md)
