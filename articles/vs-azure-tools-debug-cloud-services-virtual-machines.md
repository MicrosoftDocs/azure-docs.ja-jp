---
title: "Visual Studio での Azure クラウド サービスまたは仮想マシンのデバッグ | Microsoft Docs"
description: "Visual Studio でのクラウド サービスまたは仮想マシンのデバッグ"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 01623fa76175091439d5a571fb8b8f96aee01c4c
ms.openlocfilehash: dd9019a6feb86d90fc6219b06bf1e69da4c2b18f


---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>Visual Studio での Azure クラウド サービスまたは仮想マシンのデバッグ
Visual Studio には、Azure クラウド サービスと仮想マシンのデバッグに役立つさまざまなオプションがあります。

## <a name="debug-your-cloud-service-on-your-local-computer"></a>ローカル コンピューターでクラウド サービスをデバッグする
Azure コンピューティング エミュレーターを使用してローカル コンピューターでクラウド サービスをデバッグすれば、時間とコストの節約になります。 サービスをデプロイする前にローカルでデバッグすると、コンピューティング時間の料金を支払うことなく、信頼性とパフォーマンスを改善できます。 ただし、一部には Azure でクラウド サービスを実行した場合にのみ発生するエラーもあります。 サービスを発行し、デバッガーをロール インスタンスにアタッチするときに、リモート デバッグを有効にすると、このようなエラーをデバッグできます。

