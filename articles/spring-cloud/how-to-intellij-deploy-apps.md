---
title: チュートリアル:IntelliJ を使用して Azure Spring Cloud アプリケーションをデプロイする
description: IntelliJ を使用してアプリケーションを Azure Spring Cloud にデプロイします。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 64ff778f9735c690f4622402ef2e3124185fa3ea
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877097"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>IntelliJ を使用して Azure Spring Cloud アプリケーションをデプロイする

**この記事の適用対象:** ✔️ Java

Azure Spring Cloud の IntelliJ プラグインでは、IntelliJ IDEA からのアプリケーションのデプロイがサポートされています。  

この例を実行する前に、[基本的なクイックスタート](spring-cloud-quickstart.md)を試してみることができます。

## <a name="prerequisites"></a>前提条件
* [IntelliJ IDEA、Community または Ultimate Edition、バージョン 2020.1 または 2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>プラグインをインストールする
IntelliJ **プラグイン** UI から Azure Toolkit for IntelliJ IDEA 3.43.0 を追加できます。

1. IntelliJ を起動します。  以前にプロジェクトを開いていた場合は、プロジェクトを閉じて、ようこそダイアログを表示します。 右下のリンクから **[Configure]\(構成\)** を選択し、 **[Plugins]\(プラグイン\)** をクリックしてプラグインの構成ダイアログを開き、 **[Install Plugins from disk]\(ディスクからプラグインをインストール\)** を選択します。

    ![[Configure]\(構成\) の選択](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Azure Toolkit for IntelliJ を検索します。  **[インストール]** をクリックします。

    ![プラグインをインストールする](media/spring-cloud-intellij-howto/install-plugin.png)

1. **[Restart IDE]\(IDE を再起動する\)** をクリックします。

## <a name="tutorial-procedures"></a>チュートリアルの手順
次の手順では、IntelliJ IDEA を使用して Hello World アプリケーションをデプロイします。

* gs-spring-boot プロジェクトを開く
* Azure Spring Cloud にデプロイする
* ストリーミング ログを表示する

## <a name="open-gs-spring-boot-project"></a>gs-spring-boot プロジェクトを開く

1. このチュートリアルのソース リポジトリをダウンロードして解凍するか、次の Git で Git Clone を使用して複製します: https://github.com/spring-guides/gs-spring-boot.git 
1. gs-spring-boot\complete に移動します。
1. IntelliJ の **[Welcome]\(ようこそ\)** ダイアログを開き、 **[Import Project]\(プロジェクトのインポート\)** を選択してインポート ウィザードを開きます。
1. `gs-spring-boot\complete` フォルダーを選択します。

    ![プロジェクトのインポート](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Azure Spring Cloud にデプロイする
Azure にデプロイするには、Azure アカウントでサインインし、ご自分のサブスクリプションを選択する必要があります。  サインインの詳細については、「[インストールとサインイン](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)」を参照してください。

1. IntelliJ のプロジェクト エクスプローラーでプロジェクトを右クリックし、 **[Azure]**  ->  **[Deploy to Azure Spring Cloud]\(Azure Spring Cloud にデプロイ\)** を選択します。

    ![Azure へのデプロイ 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. **[Name]\(名前\)** フィールドのアプリの名前はそのまま使用します。 **[Name]\(名前\)** は、アプリ名ではなく構成を参照します。 通常、ユーザーはこれを変更する必要はありません。
1. **[Artifact]\(アーティファクト\)** のプロジェクトの識別子はそのまま使用します。
1. **[App]\(アプリ\)** を選択し、 **[Create app]\(アプリの作成\)** をクリックします。

    ![Azure へのデプロイ 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. **[App Name]\(アプリ名\)** に入力し、 **[OK]** をクリックします。

    ![Azure へのデプロイ [OK]](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. **[Run]\(実行\)** ボタンをクリックして、デプロイを開始します。 

    ![Azure へのデプロイ 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. このプラグインは、プロジェクトに対して `mvn package` コマンドを実行してから、新しいアプリを作成し、`package` コマンドによって生成された jar をデプロイします。

1. アプリの URL が出力ウィンドウに表示されない場合は、Azure portal から取得します。 リソース グループから Azure Spring Cloud のインスタンスに移動します。  次に、 **[Apps]\(アプリ\)** をクリックします。  実行中のアプリが一覧表示されます。

    ![テスト URL の取得](media/spring-cloud-intellij-howto/get-test-url.png)

1. ブラウザーで URL に移動します。

    ![ブラウザーで移動 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>ストリーミング ログを表示する
ログを取得するには、次のようにします。
1. **Azure Explorer** を選択し、 **[Spring Cloud]** を選択します。
1. 実行中のアプリを右クリックします。
1. ドロップダウン リストから **[Streaming Logs]\(ストリーミング ログ\)** を選択します。

    ![ストリーミング ログの選択](media/spring-cloud-intellij-howto/streaming-logs.png)

1. インスタンスを選択します。

    ![インスタンスの選択](media/spring-cloud-intellij-howto/select-instance.png)

1. ストリーミング ログが出力ウィンドウに表示されます。

    ![ストリーミング ログの出力](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>次のステップ
* [Azure Spring Cloud 用に Spring アプリケーションを準備する](how-to-prepare-app-deployment.md)
* [Azure Toolkit for IntelliJ の詳細を確認する](/azure/developer/java/toolkit-for-intellij/)
