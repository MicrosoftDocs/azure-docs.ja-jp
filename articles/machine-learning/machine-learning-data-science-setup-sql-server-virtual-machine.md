---
title: "SQL Server 仮想マシンを IPython Notebook サーバーとして設定する | Microsoft Docs"
description: "SQL Server および IPython Server のあるデータ サイエンス仮想マシンを設定します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1fd6014a-d180-4558-b4eb-d9b5a331a99f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: xibingao;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 497b683b1058e134c3c79dbc8c8a119ff20e330b
ms.lasthandoff: 03/25/2017


---
# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>高度な分析のために Azure SQL Server 仮想マシンを IPython Notebook サーバーとして設定する
このトピックでは、クラウド ベースのデータ サイエンス環境の一部として使われる SQL Server 仮想マシンをプロビジョニングおよび構成する方法について説明します。 IPython Notebook、Azure ストレージ エクスプローラー、AzCopy などのサポート ツールに加えて、データ サイエンス プロジェクトに役立つ他のユーティリティのある Windows 仮想マシンを構成します。 たとえば Azure ストレージ エクスプローラーおよび AzCopy には、ローカル マシンから Azure BLOB ストレージにデータをアップロードしたり、BLOB ストレージからローカル マシンにデータをダウンロードしたりするための便利な機能が備わっています。

Azure の仮想マシン イメージ ギャラリーには、Microsoft SQL Server を含むイメージがいくつか用意されています。 実際のデータ要件に適した SQL Server VM イメージを選択してください。 推奨されるイメージは、次のとおりです。

* SQL Server 2012 SP2 Enterprise (小規模および中規模のデータ サイズ向け)
* SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads (大規模、または非常に大規模なデータ サイズ向け)
  
  > [!NOTE]
  > SQL Server 2012 SP2 Enterprise イメージには **データ ディスクが含まれていません**。 データを格納するために 1 つ以上の仮想ハード ディスクを追加またはアタッチする必要があります。 Azure の仮想マシンを作成する場合は、オペレーティング システムをディスクの C ドライブにマップし、一時ディスクを D ドライブにマップします。 データの格納に D ドライブを使用しないでください。 名前が示すとおり、D ドライブは一時的なストレージのみを提供します。 Azure Storage に配置されていないため、冗長性やバックアップは提供しません。
  > 
  > 

## <a name="Provision"></a>Azure クラシック ポータルに接続し、SQL Server 仮想マシンをプロビジョニングする
1. アカウントを使用して [Azure クラシック ポータル](http://manage.windowsazure.com/) にログインします。
   Azure アカウントを持っていない場合は、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)にアクセスしてください。
