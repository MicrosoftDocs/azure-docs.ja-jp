---
title: "Azure Web Apps でのノード アプリケーションのベスト プラクティスとトラブルシューティング ガイド"
description: "Azure Web Apps でのノード アプリケーションのベスト プラクティスとトラブルシューティング手順について説明します。"
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 06/06/2016
ms.author: ranjithr;wadeh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 802a6126302a30aefae5841f42d8b9d782065638


---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Azure Web Apps でのノード アプリケーションのベスト プラクティスとトラブルシューティング ガイド
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

この記事では、Azure Web Apps で実行されている[ノード アプリケーション](app-service-web-nodejs-get-started.md) ([iisnode](https://github.com/azure/iisnode) を使用) のベスト プラクティスとトラブルシューティングの手順について説明します。

> [!WARNING]
> 運用サイトでトラブルシューティング手順を使用する場合は、注意が必要です。 ステージング スロットなどの運用環境以外のセットアップでアプリのトラブルシューティングを行い、問題が修正されたら、ステージング スロットを運用スロットと交換することをお勧めします。
> 
> 

## <a name="iisnode-configuration"></a>IISNODE の構成
この [スキーマ ファイル](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) は、iisnode 用に構成できるすべての設定を示しています。 アプリケーションに役立つ設定には、次のようなものがあります。

* nodeProcessCountPerApplication
  
    この設定は、IIS アプリケーションごとに起動されるノード プロセスの数を制御します。 既定値は 1 です。 これを 0 に設定すると、VM コアと同じ数の node.exe を起動できます。 ほとんどのアプリケーションでは、推奨値は 0 です。これにより、コンピューター上のすべてのコアを活用できます。 node.exe はシングル スレッドであるため、1 つの node.exe で使用されるコアは最大 1 つです。ノード アプリケーションから最大限のパフォーマンスを得るには、すべてのコアを使用します。
* nodeProcessCommandLine
  
    この設定は、node.exe のパスを制御します。 この値を設定すると、node.exe のバージョンを指定することができます。
* maxConcurrentRequestsPerProcess
  
    この設定は、iisnode が各 node.exe に送信する同時要求の最大数を制御します。 Azure Web Apps では、この設定の既定値は無制限です。 この設定について配慮する必要はありません。 Azure Web Apps 以外では、既定値は 1024 になります。 アプリケーションが受け取る要求の数と、アプリケーションが各要求を処理する速度に応じて、この設定を構成できます。
* maxNamedPipeConnectionRetry
  
    この設定は、iisnode が node.exe に要求を送信する際に経由する名前付きパイプで接続の作成を再試行する最大回数を制御します。 この設定と namedPipeConnectionRetryDelay を併用することで、iisnode 内の各要求の合計タイムアウトを決定します。 Azure Web Apps では、既定値は 200 です。 合計タイムアウト (秒) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    この設定は、iisnode が名前付きパイプ経由で node.exe に要求を送信する場合に、各再試行間で待機する時間 (ミリ秒) を制御します。 既定値は 250 ミリ秒です。
    合計タイムアウト (秒) = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    既定では、Azure Web Apps での iisnode の合計タイムアウトは、200 \* 250 ミリ秒 = 50 秒です。
* logDirectory
  
    この設定は、iisnode が stdout と stderr をログするディレクトリを制御します。 既定値は、メイン スクリプト ディレクトリ (メインの server.js が存在するディレクトリ) に対して相対的な iisnode です。
* debuggerExtensionDll
  
    この設定は、ノード アプリケーションのデバッグ時に iisnode が node-inspector のどのバージョンを使用するかを制御します。 現在、この設定に有効な値は、iisnode-inspector-0.7.3.dll と iisnode-inspector.dll の 2 つだけです。 既定値は、iisnode-inspector-0.7.3.dll です。 iisnode-inspector-0.7.3.dll バージョンは、node-inspector-0.7.3 と WebSocket を使用するため、このバージョンを使用するには Azure Web App で WebSocket を有効にする必要があります。 新しい node-inspector を使用するよう iisnode を構成する方法の詳細については、<http://www.ranjithr.com/?p=98> を参照してください。
* flushResponse
  
    IIS の既定の動作として、応答データは、フラッシュするまでか、応答が終了するまでのどちらか早い方のタイミングで、最大 4 MB がバッファーに格納されます。 iisnode には、この動作より優先される構成設定があります。iisnode が node.exe から応答エンティティを受け取ったらすぐに応答エンティティ本体のフラグメントをフラッシュするには、web.config で iisnode/@flushResponse 属性を true に設定する必要があります。
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    応答エンティティ本体のすべてのフラグメントのフラッシュを有効にすると、パフォーマンスのオーバーヘッドが増加し、システムのスループットが最大で 5% (v0.1.13 の場合) 低下します。そのため、この設定は、(たとえば、web.config で <location> 要素を使用して) 応答ストリーミングを必要とするエンドポイントだけに限定することをお勧めします。
  
    さらに、ストリーミング アプリケーションの場合は、iisnode ハンドラーの responseBufferLimit も 0 に設定する必要があります。
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    これは、変更を監視する対象となるファイルの一覧です (セミコロン区切り)。 ファイルを変更すると、アプリケーションのリサイクルが発生します。 各エントリは、省略可能なディレクトリ名と必須のファイル名で構成されます。これらの名前は、アプリケーションのメイン エントリ ポイントがあるディレクトリからの相対位置になります。 ワイルドカードは、ファイル名部分のみに使用できます。 既定値は "\*.js;web.config" です。
* recycleSignalEnabled
  
    既定値は false です。 有効にした場合、ノード アプリケーションは名前付きパイプ (環境変数 IISNODE\_CONTROL\_PIPE) に接続し、"リサイクル" メッセージを送信できます。 これにより、w3wp は適切にリサイクルされます。
* idlePageOutTimePeriod
  
    既定値は 0 で、この機能が無効であることを意味します。 0 より大きい値を設定すると、iisnode は 'idlePageOutTimePeriod' ミリ秒ごとにすべての子プロセスをページアウトします。 ページアウトの意味を理解するには、こちらの [ドキュメント](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx)を参照してください。 大量のメモリを消費するため、たまにメモリをディスクにページアウトして RAM の一部を解放する必要があるアプリケーションには、この設定が役立ちます。

> [!WARNING]
> 運用アプリケーションで以下の構成設定を有効にする場合は、注意が必要です。 稼働中の運用アプリケーションでは、これらの設定を有効にしないことをお勧めします。
> 
> 

* debugHeaderEnabled
  
    既定値は false です。 true に設定すると、iisnode は送信する各 HTTP 応答に HTTP 応答ヘッダー iisnode-debug を追加します。iisnode-debug ヘッダーの値は URL です。 診断情報の各部分は URL フラグメントを見て収集できますが、ブラウザーで URL を開くと視覚的にわかりやすく表示できます。
* loggingEnabled
  
    この設定は、iisnode による stdout と stderr のログを制御します。 iisnode は、起動したノード プロセスから stdout と stderr をキャプチャし、'logDirectory' 設定で指定されたディレクトリに書き込みます。 これが有効になると、アプリケーションはファイル システムにログを書き込みます。アプリケーションによるログ記録の量によっては、パフォーマンスに影響が出る場合があります。
* devErrorsEnabled
  
    既定値は false です。 true に設定すると、iisnode はブラウザーに HTTP 状態コードと Win32 エラー コードを表示します。 win32 コードは、特定の種類の問題をデバッグするときに有用です。
* debuggingEnabled (実稼働の運用サイトでは有効にしないでください)
  
    この設定は、デバッグ機能を制御します。 iisnode は、node-inspector と統合されています。 この設定を有効にすることで、ノード アプリケーションのデバッグが有効になります。 この設定が有効になると、ノード アプリケーションへの最初のデバッグ要求の際に、iisnode によって、必要な node-inspector ファイルが 'debuggerVirtualDir' ディレクトリに配置されます。 http://yoursite/server.js/debug に要求を送信することによって、node-inspector を読み込むことができます。 デバッグ URL セグメントは、"debuggerPathSegment" 設定で制御できます。 既定では、debuggerPathSegment = 'debug' です。 他のユーザーによる検出が難しくなるように、たとえば GUID に設定することができます。
  
    デバッグの詳細については、こちらの [リンク](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) を参照してください。

## <a name="scenarios-and-recommendationstroubleshooting"></a>シナリオと推奨事項/トラブルシューティング
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>ノード アプリケーションによる発信呼び出しが多すぎる
多くのアプリケーションは、通常の操作の一環として、発信接続を行う必要があります。 たとえば、要求を受信すると、ノード アプリケーションは別の場所の REST API に問い合わせて、要求を処理するための情報を取得しようとします。 http または https 呼び出しを行うときに、キープ アライブ エージェントを使用する場合があります。 たとえば、これらの発信呼び出しを行うときに、キープ アライブ エージェントとして agentkeepalive モジュールを使用できます。 これにより、Azure Web アプリ VM でソケットが再利用されるため、発信要求ごとに新しいソケットを作成するオーバーヘッドが縮小されます。 また、多くの発信要求を行う際に使用するソケット数が少なくなるため、VM ごとに割り当てられる maxSockets を超えることはなくなります。 Azure Web Apps では、agentKeepAlive maxSockets 値を VM あたり合計 160 ソケットに設定することが推奨されます。 つまり、VM で 4 つの node.exe を実行している場合は、node.exe ごとに agentKeepAlive maxSockets を 40 (VM あたり合計 160) に設定することになります。

agentKeepALive 構成の例:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

この例では、VM で 4 つの node.exe を実行していることを想定しています。 VM で実行している node.exe の数が異なる場合は、それに応じて maxSockets の設定を変更することが必要になります。

### <a name="my-node-application-is-consuming-too-much-cpu"></a>ノード アプリケーションが消費する CPU が多すぎる
ポータルでは、高い CPU 消費率に関して Azure Web Apps からの推奨事項が表示されます。 特定の [メトリック](web-sites-monitor.md)を監視するためにモニターをセットアップすることもできます。 [Azure ポータル ダッシュボード](../application-insights/app-insights-web-monitor-performance.md)で CPU 使用率をチェックするときに、CPU の最大値を確認して、ピーク値を見逃さないようにしてください。
アプリケーションによる CPU の消費量が多すぎると思っても、理由を説明できない場合は、ノード アプリケーションをプロファイリングする必要があります。

### 
#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>V8-Profiler を使用した Azure Web Apps のノード アプリケーションのプロファイリング
たとえば、以下のような hello world アプリをプロファイリングするとします。

```
var http = require('http');    
function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
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

scm サイト https://yoursite.scm.azurewebsites.net/DebugConsole に移動します。

次に示すように、コマンド プロンプトが表示されます。 site/wwwroot ディレクトリに移動します。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

コマンド "npm install v8-profiler" を実行します。

これにより、node\_modules ディレクトリとその依存関係すべてに v8-profiler がインストールされます。
ここで、アプリケーションをプロファイリングするように server.js を編集します。

```
var http = require('http');    
var profiler = require('v8-profiler');    
var fs = require('fs');

function WriteConsoleLog() {    
    for(var i=0;i<99999;++i) {    
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

上記の変更によって、WriteConsoleLog 関数はプロファイリングされ、プロファイル出力が site wwwroot の下にある 'profile.cpuprofile' ファイルに書き込まれます。 アプリケーションに要求を送信します。 'profile.cpuprofile' ファイルが site wwwroot の下に作成されたことがわかります。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

このファイルをダウンロードします。このファイルは、Chrome の F12 ツールを使用して開く必要があります。 Chrome で F12 キーを押し、[Profiles] タブをクリックします。 [Load] ボタンをクリックします。 ダウンロードした profile.cpuprofile ファイルを選択します。 読み込んだプロファイルをクリックします。

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

次に示すように、95% の時間が WriteConsoleLog 関数によって消費されています。 問題の原因となる正確な行番号とソース ファイルも表示されます。

### <a name="my-node-application-is-consuming-too-much-memory"></a>ノード アプリケーションで消費されるメモリが多すぎる
ポータルでは、高いメモリ消費量に関して Azure Web Apps からの推奨事項が表示されます。 特定の [メトリック](web-sites-monitor.md)を監視するためにモニターをセットアップすることもできます。 [Azure ポータル ダッシュボード](../application-insights/app-insights-web-monitor-performance.md)でメモリ使用量をチェックするときに、メモリの最大値を確認して、ピーク値を見逃さないようにしてください。

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>node.js のリーク検出とヒープ比較
[node-memwatch](https://github.com/lloyd/node-memwatch) を使うと、メモリ リークの特定に役立ちます。
v8-profiler と同じように memwatch をインストールし、ヒープのキャプチャと比較を行い、アプリケーションのメモリ リークを特定するようにコードを編集することができます。

### <a name="my-nodeexes-are-getting-killed-randomly"></a>node.exe がランダムに強制終了される
この問題が発生する理由はいくつかあります。

1. アプリケーションが、キャッチされない例外をスローしています。d:\\home\\LogFiles\\Application\\logging-errors.txt ファイルで、スローされた例外の詳細を確認してください。 このファイルにはスタック トレースがあるため、これに基づいてアプリケーションを修正できます。
2. アプリケーションが使用するメモリが多すぎて、他のプロセスの開始に影響が出ています。 VM メモリの合計が 100% に近い場合は、他のプロセスにも作業を行う機会を与えるために、プロセス マネージャーによって node.exe が強制終了されることがあります。 この問題を解決するには、アプリケーションがメモリをリークしていないことを確認してください。また、実際にはアプリケーションが大量のメモリの使用を必要としている場合は、より多くの RAM を備えたより大きな VM にスケールアップしてください。

### <a name="my-node-application-does-not-start"></a>ノード アプリケーションが起動しない
アプリケーションが起動時に 500 エラーを返す場合は、次のような原因が考えられます。

1. node.exe が適切な場所にありません。 nodeProcessCommandLine 設定を確認してください。
2. メイン スクリプト ファイルが適切な場所にありません。 web.config をチェックし、handlers セクションのメイン スクリプト ファイル名がメイン スクリプト ファイルと一致していることを確認してください。
3. Web.config の構成が正しくありません。設定の名前/値を確認してください。
4. コールド スタート。アプリケーションの起動に時間がかかりすぎます。 アプリケーションでかかる時間が (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1,000 秒を超えると、iisnode は 500 エラーを返します。 iisnode がタイムアウトして 500 エラーを返すことを防ぐために、アプリケーションの開始時間と一致するようにこれらの設定の値を大きくしてください。

### <a name="my-node-application-crashed"></a>ノード アプリケーションがクラッシュした
アプリケーションが、キャッチされない例外をスローしています。d:\\home\\LogFiles\\Application\\logging-errors.txt ファイルで、スローされた例外の詳細を確認してください。 このファイルにはスタック トレースがあるため、これに基づいてアプリケーションを修正できます。

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>ノード アプリケーションの起動 (コールド スタート) に時間がかかりすぎる
この問題の最も一般的な理由は、アプリケーションが node\_modules に多数のファイルを持っていて、起動時にこれらのファイルの大半を読み込もうとすることです。 既定では、ファイルが Azure Web Apps 上のネットワーク共有に存在するため、多くのファイルの読み込みには時間がかかります。
この処理を高速化するための解決策は、次のようにいくつかあります。

1. npm3 を使用してモジュールをインストールすることによって、依存関係構造がフラットであり、重複する依存関係がないことを確認してください。
2. node\_modules の遅延読み込みを試し、起動時にすべてのモジュールを読み込まないようにします。 つまり、require('module') の呼び出しは、そのモジュールを使用する関数内で、実際に必要になったときに行われます。
3. Azure Web Apps には、ローカル キャッシュと呼ばれる機能が用意されています。 この機能は、コンテンツをネットワーク共有から VM 上のローカル ディスクにコピーします。 ファイルはローカルにあるため、node\_modules の読み込み時間は短縮されます。 ローカル キャッシュの使用方法の詳細については、こちらの [ドキュメント](../app-service/app-service-local-cache.md) で説明しています。

## <a name="iisnode-http-status-and-substatus"></a>IISNODE の HTTP の状態と副状態
この [ソース ファイル](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) には、エラーが発生した際に iisnode が返す可能性のある、状態と副状態の考えられるすべての組み合わせが示されています。

アプリケーションで win32 エラー コードを確認するには、FREB を有効にしてください (パフォーマンス上の理由により、非運用サイトのみで FREB を有効にしてください)。

| HTTP の状態 | HTTP の副状態 | 考えられる理由 |
| --- | --- | --- |
| 500 |1,000 |IISNODE に要求をディスパッチしているときに問題が発生しました。node.exe が起動されたかどうかを確認してください。 Node.exe が起動時にクラッシュした可能性があります。 web.config 構成でエラーを確認してください。 |
| 500 |1001 |- Win32Error 0x2 - アプリが URL に応答していません。 URL 書き換えルールか、express アプリで正しいルートが定義されているかどうかを確認してください。 - Win32Error 0x6d – 名前付きパイプがビジー状態です。– パイプがビジーであるため、node.exe が要求を受け付けません。 高 CPU 使用率を確認してください。 - その他のエラー – node.exe がクラッシュしたかどうかを確認してください。 |
| 500 |1002 |Node.exe がクラッシュしました。– d:\\home\\LogFiles\\logging-errors.txt でスタック トレースを確認してください。 |
| 500 |1003 |パイプの構成の問題。これが表示されることはないはずですが、表示される場合は、名前付きパイプの構成が正しくありません。 |
| 500 |1004 ～ 1018 |要求の送信中、または node.exe との間の応答の処理中に、エラーが発生しました。 node.exe がクラッシュしたかどうかを確認してください。 d:\\home\\LogFiles\\logging-errors.txt でスタック トレースを確認してください。 |
| 503 |1,000 |メモリ不足のため、名前付きパイプ接続をこれ以上割り当てられません。 アプリで大量のメモリが使用されている理由を確認してください。 MaxConcurrentRequestsPerProcess 設定値を確認してください。 この値が無制限になっておらず、要求が多数ある場合は、エラーを防ぐために、この値を大きくします。 |
| 503 |1001 |アプリケーションがリサイクルしているため、node.exe に要求をディスパッチできませんでした。 アプリケーションのリサイクル後、要求は通常どおりに処理されます。 |
| 503 |1002 |実際の理由については、win32 エラー コードを確認してください。要求を node.exe にディスパッチできませんでした。 |
| 503 |1003 |名前付きパイプがビジー状態です。ノードが CPU を大量に消費しているかどうかを確認してください。 |

NODE.exe 内には、NODE\_PENDING\_PIPE\_INSTANCES と呼ばれる設定があります。 既定では、Azure Web Apps でない場合、この値は 4 です。 つまり、node.exe が名前付きパイプで同時に受け入れることができる要求は 4 つだけです。 Azure Web Apps では、この値が 5000 に設定されています。Azure Web Apps で実行されるほとんどのノード アプリケーションでは、この値で十分です。 NODE\_PENDING\_PIPE\_INSTANCES には大きい値が設定されていることが原因で、Azure Web Apps に 503.1003 が表示されることはありません。  |

## <a name="more-resources"></a>その他のリソース
Azure App Service での node.js アプリケーションの詳細については、以下のリンクを参照してください。

* [Get started with Node.js web apps in Azure App Service (Azure App Service で Node.js Web アプリの使用を開始する)](app-service-web-nodejs-get-started.md)
* [Azure App Service で Node.js Web アプリをデバッグする方法](web-sites-nodejs-debug.md)
* [Azure アプリケーションでの Node.js モジュールの使用](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js デベロッパー センター](../nodejs-use-node-modules-azure-apps.md)
* [優れた Kudu デバッグ コンソールの詳細](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)




<!--HONumber=Nov16_HO3-->


