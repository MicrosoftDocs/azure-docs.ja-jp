---
title: ローカルの Service Fabric クラスター上で Java アプリをデバッグする
description: このチュートリアルでは、ローカル クラスターで実行されている Service Fabric Java アプリケーションをデバッグする方法と、そのアプリケーションのログを取得する方法について説明します。
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 801702d43bae6d925fa4f7fbc37cf44bf585fa6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531992"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>チュートリアル: ローカルの Service Fabric クラスターでデプロイされた Java アプリケーションをデバッグする

このチュートリアルは、シリーズの第 2 部です。 Eclipse を使用して Service Fabric アプリケーション用にリモート デバッガーをアタッチする方法について説明します。 さらに、実行中のアプリケーションから開発者にとって都合のよい場所にログをリダイレクトする方法について説明します。

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Java Service Fabric Reliable Services アプリケーションをビルドする](service-fabric-tutorial-create-java-app.md)
> * ローカル クラスターでアプリケーションをデプロイおよびデバッグする
> * [アプリケーションを Azure クラスターにデプロイする](service-fabric-tutorial-java-deploy-azure.md)
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-java-elk.md)
> * [CI/CD を設定します](service-fabric-tutorial-java-jenkins.md)


シリーズの第 2 部で学習する内容は次のとおりです。
> [!div class="checklist"]
> * Eclipse を使用して Java アプリケーションをデバッグする
> * 構成可能な場所にログをリダイレクトする


## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* [Mac](service-fabric-get-started-mac.md) または [Linux](service-fabric-get-started-linux.md) の開発環境を設定します。 Eclipse プラグイン、Gradle、Service Fabric SDK、および Service Fabric CLI (sfctl) を手順に従ってインストールします。

## <a name="download-the-voting-sample-application"></a>投票サンプル アプリケーションをダウンロードする

[このチュートリアル シリーズの第 1 部](service-fabric-tutorial-create-java-app.md)で投票サンプル アプリケーションをビルドしていない場合は、ダウンロードすることができます。 コマンド ウィンドウで、次のコマンドを実行して、サンプル アプリのリポジトリをローカル コンピューターに複製します。

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

ローカル環境クラスターでアプリケーションを[ビルドしてデプロイ](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster)します。

## <a name="debug-java-application-using-eclipse"></a>Eclipse を使用して Java アプリケーションをデバッグする

1. マシンで Eclipse IDE を開き、**[File]\(ファイル\)、[Import...]\(インポート...\)** の順にクリックします。

2. ポップアップ ウィンドウで、**[General]\(一般\)、[Existing Projects into Workspace]\(既存のプロジェクトをワークスペースへ\)** オプションの順に選択し、[Next]\(次へ\) をクリックします。

3. [Import Projects]\(プロジェクトのインポート\) ウィンドウで、**[Select root directory]\(ルート ディレクトリの選択\)** オプションを選択し、**Voting** ディレクトリを選択します。 チュートリアル シリーズの第 1 部を完了している場合、**Voting** ディレクトリは **Eclipse-workspace** ディレクトリ内にあります。

4. デバッグするサービスの entryPoint.sh を更新して、リモートのデバッグ パラメーターで Java プロセスを開始するようにします。 このチュートリアルでは、ステートレス フロントエンドを使用します (*Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*)。この例では、デバッグ用にポート 8001 が設定されています。

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. アプリケーション マニフェストを更新して、デバッグするサービスのインスタンス数またはレプリカ数を 1 に設定します。 この設定により、デバッグに使用されるポートの競合を回避できます。 たとえば、ステートレス サービスについては、``InstanceCount="1"`` と設定し、ステートフル サービスについては、``TargetReplicaSetSize="1" MinReplicaSetSize="1"`` のように設定して、レプリカ セット サイズのターゲット値と最小値を 1 にします。

6. Eclipse IDE で、**[Run]\(実行\)、[Debug Configurations]\(デバッグ構成\)、[Remote Java Application]\(リモート Java アプリケーション\)** の順に選択し、**[New]\(新規\)** ボタンを押します。次に、以下のとおりプロパティを設定して、**[Apply]\(適用\)** をクリックします。

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* ファイルの 109 行にブレークポイントを設定します。

8. パッケージ エクスプローラーで、**Voting** プロジェクトを右クリックし、**[Service Fabric]、[Publish Application...]\(アプリケーションの発行...\)** の順にクリックします

9. **[Publish Application]\(アプリケーションの発行\)** ウィンドウで、ドロップダウンから **[Local.json]** を選択し、**[Publish]\(発行\)** をクリックします。

10. Eclipse IDE で、**[Run]\(実行\)、[Debug Configurations]\(デバッグ構成\)、[Remote Java Application]\(リモート Java アプリケーション\)** の順に選択し、作成した **投票** の構成をクリックしてから、**[Debug]\(デバッグ\)** をクリックします。

11. Web ブラウザーに移動し、**localhost:8080** にアクセスします。 これにより、ブレークポイントに自動的にヒットし、Eclipse が **デバッグ パースペクティブ** に入ります。

同様の手順を適用して、Eclipse で任意の Service Fabric アプリケーションをデバッグできます。

## <a name="redirect-application-logs-to-custom-location"></a>アプリケーション ログをカスタムの場所にリダイレクトする

次の手順では、既定の場所 */var/log/syslog* からカスタムの場所にアプリケーション ログをリダイレクトする方法について説明します。

1. 現在、Service Fabric Linux クラスターで実行されているアプリケーションでは、単一のログ ファイルの取得のみがサポートされています。 ログが常に */tmp/mysfapp0.0.log* に記録されるようにアプリケーションを設定するには、*Voting/VotingApplication/VotingWebPkg/Code/logging.properties 内に logging.properties* という名前のファイルを作成し、次の内容を追加します。

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Java 実行コマンドのために、*Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* で次のパラメーターを追加します。

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    次の例では、デバッガーを接続したサンプル実行を示しています。これは前のセクションの実行に似ています。

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

これで、Service Fabric Java アプリケーションの開発中にデバッグを行う方法とアプリケーション ログにアクセスする方法について確認できました。

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Eclipse を使用して Java アプリケーションをデバッグする
> * 構成可能な場所にログをリダイレクトする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [アプリケーションを Azure にデプロイする](service-fabric-tutorial-java-deploy-azure.md)