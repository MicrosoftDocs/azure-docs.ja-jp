---
title: "Azure Cloud Services および Virtual Machines 用の診断の構成 | Microsoft Docs"
description: "Azure Cloud Services と Azure Virtual Machines (VM) を Visual Studio でデバッグするために必要な診断情報を構成する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 76bcb201bb8d862426048b828c0c4cce0335455c


---
# <a name="configuring-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Azure クラウド サービスおよび仮想マシン用の診断の構成
Azure クラウド サービスまたは Azure 仮想マシンのトラブルシューティングを行うときは、Visual Studio を使用すると Azure 診断が構成しやすくなります。 Azure 診断は、クラウド サービスを実行する仮想マシンと仮想マシン インスタンスのシステム データとログ データを取り込み、そのデータを任意のストレージ アカウントに転送します。 Azure の診断ログの詳細については、「[Azure App Service の Web アプリの診断ログの有効化](app-service-web/web-sites-enable-diagnostic-log.md)」を参照してください。

このトピックでは、Azure クラウド サービスをデプロイする前と後、さらに Azure 仮想マシンを対象に、Visual Studio で Azure 診断を有効にして構成する方法を示します。 また、収集する診断情報の種類を選択する方法と、収集された情報を表示する方法についても取り上げます。

Azure 診断は、次の方法で構成できます。

* 診断の構成設定は、Visual Studio の **[診断構成]** ダイアログ ボックスで変更できます。 この設定は、diagnostics.wadcfgx (Azure SDK 2.4 以前の場合は diagnostics.wadcfg) というファイルに保存されます。 構成ファイルを直接変更することもできます。 構成ファイルを直接更新した場合、変更内容は、次回 Azure にクラウド サービスをデプロイするとき、またはエミュレーターでサービスを実行するときに有効になります。
* 実行中のクラウド サービスまたは実行中の仮想マシンの診断設定は、Visual Studio から **Cloud Explorer** または**サーバー エクスプローラー**を使用して変更を加えます。

## <a name="azure-26-diagnostics-changes"></a>Azure 2.6 の診断の変更
Visual Studio の Azure SDK 2.6 プロジェクトでは、次の変更が行われました。 (これらの変更はそれ以降のバージョンの Azure SDK にも当てはまります。)

* ローカル エミュレーターで診断がサポートされるようになりました。 つまり Visual Studio での開発およびテスト時に診断データを収集し、開発中のアプリケーションで正しくトレースが作成されることを確認できます。 Visual Studio で Azure ストレージ エミュレーターを使ってクラウド サービス プロジェクトを実行している間に行われる診断データ収集が、接続文字列 `UseDevelopmentStorage=true` を指定すると有効になります。 すべての診断データは、(開発ストレージ) ストレージ アカウントに収集されます。
* 診断ストレージ アカウントの接続文字列 (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) の保存先が、再びサービス構成 (.cscfg) ファイルに戻されました。 Azure SDK 2.5 では、診断ストレージ アカウントが diagnostics.wadcfgx ファイルで指定されていました。

Azure SDK 2.4 以前と Azure SDK 2.6 以降とで、接続文字列の働きに大きな違いがいくつかあります。

* Azure SDK 2.4 以前では、診断プラグインが診断ログを転送するためのストレージ アカウント情報を取得する目的でラインタイムとして接続文字列を使用していました。
* Azure SDK 2.6 以降では、Visual Studio が発行時に適切なストレージ アカウント情報を使って診断拡張機能を構成する目的で診断接続文字列を使用します。 Visual Studio が発行時に使用する各種サービス構成に対し、異なるストレージ アカウントを接続文字列で定義することができます。 しかし、(Azure SDK 2.5 以降) 診断プラグインが使用できなくなったため、.cscfg ファイルだけでは診断拡張機能を有効にできません。 Visual Studio や PowerShell などのツールを使用して個別に拡張機能を有効にする必要があります。
* PowerShell を使用して診断拡張機能を構成するプロセスを単純化するために、Visual Studio からの出力パッケージには、ロールごとの診断拡張機能のパブリック構成 XML も含まれます。 Visual Studio は、診断接続文字列を使用して、パブリック構成に存在するストレージ アカウント情報を取り込みます。 このパブリック構成ファイルは、PaaSDiagnostics.&lt;RoleName>.PubConfig.xml という形式の名前で Extensions フォルダーに作成されます。 このファイルを PowerShell ベースのデプロイで使用し、各構成をロールにマップすることができます。
* .cscfg ファイル内の接続文字列は、 [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040) で診断データにアクセスするときにも使用されるため、 **[監視]** タブで確認できます。 ポータルで監視データを詳細出力するようにサービスを構成するには、この接続文字列が必要となります。

