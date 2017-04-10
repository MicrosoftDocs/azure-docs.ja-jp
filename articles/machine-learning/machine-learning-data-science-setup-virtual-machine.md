---
title: "仮想マシンを IPython Notebook サーバーとしてセットアップする | Microsoft Docs"
description: "高度な分析のために、IPython Server を含むデータ サイエンス環境で使用できるように Azure 仮想マシンをセットアップします。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 818617c1-048e-49c2-b941-f9a983f93998
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: xibingao;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 5fb930cb71fe008ba63d2068bb36643f40259e76
ms.lasthandoff: 03/29/2017


---
# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>高度な分析のために Azure 仮想マシンを IPython Notebook サーバーとしてセットアップする
このトピックでは、高度な分析のためにデータ サイエンス環境の一部として使える Azure 仮想マシンをプロビジョニングし、構成する方法について説明します。 IPython Notebook、Azure ストレージ エクスプローラー、AzCopy などのサポート ツールと、高度な分析プロジェクトに役立つ他のユーティリティで Windows 仮想マシンを構成します。 たとえば Azure ストレージ エクスプローラーおよび AzCopy には、ローカル マシンから Azure BLOB ストレージにデータをアップロードしたり、BLOB ストレージからローカル マシンにデータをダウンロードしたりするための便利な機能が備わっています。

