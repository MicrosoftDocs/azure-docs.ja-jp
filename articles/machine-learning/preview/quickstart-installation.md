---
title: "Azure Machine Learning サービスのインストールに関するクイックスタート | Microsoft Docs"
description: "このクイックスタートでは、Azure Machine Learning リソースを作成する方法と Azure Machine Learning Workbench をインストールする方法について説明します。"
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 09/20/2017
ms.openlocfilehash: bb3ac21d2e8b8efd7b4f6943fcc3256f18cbdd68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Azure Machine Learning プレビュー アカウントの作成と Azure Machine Learning Workbench のインストール
Azure Machine Learning は、データ サイエンスと高度な分析をエンド ツー エンドで支援する統合ソリューションです。データの準備、実験の開発、モデルのデプロイをクラウド スケールで行うプロフェッショナルなデータ サイエンティストを対象としています。

このクイックスタートでは、Azure Machine Learning サービス プレビューで実験アカウントとモデル管理アカウントを作成する方法について説明します。 また、Azure Machine Learning Workbench デスクトップ アプリケーションと CLI ツールをインストールする方法についても説明します。 さらに、Azure Machine Learning プレビュー機能のクイック ツアーを行います。経時的な変化を伴わない[あやめのデータセット](https://en.wikipedia.org/wiki/iris_flower_data_set)を使用して、いくつかの物理的な特徴に基づいてあやめの種類を予測するモデルを構築します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件
現在、Azure Machine Learning Workbench デスクトップ アプリは、次のオペレーティング システムにのみインストールできます。 
- Windows 10
- Windows Server 2016
- macOS Sierra

>[!NOTE]
>macOS High Sierra はまだサポートされていません。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする
[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-azure-machine-learning-accounts"></a>Azure Machine Learning アカウントを作成する
Azure Portal を使用して Azure Machine Learning アカウントをプロビジョニングします。 
1. ポータルの左上隅にある **[新規]** ボタン (+) を選択します。

2. 検索バーに「Machine Learning」と入力します。 **[Machine Learning Experimentation (preview)]\(Machine Learning 実験 (プレビュー)\)** という検索結果を選択します。  星のアイコンをクリックして、この選択を Azure Portal のお気に入りに登録します。

   ![Azure Machine Learning の検索](media/quickstart-installation/portal-more-services.png)

3. **+ [追加]** をクリックして、新しい Machine Learning 実験アカウントを構成します。 詳細なフォームが開きます。

   ![Machine Learning 実験アカウント](media/quickstart-installation/portal-create-experimentation.png)

4. Machine Learning 実験のフォームに次の情報を入力します。

   設定|推奨値|Description
   ---|---|---
   [Experimentation account name]\(実験アカウント名\) | "_一意の名前_" |アカウントを識別する一意の名前を選択します。 独自の名前を選択できます。あるいは、実験を識別するうえで最適な部門またはプロジェクトの名前を選択できます。 この名前で使用できるのは英数字とダッシュ文字 (-) のみであり、文字数は 2 ～ 32 文字で指定する必要があります。 
   [サブスクリプション] | _該当するサブスクリプション_ |実験に使用する Azure サブスクリプション。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となっている適切なサブスクリプションを選択してください。
   リソース グループ | "_リソース グループ名_" | 新しいリソース グループ名を作成するか、サブスクリプションの既存のリソース グループ名を選択することができます。
   場所 | _ユーザーに最も近いリージョン_ | ユーザーとデータ リソースに最も近い場所を選択します。
   [Number of seats]\(シート数\) | 2 | シート数を入力します。 この選択は[料金](https://azure.microsoft.com/pricing/details/machine-learning/)に影響します。 シートは 2 つまで無料です。 このクイックスタートではシートを 2 つ使用します。 シート数は、必要に応じて後から Azure Portal で更新できます。
   ストレージ アカウント | "_一意の名前_" | **[新規作成]** を選択して名前を指定し、新しい Azure ストレージ アカウントを作成します。または、**[既存のものを使用]** を選択して、ドロップダウンから既存のストレージ アカウントを選択します。 このストレージ アカウントは、プロジェクト アーティファクトと実行履歴データを保持するために必要であり、そのために使用されます。 
   [Workspace for Experimentation account]\(実験アカウントのワークスペース\) | "_一意の名前_" | 新しいワークスペースの名前を入力します。 この名前で使用できるのは英数字とダッシュ文字 (-) のみであり、文字数は 2 ～ 32 文字で指定する必要があります。
   [Assign owner for the workspace]\(ワークスペースの所有者の割り当て\) | "_自分のアカウント_" | ご自身のアカウントをワークスペースの所有者として選択します。
   [Create Model Management Account]\(モデル管理アカウントを作成する\) | *check* | 実験アカウントの作成エクスペリエンスの一環として、Machine Learning モデル管理アカウントを作成するオプションも用意されています。 このリソースは、モデルをリアルタイム Web サービスとしてデプロイおよび管理する準備が整ってから使用されます。 モデル管理アカウントは実験アカウントと同時に作成することをお勧めします。
   アカウント名 | "_一意の名前_" | モデル管理アカウントを識別する一意の名前を選択します。 独自の名前を選択できます。あるいは、実験を識別するうえで最適な部門またはプロジェクトの名前を選択できます。 この名前で使用できるのは英数字とダッシュ文字 (-) のみであり、文字数は 2 ～ 32 文字で指定する必要があります。 
   [Model Management pricing tier]\(モデル管理の価格レベル\) | **DEVTEST** | **[価格レベルが選択されていません]** をクリックして、新しいモデル管理アカウントの価格レベルを指定します。 コストを抑えるために、該当するサブスクリプションで利用できる場合は **DEVTEST** 価格レベルを選択します (利用には制限があります)。DEVTEST を利用できない場合は、S1 価格レベルを選択します。 **[選択]** をクリックして、価格レベルの選択を保存します。 
   [ダッシュボードにピン留めする] | _check_ | **[ダッシュボードにピン留めする]** オプションをオンにして、Azure Portal のフロント ダッシュボード ページで Machine Learning 実験アカウントを追跡しやすくします。

5. **[作成]** をクリックして、作成プロセスを開始します。

6. Azure Portal ツール バーの右上にある **[通知]** (ベル アイコン) をクリックして、デプロイ プロセスを監視します。 

   通知に [デプロイを実行しています...] と表示されます。 デプロイが完了したら、状態が [デプロイメントに成功しました] に変わります。 成功すると、Machine Learning 実験アカウントのページが開きます。
   
   ![Azure Portal の通知](media/quickstart-installation/portal-notification.png)

次に、ローカル コンピューターで使用しているオペレーティング システムに応じて、次のどちらかのセクションに従って Azure Machine Learning Workbench をコンピューターにインストールします。 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Azure Machine Learning Workbench を Windows にインストールする
Azure Machine Learning Workbench を Windows 10、Windows Server 2016 またはそれ以降にインストールします。

1. 最新の Azure Machine Learning Workbench インストーラー **[AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)** をダウンロードします。

2. エクスプローラーで、ダウンロードしたインストーラー _AmlWorkbenchSetup.msi_ をダブルクリックします。

   >[!IMPORTANT]
   >インストーラーは、ディスク上に完全にダウンロードしてから起動します。 ブラウザーのダウンロード ウィジェットで直接起動しないようにしてください。

3. 画面に表示される指示に従って、インストールを完了します。

   インストーラーによって、必要な依存コンポーネントがすべてダウンロードされます (Python、Miniconda、その他の関連ライブラリなど)。 すべてのコンポーネントのインストールが完了するには、約 30 分かかる場合があります。 

4. Azure Machine Learning Workbench は次のディレクトリにインストールされます。
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>Azure Machine Learning Workbench を macOS にインストールする
Azure Machine Learning Workbench を、macOS Sierra が実行されているコンピューターにインストールします。

1. [Homebrew](http://brew.sh) を使用して openssl ライブラリをインストールします。 詳細については、「[Mac における .NET Core の前提条件](https://docs.microsoft.com/dotnet/core/macos-prerequisites)」を参照してください。
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. 最新の Azure Machine Learning Workbench インストーラー **[AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)** をダウンロードします。

   >[!IMPORTANT]
   >インストーラーは、ディスク上に完全にダウンロードしてから起動します。 ブラウザーのダウンロード ウィジェットで直接起動しないようにしてください。

3. Finder で、ダウンロードしたインストーラー _AmlWorkbench.dmg_ をダブルクリックします。

4. 画面に表示される指示に従って、インストールを完了します。

   インストーラーによって、必要な依存コンポーネントがすべてダウンロードされます (Python、Miniconda、その他の関連ライブラリなど)。 すべてのコンポーネントのインストールが完了するには、約 30 分かかる場合があります。 

5. Azure Machine Learning Workbench は次のディレクトリにインストールされます。 

   `/Applications/AmlWorkbench.app`

## <a name="run-azure-machine-learning-workbench-to-log-in-the-first-time"></a>Azure Machine Learning Workbench を実行して初めてログインする
1. インストール プロセスが完了したら、インストーラーの最後の画面にある **[Launch Workbench]\(Workbench の起動\)** ボタンをクリックします。 インストーラーを閉じていた場合には、デスクトップ上の Machine Learning Workbench へのショートカットと **Azure Machine Learning Workbench** という名前のスタート メニューを探して、アプリを起動します。

2. 先ほど Azure リソースのプロビジョニングに使用したのと同じアカウントで Workbench にログインします。 

3. ログイン プロセスが正常に完了したら、先ほど作成した Machine Learning 実験アカウントの検出が Workbench によって試行されます。 お使いの資格情報でアクセスできるすべての Azure サブスクリプションが検索されます。 実験アカウントが 1 つでも見つかると、Workbench によってそのアカウントが開かれます。 次に、そのアカウント内のワークスペースとプロジェクトが一覧表示されます。 

   >[!TIP]
   > 複数の実験アカウントにアクセスできる場合、Workbench アプリの左下隅にあるアバター アイコンをクリックして、異なるアカウントに切り替えることができます。

Web サービスをデプロイするための環境の作成については、[デプロイ環境の設定](deployment-setup-configuration.md)に関するページを参照してください。

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する
1. Azure ML Workbench アプリを起動してログインします。 

2. **[ファイル]** --> **[新しいプロジェクト]** の順にクリックします (または **[プロジェクト]** ウィンドウで **+** 記号をクリックします)。 

3. **[プロジェクト名]** フィールドと **[プロジェクト ディレクトリ]** フィールドに入力します。 **[プロジェクトの説明]** はオプションですが、役に立ちます。 ここでは、**[Visualstudio.com GIT Repository URL]\(Visualstudio.com Git リポジトリ URL\)** フィールドを空白のままにしておきます。 ワークスペースを選択し、**[Classifying Iris]\(あやめの分類\)** をプロジェクト テンプレートとして選択します。

   >[!TIP]
   >必要に応じて、[VSTS (Visual Studio Team Service)](https://www.visualstudio.com) プロジェクトでホストされている Git リポジトリの URL を、Git リポジトリのテキスト フィールドに入力することができます。 この Git リポジトリは既に存在している必要があるほか、master ブランチがなく空である必要があります。 また、そのリポジトリに対する書き込みアクセスが必要です。 ここで Git リポジトリを追加すると、後でローミングと共有のシナリオを有効にできます。 詳細については、[こちら](using-git-ml-project.md)を参照してください。

4. **[作成]** ボタンをクリックしてプロジェクトを作成します。 新しいプロジェクトが作成され、自動的に開きます。 この時点で、プロジェクト ホーム ページ、データ ソース、ノートブック、ソース コード ファイルを確認できます。 

    >[!TIP]
    >また、IDE (統合開発環境) のリンクを構成するだけで VS Code や他のエディターでプロジェクトを開いて、その中のプロジェクト ディレクトリを開くこともできます。 詳細については、[こちら](how-to-configure-your-IDE.md)を参照してください。 

## <a name="run-a-python-script"></a>Python スクリプトを実行する
ローカル コンピューター上でスクリプトを実行してみましょう。 

1. プロジェクトはそれぞれ別個の **[プロジェクト ダッシュボード]** ページで開きます。 アプリケーションの上部付近にあるコマンド バーの実行ボタンの左側で、`local` を実行ターゲットとして選択し、`iris_sklearn.py` を実行するスクリプトとして選択します。  このほかにも、サンプルには後で確認できるファイルが多数含まれています。 

   ![画像](media/quickstart-installation/run_control.png)

2. **[引数]** テキスト フィールドに「`0.01`」と入力します。 この数値は、正則化項を設定するためにコードで使用されます (正則化項は、線形回帰モデルをどのように訓練するかを構成するために使用される値です)。 

3. **[実行]** ボタンをクリックして、コンピューター上で `iris_sklearn.py` の実行を開始します。 

   このコードでは、一般的な Python [scikit-learn](http://scikit-learn.org/stable/index.html) ライブラリの[ロジスティック回帰](https://en.wikipedia.org/wiki/logistic_regression)アルゴリズムを使用して、モデルが構築されます。

4. まだ表示されていなければ、右側から **[ジョブ]** パネルがスライドして表示され、そのパネルに `iris_sklearn` ジョブが追加されます。 ジョブの実行が開始されると、状態が **[送信しています]** から **[実行中]** に切り替わり、数秒後に **[完了]** になります。 

5. おめでとうございます。 Azure ML Workbench で Python スクリプトを正常に実行できました。

6. 手順 2. ～ 4. を数回繰り返します。 毎回、`10` から `0.001` までの範囲で異なる引数の値を使用してください。

## <a name="view-run-history"></a>実行履歴を表示する
1. **[実行]** ビューに移動して、実行リストの **iris_sklearn.py** をクリックします。 `iris_sklearn.py` の実行履歴ダッシュボードが開きます。 そこでは、`iris_sklearn.py` で実行したすべての実行が表示されます。 

   ![画像](media/quickstart-installation/run_view.png)

2. 実行履歴ダッシュボードには、上位のメトリック、一連の既定のグラフ、各実行のメトリックの一覧も表示されます。 このビューは、構成の並べ替え、フィルター処理、調整を行ってカスタマイズできます。そのためには、構成アイコンまたはフィルター アイコンをクリックします。

   ![画像](media/quickstart-installation/run_dashboard.png)

3. 完了した実行をクリックすると、その特定の実行に関する詳細なビュー (追加のメトリック、生成されたファイル、役に立つ可能性があるその他のログなど) を確認できます。

## <a name="next-steps"></a>次のステップ
これで、Azure Machine Learning 実験アカウントと Azure Machine Learning モデル管理アカウントを正常に作成できました。 また、Azure Machine Learning Workbench デスクトップ アプリとコマンド ライン インターフェイスをインストールしました。 さらに、新しいプロジェクトを作成し、スクリプトを実行してモデルを作成してから、そのスクリプトの実行履歴を確認しました。

あやめのモデルを Web サービスとしてデプロイする方法など、このワークフローをさらに深く体験したい場合には、あやめの分類のチュートリアルを最後まで進めてください。このチュートリアルでは、[データの準備](tutorial-classifying-iris-part-1.md)、[実験](tutorial-classifying-iris-part-2.md)、[モデル管理](tutorial-classifying-iris-part-3.md)の詳細な手順を説明しています。 

> [!div class="nextstepaction"]
> [あやめの分類のチュートリアル](tutorial-classifying-iris-part-1.md)