## <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Azure SDK 2.6 以降へのプロジェクトの移行
Azure SDK 2.5 から Azure SDK 2.6 以降に移行するとき、.wadcfgx ファイルで指定した診断ストレージ アカウントがある場合、そのアカウントが維持されます。 さまざまなストレージ構成で異なるストレージ アカウントの使用の柔軟性を利用するには、接続文字列をプロジェクトに手動で追加する必要があります。 Azure SDK 2.4 以前から Azure SDK 2.6 にプロジェクトを移行する場合は、診断接続文字列が保持されます。 ただし、前のセクションでも触れたように、Azure SDK 2.6 では接続文字列の扱いが変更されているので注意してください。

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>使用する診断ストレージ アカウントを Visual Studio がどのように決定するか
* 診断接続文字列が .cscfg ファイルに指定されている場合、Visual Studio は、発行時のほか、パッケージ化時にパブリック構成の xml ファイルを生成するときに、.cscfg ファイルに指定されている診断接続文字列を使用して診断拡張機能を構成します。
* 診断接続文字列が .cscfg ファイルに指定されていない場合、Visual Studio は .wadcfgx ファイルに指定されたストレージ アカウントを使用して、発行時、およびパッケージ化におけるパブリック構成 xml ファイルの生成時に診断拡張機能を構成するようにフォールバックします。
* .cscfg ファイルの診断接続文字列は、.wadcfgx ファイルのストレージ アカウントよりも優先されます。 診断接続文字列が .cscfg ファイルに指定されている場合、Visual Studio はそちらを使用し、.wadcfgx のストレージ アカウントは無視します。

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>"開発ストレージ接続文字列の更新" チェック ボックスの機能
**[Microsoft Azure への公開時に診断とキャッシュのための開発ストレージの接続文字列を Microsoft Azure ストレージ アカウントの資格情報で更新する]** チェック ボックスの機能は、発行時に指定した Azure ストレージ アカウントで開発ストレージ アカウントの接続文字列を更新することです。

たとえば、このチェック ボックスをオンにして、診断接続文字列で `UseDevelopmentStorage=true`を指定するとします。 この診断接続文字列は、プロジェクトを Azure に発行するとき、公開ウィザードで指定されたストレージ アカウントに自動的に更新されます。 ただし、実際のストレージ アカウントが診断接続文字列として指定されている場合は、そのアカウントが代わりに使用されます。

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Azure SDK 2.4 以前と Azure SDK 2.5 以降の診断機能の違い
プロジェクトを Azure SDK 2.4 から Azure SDK 2.5 以降にアップグレードする場合、次の診断機能の違いに留意してください。

* **構成 API は非推奨となりました** – Azure SDK 2.4 以前のバージョンでは、プログラムから診断機能を構成できましたが、Azure SDK 2.5 以降ではプログラムによる診断の構成が非推奨扱いとなりました。 現在、診断の構成をコードで定義している場合、その診断機能を引き続き利用するためには、移行したプロジェクトでそれらの設定を最初から構成し直す必要があります。 Azure SDK 2.4 の診断構成ファイルは diagnostics.wadcfg で、Azure SDK 2.5 以降では diagnostics.wadcfgx です。
* **クラウド サービス アプリケーションの診断はロール レベルでのみ構成でき、インスタンス レベルでは構成できません。**
* **アプリケーションをデプロイするたびに診断の構成が更新されます** – サーバー エクスプローラーで診断の構成を変更してからアプリを再デプロイした場合、これによってパリティの問題が発生する可能性があります。
* **Azure SDK 2.5 以降では、クラッシュ ダンプがコードからではなく診断構成ファイルで構成されます** - クラッシュ ダンプをコードから構成していた場合は、その構成を手動でコードから構成ファイルに移す必要があります。Azure SDK 2.6 への移行の際にクラッシュ ダンプは移行されません。