## <a name="create-vm"></a>手順 1: 汎用の Azure 仮想マシンを作成する
Azure 仮想マシンが既にあり、IPython Notebook サーバーをセットアップするだけの場合は、この手順を省略し、「 [手順 2： IPython Notebook 用のエンドポイントを既存の仮想マシンに追加する](#add-endpoint)」に進んでかまいません。

Azure で仮想マシンを作成するプロセスを始める前に、プロジェクトのデータ処理に必要なマシンのサイズを決定する必要があります。 小さいマシンは大きいマシンに比べてメモリの量と CPU コア数が少ないですが、より安価でもあります。 マシンの種類と価格については、「<a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Virtual Machines 価格</a>」ページをご覧ください。

1. <a href="https://manage.windowsazure.com" target="_blank">Azure クラシック ポータル</a>にログインし、左下隅にある **[新規]** をクリックします。 ウィンドウがポップアップ表示されます。 **[Compute]** -> **[仮想マシン]** -> **[ギャラリーから]** の順に選択します。
   
    ![ワークスペースの作成][24]
2. 次のいずれかのイメージを選択します。
   
   * Windows Server 2012 R2 Datacenter
   * Windows Server Essentials エクスペリエンス (Windows Server 2012 R2)
     
     その後、右下にある右向きの矢印をクリックして、次の構成ページに進みます。
     
     ![Create workspace][25]
3. 作成する仮想マシンの名前を入力し、マシンで処理するデータのサイズおよびマシンの想定機能 (メモリ サイズと CPU コア数) に基づいてマシンのサイズ (既定: A3) を選択し、マシンのユーザー名とパスワードを入力します。 その後、右向きの矢印をクリックして、次の構成ページに進みます。
   
    ![ワークスペースの作成][26]
4. この仮想マシンに使用する予定の **[ストレージ アカウント]** を含む **[リージョン/アフィニティ グループ/仮想ネットワーク]** を選択し、そのストレージ アカウントを選択します。 下部にある **[エンドポイント]** フィールドで、エンドポイントの名前 (ここでは「IPython」) を入力してエンドポイントを追加します。 エンドポイントの **[名前]** として任意の文字列を選択し、**[パブリック ポート]** として**使用可能な** 0 から 65536 までの任意の整数を選択できます。 **[プライベート ポート]** は **9999** でなければなりません。 インターネット サービスに既に割り当てられているパブリック ポートの使用は**避ける**必要があります。 <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">インターネット サービス用のポート</a>に関するページに、使用しないようにする必要がある割り当て済みのポートの一覧があります。
   
    ![ワークスペースの作成][27]
5. 仮想マシンのプロビジョニング プロセスを開始するには、チェック マークをクリックします。
   
    ![Create workspace][28]

仮想マシンのプロビジョニング プロセスが完了するには、15 ～ 25 分かかる場合があります。 仮想マシンが作成されると、このマシンの状態が **[実行中]**と表示されます。

![Create workspace][29]

## <a name="add-endpoint"></a>手順 2: IPython Notebook 用のエンドポイントを既存の仮想マシンに追加する
「手順 1」の指示に従って仮想マシンを作成した場合、IPython Notebook 用のエンドポイントは既に作成されており、このステップを省略できます。

仮想マシンが既に存在するときに、次の「手順 3」でインストールする IPython Notebook 用のエンドポイントを追加する必要がある場合は、まず Azure クラシック ポータルにログインし、仮想マシンを選択して、IPython Notebook サーバー用のエンドポイントを追加します。 次の図は、IPython Notebook 用のエンドポイントを Windows 仮想マシンに追加した後のポータルのスクリーン ショットを示しています。

![Create workspace][17]

## <a name="run-commands"></a>手順 3: IPython Notebook とその他のサポート ツールをインストールする
仮想マシンの作成後、リモート デスクトップ プロトコル (RDP) を使用して Windows 仮想マシンにログオンします。 詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](../virtual-machines/windows/classic/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。 **管理者**として (**Powershell コマンド ウィンドウではなく**) **コマンド プロンプト**を開き、次のコマンドを実行します。

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

インストールが完了すると、*C:\\Users\\\<ユーザー名\>\\Documents\\IPython Notebooks* ディレクトリ内で IPython Notebook サーバーが自動的に起動します。

メッセージが表示されたら、IPython Notebook のパスワードと、マシン管理者のパスワードを入力します。 これにより、IPython Notebook をマシン上のサービスとして実行させることができます。

## <a name="access"></a>手順 4: Web ブラウザーから IPython Notebook にアクセスする
IPython Notebook サーバーにアクセスするには、Web ブラウザーを開き、URL テキスト ボックスに「*https://&#60;仮想マシンの DNS 名>:&#60;パブリック ポート番号>*」と入力します。 ここでは、&#60;*パブリック ポート番号*> として、IPython Notebook エンドポイントの追加時に指定したポート番号を入力してください。

*&#60;仮想マシンの DNS 名>* は、Azure クラシック ポータルで確認できます。 クラシック ポータルにログインしたら、**[仮想マシン]** をクリックし、作成済みのマシンを選択して **[ダッシュボード]** を選択すると、DNS 名が次のように表示されます。

![ワークスペースの作成][19]

次の図のように、"*この Web サイトのセキュリティ証明書には問題があります*" (Internet Explorer) または "*この接続ではプライバシーが保護されません*" (Chrome) という警告が表示されます。 Internet Explorer の場合は **[このサイトの閲覧を続行する (推奨されません)]**、Chrome の場合は **[詳細設定]** の **[&#60;*DNS 名*> にアクセスする (安全ではありません)]** をクリックして続行します。 次に、既に指定したパスワードを入力して IPython Notebook にアクセスします。

**Internet Explorer:**
![ワークスペースを作成します][20]

**Chrome:**
![ワークスペースを作成します][21]

IPython Notebook にログオンした後、 *DataScienceSamples* ディレクトリがブラウザーに表示されます。 このディレクトリには、ユーザーがデータ サイエンス タスクを実行するうえで役立つ、Microsoft が共有するサンプル IPython Notebook が含まれています。 これらのサンプル IPython Notebook は、IPython Notebook サーバーのセットアップ プロセス中に [**GitHub リポジトリ**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)から仮想マシンにチェックアウトされます。 Microsoft はこのリポジトリを保守し、頻繁に更新しています。 ユーザーは GitHub リポジトリを参照して、最近更新されたサンプル IPython Notebook を入手できます。
![Create workspace][18]

## <a name="upload"></a>手順 5: 既存の IPython Notebook をローカル マシンから IPython Notebook サーバーにアップロードする
IPython Notebook を使用すると、ユーザーはローカル マシン上の既存の IPython Notebook を仮想マシン上の IPython Notebook サーバーに簡単にアップロードできます。 Web ブラウザーで IPython Notebook にログオンした後、IPython Notebook のアップロード先となる**ディレクトリ**にクリックして移動します。 次に、 **[ファイル エクスプローラー]**で、ローカル マシンからアップロードする IPython Notebook の .ipynb ファイルを選択し、Web ブラウザーの IPython Notebook ディレクトリにドラッグ アンド ドロップします。 **[アップロード]** ボタンをクリックすると、.ipynb ファイルが IPython Notebook サーバーにアップロードされます。 その後、他のユーザーは自分の Web ブラウザーからそれを使用できるようになります。

![Create workspace][22]

![ワークスペースの作成][23]

## <a name="shutdown"></a>未使用時に仮想マシンをシャットダウンして割り当てを解除する
Azure Virtual Machines の料金は **従量課金制**です。 仮想マシンを使用しないときに課金されないようにするには、未使用時に **[停止 (割り当て解除)]** 状態にする必要があります。

> [!NOTE]
> (Windows 電源オプションを使用して) 仮想マシン内部から VM をシャット ダウンすると、その VM は停止しますが、割り当てられた状態のままになります。 継続的に課金されないようにするには、常に [Azure クラシック ポータル](http://manage.windowsazure.com/)から仮想マシンを停止してください。 また、Powershell で VM を停止することもできます。その際、**ShutdownRoleOperation** 呼び出しで "PostShutdownAction" を "StoppedDeallocated" にしてください。
> 
> 

仮想マシンをシャット ダウンして割り当て解除するには、次のようにします。

1. アカウントを使用して [Azure クラシック ポータル](http://manage.windowsazure.com/) にログインします。  
2. 左側のナビゲーション バーから **[仮想マシン]** を選択します。
3. 仮想マシンの一覧で、仮想マシンの名前をクリックして **[ダッシュボード]** ページに移動します。
4. ページの下部にある **[シャット ダウン]**をクリックします。

![VM シャットダウン][15]

仮想マシンの割り当てが解除されますが、削除はされません。 Azure クラシック ポータルから、いつでも仮想マシンを再起動することができます。

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Azure VM を使用する準備ができました。次のステップは ...
これで、仮想マシンをデータ サイエンス演習で使用する準備ができました。 また、仮想マシンを IPython Notebook サーバーとして使用し、データの探索と処理など、Azure Machine Learning および Team Data Science Process に関連するタスクを行う準備もできました。

Team Data Science Process の次のステップは、[ラーニング パス](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)に示されています。HDInsight にデータを移動し、Azure Machine Learning でデータの情報を取得する準備としてデータを処理してサンプリングする手順などがあります。

[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png

