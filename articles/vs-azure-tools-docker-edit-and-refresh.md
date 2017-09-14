---
title: "ローカルの Docker コンテナーでアプリをデバッグする | Microsoft Docs"
description: "編集と更新を使用して、ローカルの Docker コンテナーで実行されているアプリに変更を加え、デバッグのブレークポイントを設定する方法について説明します。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: fcd58736d8915a61683a416fb9bf3892ba7b7bd8
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# ローカルの Docker コンテナーでアプリをデバッグする
<a id="debugging-apps-in-a-local-docker-container" class="xliff"></a>
## 概要
<a id="overview" class="xliff"></a>
Visual Studio Tools for Docker を使用すると、一貫した方法でアプリケーションの開発と検証を Linux Docker コンテナーでローカルで実行できます。
コード変更のたびにコンテナーを再起動する必要はありません。
この記事では、"編集と更新" の機能を使用して、ローカルの Docker コンテナーで ASP.NET Core Web アプリを起動し、必要な変更を行い、その変更を反映するためにブラウザーの表示を更新する方法について説明します。
また、デバッグ用のブレークポイントを設定する方法についても説明します。

> [!NOTE]
> Windows コンテナーのサポートは今後のリリースで開始する予定です
>
>

## 前提条件
<a id="prerequisites" class="xliff"></a>
次のツールをインストールする必要があります。

* [Visual Studio の最新バージョン](https://www.visualstudio.com/downloads/)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

ローカルで Docker コンテナーを実行するには、ローカルの Docker クライアントが必要です。
Hyper-V を無効にして、[Docker Toolbox](https://www.docker.com/products/docker-toolbox) を使用するか、または Hyper-V を使用し、Windows 10 が必要な [Docker for Windows](https://www.docker.com/get-docker) を使用することができます。

Docker Toolbox を使用する場合は、[Docker クライアントを構成する](vs-azure-tools-docker-setup.md)必要があります。

## 1.Web アプリを作成する
<a id="1-create-a-web-app" class="xliff"></a>
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 2.Docker サポートの追加
<a id="2-add-docker-support" class="xliff"></a>
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 3.コードを編集して更新する
<a id="3-edit-your-code-and-refresh" class="xliff"></a>
変更をすばやく反復処理するには、コンテナー内でアプリケーションを起動して変更を行うと、IIS Express を使用する場合のように表示できます。

1. ソリューション構成を `Debug` に設定して **Ctrl + F5** キーを押すと、Docker イメージが作成され、ローカルで実行されます。

    コンテナー イメージが作成されて Docker コンテナーで実行されると、Visual Studio は既定のブラウザーでその Web アプリを起動します。
    Microsoft Edge ブラウザーを使用している場合、またはエラーが発生している場合は、「 [トラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md) 」セクションを参照してください。
2. [About] ページに移動して、変更を加えます。
3. Visual Studio に戻り、 `Views\Home\About.cshtml`を開きます。
4. ファイルの最後に次の HTML コンテンツを追加し、変更を保存します。

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. 出力ウィンドウで .NET ビルドが完了したことを確認し、次の行が表示されたら、お使いのブラウザーに戻り、[About] ページを更新します。

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. 変更が適用されました。

## 4.ブレークポイントを使ってデバッグする
<a id="4-debug-with-breakpoints" class="xliff"></a>
多くの場合、変更にはさらなる検査が必要になります。この場合、Visual Studio のデバッグ機能を活用します。

1. Visual Studio に戻り、`Controllers\HomeController.cs` を開きます。
2. About() メソッドの内容を次のように置き換えます。

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. ブレークポイントを `string message`... 行の左側に設定します。
4. **F5** キーを押して、デバッグを開始します。
5. [About] ページに移動して、ブレークポイントを探します。
6. Visual Studio に切り替えてブレークポイントを表示し、メッセージの値を検査します。

   ![][2]

## 概要
<a id="summary" class="xliff"></a>
[Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)により、Docker コンテナー内での開発の生産性が実際に向上するため、ローカル作業の生産性が上がります。

## トラブルシューティング
<a id="troubleshooting" class="xliff"></a>
[Docker エラーのトラブルシューティング](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## Visual Studio、Windows、および Azure での Docker の詳細について
<a id="more-about-docker-with-visual-studio-windows-and-azure" class="xliff"></a>
* [Visual Studio 用 Docker ツール](http://aka.ms/dockertoolsforvs) - コンテナー内での .NET Core コードの開発
* [Visual Studio Team Services 用 Docker ツール](http://aka.ms/dockertoolsforvsts) - Docker コンテナーの構築およびデプロイ
* [Visual Studio Code 用 Docker ツール](http://aka.ms/dockertoolsforvscode) - Docker ファイルを編集するための言語サービス (e2e シナリオは増加予定)
* [Windows コンテナー情報](http://aka.ms/containers)- Windows Server および Nano Server の情報
* [Azure Container Service](https://azure.microsoft.com/services/container-service/) - [Azure Container Service の内容](http://aka.ms/AzureContainerService)
* Docker を使用した作業の例については、[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect の[デモ](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)の「[Working with Docker (Docker を使用した作業)](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker)」を参照してください。 HealthClinic.biz のデモに関連する他のクイック スタートについては、「 [Azure Developer Tools Quickstarts (Azure 開発者ツールのクイック スタート)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)」を参照してください。

## さまざまな Docker ツール
<a id="various-docker-tools" class="xliff"></a>
[Some great docker tools (Steve Lasker's blog) (いくつかの優れた Docker ツール (Steve Lasker のブログ))](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## お勧めの記事
<a id="good-articles" class="xliff"></a>
[Introduction to Microservices from NGINX (Nginx のマイクロサービスの概要)](https://www.nginx.com/blog/introduction-to-microservices/)

## プレゼンテーション
<a id="presentations" class="xliff"></a>
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e (Steve Lasker: VS Live ラスベガス 2016 - Docker e2e)](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction to ASP.NET Core @ build 2016 - Where You At Demo (ASP.NET Core の概要 @ ビルド 2016 - デモンストレーション)](https://channel9.msdn.com/Events/Build/2016/B810)
* [コンテナーでの .NET アプリの開発、Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png

