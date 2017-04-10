---
title: "Visual Studio を使用した Windows での Docker クライアント エラーのトラブルシューティング | Microsoft Docs"
description: "Visual Studio を使用して、Windows 上で Web アプリを作成し、Docker にデプロイする際に発生する問題のトラブルシューティングを行います。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 649cc1a78f3a9f343533cb18fb7d763e4f9ea196
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.lasthandoff: 02/04/2017


---

# <a name="troubleshoot-visual-studio-docker-development"></a>Visual Studio Docker 開発のトラブルシューティング

Visual Studio Tools for Docker プレビューを使用する際に、プレビューの性質によりいくつかの問題が発生する可能性があります。
一般的な問題と解決策のいくつかを以下に示します。  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux コンテナー**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>.NET Core Web またはコンソール アプリケーションをデバッグする際にビルド エラーが発生する  

これは、プロジェクトが存在するドライブが Docker for Windows と共有されていない場合に発生すると考えられます。  次のようなエラーが表示される場合があります。

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
この問題を解決するには:

1. 通知領域で **[Windows 用の Docker]** を右クリックし、**[設定]** を選択します。  
2. **[Shared Drives (共有ドライブ)]** を選択し、プロジェクトが存在するドライブを共有します。

### <a name="windows-containers"></a>**Windows コンテナー**

以下の問題は、Windows コンテナーで .NET Framework Web やコンソール アプリケーションをデバッグする際にのみ発生します。

#### <a name="prerequisites"></a>前提条件

1. Visual Studio 2017 RC (またはそれ以降) が .NET Core および Docker プレビュー ワークロードと共にインストールされている必要があります。
2. 最新の Windows Update の修正プログラムが適用された Windows 10 Anniversary Update。 具体的には、[KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) がインストールされている必要があります。 
3. [Docker for Windows](https://docs.docker.com/docker-for-windows/) (ビルド 1.13.0 以降) がインストールされている必要があります。
4. **[Switch to Windows containers (Windows コンテナーに切り替える)]** が選択されている必要があります。 通知領域で、**[Docker for Windows]** をクリックし、**[Switch to Windows containers (Windows コンテナーに切り替える)]** を選択します。 コンピューターの再起動後も、この設定が保持されていることを確認します。

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>コンソール アプリケーションのデバッグ中に、コンソール出力が Visual Studio の出力ウィンドウに表示されない

これは、Visual Studio Debugger (msvsmon.exe) の既知の問題であり、今回のシナリオのために設計されたものではありません。 このシナリオのサポートは、今後のリリースに含まれる可能性があります。 Visual Studio でコンソール アプリケーションからの出力を表示するには、**[Docker: Start Project (Docker: プロジェクトの開始)]** を使用します。これは、**[Start without Debugging (デバッグなしで開始)]** と同じ機能です。

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>リリース構成の Web アプリケーションをデバッグすると、「(403) 許可されていません」のエラーで失敗する

この問題を回避するには、ソリューションの web.release.config ファイルを開き、次の行をコメント アウトするか削除します。

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux コンテナー**

#### <a name="unable-to-validate-volume-mapping"></a>ボリューム マッピングを検証できない
ボリューム マッピングは、アプリケーションのソース コードやバイナリをコンテナー内のアプリケーション フォルダーと共有するために欠かせない関連付けです。  個々のボリューム マッピングは、docker-compose.dev.debug.yml と docker-compose.dev.release.yml に格納されます。 ホスト コンピューター上でファイルに変更が加えられると、その変更がコンテナーで同様のフォルダー構造で反映されます。

ボリューム マッピングを有効にするには:

1. 通知領域で **[Moby]** をクリックし、**[設定]** を選択します。
2. **[Shared Drives (共有ドライブ)]**を選択します。
3. プロジェクトをホストするドライブと %USERPROFILE% が存在するドライブを選択します。
4. **[Apply]**をクリックします。

ボリューム マッピングが正常に機能しているかどうかをテストするには、1 つ以上のドライブが共有された後で、Visual Studio 内でリビルドを実行するか F5 キーを押します。または、コマンド プロンプトから次のコードを実行します。

> [!NOTE]
> この例は、Users フォルダー が C ドライブ上にあり、共有されていることを前提としています。
> 別のドライブを共有した場合は、必要に応じて変更してください。

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Linux コンテナーで次のコードを実行します。

```
/ # ls
```

ユーザー/パブリック フォルダーのディレクトリ一覧が表示されます。 /C/ユーザー/パブリック フォルダーが空でないにもかかわらず、ファイルが表示されない場合は、ボリューム マッピングが正しく構成されていません。

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

ワームホール ディレクトリに移動して、`/c/Users/Public` ディレクトリの内容を確認します。

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Linux VM を使用する場合、コンテナーのファイル システムでは大文字と小文字が区別されます。

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>ビルド: "PrepareForBuild" タスクが予期せずに失敗した

Microsoft.DotNet.Docker.CommandLine.ClientException: 接続中にエラーが発生しました。

既定の Docker ホストが実行されていることを確認します。 コマンド プロンプトを開き、次のコマンドを実行します。

```
docker info
```

エラーが返される場合は、**Docker For Windows** デスクトップ アプリを起動してください。 デスクトップ アプリが実行されているときは、通知領域に **[Moby]** アイコンが表示されます。 **[Moby]** を右クリックし、**[設定]** を開きます。 **[リセット]** をクリックし、Docker を再起動します。

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>[追加] > [Docker サポート] を選択すると、またはコンテナー内の ASP.NET Core アプリケーションのデバッグ (F5) を実行すると、エラー ダイアログが表示される

拡張機能をアンインストールしてからインストールした後に、Visual Studio の MEF (Managed Extensibility Framework) キャッシュが破損することがあります。 このキャッシュが破損すると、Docker サポートを追加する際や ASP.NET Core アプリケーションの実行またはデバッグ (F5) の際に、さまざまなエラー メッセージが表示される可能性があります。 一時的な回避策として、次の手順に従って、MEF キャッシュを削除してからもう一度生成してください。

1. Visual Studio のすべてのインスタンスを閉じます。
1. %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\ を開きます。
1. 次のフォルダーを削除します。
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Visual Studio を開きます。
1. シナリオをもう一度実行します。