エミュレーターは、Azure コンピューティング サービスをシミュレートし、ローカル環境で動作するので、クラウド サービスのテストとデバッグを行ってからデプロイすることができます。 エミュレーターでは、ロール インスタンスのライフサイクルが処理され、ローカル ストレージなどのシミュレートされるリソースにアクセスできます。 Visual Studio でサービスをデバッグまたは実行すると、エミュレーターがバックグラウンド アプリケーションとして自動的に起動され、サービスがエミュレーターにデプロイされます。 エミュレーターを使用すると、ローカル環境で実行されているサービスを表示できます。 完全バージョンまたは Express バージョンのエミュレーターを実行できます (Azure 2.3 以降は、Express バージョンのエミュレーターが既定です)。「[Emulator Express を使用したローカルでのクラウド サービス実行とデバッグ](https://msdn.microsoft.com/library/dn339018.aspx)」を参照してください。

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>ローカル コンピューターでクラウド サービスをデバッグするには
1. メニュー バーで、**[デバッグ]**、**[デバッグの開始]** の順に選択し、Azure クラウド サービス プロジェクトを実行します。 または、F5 キーを押します。 コンピューティング エミュレーターが起動することを示すメッセージが表示されます。 エミュレーターが起動すると、システム トレイ アイコンでそのことを確認できます。

    ![Azure emulator in the system tray](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)
2. 通知領域にある Azure アイコンのショートカット メニューを開いて **[コンピューティング エミュレーターの UI を表示]**を選択し、コンピューティング エミュレーターのユーザー インターフェイスを表示します。

    UI の左側にあるウィンドウには、現在コンピューティング エミュレーターにデプロイされているサービスと各サービスが実行しているロール インスタンスが表示されます。 サービスまたはロールを選択すると、右ペインにライフサイクル情報、ログ情報、および診断情報を表示できます。 含まれているウィンドウの上部余白にフォーカスを置くと、ウィンドウが右ペイン全体に拡大します。
3. **[デバッグ]** メニューのコマンドを選択し、コードにブレークポイントを設定して、アプリケーションをステップ実行します。 デバッガーでのアプリケーションのステップ実行に合わせてウィンドウが更新され、アプリケーションの現在の状態が表示されます。 デバッグを停止すると、アプリケーションのデプロイは削除されます。 アプリケーションに Web ロールが含まれていて、スタートアップ アクション プロパティで Web ブラウザーを起動するように設定している場合は、Visual Studio によってブラウザーで Web アプリケーションが起動されます。 サービス構成でロールのインスタンス数を変更する場合は、ロールのこれらの新しいインスタンスをデバッグできるように、クラウド サービスを停止してからデバッグを再開する必要があります。

    **注:** サービスの実行またはデバッグを停止しても、ローカルのコンピューティング エミュレーターとストレージ エミュレーターは停止しません。 これらは、通知領域から明示的に停止する必要があります。

## <a name="debug-a-cloud-service-in-azure"></a>Azure でクラウド サービスをデバッグする
リモート コンピューターからクラウド サービスをデバッグするには、ロール インスタンスを実行する仮想マシンに必要なサービス (msvsmon.exe など) をインストールするために、クラウド サービスのデプロイ時にデバッグ機能を明示的に有効にする必要があります。 サービスを発行するときにリモート デバッグを有効にしなかった場合、リモート デバッグを有効にしてサービスを再発行する必要があります。

クラウド サービスのリモート デバッグを有効にしても、パフォーマンスが低下したり、追加料金が発生したりすることはありません。 運用サービスでは、サービスを利用すクライアントに悪影響が生じる可能性があるため、リモート デバッグを使用しないでください。

> [!NOTE]
> Visual Studio からクラウド サービスを発行する場合、.NET Framework 4 または .NET Framework 4.5 を対象とするサービスのどのロールに対しても **IntelliTrace** を有効にできます。 **IntelliTrace**を使用すると、過去にロール インスタンスで発生したイベントを調べ、そのときのコンテキストを再現できます。 [IntelliTrace および Visual Studio を使用した発行済みのクラウド サービスのデバッグ](http://go.microsoft.com/fwlink/?LinkID=623016)に関するページ、および「[IntelliTrace の使用](https://msdn.microsoft.com/library/dd264915.aspx)」を参照してください。
>
>

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>クラウド サービスのリモート デバッグを有効にするには
1. Azure プロジェクトのショートカット メニューを開き、 **[発行]**を選択します。
2. **[ステージング]** 環境と **[デバッグ]** 構成を選択します。

    これは、単なるガイドラインです。 運用環境でテスト環境を実行することもできます。 ただし、運用環境でリモート デバッグを有効にすると、ユーザーに悪影響が生じる可能性があります。 リリース構成も選択できますが、デバッグ構成の方がデバッグは簡単です。

    ![Choose the Debug configuration](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)
3. 通常の手順に従います。ただし、**[詳細設定]** タブの **[すべてのロールのリモート デバッガーを有効にする]** チェック ボックスをオンにします。

    ![Debug Configuration](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Azure でクラウド サービスにデバッガーをアタッチするには
1. サーバー エクスプ ローラーで、クラウド サービスのノードを展開します。
2. アタッチ先のロールまたはロール インスタンスのショートカット メニューを開き、 **[デバッガーのアタッチ]**を選択します。

    ロールをデバッグすると、Visual Studio デバッガーによって、そのロールの各インスタンスにアタッチされます。 デバッガーは、コード行を実行し、ブレーク ポイントのいずれかの条件を満たす最初のロール インスタンスのブレーク ポイントで停止します。 インスタンスをデバッグすると、その特定のインスタンスがコード行を実行してブレーク ポイントの条件を満たす場合にのみ、デバッガーがそのインスタンスにのみアタッチし、ブレーク ポイントで停止します。

    ![[デバッガーのアタッチ]](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)
3. デバッガーがインスタンスにアタッチされた後、通常どおりにデバッグします。 デバッガーは、ロールの適切なホスト プロセスに自動的にアタッチされます。 ロール応じて、w3wp.exe、WaWorkerHost.exe、または WaIISHost.exe にアタッチします。 デバッガーがアタッチされるプロセスを検証するには、サーバー エクスプローラーでインスタンス ノードを展開します。 Azure プロセスの詳細については、[Azure ロールのアーキテクチャ](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)に関するページを参照してください。

    ![Select code type dialog box](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
4. デバッガーがアタッチされているプロセスを確認するには、メニュー バーの [デバッグ]、[Windows]、[プロセス] を選択して、[プロセス] ダイアログ ボックスを開きます(キーボード: Ctrl + Alt + Z)。 特定のプロセスをデタッチするには、ショートカット メニューを開き、 **[プロセスのデタッチ]**を選択します。 または、サーバー エクスプローラーでインスタンス ノードを特定し、プロセスを検索し、ショートカット メニューを開き、 **[プロセスのデタッチ]**を選択します。

    ![Debug Processes](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> リモート デバッグ時、ブレークポイントで長時間停止させることは避けてください。 Azure では、プロセスの停止時間が数分を超えるとプロセスを応答不能と見なし、そのインスタンスへのトラフィック送信を停止します。 停止時間が長くなると、msvsmon.exe はプロセスからデタッチされます。
>
>

インスタンスまたはロールのすべてのプロセスからデバッガーをデタッチするには、デバッグ対象のロールまたはインスタンスのショートカット メニューを開き、 **[デバッガーのデタッチ]**を選択します。

## <a name="limitations-of-remote-debugging-in-azure"></a>Azure でのリモート デバッグの制限
Azure SDK 2.3 以降、リモート デバッグには次の制限があります。

* リモート デバッグを有効にすると、インスタンス数が 25 を超えるロールではクラウド サービスを発行できません。
* デバッガーで使用されるポートは、30400 ～ 30424、31400 ～ 31424、および 32400 ～ 32424 です。 これらのポートを使用しようとすると、サービスの発行はできず、次のいずれかのエラー メッセージが Azure のアクティビティ ログに出力されます。

  * .csdef ファイルに対して .cscfg ファイルを検証しているときにエラーが発生しました。
    ロール 'ロール' エンドポイント Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector の予約されたポート範囲 '範囲' は、定義済みのポートまたは範囲と重複しています。
  * 割り当てに失敗しました。 後で再試行するか、VM のサイズまたはロール インスタンスの数を減らすか、別のリージョンにデプロイしてみてください。

## <a name="debugging-azure-virtual-machines"></a>Azure 仮想マシンをデバッグする
Azure 仮想マシンで実行されているプログラムをデバッグするには、Visual Studio のサーバー エクスプローラーを使用します。 Azure 仮想マシンでリモート デバッグを有効にすると、仮想マシンにリモート デバッグ拡張機能がインストールされます。 インストール後は、仮想マシンのプロセスにアタッチし、通常どおりデバッグすることができます。

> [!NOTE]
> Azure リソース マネージャー スタックを通して作成した仮想マシンについては、Visual Studio 2015 のクラウド エクスプローラーを使用して、リモートでデバッグできます。 詳細については、 [クラウド エクスプローラーを使用した Azure リソースの管理](http://go.microsoft.com/fwlink/?LinkId=623031)に関するページを参照してください。
>
>

### <a name="to-debug-an-azure-virtual-machine"></a>Azure 仮想マシンをデバッグするには
1. サーバー エクスプローラーで、Virtual Machines ノードを展開し、デバッグする仮想マシンのノードを選択します。
2. コンテキスト メニューを開き、 **[デバッグを有効にする]**を選択します。 仮想マシンでデバッグを有効にすることを確認するメッセージが表示されたら、 **[はい]**を選択します。

    仮想マシンにリモート デバッグ拡張機能がインストールされ、デバッグが有効になります。

    ![Virtual machine enable debugging command](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure activity log](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
3. リモート デバッグ拡張機能のインストールが完了したら、仮想マシンのコンテキスト メニューを開き、 **[デバッガーのアタッチ...]**

    Azure は、仮想マシンのプロセスの一覧を取得し、[プロセスにアタッチ] ダイアログ ボックスに表示します。

    ![Attach debugger command](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
4. **[プロセスにアタッチ]** ダイアログ ボックスで、**[選択]** を選択し、デバッグするコードの種類のみが表示されるように結果リストを制限します。 32 ビットまたは 64 ビット マネージ コードとネイティブ コードのいずれかまたは両方をデバッグできます。

    ![Select code type dialog box](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
5. 仮想マシンでデバッグするプロセスを選択し、 **[アタッチ]**を選択します。 たとえば、仮想マシンで Web アプリをデバッグする場合は、w3wp.exe プロセスを選択できます。 詳細については、「[Visual Studio での 1 つ以上のプロセスのデバッグ](https://msdn.microsoft.com/library/jj919165.aspx)」および [Azure ロールのアーキテクチャ](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)に関するページを参照してください。

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>デバッグ用の Web プロジェクトと仮想マシンを作成する
Azure プロジェクトを発行する前に、デバッグとテストのシナリオをサポートし、テストおよびモニタリング プログラムをインストールできる環境でテストすることをお勧めします。 実行方法の 1 つは、仮想マシンでアプリケーションをリモート デバッグすることです。

Visual Studio ASP.NET プロジェクトでは、アプリケーションのテストに使用できる便利な仮想マシンを作成するオプションを用意しています。 仮想マシンには、一般的な必要なエンドポイント (PowerShell、リモート デスクトップ、WebDeploy など) が含まれています。

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>デバッグ用の Web プロジェクトと仮想マシンを作成するには
1. Visual Studio で、新しい ASP.NET Web アプリケーションを作成します。
2. [新しい ASP.NET プロジェクト] ダイアログの [Azure] セクションのドロップダウン リスト ボックスで、 **[仮想マシン]** を選択します。 **[リモート リソースを作成する]** チェック ボックスはオンのままにします。 **OK** を選択して続行します。

    **[Azure での仮想マシンの作成]** ダイアログ ボックスが表示されます。

    ![Create ASP.NET web project dialog box](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **注:** まだ Azure アカウントにサインインしていない場合は、サインインするように求められます。

1. 仮想マシンのさまざまな設定を選択し、 **[OK]**を選択します。 詳細については、「 [Virtual Machines](http://go.microsoft.com/fwlink/?LinkId=623033) 」を参照してください。

    DNS 名として入力した名前は、仮想マシンの名前になります。

    ![Create virtual machine on Azure dialog box](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    Azure によって仮想マシンが作成され、エンドポイント (リモート デスクトップや Web 配置など) のプロビジョニングと構成が行われます。
2. 仮想マシンの構成が完了したら、サーバー エクスプローラーで仮想マシンのノードを選択します。
3. コンテキスト メニューを開き、 **[デバッグを有効にする]**を選択します。 仮想マシンでデバッグを有効にすることを確認するメッセージが表示されたら、 **[はい]**を選択します。

    仮想マシンにリモート デバッグ拡張機能がインストールされ、デバッグが有効になります。

    ![Virtual machine enable debugging command](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Azure activity log](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
4. [Visual Studio でワンクリック発行を使用して Web プロジェクトをデプロイする方法](https://msdn.microsoft.com/library/dd465337.aspx)に関するページの説明に従って、プロジェクトを発行します。 ここでは、仮想マシンでデバッグするため、**Web の発行**ウィザードの **[設定]** ページで、構成として **[デバッグ]** を選択します。 このように設定することで、デバッグ中もコードのシンボルを使用できます。

    ![Publish settings](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)
5. プロジェクトが既にデプロイされている場合は、**[ファイルの発行オプション]** で **[転送先で追加のファイルを削除します。]** をオンにします。
6. プロジェクトを発行したら、サーバー エクスプローラーで仮想マシンのコンテキスト メニューを開き、 **[デバッガーのアタッチ...]**

    Azure は、仮想マシンのプロセスの一覧を取得し、[プロセスにアタッチ] ダイアログ ボックスに表示します。

    ![Attach debugger command](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
7. **[プロセスにアタッチ]** ダイアログ ボックスで、**[選択]** を選択し、デバッグするコードの種類のみが表示されるように結果リストを制限します。 32 ビットまたは 64 ビット マネージ コードとネイティブ コードのいずれかまたは両方をデバッグできます。

    ![Select code type dialog box](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
8. 仮想マシンでデバッグするプロセスを選択し、 **[アタッチ]**を選択します。 たとえば、仮想マシンで Web アプリをデバッグする場合は、w3wp.exe プロセスを選択できます。 詳細については、「 [Visual Studio での 1 つ以上のプロセスのデバッグ](https://msdn.microsoft.com/library/jj919165.aspx) 」を参照してください。

## <a name="next-steps"></a>次のステップ
* **Intellitrace** を使用して、リリース サーバーから呼び出しおよびイベントのログを収集します。 [IntelliTrace および Visual Studio を使用した発行済みのクラウド サービスのデバッグ](http://go.microsoft.com/fwlink/?LinkID=623016)に関するページを参照してください。
* **Azure 診断** を使用して、ロールが開発環境と Azure のどちらで実行されているかにかかわらず、ロール内で実行されているコードの詳細な情報をログに記録します。 「 [Azure 診断を使用したログ データの収集](http://go.microsoft.com/fwlink/p/?LinkId=400450)」を参照してください。



<!--HONumber=Dec16_HO2-->


