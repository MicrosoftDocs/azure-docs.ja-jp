---
title: "Azure および Azure Stack へのアプリのデプロイ | Microsoft Docs"
description: "ハイブリッド CI/CD パイプラインを使用して、アプリを Azure と Azure Stack にデプロイする方法を説明します。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: helaw
ms.custom: mvc
ms.openlocfilehash: 83bb401d5d65cd2c34015a1a14673363aeee81d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Azure と Azure Stack へのアプリのデプロイ
*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

ハイブリッド[継続的インテグレーション](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[継続的配信](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (CI/CD) パイプラインによって、アプリのビルド、テスト、および複数のクラウドへのデプロイを行うことができます。  このチュートリアルでは、サンプル環境をビルドし、ハイブリッド CI/CD パイプラインを利用する方法について学習します。
 
> [!div class="checklist"]
> * コード コミットに基づいて、Visual Studio Team Services (VSTS) リポジトリに対して新しいビルドを開始します。
> * ユーザー受け入れテストのため、新しくビルドされたコードを Azure に自動的にデプロイします。
> * コードがテストに合格したら、自動的に Azure Stack にデプロイします。 


## <a name="prerequisites"></a>前提条件
ハイブリッド CI/CD パイプラインをビルドするにはいくつかのコンポーネントが必要で、準備に多少時間がかかることがあります。  これらのコンポーネントの一部を既に持っている場合は、開始する前にそれらが要件を満たしていることを確認してください。

このトピックは、Azure と Azure Stack について一定の知識があることを前提にしています。 続行する前に知識を深めておく場合は、以下の各トピックから開始するようにしてください。

- [Azure 入門](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Azure Stack の主要概念](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
 - [Web アプリ](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)を作成し、それを[FTP 発行](../../app-service/app-service-deploy-ftp.md)のために構成します。  後で使用するため、新しい Web アプリの URL を書き留めておきます。


### <a name="azure-stack"></a>Azure Stack
 - [Azure Stack をデプロイします](../azure-stack-run-powershell-script.md)。  通常、インストールが完了するまで数時間かかるため、それに応じて計画を立ててください。
 - [App Service](../azure-stack-app-service-deploy.md) PaaS サービスを Azure Stack にデプロイします。
 - Web アプリを作成し、それを[FTP 発行](../azure-stack-app-service-enable-ftp.md)のために構成します。  後で使用するため、新しい Web アプリの URL を書き留めておきます。  

### <a name="developer-tools"></a>開発者用ツール
 - [VSTS ワークスペース](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)を作成します。  サインアップ プロセスによって "MyFirstProject" という名前のプロジェクトが作成されます。  
 - [Visual Studio 2017 をインストール](https://docs.microsoft.com/visualstudio/install/install-visual-studio)して [VSTS にサインイン](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)します。
 - プロジェクトに接続し、[ローカルに複製](https://www.visualstudio.com/docs/git/gitquickstart)します。
 - VSTS 内に[エージェント プール](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues)を作成します。
 - Visual Studio をインストールし、Azure Stack 上の仮想マシンに [VSTS ビルド エージェント](https://www.visualstudio.com/docs/build/actions/agents/v2-windows)をデプロイします。 
 

## <a name="create-app--push-to-vsts"></a>アプリの作成と VSTS へのプッシュ

### <a name="create-application"></a>アプリケーションの作成
このセクションでは、シンプルな ASP.NET アプリケーションを作成し、VSTS にプッシュします。  以下の手順は、通常の開発者ワークフローを表していて、複数の開発ツールと言語に適合させることができます。 

1.  Visual Studio を開きます。
2.  チーム エクスプ ローラー空間の **[ソリューション]** 領域で、**[新規作成]** をクリックします。
3.  **[Visual C#]**  >  **[Web]**  >  **[ASP.NET Web アプリケーション (.NET Framework)]** と選択します。
4.  アプリケーションの名前を入力して **[OK]** をクリックします。
5.  次の画面で、既定値 (Web フォーム) のまま **[OK]** をクリックします。

### <a name="commit-and-push-changes-to-vsts"></a>コミットと VSTS への変更のプッシュ
1.  Visual Studio でチーム エクスプ ローラーを使用し、ドロップダウンを選択して **[変更]** をクリックします。
2.  コミット メッセージを指定し、**[すべてコミット]** を選択します。 ソリューション ファイルの保存を求められる場合があります。[はい] をクリックしてすべてを保存します。
3.  コミットされると、変更をプロジェクトに同期するかどうか尋ねられます。 **[同期]** を選択します。

    ![コミットが完了したときのコミット画面を示す画像](./media/azure-stack-solution-pipeline/image1.png)

4.  同期のタブの *[Ongoing]* (発信) に、新しいコミットが表示されます。  **[プッシュ]** を選択し、変更を VSTS に同期します。

    ![同期の手順を示す画像](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>VSTS でのコードのレビュー
変更をコミットして VSTS にプッシュしたら、VSTS ポータルからコードを確認します。  **[コード]** を選択し、ドロップダウン メニューから **[ファイル]** を選択します。  作成したソリューションを表示できます。

## <a name="create-build-definition"></a>ビルド定義の作成
ビルド プロセスでは、コード変更の各コミット時に、デプロイのためにアプリケーションをどのようにビルドし、パッケージ化するかを定義します。 この例では、含まれるテンプレートを使用して ASP.NET アプリのビルド プロセスを構成していますが、この構成は、実際のアプリケーションに応じて変更することができます。

1.  Web ブラウザーから VSTS ワークスペースにサインインします。
2.  バナーから **[ビルドとリリース]**、**[ビルド]** と選択します。
3.  **[+ 新しい定義]** をクリックします。
4.  テンプレートの一覧から **[ASP.NET (Preview)]** を選択し、**[適用]** を選択します。
5.  *[ソリューションのビルド]* の手順で、*[MSBuild 引数]* フィールドを変更します。

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  **[オプション]** タブを選択し、Azure Stack 上の仮想マシンにデプロイしたビルド エージェントのエージェント キューを選択します。 
7.  **[トリガー]** タブを選択し、**[継続的インテグレーション]** を有効にします。
7.  **[保存してキューに登録]** をクリックし、ドロップダウンから **[保存]** を選択します。 

## <a name="create-release-definition"></a>リリース定義の作成
リリース プロセスでは、前の手順で作成したビルドをどのように環境にデプロイするかを定義します。  このチュートリアルでは FTP を使用して、Azure Web アプリに ASP.NET アプリを公開します。 Azure へのリリースを構成するには、次の手順を実行します。

1.  VSTS バナーから **[ビルドとリリース]**、**[リリース]** と選択します。
2.  緑の **[+ 新しい定義]** をクリックします。
3.  **[Empty]** (空) を選択し、**[次へ]** をクリックします。
4.  *[継続的なデプロイ]* チェック ボックスをオンにして、**[作成]** をクリックします。

これで空のリリース定義を作成し、ビルドに関連付けました。Azure 環境向けの手順を追加します。

1.  緑の **[+]** をクリックしてタスクを追加します。
2.  **[すべて]** を選択してから、一覧から **[FTP Upload]** (FTP アップロード) を追加し、**[閉じる]** を選択します。
3.  追加したばかりの **[FTP Upload]** (FTP アップロード) タスクを選択し、以下のパラメーターを構成します。
    
    | パラメーター | 値 |
    | ----- | ----- |
    |認証方法| Enter Credentials (資格情報の入力)|
    |サーバー URL | Azure ポータルから取得した Web アプリの FTP URL |
    |ユーザー名 | Web アプリのために FTP の資格情報を作成したときに構成したユーザー名 |
    |パスワード | Web アプリのために FTP の資格情報を確立したときに作成したパスワード|
    |ソース ディレクトリ | $(System.DefaultWorkingDirectory)\**\ |
    |リモート ディレクトリ | /site/wwwroot/ |
    |Preserve file paths (ファイルのパスを保持する) | 有効 (チェックボックスがオン)|

4.  **[保存]**

最後に、次の手順を使用して、デプロイされたエージェントを格納しているエージェント プールを使用するようにリリース定義を構成します。
1.  目的のリリース定義を選択し、**[編集]** をクリックします。
2.  中央の列から **[エージェントで実行]** を選択します。  右側の列で、Azure Stack で実行されるビルド エージェントを格納しているエージェント キューを選択します。  
    ![特定のキューを使用するリリース定義の構成を示す画像](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>Azure へのアプリのデプロイ
この手順では、新しくビルドされた CI/CD パイプラインを使用して、Azure 上の Web アプリに ASP.NET アプリをデプロイします。 

1.  VSTS のバナーから **[ビルドとリリース]**、**[ビルド]** と選択します。
2.  前に作成したビルド定義で **[...]** をクリックし、**[新しいビルドをキューに配置]** を選択します。
3.  既定値をそのまま使用し、**[Ok]** をクリックします。  ビルドが開始され、進行状況が表示されます。
4.  ビルドが完了したら、**[ビルドとリリース]** を選択して **[リリース]** を選択することで状態を追跡できます。
5.  ビルドの完了後は、Web アプリを作成するときに書き留めた URL を使用して Web サイトにアクセスします。    


## <a name="add-azure-stack-to-pipeline"></a>パイプラインへの Azure Stack の追加
これで、Azure に展開して CI/CD パイプラインをテストしたので、次は Azure Stack をパイプラインに追加します。  以下の手順では、新しい環境を作成し、Azure Stack にアプリをデプロイする FTP アップロード タスクを追加します。  リリース承認者も追加します。これは、Azure Stack へのコード リリースに関する ”サインオフ” をシミュレートする方法として機能します。  

1.  リリース定義で、**[+ Add Environment]** (+ 環境の追加)、**[Create new environment]** (新しい環境の作成) と選択します。
2.  **[Empty]** (空) を選択し、**[次へ]** をクリックします。
3.  **[Specific users]** (特定のユーザー) を選択し、アカウントを指定します。  **[作成]**を選択します。
4.  既存の名前を選択し、「*Azure Stack*」と入力して、環境の名前を変更します。
5.  ここで、Azure Stack 環境を選択し、**[Add tasks]** (タスクの追加) を選択します。
6.  **[FTP Upload]** (FTP アップロード) タスクを選択し、**[追加]** を選択してから **[閉じる]** を選択します。


### <a name="configure-ftp-task"></a>FTP タスクの構成
リリースを作成したので、Azure Stack 上の Web アプリに発行するために必要な手順を構成します。  Azure 用の FTP アップロード タスクを構成したのと同様に、Azure Stack 用のタスクを構成します。

1.  追加したばかりの **[FTP Upload]** (FTP アップロード) タスクを選択し、以下のパラメーターを構成します。
    
    | パラメーター | 値 |
    | -----     | ----- |
    |認証方法| Enter Credentials (資格情報の入力)|
    |サーバー URL | Azure Stack ポータルから取得した Web アプリの FTP URL |
    |ユーザー名 | Web アプリのために FTP の資格情報を作成したときに構成したユーザー名 |
    |パスワード | Web アプリのために FTP の資格情報を確立したときに作成したパスワード|
    |ソース ディレクトリ | $(System.DefaultWorkingDirectory)\**\ |
    |リモート ディレクトリ | /site/wwwroot/|
    |Preserve file paths (ファイルのパスを保持する) | 有効 (チェックボックスがオン)|

2.  **[保存]**

最後に、次の手順を使用して、デプロイされたエージェントを格納しているエージェント プールを使用するようにリリース定義を構成します。
1.  目的のリリース定義を選択し、**[編集]** をクリックします。
2.  中央の列から **[エージェントで実行]** を選択します。 右側の列で、Azure Stack で実行されるビルド エージェントを格納しているエージェント キューを選択します。  
    ![特定のキューを使用するリリース定義の構成を示す画像](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>新しいコードのデプロイ
これで、Azure Stack への発行を行う最後の手順によって、ハイブリッド CI/CD パイプラインをテストできます。  このセクションでは、サイトのフッターを変更し、パイプラインを通じてデプロイを開始します。  完了すると、レビューのために Azure にデプロイされた変更が表示されます。その後、リリースを承認すると、Azure Stack への発行が行われます。

1. Visual Studio で *site.master* ファイルを開き、次の行を
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    次のようにします。

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  変更をコミットし、VSTS に同期します。  
4.  VSTS ワークスペースから **[ビルドとリリース]**  >  **[ビルド]** と選択して、ビルドの状態を確認します。
5.  処理が進行中のビルドが表示されます。  状態をダブルクリックすると、ビルドの進行状況を参照できます。  コンソールに ”Finished build” (完了したビルド) と表示されたら、**[ビルドとリリース]**  >  **[ビルド]** と選択して目的のリリースに移動します。  そのリリースをダブルクリックします。
6.  リリースにはレビューが必要であるという通知が表示されます。 Web アプリの URL を確認し、新しい変更があることを確認します。  VSTS でリリースを承認します。
    ![特定のキューを使用するリリース定義の構成を示す画像](./media/azure-stack-solution-pipeline/image4.png)
    
7.  Web アプリの作成時に書き留めた URL を使用して Web サイトにアクセスし、Azure Stack への発行が完了したことを確認します。
    ![フッターが変更された ASP.NET アプリを示す画像](./media/azure-stack-solution-pipeline/image5.png)


新しいハイブリッド CI/CD パイプラインを、その他のハイブリッド クラウド パターンの構成要素として利用できるようになりました。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、以下のことを行うハイブリッド CI/CD パイプラインの構築方法を学習しました。

> [!div class="checklist"]
> * コード コミットに基づいて、Visual Studio Team Services (VSTS) リポジトリに対して新しいビルドを開始する。
> * ユーザー受け入れテストのため、新しくビルドされたコードを Azure に自動的にデプロイする。
> * コードがテストに合格したら、自動的に Azure Stack にデプロイする。 

ハイブリッド CI/CD パイプラインを用意できたので、引き続き、Azure Stack のためにアプリを開発する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack 向けの開発](azure-stack-developer.md)


