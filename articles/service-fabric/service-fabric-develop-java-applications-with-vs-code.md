---
title: Visual Studio Code を使用して Java アプリケーションを開発する
description: この記事では、Visual Studio Code を使用して Java Service Fabric アプリケーションをビルド、デプロイ、およびデバッグする方法について説明します。
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87327363"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Visual Studio Code を使用して Java Service Fabric アプリケーションを開発する

[VS Code 用の Service Fabric Reliable Services 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services)を使用すると、Windows、Linux、および macOS オペレーティング システム上で Java Service Fabric アプリケーションを簡単にビルドできます。

この記事では、Visual Studio Code を使用して Java Service Fabric アプリケーションをビルド、デプロイ、およびデバッグする方法について説明します。

> [!IMPORTANT]
> Service Fabric Java アプリケーションは Windows マシン上で開発できますが、デプロイは Azure Linux クラスターに対してのみ実行できます。 Java アプリケーションのデバッグは Windows 上ではサポートされていません。

## <a name="prerequisites"></a>前提条件

この記事では、VS Code、VS Code 用の Service Fabric Reliable Services 拡張機能、および開発環境に必要な依存関係が既にインストールされていることを前提としています。 詳細については、[作業の開始](./service-fabric-get-started-vs-code.md#prerequisites)に関するセクションを参照してください。

## <a name="download-the-sample"></a>サンプルのダウンロード
この記事では、[Service Fabric Java アプリケーション クイックスタート サンプルの GitHub リポジトリ](https://github.com/Azure-Samples/service-fabric-java-quickstart)の Voting アプリケーションを使用しています。 

リポジトリを開発マシンに複製するには、ターミナル ウィンドウ (Windows ではコマンド ウィンドウ) から次のコマンドを実行します。

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>VS Code でアプリケーションを開く

VS Code を開きます。  **アクティビティ バー** の [エクスプローラー] アイコンをクリックし、 **[フォルダーを開く]** をクリックするか、 **[ファイル]、[フォルダーを開く]** の順にクリックします。 リポジトリを複製したフォルダーの *./service-fabric-java-quickstart/Voting* ディレクトリに移動し、 **[OK]** をクリックします。 このワークスペースには、以下のスクリーンショットと同じファイルが含まれています。

![ワークスペースの Java Voting アプリケーション](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>アプリケーションのビルド

1. (Ctrl + Shift + P キー) を押して VS Code で **コマンド パレット** を開きます。
2. **[Service Fabric:Build Application]\(Service Fabric: アプリケーションのビルド\)** コマンドを探して選択します。 ビルドの出力は統合ターミナルに送信されます。

   ![VS Code の [Build Application]\(アプリケーションのビルド\) コマンド](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>アプリケーションをローカル クラスターにデプロイする
アプリケーションをビルドしたら、ローカル クラスターにデプロイできます。 

> [!IMPORTANT]
> ローカル クラスターへの Java アプリケーションのデプロイは、Windows マシンではサポートされていません。

1. **コマンド パレット** から **[Service Fabric:Deploy Application (Localhost)]\(Service Fabric: アプリケーションのデプロイ (Localhost)\)** コマンドを選択します。 インストール プロセスの出力は統合ターミナルに送信されます。

   ![VS Code の [Deploy Application]\(アプリケーションのデプロイ) コマンド](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. デプロイが完了したら、ブラウザーを起動して Service Fabric Explorer を開きます (`http://localhost:19080/Explorer`)。 アプリケーションが実行中であることがわかります。 この処理には時間がかかる場合があります。 

   ![Service Fabric Explorer の Voting アプリケーション](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. アプリケーションが実行されていることを確認したら、ブラウザーを起動して `http://localhost:8080` のページを開きます。 これはアプリケーションの Web フロントエンドです。 項目を追加し、その項目をクリックして投票することができます。

   ![ブラウザーの Voting アプリケーション](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. クラスターからアプリケーションを削除するには、次を選択します: **[Service Fabric: Remove Application]\(Service Fabric: アプリケーションの削除\)** コマンド (**コマンド パレット** から)。 アンインストール プロセスの出力は統合ターミナルに送信されます。 Service Fabric Explorer を使用して、アプリケーションがローカル クラスターから削除されたことを確認できます。

## <a name="debug-the-application"></a>アプリケーションのデバッグ
VS Code でアプリケーションをデバッグする場合、ローカル クラスター上でアプリケーションを実行する必要があります。 ブレークポイントをコードに追加することができます。

> [!IMPORTANT]
> Java アプリケーションのデバッグは Windows マシン上ではサポートされていません。

デバッグのために VotingDataService と Voting アプリケーションを準備するには、次の手順を実行します。

1. *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* ファイルを更新します。
('#' を使用して) 6 行目のコマンドをコメントアウトし、ファイルの最後に次のコマンドを追加します。

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. *Voting/VotingApplication/ApplicationManifest.xml* ファイルを更新します。 **StatefulService** 要素の **MinReplicaSetSize** 属性と **TargetReplicaSetSize** 属性を "1" に設定します。
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. VS Code で **アクティビティ バー** の [デバッグ] アイコンをクリックして、デバッガー ビューを開きます。 デバッガー ビューの上部にある歯車アイコンをクリックし、ドロップダウン メニューから **[Java]** を選択します。 launch.json ファイルが開きます。 

   ![VS Code ワークスペースの [デバッグ] アイコン](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. launch.json ファイルの **Debug (Attach)** という名前の構成のポート値を **8001** に設定します。 ファイルを保存します。

   ![launch.json のデバッグ設定](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. アプリケーションをローカル クラスターにデプロイするには、次を使用します: **[Service Fabric: Deploy Application (Localhost)]\(Service Fabric: アプリケーションのデプロイ (Localhost)\)** コマンド。 Service Fabric Explorer でアプリケーションが実行されていることを確認します。 これで、アプリケーションをデバッグする準備が整いました。

ブレークポイントを設定するには、次の手順を実行します。

1. エクスプローラーで、 */Voting/VotingDataService/src/statefulservice/VotingDataService.java* ファイルを開きます。 `addItem` メソッドの `try` ブロックの最初のコード行 (80 行目) にブレークポイントを設定します。
   
   ![Voting データ サービスにブレークポイントを設定する](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > 実行可能なコード行にブレークポイントを設定します。 たとえば、メソッドの宣言、`try` ステートメント、または `catch` ステートメントに設定されているブレークポイントは、デバッガーで無視されます。
2. デバッグを開始するには、**アクティビティ バー** の [デバッグ] アイコンをクリックし、デバッグ メニューから **[Debug (Attach)]\(デバッグ (アタッチ)\)** を選択し、実行ボタン (緑色の矢印) をクリックします。

   ![[Debug (Attach)]\(デバッグ (アタッチ)\) の構成](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Web ブラウザーで `http://localhost:8080` にアクセスします。 テキスト ボックスに新しい項目を入力し、 **[+ 追加]** をクリックします。 ブレークポイントにヒットするはずです。 VS Code の上部にある [デバッグ] ツールバーを使用して、実行の継続、行のステップ オーバー、メソッドへのステップ イン、現在のメソッドからのステップ アウトを行うことができます。 
   
   ![ブレークポイントのヒット](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. デバッグ セッションを終了するには、VS Code の上部にある [デバッグ] ツールバーのプラグ アイコンをクリックします。
   
   ![デバッガーから切断する](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. デバッグが完了したら、 **[Service Fabric:Remove Application]\(Service Fabric: アプリケーションの削除\)** コマンドを使用して、ローカル クラスターから Voting アプリケーションを削除できます。 

## <a name="next-steps"></a>次のステップ

* [VS Code を使用して C# Service Fabric アプリケーションを開発し、デバッグする](./service-fabric-develop-csharp-applications-with-vs-code.md)方法について学びます。
