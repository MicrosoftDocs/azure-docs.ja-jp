Azure への仮想マシンの作成は、Visual Studio のサーバー エクスプローラーを使って行うことができます。

## <a name="create-an-azure-virtual-machine-in-server-explorer"></a>サーバー エクスプローラーで Azure 仮想マシンを作成する
仮想マシンは [Microsoft Azure 管理ポータル](http://go.microsoft.com/fwlink/?LinkID=253103)で作成できますが、サーバー エクスプローラーからコマンドを使用して Azure に仮想マシンを作成することもできます。 仮想マシンは、負荷分散された共通のパブリック エンドポイントの内側に置くフロント エンドなどとして使用できます。

### <a name="to-create-a-new-virtual-machine"></a>新しい仮想マシンを作成するには
1. サーバー エクスプローラーで **[Azure]** ノードを開き、**[Virtual Machines]** をクリックします。
2. コンテキスト メニューの **[仮想マシンの作成]**をクリックします。
   
    **仮想マシンの新規作成** ウィザードが表示されます。
   
    ![[仮想マシンの作成] コマンド](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718342.png)
3. **[サブスクリプションの選択]** ページで、仮想マシンを作成するときに使用するサブスクリプションを選択し、**[次へ]** をクリックします。
   
    Azure にサインインしていない場合は、 **[サインイン]** をクリックしてサインインします。 ご利用の Azure サブスクリプションをまだ選択していない場合は、ドロップダウン ボックスから選択します。
4. **[仮想マシン イメージの選択]** ページの **[イメージの種類]** ボックスの一覧でイメージの種類を選択し、**[イメージ名]** ボックスの一覧から仮想マシン イメージを選択します。 終了したら **[次へ]**をクリックします。
   
    ![[仮想マシン イメージの選択] ページ](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744137.png)
   
    以下の種類から選ぶことができます。
   
   * **[公開イメージ]** には、オペレーティング システムの仮想マシン イメージと、Windows Server や SQL Server などのサーバー ソフトウェアが一覧表示されます。
   * **[MSDN イメージ]** には、Visual Studio や Microsoft Dynamics など、MSDN のサブスクライバーが利用できるソフトウェアの仮想マシン イメージが一覧表示されます。
   * **[プライベート イメージ]** には、ある目的に特化して作成した仮想マシン イメージや汎用的な目的で作成した仮想マシン イメージが一覧表示されます。
     
     特殊化および一般化された仮想マシンについては、 [VM イメージ](https://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/)に関するページを参照してください。 構成済みの新しい仮想マシンをすばやく作成できるよう、仮想マシンをテンプレートに変換する方法については、「 [イメージとして使用する Windows 仮想マシンのキャプチャ方法](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) 」を参照してください。
     
     仮想マシン イメージ名をクリックすると、ページの右側にイメージに関する情報が表示されます。
     
     > [!NOTE]
     > **[パブリック イメージ]** または **[MSDN イメージ]** の一覧は読み取り専用なので、仮想マシン イメージを追加することはできません。 作成した仮想マシンはすべて **[プライベート イメージ]** の一覧に追加されます。
     > 
     > 
     
     Visual Studio レベルのサブスクリプションを持つ MSDN サブスクライバーの場合、Visual Studio と他のいくつかのイメージを含む事前に構築された Azure 仮想マシンを作成できます。 詳細については、[Visual Studio でのイメージを使用した仮想マシン作成と MSDN サブスクライバー向け Visual Studio 2013 ギャラリー イメージ](http://visualstudio2013msdngalleryimage.azurewebsites.net)に関するページと、[MSDN サブスクリプション](https://www.visualstudio.com/products/msdn-subscriptions-vs)に関するページを参照してください。
5. **[仮想マシンの基本設定]** ページにマシン名を入力し、サイズ、ユーザー名、パスワードなど、仮想マシンの仕様を追加します。 終了したら **[次へ]**をクリックします。
   
    リモート デスクトップを使用するマシンにログインするときに、この新しい名前とパスワードを使用するので、忘れた場合に備えて書き留めておくことをお勧めします。 Visual Studio で Azure 仮想マシンを作成した後、 [Microsoft Azure 管理ポータル](http://go.microsoft.com/fwlink/?LinkID=253103)でサイズなどの設定を変更できます。
   
   > [!NOTE]
   > 選択する仮想マシンのサイズが大きいほど、適用される料金も大きくなります。 詳細については、 [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/) のページを参照してください。
   > 
   > 
6. Visual Studio で作成した仮想マシンの場合、クラウド サービスが必要です。 **[クラウド サービスの設定]** ページで、仮想マシン用のクラウド サービスを選択します。まだクラウド サービスがない場合、または新規作成する場合は、ボックスの一覧の **[<新規作成>]** をクリックします。 また、ストレージ アカウントも必要になるため、**[ストレージ アカウント]** ボックスの一覧からストレージ アカウントを選択します (または新しいストレージ アカウントを作成します)。 詳細については、「[Microsoft Azure Storage の概要](../articles/storage/storage-introduction.md)」をご覧ください。
7. 仮想ネットワーク (省略可能) を指定する場合、[Virtual Network] ボックスと [サブネット] ボックスの一覧から選択します。
   
    仮想マシンが可用性セットのメンバーである場合、その仮想マシンは別の障害ドメインにデプロイされます。 詳細については、 [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) のページをご覧ください。
8. 仮想マシンを可用性セットに登録する場合 (これも省略可能)、 **[可用性セットの指定]** チェック ボックスをオンにし、ドロップダウン ボックスで可用性セットを選択します。 終了したら **[次へ]** をクリックします。
   
    可用性セットに仮想マシンを追加しておくと、ネットワークの障害時やローカル ハード ディスクの障害時、予定されたシステム停止時にもアプリケーションを使用できます。 [Microsoft Azure 管理ポータル](http://go.microsoft.com/fwlink/?LinkID=253103) を使用して、仮想ネットワーク、サブネット、可用性セットを作成する必要があります。 詳細については、「 [Virtual Machines の可用性管理](https://azure.microsoft.com/documentation/articles/manage-availability-virtual-machines/) 」をご覧ください。
9. **[エンドポイント]** ページで、仮想マシンのユーザーに公開するパブリック エンドポイントを指定します。 たとえば、既定で有効になるリモート デスクトップ エンドポイントと PowerShell エンドポイントに加えて、HTTP (ポート 80) を有効にすることができます。 エンドポイントを追加するには、**[ポート名]** ボックスの一覧からいずれかを選択し、**[追加]** をクリックします。 エンドポイントを削除するには、エンドポイント リストの名前の横にある赤色の **[X]** を選択します。
   
    ![仮想マシン ウィザードの [エンドポイント] ページ。](./media/virtual-machines-common-classic-create-manage-visual-studio/IC718351.png)
   
    使用できるエンドポイントは、仮想マシン用に選択したクラウド サービスによって変わります。 詳細については、 [Azure サービスのエンドポイント](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) に関するページをご覧ください。
   
   > [!NOTE]
   > パブリック エンドポイントを有効にすると、仮想マシン上のサービスがインターネットから使用できる状態となります。 仮想マシンにエンドポイントとサービスをインストールしたら、エンドポイントのアクセス制御リスト (ACL) を設定するなど、適切な構成を行ってください。 詳細については、「 [仮想マシンに対してエンドポイントを設定する方法](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) 」をご覧ください。
   > 
   > 
10. 仮想マシンの設定が完了したら、 **[作成]** ボタンをクリックして仮想マシンを作成します。
    
     Azure で仮想マシンが作成されると、 **[Azure のアクティビティ ログ]** には仮想マシンの作成操作の進行状況が表示されます。
    
     ![仮想マシンのアクティビティ ログ - 進行中。](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744138.png)
    
     仮想マシン情報のみを表示するには、**[Azure のアクティビティ ログ]** で **[Virtual Machines]** タブを選択します。
    
     ![仮想マシンのアクティビティ ログ - 完了。](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744139.png)
    
     操作が正常に完了すると、サーバー エクスプローラーの **[Virtual Machines]** ノードに新しい仮想マシンが表示されます。 **[リモート デスクトップを使用して接続]** ショートカットをクリックしてログインできます。
    
     ![サーバー エクスプ ローラーに表示された仮想マシン。](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744140.png)

## <a name="manage-your-virtual-machines"></a>仮想マシンの管理
仮想マシンの構成ページでは、選択した仮想マシンに関してシャットダウン、接続、更新、チェックポイントの追加を実行できるほか、仮想マシンの設定を表示または変更することもできます。 そのための方法は次のとおりです。

* 仮想マシンのサイズを変更する。
* 仮想マシンで使用する可用性セットを選択する。
* パブリック エンドポイントの設定を追加、削除、変更する。
* 仮想マシンの拡張機能を追加、削除、構成する。
* 仮想マシンに関連付けられているディスクの情報を表示する。

### <a name="view-or-change-virtual-machine-settings"></a>仮想マシンの設定の表示と変更
1. サーバー エクスプローラーの **Azure Virtual Machines** ノードで目的の仮想マシンを選択します。
2. ショートカット メニューで **[構成]** を選択し、仮想マシンの構成ページを表示します。
   
    ![Azure の仮想マシンの構成ページ](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744141.png)
3. 仮想マシンの情報を表示または変更します。

### <a name="save-or-restore-the-status-of-your-virtual-machine"></a>仮想マシンの状態の保存と復元
仮想マシンを構成し、ソフトウェアをインストールする際は、仮想マシンにチェックポイントを作成することによって定期的に進捗を保存しておくことをお勧めします。 チェックポイントは、仮想マシンの現在の状態のスナップショット、つまりイメージです。 仮想マシンに問題が発生したとき、または仮想マシンを再構成するときに、最初からやり直すのではなく以前のチェックポイントの状態に復元することにより時間を節約できます。

### <a name="to-create-a-virtual-machine-checkpoint"></a>仮想マシンのチェックポイントを作成するには
1. サーバー エクスプローラーの **Azure Virtual Machines** ノードで目的の仮想マシンを選択します。
2. ショートカット メニューで **[構成]** を選択し、仮想マシンの構成ページを表示します。
3. 構成ページの **[イメージのキャプチャ]** をクリックします。
   
    ![Azure の構成ページのキャプチャ ボタン](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744142.png)
   
    **[仮想マシンのキャプチャ]** ダイアログが表示されます。
   
    ![Azure の仮想マシンのキャプチャ ダイアログ ボックス](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744143.png)
4. イメージにラベルと説明を指定します。 既定のラベルと説明が付けられていますが、必要に応じて上書きすることができます。
5. この仮想マシンで既に Sysprep が実行されている場合は、 **[仮想マシンで Sysprep を実行しました]** をオンにします。
   
    Sysprep は、仮想マシンの Windows からシステム固有のデータを削除して、他の仮想マシンで利用できるテンプレートを作成することを主な目的としたツールです。 詳細については、 [テンプレートとして使用する Windows 仮想マシンのキャプチャ方法](https://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) に関するページをご覧ください。 Sysprep を実行する前に、VM をバックアップします。
6. キャプチャの設定が完了したら、 **[キャプチャ]** をクリックしてチェックポイントを作成します。
   
    Azure でチェックポイントが作成される間、 **[Azure のアクティビティ ログ]** に操作の進行状況が表示されます。
   
    ![仮想マシンのチェックポイントのキャプチャ](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744144.png)
   
    チェックポイント操作が完了すると、 **[Azure のアクティビティ ログ]**に表示されます。
   
    ![チェックポイント操作の完了](./media/virtual-machines-common-classic-create-manage-visual-studio/IC744145.png)

## <a name="to-manage-virtual-machine-checkpoints"></a>仮想マシンのチェックポイントを管理するには
### <a name="to-restore-a-virtual-machine-to-a-previously-saved-state"></a>以前に保存した状態に仮想マシンを復元するには
* 「 [Step-by-Step: Perform Cloud Restores of Microsoft Azure Virtual Machines using PowerShell - Part 2 (PowerShell で Microsoft Azure Virtual Machines のクラウド復元を実行する手順 - パート 2)](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx)」のページで概説されている手順に従います。

### <a name="to-delete-a-checkpoint"></a>チェックポイントを削除するには
1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/?LinkID=253103)に移動します。
2. 仮想マシンの構成ページの上部にある **[イメージ]** タブを選択します。
3. 削除するチェックポイントを選択し、ページの下部にある **[削除]** をクリックします。

## <a name="shut-down-your-virtual-machine"></a>仮想マシンのシャットダウン
1. サーバー エクスプローラーの **Azure Virtual Machines** ノードで、シャットダウンする仮想マシンを選択します。
2. ショートカット メニューで **[シャットダウン]** コマンドをクリックするか、**[構成]** をクリックして仮想マシンの構成ページを表示してから **[シャットダウン]** をクリックします。

## <a name="next-steps"></a>次のステップ
仮想マシンの作成の詳細については、[Linux を実行する仮想マシンの作成](../articles/virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページおよび [Azure プレビュー ポータルでの Windows を実行する仮想マシンの作成](../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

