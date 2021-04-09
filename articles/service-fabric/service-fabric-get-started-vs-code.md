---
title: Azure Service Fabric と VS Code の概要
description: この記事では、Visual Studio Code を使用して Service Fabric アプリケーションを作成する方法の概要について説明します。
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: a655199d854462285ca98a2d8f454c483c061f99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92316672"
---
# <a name="service-fabric-for-visual-studio-code"></a>Visual Studio Code 用の Service Fabric

[VS Code 用の Service Fabric Reliable Services 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)には、Windows、Linux、および macOS オペレーティング システム上で Service Fabric アプリケーションを作成、ビルド、およびデバッグするために必要なツールが用意されています。

この記事では、拡張機能の要件と設定の概要と、拡張機能で提供されているさまざまなコマンドの使用方法について説明します。 

> [!IMPORTANT]
> Service Fabric Java アプリケーションは Windows マシン上で開発できますが、デプロイは Azure Linux クラスターに対してのみ実行できます。 Java アプリケーションのデバッグは Windows 上ではサポートされていません。

## <a name="prerequisites"></a>前提条件

すべての環境に以下の前提条件をインストールする必要があります。

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman Generators -- アプリケーションに適したジェネレーターをインストールしてください

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Java 開発の場合は、以下の前提条件をインストールする必要があります。

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (バージョン 1.8)
* [Gradle](https://gradle.org/install/)
* [Debugger for Java VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Java サービスのデバッグに必要です。 Java のデバッグ サービスは Linux 上でのみサポートされています。 VS Code の **アクティビティ バー** にある [拡張機能] アイコンをクリックし、拡張機能を検索してインストールするか、VS Code Marketplace からインストールすることができます。

.NET Core/C# 開発の場合は、以下の前提条件をインストールする必要があります。

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (バージョン 2.0.0 以降)
* [C# for Visual Studio Code (OmniSharp 搭載) VS Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) C# サービスのデバッグに必要です。 VS Code の **アクティビティ バー** にある [拡張機能] アイコンをクリックし、拡張機能を検索してインストールするか、VS Code Marketplace からインストールすることができます。

## <a name="setup"></a>セットアップ

1. VS Code を開きます。
2. VS Code の左側にある **アクティビティ バー** で [デバッグ] アイコンをクリックします。 「Service Fabric」を探します。 Service Fabric Reliable Services 拡張機能の **[インストール]** をクリックします。

## <a name="commands"></a>コマンド
VS Code の Service Fabric Reliable Services 拡張機能には、開発者が Service Fabric プロジェクトを作成してデプロイするために役立つコマンドが多数用意されています。 **コマンド パレット** からコマンドを呼び出すには、`(Ctrl + Shift + p)` を押し、入力バーにコマンド名を入力し、プロンプト リストから目的のコマンドを選択します。 

* [Service Fabric: Create Application]\(Service Fabric: アプリケーションの作成\) 
* [Service Fabric: Publish Application]\(Service Fabric: アプリケーションの発行\) 
* [Service Fabric: Deploy Application]\(Service Fabric: アプリケーションのデプロイ\) 
* [Service Fabric: Remove Application]\(Service Fabric: アプリケーションの削除\)  
* [Service Fabric: Build Application]\(Service Fabric: アプリケーションのビルド\) 
* [Service Fabric: Clean Application]\(Service Fabric: アプリケーションの消去\) 

### <a name="service-fabric-create-application"></a>[Service Fabric: Create Application]\(Service Fabric: アプリケーションの作成\)

**[Service Fabric: Create Application]\(Service Fabric: アプリケーションの作成\)** コマンドで、現在のワークスペースに新しい Service Fabric アプリケーションを作成します。 開発マシンにインストールされている Yeoman ジェネレーターに応じて、Java、C#、コンテナー、ゲスト プロジェクトなど、複数種類の Service Fabric アプリケーションを作成できます。 

1.  **[Service Fabric: アプリケーションの消去]** コマンドを選択します。
2.  新しい Service Fabric アプリケーションの種類を選択します。 
3.  作成するアプリケーションの名前を入力します。
3.  Service Fabric アプリケーションに追加するサービスの種類を選択します。 
4.  メッセージに従って、サービスに名前を付けます。 
5.  新しい Service Fabric アプリケーションがワークスペースに表示されます。
6.  新しいアプリケーション フォルダーを開いて、ワークスペースのルート フォルダーにします。 ここからコマンドの実行を継続できます。

### <a name="service-fabric-add-service"></a>[Service Fabric: Add Service]\(Service Fabric: サービスの追加\)
**[Service Fabric: Add Service]\(Service Fabric: サービスの追加\)** コマンドで、新しいサービスを既存の Service Fabric アプリケーションに追加します。 サービスを追加するアプリケーションは、ワークスペースのルート ディレクトリである必要があります。 

1.  **[Service Fabric: Add Application]\(Service Fabric: アプリケーションの追加\)** コマンドを選択します。
2.  現在の Service Fabric アプリケーションの種類を選択します。 
3.  Service Fabric アプリケーションに追加するサービスの種類を選択します。 
4.  メッセージに従って、サービスに名前を付けます。 
5.  新しいサービスがプロジェクト ディレクトリに表示されます。 

### <a name="service-fabric-publish-application"></a>[Service Fabric: Publish Application]\(Service Fabric: アプリケーションの発行\)
**[Service Fabric: Publish Application]\(Service Fabric: アプリケーションの発行\)** コマンドで、リモート クラスター上に Service Fabric アプリケーションをデプロイします。 ターゲット クラスターには、セキュリティで保護されたクラスターまたは保護されていないクラスターを指定できます。 Cloud.json にパラメーターが設定されていない場合、アプリケーションはローカル クラスターにデプロイされます。

1.  アプリケーションを初めてビルドすると、Cloud.json ファイルがプロジェクト ディレクトリに生成されます。
2.  接続するクラスターの値を Cloud.json ファイルに入力します。
3.  **[Service Fabric: Publish Application]\(Service Fabric: アプリケーションの発行\)** コマンドを選択します。
4.  Service Fabric Explorer でターゲット クラスターを表示し、アプリケーションがインストールされていることを確認します。 

### <a name="service-fabric-deploy-application-localhost"></a>[Service Fabric: Deploy Application (Localhost)]\(Service Fabric: アプリケーションのデプロイ (Localhost)\)
**[Service Fabric: Deploy Application]\(Service Fabric: アプリケーションのデプロイ\)** コマンドで、リローカル クラスター上に Service Fabric アプリケーションをデプロイします。 コマンドを使用する前に、ローカル クラスターが実行されていることを確認します。 

1. **[Service Fabric: Deploy Application]\(Service Fabric: アプリケーションのデプロイ\)** コマンドを選択します。
2. Service Fabric Explorer でローカル クラスターを表示し (http:\//localhost:19080/Explorer)、アプリケーションがインストールされていることを確認します。 この処理には時間がかかる場合があります。
3. Cloud.json ファイルでパラメーターを指定せずに **[Service Fabric: Publish Application]\(Service Fabric: アプリケーションの発行\)** コマンドを使用して、ローカル クラスターにデプロイすることもできます。

> [!NOTE]
> ローカル クラスターへの Java アプリケーションのデプロイは、Windows マシンではサポートされていません。

### <a name="service-fabric-remove-application"></a>[Service Fabric: Remove Application]\(Service Fabric: アプリケーションの削除\)
**[Service Fabric: Remove Application]\(Service Fabric: アプリケーションの削除\)** コマンドで、VS Code 拡張機能を使用して以前にデプロイされたクラスターから Service Fabric アプリケーションを削除します。 

1.  **[Service Fabric: Remove Application]\(Service Fabric: アプリケーションの削除\)** コマンドを選択します。
2.  Service Fabric Explorer でクラスターを表示し、アプリケーションが削除されたことを確認します。 この処理には時間がかかる場合があります。

### <a name="service-fabric-build-application"></a>[Service Fabric: Build Application]\(Service Fabric: アプリケーションのビルド\)
**[Service Fabric: アプリケーションのビルド]** コマンドで、Java または C# Service Fabric アプリケーションをビルドできます。 

1.  このコマンドを実行する前に、現在のフォルダーがアプリケーション ルート フォルダーであることを確認します。 このコマンドでアプリケーションの種類 (C# または Java) が特定され、それに応じてアプリケーションがビルドされます。
2.  **[Service Fabric: Build Application]\(Service Fabric: アプリケーションのビルド\)** コマンドを選択します。
3.  ビルド プロセスの出力は統合ターミナルに書き込まれます。

### <a name="service-fabric-clean-application"></a>[Service Fabric: Clean Application]\(Service Fabric: アプリケーションの消去\)
**[Service Fabric: Clean Application]\(Service Fabric: アプリケーションの消去\)** コマンドで、ビルドによって生成されたすべての jar ファイルおよびネイティブ ライブラリを削除します。 Java アプリケーションにのみ有効です。 

1.  このコマンドを実行する前に、現在のフォルダーがアプリケーション ルート フォルダーであることを確認します。 
2.  **[Service Fabric: Clean Application]\(Service Fabric: アプリケーションの消去\)** コマンドを選択します。
3.  消去プロセスの出力は統合ターミナルに書き込まれます。

## <a name="next-steps"></a>次のステップ

* [VS Code を使用して C# Service Fabric アプリケーションを開発し、デバッグする](./service-fabric-develop-csharp-applications-with-vs-code.md)方法について学びます。
* [VS Code を使用して Java Service Fabric アプリケーションを開発し、デバッグする](./service-fabric-develop-java-applications-with-vs-code.md)方法について学びます。