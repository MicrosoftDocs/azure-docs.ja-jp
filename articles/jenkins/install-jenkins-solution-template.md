---
title: "Azure に Jenkins サーバーを作成する"
description: "Jenkins ソリューション テンプレートから Azure Linux 仮想マシンに Jenkins をインストールし、サンプル Java アプリケーションをビルドする方法について説明します。"
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---

# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>Azure Portal から Azure Linux VM に Jenkins サーバーを作成する

このクイックスタートでは、Ubuntu Linux VM に [Jenkins](https://jenkins.io) をインストールする方法について説明します。Azure で動作するように構成されたプラグインとツールのインストールも行います。 最終的には、[GitHub](https://github.com) からのサンプル Java アプリをビルドする Jenkins サーバーが Azure で稼働することになります。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション
* コンピューターのコマンド ラインでの SSH アクセス (Bash シェル、[PuTTY](http://www.putty.org/) など)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>ソリューション テンプレートからの Jenkins VM の作成

Web ブラウザーから [Jenkins の Marketplace イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)を開き、ページ左側から **[今すぐ入手する]** を選択します。 料金の詳細を確認して **[続行]** を選択し、**[作成]** を選択して、Azure Portal で Jenkins サーバーを構成します。 
   
![Azure Portal ダイアログ](./media/install-jenkins-solution-template/ap-create.png)

**[基本設定の構成]** タブで、次のフィールドに入力します。

![基本設定を構成する](./media/install-jenkins-solution-template/ap-basic.png)

* **[名前]** には **Jenkins** を使用します。
* **[ユーザー名]** を入力します。 ユーザー名は、[特定の要件](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)を満たしている必要があります。
* **[認証の種類]** として **[パスワード]** を選択し、パスワードを入力します。 パスワードには、大文字と数字に加え、1 つの特殊文字を含んでいる必要があります。
* **[リソース グループ]** には「**myJenkinsResourceGroup**」を使用します。
* **[場所]** ドロップダウンから **[米国東部]** の [Azure リージョン](https://azure.microsoft.com/regions/)を選択します。

**[OK]** を選択して、**[Configure additional options]\(追加オプションの構成\)** タブに移動します。一意のドメイン名を入力して Jenkins サーバーを指定し、**[OK]** を選択します。

![追加オプションの設定](./media/install-jenkins-solution-template/ap-addtional.png)  

 検証に成功したら、**[概要]** タブから再度 **[OK]** を選択します。最後に、**[購入]** を選択して Jenkins VM を作成します。 サーバーの準備が完了すると、Azure Portal に次のような通知が表示されます。   

![Jenkins の準備が完了したことを示す通知](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>Jenkins に接続する

Web ブラウザーから対象の仮想マシン (例: http://jenkins2517454.eastus.cloudapp.azure.com/) にアクセスします。 セキュリティで保護されていない HTTP 経由では Jenkins コンソールにアクセスできません。そのためこのページには、ご使用のコンピューターから SSH トンネルを使って安全に Jenkins コンソールにアクセスする手順が掲載されています。

![Jenkins のロックを解除する](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

このページのコマンド ラインから `ssh` コマンドを使用してトンネルを設定します。`username` は、先ほどソリューション テンプレートから仮想マシンをセットアップするときに選択した仮想マシンの管理者ユーザーの名前に置き換えてください。

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

トンネルを開始した後、ローカル コンピューターで http://localhost:8080/ に移動します。 

Jenkins VM に SSH 接続している状態で、コマンド ラインから次のコマンドを実行して初期パスワードを取得します。

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

初めて使うときは、この初期パスワードを使って Jenkins ダッシュボードのロックを解除します。

![Jenkins のロックを解除する](./media/install-jenkins-solution-template/jenkins-unlock.png)

次のページの **[Install suggested plugins]\(推奨プラグインのインストール\)** を選択し、Jenkins ダッシュボードへのアクセスに使用した Jenkins 管理者ユーザーを作成します。

![Jenkins の準備ができました。](./media/install-jenkins-solution-template/jenkins-welcome.png)

Jenkins サーバーでコードをビルドする準備が整いました。

## <a name="create-your-first-job"></a>最初のジョブの作成

Jenkins コンソールから **[Create new jobs]\(新しいジョブの作成\)** を選択して **mySampleApp** という名前を付け、**[Freestyle project]\(フリースタイル プロジェクト\)** を選択して **[OK]** を選択します。

![新しいジョブの作成](./media/install-jenkins-solution-template/jenkins-new-job.png) 

**[ソース コード管理]** タブを選択して **[Git]** を有効にし、**[リポジトリの URL]** フィールドに次の URL を入力します。`https://github.com/spring-guides/gs-spring-boot.git`

![Git リポジトリの定義](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

**[ビルド]** タブを選択し、**[ビルド ステップの追加]**、**[Invoke Gradle script]\(Gradle スクリプトの呼び出し\)** の順に選択します。 **[Use Gradle Wrapper]\(Gradle ラッパーの使用\)** を選択して、**[Wrapper location]\(ラッパーの場所\)** に「`complete`」、**[タスク]** に「`build`」と入力します。

![Gradle ラッパーを使用したビルド](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

**[Advanced]\(詳細設定\)** を選択し、 **[Root Build script]\(ルート ビルド スクリプト\)** フィールドに「`complete`」と入力します。 [ **保存**] を選択します。

![Gradle ラッパー ビルド ステップの詳細設定](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>コードのビルド

**[Build Now]\(今すぐビルド\)** を選択してコードをコンパイルし、サンプル アプリをパッケージします。 ビルドが完了したら、プロジェクトの **[ワークスペース]** リンクを選択します。

![ワークスペースを参照画面で選択し、ビルドから JAR ファイルを取得します。](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

ビルドが成功したことを確かめるために、`complete/build/libs` に移動し、`gs-spring-boot-0.1.0.jar` が存在することを確認します。 Azure 上の Jenkins サーバーで独自のプロジェクトをビルドする準備が整いました。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure VM を Jenkins エージェントとして追加する](jenkins-azure-vm-agents.md)