2. Azure クラシック ポータルで、Web ページの左下にある **[+ 新規]** をクリックし、**[コンピューティング]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。
3. **[仮想マシンの作成]** ページで、実際のデータ要件に応じた SQL Server を含む仮想マシン イメージを 1 つ選択し、ページの右下にある次へ進む矢印をクリックします。 Azure でサポートされる SQL Server イメージの最新情報については、「[Azure Virtual Machines における SQL Server](http://go.microsoft.com/fwlink/p/?LinkId=294719)」ドキュメント セットの「[Azure Virtual Machines における SQL Server の概要](http://go.microsoft.com/fwlink/p/?LinkId=294720)」トピックを参照してください。
   
   ![SQL Server VM の選択][1]
4. 最初の **[仮想マシンの構成]** ページで、次の情報を指定します。
   
   * **[仮想マシン名]**に入力します。
   * **[新しいユーザー名]** ボックスに、VM ローカル管理者アカウントの一意のユーザー名を入力します。
   * **[新しいパスワード]** ボックスに、強力なパスワードを入力します。 詳細については、「 [強力なパスワード](http://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。
   * **[パスワードの確認]** ボックスに、パスワードを再度入力します。
   * **[サイズ]** ドロップダウン リストで、適切なサイズを選択します。
     
     > [!NOTE]
     > 仮想マシンのサイズは、プロビジョニング中に指定します。運用環境のワークロードで推奨される最小サイズは A2 です。 SQL Server Enterprise Edition の使用時に推奨される仮想マシンの最小サイズは A3 です。 SQL Server Enterprise Edition を使用する場合は、A3 以上を選択してください。 SQL Server 2012 または 2014 Enterprise Optimized for Transactional Workloads イメージを使用する場合は A4 を選択してください。
     > SQL Server 2012 または 2014 Enterprise Optimized for Data Warehousing Workloads イメージを使用する場合は、A7 を選択してください。 選択したサイズにより、構成できるデータ ディスクの数が制限されます。 使用可能な仮想マシンのサイズと仮想マシンに接続できるデータ ディスクの数に関する最新情報については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。 価格については、「[VIrtual Macines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)」を参照してください。
     > 
     > 
   
   右下にある次へ進む矢印をクリックして続行します。
   
   ![VM 構成][2]
5. 2 回目の **[仮想マシンの構成]** ページで、ネットワーク、ストレージ、可用性に関するリソースを構成します。
   
   * **[クラウド サービス]** ボックスで、**[新しいクラウド サービスの作成]** を選択します。
   * **[クラウド サービス DNS 名]** ボックスに、目的の DNS 名の最初の部分を入力すると、**TESTNAME.cloudapp.net** という形式の完全な名前を指定できます。
   * **[リージョン/アフィニティ グループ/仮想ネットワーク]** ボックスで、この仮想イメージをホストするリージョンを選択します。
   * **[ストレージ アカウント]**で、既存のストレージ アカウントか、自動的に生成されたストレージ アカウントを選択します。
   * **[可用性セット]** ボックスの一覧の **[(なし)]** を選択します。
   * 価格に関する情報を読んで同意します。
6. **[エンドポイント]** セクションで、**[名前]** の下で空のドロップダウンをクリックし、**[MSSQL]** を選択し、データベース エンジンのインスタンスのポート番号を入力します (既定のインスタンスの場合は「**1433**」です)。
7. また、SQL Server VM を、後の手順で構成される IPython Notebook Server として機能させることもできます。
   IPython Notebook サーバー用に使われるポートを指定するために新しいエンドポイントを追加してください。 **[名前]** 列に名前を入力し、パブリック ポート用に任意のポート番号、プライベート ポート用に 9999 をそれぞれ選択します。
   
   右下にある次へ進む矢印をクリックして続行します。
   
   ![MSSQL ポートと IPython のポートの選択][3]
8. 既定の **[VM エージェントのインストール]** オプションをオンにしたままで、ウィザードの右下隅にあるチェック マークをクリックして、VM プロビジョニング手順を完了します。
   
   `![VM 最終オプション][4]
9. Azure によって仮想マシンの準備が行われるまで待ちます。 仮想マシンの状態は次のように進行します。
   
   * 開始中 (プロビジョニング)
   * 停止済み
   * 開始中 (プロビジョニング)
   * 実行中 (プロビジョニング)
   * 実行中

## <a name="RemoteDesktop"></a>リモート デスクトップを使用して仮想マシンを開き、セットアップを完了する
1. プロビジョニングが完了したら、仮想マシンの名前をクリックして [ダッシュボード] ページに移動します。 ページの下部にある **[接続]**をクリックします。
2. Windows リモート デスクトップ プログラム (`%windir%\system32\mstsc.exe`) を使用して rpd ファイルを開くよう選択します。
3. **[Windows セキュリティ]** ダイアログ ボックスで、先ほど指定したローカル管理者アカウントのパスワードを入力します
   (仮想マシンの資格情報を確認するように求められることがあります)。
4. この仮想マシンに初めてログオンした場合は、デスクトップのセットアップや Windows の更新プログラムの適用、Windows の初期構成タスク (sysprep) の完了など複数のプロセスが必要になる場合があります。 Windows sysprep の完了後、SQL Server セットアップによって構成タスクが完了されます。 これらのタスクが完了するまで、数分かかることがあります。 `SELECT @@SERVERNAME` は正しい名前を返さず、SQL Server Management Studio がスタート ページに表示されないことがあります。

Windows リモート デスクトップで仮想マシンに接続したら、仮想マシンは他のコンピューターと同様に使用できます。 SQL Server Management Studio (仮想マシン上で実行) を使用して、通常どおりに SQL Server の既定インスタンスに接続します。

## <a name="InstallIPython"></a>IPython Notebook とその他のサポート ツールをインストールする
IPython Notebook サーバーとして機能するよう新しい SQL Server VM を構成し、AzCopy、Azure ストレージ エクスプローラー、便利な Data Science Python パッケージその他のサポート ツールを追加でインストールできるよう、特別なカスタマイズ スクリプトが用意されています。 インストールするには、次のようにします。

* Windows の **[スタート]** アイコンを右クリックし、**[コマンド プロンプト (管理者)]** をクリックします。
* 次のコマンドをコピーして、コマンド プロンプトに貼り付けます。
  
        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
* メッセージが表示されたら、IPython Notebook サーバーの適切なパスワードを入力します。
* カスタマイズ スクリプトは、次に示すようなインストール後の手順を自動化します。
  * IPython Notebook サーバーのインストールとセットアップ
  * 既に作成した次のエンドポイント用に Windows ファイアウォールで TCP ポートを開く
  * SQL Server リモート接続用
  * IPython Notebook サーバーのリモート接続用
  * サンプルの IPython ノートブックおよび SQL スクリプトを取得する
  * 便利な Data Science Python パッケージをダウンロードしてインストールする
  * AzCopy や Azure ストレージ エクスプローラーなどの Azure ツールをダウンロードしてインストールする   
    <br>
* 任意のローカルまたはリモート ブラウザーから、URL 形式 `https://<virtual_machine_DNS_name>:<port>`を使用して IPython Notebook にアクセスし実行することができます (ポートは仮想マシンのプロビジョニング中に選択した IPython パブリック ポート)。
* IPython Notebook サーバーはバックグラウンド サービスとして実行され、仮想マシンの再起動時に自動的に再起動されます。

## <a name="Optional"></a>必要に応じて、データ ディスクをアタッチする
データ ディスク、つまり C ドライブ (OS ディスク) と D ドライブ (一時的なディスク) 以外のディスクが VM イメージに含まれない場合、データを格納するために 1 つ以上のデータ ディスクを追加する必要があります。 SQL Server 2012 SP2 Enterprise Optimized for DataWarehousing Workloads 用の VM イメージは、SQL Server データおよびログ ファイル用の追加のディスク付きで事前構成されています。

> [!NOTE]
> データの格納に D ドライブを使用しないでください。 名前が示すとおり、D ドライブは一時的なストレージのみを提供します。 Azure Storage に配置されていないため、冗長性やバックアップは提供しません。
> 
> 

追加のデータ ディスクをアタッチするには、「[Windows 仮想マシンにデータ ディスクをアタッチする方法](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」で説明されている次のような手順に従います。

1. 前の手順でプロビジョニングされた仮想マシンに 1 つ以上の空のディスクをアタッチする
2. 仮想マシンでの新しいディスクの初期化

## <a name="SSMS"></a>SQL Server Management Studio に接続し、混合モード認証を有効にする
ドメイン環境がない場合、SQL Server Database エンジンで Windows 認証を使用することはできません。 別のコンピューターからデータベース エンジンに接続するには、混合モード認証用に SQL Server を構成します。 混合モード認証では、SQL Server 認証と Windows 認証の両方が許可されます データのインポート モジュールを使用して [Azure Machine Learning Studio](https://studio.azureml.net) で SQL Server VM データベースからデータを直接取り込むには、SQL 認証モードが必要です。

1. リモート デスクトップを使用して仮想マシンに接続している間に、Windows **[検索]** ウィンドウを使用して「**SQL Server Management Studio** (SMSS)」と入力します。 クリックして SQL Server Management Studio (SSMS) を開始します。 SSMS のショートカットをデスクトップ上に作成して、将来使用することができます。
   
   ![SSMS を起動する][5]
   
   初めて Management Studio を開く場合は、ユーザーの Management Studio 環境の作成が必要になります。 これには数分かかることがあります。
2. Management Studio が開くと、**[サーバーへの接続]** ダイアログ ボックスが表示されます。 **[サーバー名]** ボックスに、オブジェクト エクスプローラーを使用してデータベース エンジンに接続する仮想マシンの名前を入力します
   (**[サーバー名]** として、仮想マシン名の代わりに **[(ローカル)]** または単一のピリオドを指定することもできます)。 **[Windows 認証]** を選択し、**[ユーザー名]** ボックスの **[*your\_VM\_name*\\your\_local\_administrator]** はそのままにしておきます。 **[接続]**をクリックします。
   
   ![サーバーへの接続][6]
   
   <br>
   
   > [!TIP]
   > Windows レジストリ キーの変更を使用するか、SQL Server Management Studio を使用して、SQL Server 認証モードを変更することができます。 レジストリ キーの変更を使用して認証モードを変更するには、 **[新しいクエリ]** を開始して、次のスクリプトを実行します。
   > 
   > 
   
       USE master
       go
   
       EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
       go

    SQL Server Management Studio を使用して認証モードを変更するには、次のようにします。

1. **SQL Server Management Studio のオブジェクト エクスプローラー**で、SQL Server のインスタンス名 (仮想マシン名) を右クリックし、**[プロパティ]** をクリックします。
   
   ![サーバー プロパティ][7]
2. **[セキュリティ]** ページの **[サーバー認証]** で、**[SQL Server 認証モードと Windows 認証モード]** を選択し、**[OK]** をクリックします。
   
   ![認証モードを選択する][8]
3. **[SQL Server Management Studio]** ダイアログ ボックスで、SQL Server の再起動が必要であるというメッセージに対して **[OK]** をクリックします。
4. **オブジェクト エクスプローラー**でサーバーを右クリックし、**[再起動]** をクリックします (実行中であれば、SQL Server エージェントも再起動する必要があります)。
   
   ![再起動][9]
5. **[SQL Server Management Studio]** ダイアログ ボックスで、SQL Server の再起動に同意を求めるメッセージに対して **[はい]** をクリックします。

## <a name="Logins"></a>SQL Server 認証ログインを作成する
別のコンピューターからデータベース エンジンに接続するには、1 つ以上の SQL Server 認証ログインを作成する必要があります。  

新しい SQL Server ログインをプログラムによって作成することも、SQL Server Management Studio を使って作成することもできます。 SQL 認証の新しい sysadmin ユーザーをプログラムで作成するには、 **[新規クエリ]** を開始し、次のスクリプトを実行します。 <new user name\> と <new password\> を任意の <*ユーザー名*> と <*パスワード*> に置き換えます。 

    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


必要に応じてパスワード ポリシーを調整します (サンプル コードではポリシー検査とパスワード期限設定がオフになっています)。 SQL Server のログインの詳細については、「 [ログインの作成](http://msdn.microsoft.com/library/aa337562.aspx)」を参照してください。  

SQL Server Management Studio を使用して新しい SQL Server ログインを作成するには、次のようにします。

1. **SQL Server Management Studio のオブジェクト エクスプローラー**で、新しいログインを作成するサーバー インスタンスのフォルダーを展開します。
2. **[セキュリティ]** フォルダーを右クリックし、**[新規作成]** をポイントして、**[ログイン…]** を選択します。
   
   ![新しいログイン][10]
3. **[ログイン - 新規作成]** ダイアログ ボックスの **[全般]** ページで、新しいユーザーの名前を **[ログイン名]** ボックスに入力します。
4. **[SQL Server 認証]**を選択します。
5. **[パスワード]** ボックスに、新しいユーザーのパスワードを入力します。 **[パスワードの確認]** ボックスに、パスワードを再度入力します。
6. パスワードの強制と複雑さに関するパスワード ポリシー オプションを適用するには、 **[パスワード ポリシーを適用する]** (推奨) をオンにします。 [SQL Server 認証] が選択されている場合、これは既定のオプションです。
7. 失効に関するパスワード ポリシー オプションを適用するには、 **[パスワードの期限を適用する]** (推奨) をオンにします。 このチェック ボックスをオンにする場合は、[パスワード ポリシーを適用する] がオンになっている必要があります。 [SQL Server 認証] が選択されている場合、これは既定のオプションです。
8. ログインの初回使用後に新しいパスワードの作成をユーザーに強制するには、 **[ユーザーは次回ログイン時にパスワードを変更する]** をオンにします (このログインが別のユーザー用の場合は、このチェック ボックスをオンにすることをお勧めします。 ログインが自分用の場合は、オンにしないでください)。このチェック ボックスをオンにする場合は、[パスワードの期限を適用する] がオンになっている必要があります。 [SQL Server 認証] が選択されている場合、これは既定のオプションです。
9. **[既定のデータベース]** ボックスの一覧から、ログインの既定のデータベースを選択します。 **master** です。 ユーザー データベースをまだ作成していない場合は、 **master**のままにしておきます。
10. **[既定の言語]** の値は、**[既定]** のままにしておきます。
    
    ![ログインのプロパティ][11]
11. これが初めて作成するログインである場合は、このログインを SQL Server 管理者専用に使用することが考えられます。 その場合は、**[サーバー ロール]** ページで、**[sysadmin]** をオンにします。
    
    > [!IMPORTANT]
    > sysadmin 固定サーバー ロールのメンバーには、データベース エンジンに対する完全な制御権が与えられています。 セキュリティ上の理由から、このロールのメンバーシップを慎重に制限してください。
    > 
    > 
    
    ![[sysadmin]][12]
12. [OK] をクリックします。

## <a name="DNS"></a>仮想マシンの DNS 名を特定する
別のコンピューターから SQL Server Database エンジンに接続するには、仮想マシンのドメイン ネーム システム (DNS) 名が必要になります

(これは、仮想マシンを識別するためにインターネットで使用される名前です。 IP アドレスを使用することもできますが、Azure で冗長化またはメンテナンスのためにリソースが移動された場合、IP アドレスは変わる可能性があります。 DNS 名は、新しい IP アドレスにリダイレクトできるため、安定しています)。

1. Azure クラシック ポータルで (または前の手順から)、 **[仮想マシン]**を選択します。
2. **[仮想マシン インスタンス]** ページの **[DNS 名]** 列で、**http://** から始まる仮想マシンの DNS 名を探してコピーします (名前全体がユーザー インターフェイスに表示されないことがありますが、右クリックしてコピーすることができます)。

## <a name="cde"></a>別のコンピューターからデータベース エンジンに接続する
1. インターネットに接続されたコンピューターで、SQL Server Management Studio を開きます。
2. **[サーバーへの接続]** または **[データベース エンジンへの接続]** ダイアログ ボックスで、**[サーバー名]** ボックスに、先のタスクで決定した仮想マシンの DNS 名とパブリック エンドポイントのポート番号を "*DNS 名,ポート番号*" という形式で入力します (例: **tutorialtestVM.cloudapp.net,57500**)。
3. **[認証]** ボックスで、**[SQL Server 認証]** を選択します。
4. **[ログイン]** ボックスに、前のタスクで作成したログインの名前を入力します。
5. **[パスワード]** ボックスに、前のタスクで作成したログインのパスワードを入力します。
6. **[接続]**をクリックします。

## <a name="amlconnect"></a>Azure Machine Learning からデータベース エンジンに接続します
この後の Team Data Science Process 手順で、 [Azure Machine Learning Studio](https://studio.azureml.net) を使用して機械学習モデルを構築し、デプロイします。 トレーニングまたはスコアリングのために SQL Server VM データベースから Azure Machine Learning にデータを直接取り込むには、 **Azure Machine Learning Studio** の新しい実験で [データのインポート](https://studio.azureml.net) モジュールを使用します。 このトピックの詳細は、Team Data Science Process のガイド リンクに記載されています。 概要については、「 [Azure Machine Learning Studio とは](machine-learning-what-is-ml-studio.md)」を参照してください。

1. [データのインポート モジュール](https://msdn.microsoft.com/library/azure/dn905997.aspx)の **[プロパティ]** ウィンドウで、**[データ ソース]** ドロップダウン リストから **[Azure SQL Database]** を選択します。
2. **[データベース サーバー名]** テキスト ボックスに、「`tcp:<DNS name of your virtual machine>,1433`」と入力します。
3. **[サーバーのユーザー アカウント名]** テキスト ボックスに SQL ユーザー名を入力します。
4. **[サーバーのユーザー アカウント パスワード]** テキスト ボックスに SQL ユーザーのパスワードを入力します。
   
   ![Azure Machine Learning の [データのインポート]][13]

## <a name="shutdown"></a>使用されていないときは仮想マシンをシャット ダウンし割り当てを解除する
Azure Virtual Machines の料金は **従量課金制**です。 仮想マシンを使用しないときに課金されないようにするには、 **[停止 (割り当て解除)]** 状態にする必要があります。

> [!NOTE]
> (Windows 電源オプションを使用して) 内部から仮想マシンをシャット ダウンすると、その VM は停止しますが、割り当てられた状態のままになります。 請求されないようにするには、必ず [Azure クラシック ポータル](http://manage.windowsazure.com/)から仮想マシンを停止してください。 また、Powershell で VM を停止することもできます。その際、ShutdownRoleOperation 呼び出しで "PostShutdownAction" を "StoppedDeallocated" にしてください。
> 
> 

仮想マシンをシャット ダウンして割り当て解除するには、次のようにします。

1. アカウントを使用して [Azure クラシック ポータル](http://manage.windowsazure.com/) にログインします。  
2. 左側のナビゲーション バーから **[仮想マシン]** を選択します。
3. 仮想マシンの一覧で、仮想マシンの名前をクリックして **[ダッシュボード]** ページに移動します。
4. ページの下部にある **[シャット ダウン]**をクリックします。

![VM シャットダウン][15]

仮想マシンの割り当てが解除されますが、削除はされません。 Azure クラシック ポータルから、いつでも仮想マシンを再起動することができます。

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Azure SQL Server VM を使用する準備ができました。次のステップは ...
これで、仮想マシンをデータ サイエンス演習で使用する準備ができました。 また、仮想マシンを IPython Notebook サーバーとして使用し、データの探索と処理など、Azure Machine Learning および Team Data Science Process (TDSP) に関連するタスクを行う準備もできました。

データ サイエンス プロセスの次のステップは、「[Team Data Science Process](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)」に示されています。HDInsight にデータを移動し、Azure Machine Learning でデータの情報を取得する準備としてデータを処理してサンプリングする手順などがあります。

[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png


