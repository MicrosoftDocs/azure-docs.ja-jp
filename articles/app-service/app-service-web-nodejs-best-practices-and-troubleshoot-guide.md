---
title: Node.js のベスト プラクティスとトラブルシューティング
description: Azure App Service で実行されている node.js アプリケーションのベスト プラクティスとトラブルシューティングの手順について説明します。
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: bfbd93cc3d4e67c8a96a1413221fdd7190c4f0b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572631"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Azure App Service Windows での node アプリケーションのベスト プラクティスとトラブルシューティング ガイド

この記事では、Azure App Service で実行されている [Windows Node.js アプリケーション](quickstart-nodejs.md?pivots=platform-windows) ([iisnode](https://github.com/azure/iisnode) を使用) のベスト プラクティスとトラブルシューティングの手順について説明します。

> [!WARNING]
> 運用サイトでトラブルシューティング手順を使用する場合は、注意が必要です。 ステージング スロットなどの運用環境以外のセットアップでアプリのトラブルシューティングを行い、問題が修正されたら、ステージング スロットを運用スロットと交換することをお勧めします。
>

## <a name="iisnode-configuration"></a>IISNODE の構成

この[スキーマ ファイル](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml)は、iisnode 用に構成できるすべての設定を示しています。 アプリケーションに役立つ設定には、次のようなものがあります。

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

この設定は、IIS アプリケーションごとに起動されるノード プロセスの数を制御します。 既定値は 1 です。 値を 0 に変更することで、VM vCPU と同じ数の node.exe を起動できます。 ほとんどのアプリケーションでは、推奨値は 0 です。これにより、コンピューター上のすべての vCPU を使用できます。 node.exe はシングルスレッドなので、1 つの node.exe で最大 1 vCPU を使用します。 node アプリケーションのパフォーマンスを最大にするために、すべての vCPU を使用します。

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

この設定は、node.exe のパスを制御します。 この値を設定すると、node.exe のバージョンを指定することができます。

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

この設定は、iisnode が各 node.exe に送信する同時要求の最大数を制御します。 Azure App Service では、既定値は無制限です。 アプリケーションが受け取る要求の数と、アプリケーションが各要求を処理する速度に応じて、この値を構成できます。

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

この設定は、iisnode が node.exe に要求を送信する際に経由する名前付きパイプで接続の作成を再試行する最大回数を制御します。 この設定と namedPipeConnectionRetryDelay を併用することで、iisnode 内の各要求の合計タイムアウトを決定します。 Azure App Service では、既定値は 200 です。 合計タイムアウト (秒) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

この設定は、iisnode が名前付きパイプ経由で node.exe に要求を送信する場合に、各再試行間で待機する時間 (ミリ秒) を制御します。 既定値は 250 ミリ秒です。
合計タイムアウト (秒) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

既定では、Azure App Service での iisnode の合計タイムアウトは、200 \* 250 ミリ秒 = 50 秒です。

### <a name="logdirectory"></a>logDirectory

この設定は、iisnode が stdout と stderr をログするディレクトリを制御します。 既定値は、メイン スクリプト ディレクトリ (メインの server.js が存在するディレクトリ) に対して相対的な iisnode です。

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

この設定は、node アプリケーションのデバッグ時に iisnode が node-inspector のどのバージョンを使用するかを制御します。 現在、この設定に有効な値は、iisnode-inspector-0.7.3.dll と iisnode-inspector.dll の 2 つだけです。 既定値は、iisnode-inspector-0.7.3.dll です。 iisnode-inspector-0.7.3.dll バージョンでは node-inspector-0.7.3 と Web ソケットを使用します。 このバージョンを使用するには、Azure WebApp で Web ソケットを有効にする必要があります。 新しい node-inspector を使用するよう iisnode を構成する方法の詳細については、<https://ranjithblogs.azurewebsites.net/?p=98> を参照してください。

### <a name="flushresponse"></a>flushResponse

IIS の既定の動作として、応答データは、フラッシュするまでか、応答が終了するまでのどちらか早い方のタイミングで、最大 4 MB がバッファーに格納されます。 iisnode には、この動作をオーバーライドする構成設定があります。iisnode が node.exe から応答エンティティを受け取ったらすぐに応答エンティティ本体のフラグメントをフラッシュするには、web.config で iisnode/@flushResponse 属性を true に設定する必要があります。

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

応答エンティティ本体のすべてのフラグメントを有効にすると、パフォーマンスのオーバーヘッドが追加され、システムのスループットが 5% 減少します (v0.1.13 時点)。 最善は、この設定のスコープを応答のストリーミングが必要なエンドポイントのみにすることです (たとえば web.config で`<location>` 要素を使用します)。

さらに、ストリーミング アプリケーションの場合は、iisnode ハンドラーの responseBufferLimit も 0 に設定する必要があります。

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

変更を監視する対象となるファイルの一覧です (セミコロン区切り)。 ファイルを変更すると、アプリケーションのリサイクルが発生します。 各エントリは、省略可能なディレクトリ名と必須のファイル名で構成されます。これらの名前は、アプリケーションのメイン エントリ ポイントがあるディレクトリからの相対位置になります。 ワイルドカードは、ファイル名部分のみに使用できます。 既定値は `*.js;iisnode.yml` です。

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

既定値は false です。 有効にした場合、node アプリケーションは名前付きパイプ (環境変数 IISNODE\_CONTROL\_PIPE) に接続し、"リサイクル" メッセージを送信できます。 これにより、w3wp は適切にリサイクルされます。

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

既定値は 0 で、この機能が無効であることを意味します。 0 より大きい値を設定すると、iisnode は 'idlePageOutTimePeriod' ミリ秒ごとにすべての子プロセスをページアウトします。 ページアウトの意味を理解するには、こちらの [ドキュメント](/windows/desktop/api/psapi/nf-psapi-emptyworkingset)を参照してください。 大量のメモリを消費するため、たまにメモリをディスクにページアウトして RAM の一部を解放する必要があるアプリケーションには、この設定が役立ちます。

> [!WARNING]
> 運用アプリケーションで以下の構成設定を有効にする場合は、注意が必要です。 稼働中の運用アプリケーションでは、これらの設定を有効にしないことをお勧めします。
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

既定値は false です。 true に設定すると、iisnode は送信するすべての HTTP 応答に HTTP 応答ヘッダー `iisnode-debug` を追加します。`iisnode-debug` ヘッダーの値は URL です。 診断情報の各部分は URL フラグメントを調べることで取得できますが、ブラウザーで URL を開いて視覚化できます。

### <a name="loggingenabled"></a>loggingEnabled

この設定は、iisnode による stdout と stderr のログを制御します。 iisnode は、起動したノード プロセスから stdout と stderr をキャプチャし、'logDirectory' 設定で指定されたディレクトリに書き込みます。 これが有効になると、アプリケーションはファイル システムにログを書き込みます。アプリケーションによるログ記録の量によっては、パフォーマンスに影響が出る場合があります。

### <a name="deverrorsenabled"></a>devErrorsEnabled

既定値は false です。 true に設定すると、iisnode はブラウザーに HTTP 状態コードと Win32 エラー コードを表示します。 win32 コードは、特定の種類の問題をデバッグするときに有用です。

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (実稼働の運用サイトでは有効にしないでください)

この設定は、デバッグ機能を制御します。 iisnode は、node-inspector と統合されています。 この設定を有効にすることで、node アプリケーションのデバッグが有効になります。 この設定が有効になると、node アプリケーションへの最初のデバッグ要求の際に、iisnode によって、node-inspector ファイルが 'debuggerVirtualDir' ディレクトリに作成されます。 `http://yoursite/server.js/debug` に要求を送信することによって、node-inspector を読み込むことができます。 デバッグ URL セグメントは、"debuggerPathSegment" 設定で制御できます。 既定では、debuggerPathSegment = 'debug' です。 他のユーザーによる検出が難しくなるように、たとえば GUID に `debuggerPathSegment` を設定できます。

デバッグの詳細については、[Windows での node.js アプリケーションのデバッグ](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html)に関するページを参照してください。

## <a name="scenarios-and-recommendationstroubleshooting"></a>シナリオと推奨事項/トラブルシューティング

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>node アプリケーションによる発信呼び出しが多すぎる

多くのアプリケーションは、通常の操作の一環として、発信接続を行う必要があります。 たとえば、要求を受信すると、node アプリケーションは別の場所の REST API に問い合わせて、要求を処理するための情報を取得しようとします。 http または https 呼び出しを行うときに、キープ アライブ エージェントを使用する場合があります。 これらの発信呼び出しを行うときに、キープ アライブ エージェントとして agentkeepalive モジュールを使用できます。

agentkeepalive モジュールは、Azure の Web WebApp VM でソケットが再利用されることを保証します。 送信要求のたびに新しいソケットを作成すると、アプリケーションにオーバーヘッドが追加されます。 アプリケーションでソケットを再利用することで、アプリケーションが VM ごとに割り当てられている maxSockets を超えないことが保証されます。 Azure App Service では、agentKeepAlive maxSockets 値を VM あたり合計 128 ソケット (node.exe の 4 つのインスタンス \* インスタンス当たり 32 の maxSockets) に設定することが推奨されています。

[agentKeepALive](https://www.npmjs.com/package/agentkeepalive) 構成の例:

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 32,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> この例では、VM で 4 つの node.exe を実行していることを想定しています。 VM で実行している node.exe の数が異なる場合は、それに応じて maxSockets の設定を変更することが必要になります。
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>node アプリケーションが消費する CPU が多すぎる

高い CPU 消費率に関する Azure App Service からの推奨事項がポータルに表示されている可能性があります。 特定の[メトリック](web-sites-monitor.md)を監視するためにモニターをセットアップすることもできます。 [Azure portal ダッシュボード](../azure-monitor/essentials/metrics-charts.md)で CPU 使用率をチェックするときに、CPU の最大値を確認して、ピーク値を見逃さないようにしてください。
アプリケーションによる CPU の消費量が明らかに多すぎるが、理由を説明できない場合は、node アプリケーションをプロファイリングして調べることができます。

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>V8-Profiler を使用した Azure App Service の node アプリケーションのプロファイリング

たとえば、以下のような hello world アプリをプロファイリングするとします。

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

デバッグ コンソール サイト (`https://yoursite.scm.azurewebsites.net/DebugConsole`) に移動します。

site/wwwroot ディレクトリに移動します。 次の図のようなコマンド プロンプトが表示されます。

![サイト/wwwroot ディレクトリとコマンド プロンプトを示すスクリーンショット。](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

コマンド `npm install v8-profiler` を実行します。

このコマンドは、node\_modules ディレクトリとその依存関係すべてに v8-profiler をインストールします。
ここで、アプリケーションをプロファイリングするように server.js を編集します。

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

上記のコードで、WriteConsoleLog 関数はプロファイリングされ、プロファイル出力が site wwwroot の下にある 'profile.cpuprofile' ファイルに書き込まれます。 アプリケーションに要求を送信します。 'profile.cpuprofile' ファイルが site wwwroot の下に作成されたことがわかります。

![profile.cpuprofile ファイルを示すスクリーンショット。](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

このファイルをダウンロードし、Chrome の F12 ツールで開きます。 Chrome で F12 キーを押し、 **[Profiles]\(プロファイル\)** タブを選択します。 **[Load]\(読み込み\)** ボタンを選択します。 ダウンロードした profile.cpuprofile ファイルを選択します。 読み込んだプロファイルをクリックします。

![読み込んだ profile.cpuprofile ファイルを示すスクリーンショット。](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

95% の時間が WriteConsoleLog 関数によって消費されたことがわかります。 出力には、問題の原因となる正確な行番号とソース ファイルも表示されます。

### <a name="my-node-application-is-consuming-too-much-memory"></a>node アプリケーションで消費されるメモリが多すぎる

アプリケーションの消費メモリ量が多すぎる場合は、Portal にメモリ消費量が多いことに関する Azure App Service の通知が表示されます。 特定の[メトリック](web-sites-monitor.md)を監視するようにモニターを設定できます。 [Azure portal ダッシュボード](../azure-monitor/essentials/metrics-charts.md)でメモリ使用量をチェックするときに、メモリの最大値を確認して、ピーク値を見逃さないようにしてください。

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>node.js のリーク検出とヒープの比較

[node-memwatch](https://github.com/lloyd/node-memwatch) を使うと、メモリ リークの特定に役立ちます。
v8-profiler と同じように `memwatch` をインストールし、ヒープのキャプチャと比較を行い、アプリケーションのメモリ リークを特定するようにコードを編集することができます。

### <a name="my-nodeexes-are-getting-killed-randomly"></a>node.exe がランダムに強制終了される

node.exe がランダムにシャット ダウンされる理由はいくつかあります。

1. アプリケーションが、キャッチされない例外をスローしています。d:\\home\\LogFiles\\Application\\logging-errors.txt ファイルで、スローされた例外の詳細を確認してください。 このファイルには、アプリケーションのデバッグと修正に役立つスタック トレースが含まれています。
2. アプリケーションが過度のメモリを消費しています。このため、他のプロセスの開始に影響を与えています。 VM メモリの合計が 100% に近い場合は、プロセス マネージャーによって node.exe が強制終了される可能性があります。 プロセス マネージャは、他のプロセスに作業を行う機会を与えるために、いくつかのプロセスを強制終了します。 この問題を修正するには、アプリケーションをプロファイルしてメモリ リークを解決します。 アプリケーションが大量のメモリを必要とする場合は、大規模な VM にスケール アップします (VM が利用できる RAM が増えます)。

### <a name="my-node-application-does-not-start"></a>node アプリケーションが起動しない

アプリケーションが起動時に 500 エラーを返す場合は、次のような原因が考えられます。

1. node.exe が適切な場所にありません。 nodeProcessCommandLine 設定を確認してください。
2. メイン スクリプト ファイルが適切な場所にありません。 web.config をチェックし、handlers セクションのメイン スクリプト ファイル名がメイン スクリプト ファイルと一致していることを確認してください。
3. Web.config の構成が正しくありません。設定の名前/値を確認してください。
4. コールド スタート。アプリケーションの起動に時間がかかりすぎます。 アプリケーションでかかる時間が (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1,000 秒を超えると、iisnode は 500 エラーを返します。 iisnode がタイムアウトして 500 エラーを返すことを防ぐために、アプリケーションの開始時間と一致するようにこれらの設定の値を大きくしてください。

### <a name="my-node-application-crashed"></a>node アプリケーションがクラッシュした

アプリケーションが、キャッチされない例外をスローしています。`d:\\home\\LogFiles\\Application\\logging-errors.txt` ファイルで、スローされた例外の詳細を確認してください。 このファイルには、アプリケーションの診断と修正に役立つスタック トレースが含まれています。

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>node アプリケーションの起動 (コールド スタート) に時間がかかりすぎる

アプリケーションの起動時間が長くなる一般的な原因は、node\_modules 内に多数のファイルがあることです。 アプリケーションは起動時にこれらのファイルのほとんどを読み込もうとします。 既定では、ファイルは Azure App Service 上のネットワーク共有に存在するため、多くのファイルの読み込みには時間がかかります。
このプロセスを高速化するための解決策は、次のようにいくつかあります。

1. node\_modules の遅延読み込みを試し、起動時にすべてのモジュールを読み込まないようにします。 遅延読み込みを行うには、モジュール コードを初めて実行する前に、関数内でモジュールが実際に必要になったときに、require('module') を呼び出す必要があります。
2. Azure App Service には、ローカル キャッシュと呼ばれる機能が用意されています。 この機能は、コンテンツをネットワーク共有から VM 上のローカル ディスクにコピーします。 ファイルはローカルにあるため、node\_modules の読み込み時間は短縮されます。

## <a name="iisnode-http-status-and-substatus"></a>IISNODE の HTTP の状態と副状態

`cnodeconstants` [ソース ファイル](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h)には、エラーが発生した際に iisnode が返す可能性のある、状態と副状態の考えられるすべての組み合わせが示されています。

アプリケーションで win32 エラー コードを確認するには、FREB を有効にしてください (パフォーマンス上の理由により、非運用サイトのみで FREB を有効にしてください)。

| HTTP の状態 | HTTP の副状態 | 考えられる理由 |
| --- | --- | --- |
| 500 |1000 |IISNODE に要求をディスパッチしているときに問題が発生しました。node.exe が起動されたかどうかを確認してください。 node.exe が起動時にクラッシュした可能性があります。 web.config 構成でエラーを確認してください。 |
| 500 |1001 |- Win32Error 0x2 - アプリが URL に応答していません。 URL 書き換えルールか、express アプリで正しいルートが定義されているかどうかを確認してください。 - Win32Error 0x6d – 名前付きパイプがビジー状態です。– パイプがビジーであるため、node.exe が要求を受け付けません。 高 CPU 使用率を確認してください。 - その他のエラー – node.exe がクラッシュしたかどうかを確認してください。 |
| 500 |1002 |Node.exe がクラッシュしました。– d:\\home\\LogFiles\\logging-errors.txt でスタック トレースを確認してください。 |
| 500 |1003 |パイプ構成問題 – 名前付きパイプの構成が正しくありません。 |
| 500 |1004 ～ 1018 |要求の送信中、または node.exe との間の応答の処理中に、エラーが発生しました。 node.exe がクラッシュしたかどうかを確認してください。 d:\\home\\LogFiles\\logging-errors.txt でスタック トレースを確認してください。 |
| 503 |1000 |メモリ不足のため、名前付きパイプ接続をこれ以上割り当てられません。 アプリで大量のメモリが使用されている理由を確認してください。 MaxConcurrentRequestsPerProcess 設定値を確認してください。 この値が無制限になっておらず、要求が多数ある場合は、エラーを防ぐために、この値を大きくします。 |
| 503 |1001 |アプリケーションがリサイクルしているため、node.exe に要求をディスパッチできませんでした。 アプリケーションのリサイクル後、要求は通常どおりに処理されます。 |
| 503 |1002 |実際の理由については、win32 エラー コードを確認してください。要求を node.exe にディスパッチできませんでした。 |
| 503 |1003 |名前付きパイプがビジー状態です – ノードが CPU を大量に消費しているかどうかを確認してください。 |

NODE.exe には、`NODE_PENDING_PIPE_INSTANCES` という設定があります。 Azure App Service では、この値は 5000 に設定されています。 つまり、node.exe では名前付きパイプを使用して同時に 5000 個の要求を受け入れることができます。 Azure App Service で実行されるほとんどの node アプリケーションでは、この値で十分です。 `NODE_PENDING_PIPE_INSTANCES` には大きい値が設定されているため、Azure App Service に 503.1003 が表示されることはありません。

## <a name="more-resources"></a>その他のリソース

Azure App Service での node.js アプリケーションの詳細については、以下のリンクを参照してください。

* [Get started with Node.js web apps in Azure App Service (Azure App Service で Node.js Web アプリの使用を開始する)](quickstart-nodejs.md)
* [Azure App Service で Node.js Web アプリをデバッグする方法](/archive/blogs/azureossds/debugging-node-js-apps-on-azure-app-services)
* [Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps:Node.js](/archive/blogs/silverlining/windows-azure-websites-node-js)
* [Node.js デベロッパー センター](../nodejs-use-node-modules-azure-apps.md)
* [優れた Kudu デバッグ コンソールの詳細](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