## <a name="enable-diagnostics-in-cloud-service-projects-before-deploying-them"></a>デプロイ前にクラウド サービス プロジェクトで診断を有効にする
サービスをデプロイ前にエミュレーターで実行するとき、Azure で実行するロールの診断データを Visual Studio で収集できます。 Visual Studio で診断設定に対して行った変更はすべて、diagnostics.wadcfgx 構成ファイルに保存されます。 これらの構成設定で、クラウド サービスのデプロイ時に診断データが保存されるストレージ アカウントを指定します。

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-enable-diagnostics-in-visual-studio-before-deployment"></a>デプロイ前に Visual Studio で診断を有効にするには
1. 目的のロールのショートカット メニューで **[プロパティ]** を選択し、ロールの**プロパティ** ウィンドウの **[構成]** タブを選択します。
2. **[診断]** セクションで、**[診断の有効化]** チェック ボックスがオンになっていることを確認します。
   
    ![[診断の有効化] オプションへのアクセス](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. 省略記号 (...) のボタンを選択し、診断データを格納するストレージ アカウントを指定します。 選択したストレージ アカウントが、診断データの保存先になります。
   
    ![Specify the storage account to use](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. Azure ストレージ エミュレーター、Azure サブスクリプション、手動で入力した資格情報のうち、どれを使って接続するかを **[ストレージ接続文字列の作成]** ダイアログ ボックスで指定します。
   
    ![Storage account dialog box](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * [Microsoft Azure ストレージ エミュレーター] オプションを選択すると、接続文字列は UseDevelopmentStorage=true に設定されます。
   * [サブスクリプション] オプションを選択した場合は、使用する Azure サブスクリプションとアカウント名を選択できます。 [アカウントの管理] ボタンをクリックすることで、Azure サブスクリプションを管理できます。
   * [手動で入力された資格情報] オプションを選択すると、使用する Azure アカウントの名前とキーの入力が求められます。
5. **[構成]** を選択して、**[診断構成]** ダイアログ ボックスを表示します。 **[全般]** と **[ログ ディレクトリ]** を除く各タブは、収集できる診断データのソースを表します。 既定の **[全般]** タブでは、診断データの収集オプションとして、**[エラーのみ]**、**[すべての情報]**、**[カスタム プラン]** を指定できます。 既定のオプションの **[エラーのみ]**では、警告またはトレース メッセージが転送されないため、必要とするストレージ容量が最小限で済みます。 [すべての情報] オプションは転送する情報量が最も多いため、ストレージ コストが最も高いオプションです。
   
    ![Enable Azure diagnostics and configuration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. この例では、収集するデータをカスタマイズできるよう、 **[カスタム プラン]** オプションを選択します。
7. **[ディスク クォータ (MB)]** ボックスでは、ストレージ アカウントで診断データ用に割り当てる容量を指定します。 既定の値は必要に応じて変更可能です。
8. 収集する診断データに対応する各タブで、**[<log type> の転送を有効にする]** チェック ボックスをオンにします。 たとえば、アプリケーション ログを収集する場合は、**[アプリケーション ログ]** タブで **[アプリケーション ログの転送を有効にする]** チェック ボックスをオンにします。 それ以外にも、診断データの種類ごとに必要な情報があれば指定します。 各タブの構成情報については、このトピックの後半にある「 **診断データのソースを構成する** 」セクションを参照してください。
9. 目的の診断データの収集をすべて有効にしたら、 **[OK]** ボタンをクリックします。
10. Visual Studio で Azure クラウド サービス プロジェクトを通常どおり実行します。 アプリケーションを使用すると、有効にしたログ情報が、指定した Azure ストレージ アカウントに保存されます。

## <a name="enable-diagnostics-in-azure-virtual-machines"></a>Azure Virtual Machines で診断を有効にする
Azure Virtual Machines の診断データを Visual Studio で収集することができます。

### <a name="to-enable-diagnostics-in-azure-virtual-machines"></a>Azure Virtual Machines で診断を有効にするには
1. **サーバー エクスプローラー**で [Azure] ノードを選択し、まだ接続していない場合は Azure サブスクリプションに接続します。
2. **[Virtual Machines]** ノードを展開します。 新しい仮想マシンを作成するか、既存の仮想マシンを選択してください。
3. 目的の仮想マシンのショートカット メニューで **[構成]**を選択します。 [仮想マシンの構成] ダイアログ ボックスが表示されます。
   
    ![Configuring an Azure virtual machine](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Microsoft Monitoring Agent 診断拡張機能がまだインストールされていない場合は追加します。 この拡張機能で、Azure 仮想マシンに関する診断データを収集することができます。 [インストールされている拡張機能] の一覧で、[使用可能な拡張機能を選択してください] ドロップダウン メニューを選択し、[Microsoft Monitoring Agent 診断] を選択します。
   
    ![Installing an Azure virtual machine extension](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
   > [!NOTE]
   > 他の診断拡張機能も仮想マシンで使用できます。 詳細については、「Azure VM 拡張機能と機能」を参照してください。
   > 
   > 
5. **[追加]** ボタンをクリックし、拡張機能を追加して、その **[診断構成]** ダイアログ ボックスを表示します。
6. **[構成]** ボタンを選択してストレージ アカウントを指定し、**[OK]** ボタンをクリックします。
   
    **[全般]** と **[ログ ディレクトリ]** を除く各タブは、収集できる診断データのソースを表します。
   
    ![Enable Azure diagnostics and configuration](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    既定の **[全般]** タブでは、診断データの収集オプションとして、**[エラーのみ]**、**[すべての情報]**、**[カスタム プラン]** を指定できます。 既定のオプションの **[エラーのみ]**では、警告またはトレース メッセージが転送されないため、必要とするストレージ容量が最小限で済みます。 **[すべての情報]** オプションは転送する情報量が最も多いため、ストレージ コストが最も高いオプションです。
7. この例では、収集するデータをカスタマイズできるよう、 **[カスタム プラン]** オプションを選択します。
8. **[ディスク クォータ (MB)]** ボックスでは、ストレージ アカウントで診断データ用に割り当てる容量を指定します。 既定の値は必要に応じて変更可能です。
9. 収集する診断データに対応する各タブで、**[<log type> の転送を有効にする]** チェック ボックスをオンにします。
   
    たとえば、アプリケーション ログを収集する場合は、**[アプリケーション ログ]** タブで **[アプリケーション ログの転送を有効にする]** チェック ボックスをオンにします。 それ以外にも、診断データの種類ごとに必要な情報があれば指定します。 各タブの構成情報については、このトピックの後半にある「 **診断データのソースを構成する** 」セクションを参照してください。
10. 目的の診断データの収集をすべて有効にしたら、 **[OK]** ボタンをクリックします。
11. 更新したプロジェクトを保存します。
    
     仮想マシンが更新されたことを示すメッセージが **[Microsoft Azure のアクティビティ ログ]** ウィンドウに表示されます。

## <a name="configure-diagnostics-data-sources"></a>診断データのソースを構成する
診断データの収集を有効にしたら、収集対象のデータ ソースと収集する情報を厳密に選択できます。 以降、 **[診断構成]** ダイアログ ボックスの一連のタブと各構成オプションについて説明します。

### <a name="application-logs"></a>[アプリケーション ログの転送を有効にする]
**アプリケーション ログ** には Web アプリケーションによって生成された診断情報が記録されます。 アプリケーション ログを取り込むには、 **[アプリケーション ログの転送を有効にする]** チェック ボックスをオンにします。 アプリケーション ログをストレージ アカウントに転送する間隔 (分) は、 **[転送期間 (分)]** の値を変更することで増減できます。 また、ログに取り込む情報の量は、[ログ レベル] の値を設定することによって変更できます。 たとえば、取り込む情報の量を増やすには **[詳細]** を、重要なエラーのみ取り込むには **[重大]** を選択してください。 アプリケーション ログを出力する特定の診断プロバイダーがある場合は、プロバイダーの GUID を **[プロバイダー GUID]** ボックスに追加することでログを取り込むことができます。

  ![[アプリケーション ログの転送を有効にする]](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

  アプリケーション ログの詳細については、「[Azure App Service の Web アプリの診断ログの有効化](app-service-web/web-sites-enable-diagnostic-log.md)」を参照してください。

### <a name="windows-event-logs"></a>Windows イベント ログ
Windows イベント ログを取り込むには、 **[Windows イベント ログの転送を有効にする]** チェック ボックスをオンにします。 イベント ログをストレージ アカウントに転送する間隔 (分) は、 **[転送期間 (分)]** の値を変更することで増減できます。 追跡するイベントの種類に該当するチェック ボックスをオンにしてください。

  ![イベント ログ](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Azure SDK 2.6 以降を使用している場合、カスタム データ ソースを指定するには、**[<Data source name>]** ボックスに目的のデータ ソースを入力し、その横の **[追加]** ボタンをクリックします。 指定したデータ ソースが diagnostics.cfcfg ファイルに追加されます。

Azure SDK 2.5 を使用している場合、カスタム データ ソースを指定するには、以下の例のように、diagnostics.wadcfgx ファイルの `WindowsEventLog` セクションにそのデータ ソースを追加してください。

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>パフォーマンス カウンター
パフォーマンス カウンターの情報は、システムのボトルネックを特定して、システムとアプリケーションのパフォーマンスを微調整するのに役立ちます。 詳細については、 [Azure アプリケーションでのパフォーマンス カウンターの作成と使用](https://msdn.microsoft.com/library/azure/hh411542.aspx) に関するページを参照してください。 パフォーマンス カウンターを取り込むには、 **[パフォーマンス カウンターの転送を有効にする]** チェック ボックスをオンにします。 イベント ログをストレージ アカウントに転送する間隔 (分) は、 **[転送期間 (分)]** の値を変更することで増減できます。 追跡するパフォーマンス カウンターに該当するチェック ボックスをオンにしてください。

  ![パフォーマンス カウンター](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

リストにないパフォーマンス カウンターを追跡するには、構文の候補を使用してパフォーマンス カウンターを入力し、 **[追加]** ボタンをクリックします。 追跡できるパフォーマンス カウンターは、仮想マシンのオペレーティング システムによって決まります。 構文の詳細については、 [カウンター パスの指定](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx)に関するページを参照してください。

### <a name="infrastructure-logs"></a>インフラストラクチャ ログ
インフラストラクチャ ログには、Azure 診断インフラストラクチャや RemoteAccess モジュール、RemoteForwarder モジュールに関する情報が記録されます。インフラストラクチャ ログを取り込むには、**[診断インフラストラクチャのログの転送を有効にする]** チェック ボックスをオンにします。 ログをストレージ アカウントに転送する間隔 (分) は、[転送期間 (分)] の値を変更することで増減できます。

  ![Diagnostics Infrastructure Logs](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

  詳細については、「 [Azure 診断を使用したログ データの収集](https://msdn.microsoft.com/library/azure/gg433048.aspx) 」を参照してください。

### <a name="log-directories"></a>[ログ ディレクトリ]
ログ ディレクトリには、インターネット インフォメーション サービス (IIS) の要求から収集したデータや、失敗した要求から収集したデータ、選択したフォルダーのログ ディレクトリから収集したデータが格納されます。ログ ディレクトリを取り込むには、**[ログ ディレクトリの転送を有効にする]** チェック ボックスをオンにします。 ログをストレージ アカウントに転送する間隔 (分) は、 **[転送期間 (分)]** の値を変更することで増減できます。

**[IIS ログ]** や **[失敗した要求ログ]** など、収集するログのボックスを選択できます。 既定のストレージ コンテナー名が指定されていますが、必要に応じて名前を変更できます。

また、任意のフォルダーからログを取り込むことができます。 **[絶対ディレクトリのログ]** セクションでパスを指定して、**[ディレクトリの追加]** ボタンを選択するだけです。 ログは指定したコンテナーに取り込まれます。

  ![[ログ ディレクトリ]](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>ETW ログ
[[Windows イベント トレーシング]](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx)(ETW) を使用している場合、ETW ログを取り込むには、**[ETW のログの転送を有効にする]** チェック ボックスをオンにします。 ログをストレージ アカウントに転送する間隔 (分) は、 **[転送期間 (分)]** の値を変更することで増減できます。

イベントは、指定したイベント ソースおよびイベント マニフェストから取り込まれます。 イベント ソースを指定するには、**[イベント ソース]** セクションで名前を入力して、**[イベント ソースの追加]** ボタンを選択します。 同様に、**[イベント マニフェスト]** セクションでイベント マニフェストを指定して、**[イベント マニフェストの追加]** ボタンを選択できます。

  ![ETW ログ](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

  ASP.NET では、[System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) 名前空間のクラスによって ETW フレームワークがサポートされています。 Microsoft.WindowsAzure.Diagnostics は、標準の [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) クラスを継承して拡張する名前空間です。この名前空間では、Azure 環境のログ記録フレームワークとして [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110) を使用できます。 詳細については、[Microsoft Azure でログ記録とトレースを制御する方法](https://msdn.microsoft.com/magazine/ff714589.aspx)と [Azure Cloud Services および Virtual Machines での診断の有効化](cloud-services/cloud-services-dotnet-diagnostics.md)に関するページを参照してください。

### <a name="crash-dumps"></a>クラッシュ ダンプ
ロール インスタンスがクラッシュしたときの情報を取り込むには、**[クラッシュ ダンプの転送を有効にする]** チェック ボックスをオンにします (ASP.NET によってほとんどの例外が処理されるため、これは一般的に worker ロールに対してのみ機能します)。クラッシュ ダンプに充てるストレージ容量の割合は、**[ディレクトリのクォータ (%)]** の値を変更することで増減できます。 クラッシュ ダンプが保存されるストレージ コンテナーを変更できるほか、ダンプの種類として **[フル]** または **[ミニ]** を選択できます。

現在追跡されているプロセスが一覧表示されます。 取り込むプロセスのチェック ボックスをオンにしてください。 別のプロセスを一覧に追加するには、プロセス名を入力して、 **[プロセスの追加]** ボタンを選択します。

  ![クラッシュ ダンプ](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

  詳細については、[Microsoft Azure でログ記録とトレースを制御する方法](https://msdn.microsoft.com/magazine/ff714589.aspx)と [Microsoft Azure 診断 (第 4 部: カスタム ログ コンポーネントと Azure 診断 1.3 の変更点)](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/) に関するページを参照してください。

## <a name="view-the-diagnostics-data"></a>診断データの表示
クラウド サービスまたは仮想マシンの診断データを収集したら、そのデータを表示することができます。

### <a name="to-view-cloud-service-diagnostics-data"></a>クラウド サービスの診断データを表示するには
1. クラウド サービスを通常どおりにデプロイし、実行します。
2. Visual Studio が生成するレポートまたはストレージ アカウントのテーブルのどちらかに診断データが表示されます。 レポートのデータを表示するには、**Cloud Explorer** または**サーバー エクスプローラー**を開き、目的のロールのノードのショートカット メニューを開いて、**[診断データの表示]** を選択します。
   
    ![[診断データの表示]](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    利用可能なデータを示したレポートが表示されます。
   
    ![Microsoft Azure Diagnostics Report in Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    最新のデータが表示されない場合は、転送間隔の経過を待ちます。
   
    **[更新]** リンクを選択してデータをすぐに更新するか、**[自動更新]** ボックスの一覧から間隔を選択してデータが自動的に更新されるように設定します。 エラー データをエクスポートするには、 **[CSV にエクスポート]** ボタンをクリックして、スプレッドシートで開くことができるコンマ区切り値ファイルを作成します。
   
    対象のデプロイに関連付けられたストレージ アカウントを **Cloud Explorer** または**サーバー エクスプローラー**で開きます。
3. テーブル ビューアーで診断テーブルを開き、収集したデータを確認します。 IIS ログおよびカスタム ログについては、BLOB コンテナーを開くことができます。 テーブルを確認することで、目的のデータを含むテーブルまたは BLOB コンテナーを見つけることができます。 テーブルのエントリにはログ ファイルのデータだけでなく EventTickCount、DeploymentId、Role、RoleInstance が含まれており、データを生成した仮想マシンとロール、さらに、生成された時刻を特定できます。 
   
   | 診断データ | 説明 | 場所 |
   | --- | --- | --- |
   | [アプリケーション ログの転送を有効にする] |System.Diagnostics.Trace クラスのメソッドを呼び出すことでコードが生成するログです。 |WADLogsTable |
   | イベント ログ |このデータは仮想マシンの Windows イベント ログから生成されます。 Windows イベント ログは、Windows だけでなくアプリケーションやサービスも、エラーまたはログ情報をレポートするために使用します。 |WADWindowsEventLogsTable |
   | パフォーマンス カウンター |仮想マシンで使用できるパフォーマンス カウンターのデータを収集できます。 パフォーマンス カウンターは、オペレーティング システムが備えている機能であり、メモリ使用率やプロセッサ時間など多くの統計情報を保持します。 |WADPerformanceCountersTable |
   | インフラストラクチャ ログ |診断インフラストラクチャ自体から生成されるログです。 |WADDiagnosticInfrastructureLogsTable |
   | IIS ログ |Web 要求を記録します。 このログのデータは、クラウド サービスへのトラフィックがあまりに多いと膨大な量になることがあるため、必要なときにのみ収集、保存することをお勧めします。 |失敗した要求のログは、デプロイ、ロール、インスタンスのパスにある wad-iis-failedreqlogs の BLOB コンテナーに格納されます。 ログ全体は wad-iis-logfiles にあります。 各ファイルのエントリは WADDirectories テーブルで生成されます。 |
   | クラッシュ ダンプ |この情報は、クラウド サービスのプロセス (通常は worker ロール) のバイナリ イメージとなります。 |wad-crush-dumps BLOB コンテナー |
   | カスタム ログ ファイル |ユーザー定義のログ データです。 |ストレージ アカウントのカスタム ログ ファイルの場所をコードで指定できます。 たとえば、カスタム BLOB コンテナーを指定できます。 |
4. データが切り捨てられる場合は、その種類のデータのバッファーを拡張したり、仮想マシンからストレージ アカウントへのデータ転送の間隔を短くしてみてください。
5. (省略可能) ストレージ コストを削減するために、折を見てストレージ アカウントからデータを消去してください。
6. フル デプロイを行うと、Azure の diagnostics.cscfg ファイル (Azure SDK 2.5 の場合は .wadcfgx) が更新され、診断構成に対する変更があれば、クラウド サービスによってピックアップされます。 一方、既存のデプロイを更新した場合は、Azure で .cscfg ファイルが更新されません。 その場合でも、次のセクションの手順に従って診断の設定を変更することができます。 フル デプロイの実行と既存のデプロイの更新の詳細については、 [Azure アプリケーションの公開ウィザード](vs-azure-tools-publish-azure-application-wizard.md)に関するページを参照してください。

### <a name="to-view-virtual-machine-diagnostics-data"></a>仮想マシンの診断データを表示するには
1. 仮想マシンのショートカット メニューで **[診断データの表示]**を選択します。
   
    ![View diagnostics data in Azure virtual machine](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    **[診断の概要]** ウィンドウが開きます。
   
    ![Azure virtual machine diagnostics summary](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    最新のデータが表示されない場合は、転送間隔の経過を待ちます。
   
    **[更新]** リンクを選択してデータをすぐに更新するか、**[自動更新]** ボックスの一覧から間隔を選択してデータが自動的に更新されるように設定します。 エラー データをエクスポートするには、 **[CSV にエクスポート]** ボタンをクリックして、スプレッドシートで開くことができるコンマ区切り値ファイルを作成します。

## <a name="configure-cloud-service-diagnostics-after-deployment"></a>デプロイ後にクラウド サービスの診断を構成する
既に実行しているクラウド サービスの問題について調べるとき、最初にロールをデプロイするときには指定しなかったデータを収集しなければならない場合があります。 その場合、サーバー エクスプローラーの設定を使用してデータの収集を開始できます。 診断は、ロールの単一インスタンスを対象に構成することも、すべてのインスタンスを対象に構成することもできます。そのどちらが対象となるかは、[診断構成] ダイアログ ボックスをインスタンスのショートカット メニューから開くか、ロールのショートカット メニューから開くかによって異なります。 ロール ノードを構成した場合、すべてのインスタンスに変更が適用されます。 インスタンス ノードを構成した場合、そのインスタンスのみに変更が適用されます。

### <a name="to-configure-diagnostics-for-a-running-cloud-service"></a>実行中のクラウド サービスの診断を構成するには
1. サーバー エクスプローラーで、 **[クラウド サービス]** ノードを展開し、目的のロールとインスタンスのどちらかまたは両方を探します。
   
    ![Configuring Diagnostics](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. インスタンス ノードまたはロール ノードのショートカット メニューで **[診断の設定の更新]**をクリックし、収集する診断の設定を選択します。
   
    構成設定については、このトピックの「 **診断データのソースを構成する** 」を参照してください。 診断データを表示する方法については、このトピックの「 **診断データの表示** 」を参照してください。
   
    **サーバー エクスプローラー**でデータ収集を変更した場合、その変更は、クラウド サービスを再度フル デプロイするまで有効となります。 既定の発行設定を使用する場合は、変更は上書きされません。既定の発行設定は、完全な再デプロイではなく、既存のデプロイの更新であるためです。 デプロイ時に設定が消去されるようにするには、公開ウィザードの **[詳細設定]** タブに移動し、**[配置の更新]** チェック ボックスをオフにします。 このチェック ボックスをオフにした状態で再デプロイすると、ロールの [プロパティ] エディターで行った .wadcfgx (または .wadcfg) ファイルの設定に戻されます。 デプロイを更新した場合、Azure で古い設定が保存されます。

## <a name="troubleshoot-azure-cloud-service-issues"></a>Azure クラウド サービスの問題のトラブルシューティング
ロールが "ビジー" 状態のままになる、リサイクルを繰り返す、内部サーバー エラーをスローするなど、クラウド サービス プロジェクトで問題が発生した場合、各種のツールと手法をその診断および修正に役立てることができます。 一般的な問題とソリューションの具体例や、そのようなエラーの診断と修正に使用する概念とツールの概要については、 [Azure PaaS 計算診断データ](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)に関するページを参照してください。

## <a name="q--a"></a>Q & A
**バッファー サイズとは何ですか。その大きさはどのくらいが適切ですか。**

ローカル ファイル システムに保存できる診断データの量は、各仮想マシン インスタンスのクォータによって制限されています。 加えてユーザーは、利用できる診断データの種類ごとにバッファー サイズを指定できます。 このバッファー サイズは、その種類のデータに対する個別のクォータのような役割を果たします。 ダイアログ ボックスの下部をチェックすることで、クォータ全体とメモリ残量を確認できます。 バッファーのサイズを大きくしたり、データの種類を増やすには、クォータ全体を操作します。 クォータ全体を変更するには、diagnostics.wadcfg/.wadcfgx 構成ファイルに変更を加えます。 診断データはアプリケーションのデータと同じファイル システムに格納されます。アプリケーションでディスク領域を大量に消費する場合、診断クォータ全体を増やすことは避けてください。

**転送間隔とは何ですか。その長さはどのくらいが適切ですか。**

転送間隔は、データの取り込み間隔、つまり前回の取り込みから次の取り込みまでの経過時間です。 転送間隔として指定された時間が経過するたびに、仮想マシンのローカル ファイル システムからストレージ アカウントのテーブルにデータが移動されます。 収集されたデータの量が、転送間隔の終わりを待たずにクォータを超えた場合、古いデータから順に破棄されます。 データがバッファー サイズまたは全体のクォータを超えるためにデータが失われる場合は、転送間隔を短くしてください。

**タイム スタンプには、どのタイム ゾーンが使用されますか。**

タイム スタンプは、クラウド サービスをホストするデータ センターのローカル タイム ゾーンです。 ログ テーブルでは次の 3 つのタイムスタンプ列が使用されます。

* **PreciseTimeStamp** はイベントの ETW タイムスタンプです。 つまり、クライアントのイベントがログに記録された時刻です。
* **TIMESTAMP** はアップロード頻度境界に切り捨てられた PreciseTimeStamp です。 したがって、アップロード頻度が 5 分で、イベント時刻が 00:17:12 の場合、TIMESTAMP は 00:15:00 になります。
* **Timestamp** は、エンティティが Azure テーブルに作成されたタイムスタンプです。

**診断情報を収集するためのコストを管理するにはどうすればよいですか。**

既定の設定 (**[ログ レベル]** は **[エラー]**、**[転送期間]** は **1 分**) はコストが最小となるように設計されています。 収集する診断データを増やしたり、転送間隔を短くしたりすると、コンピューティング コストが増大します。 必要以上に多くのデータを収集しないようにし、必要がなくなったらデータ収集を無効にしてください。 前のセクションで説明したとおり、実行時を含めいつでも有効に戻すことができます。

**IIS から失敗した要求のログを収集するにはどうすればよいですか。**

既定では、IIS は失敗した要求のログを収集しません。 それらを収集するように IIS を構成するには、Web ロールの web.config ファイルを編集してください。

**OnStart など RoleEntryPoint のメソッドからトレース情報を取得できません。何か問題があるのでしょうか。**

RoleEntryPoint のメソッドは、IIS ではなく WAIISHost.exe のコンテキストで呼び出されます。 そのため、通常であればトレースが有効になる web.config の構成情報が適用されません。 この問題を解決するには、Web ロール プロジェクトに .config ファイルを追加し、RoleEntryPoint コードを含む出力アセンブリと同じ名前を付けます。 既定の Web ロール プロジェクトでは、.config ファイルの名前は WAIISHost.exe.config です。 そのうえで、このファイルに次の行を追加します。

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

**[プロパティ]** ウィンドウで、**[出力ディレクトリにコピー]** プロパティを **[常にコピーする]** に設定します。

## <a name="next-steps"></a>次のステップ
Azure の診断ログの詳細については、[Azure Cloud Services および Virtual Machines での診断の有効化](cloud-services/cloud-services-dotnet-diagnostics.md)に関するページと「[Azure App Service の Web アプリの診断ログの有効化](app-service-web/web-sites-enable-diagnostic-log.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


