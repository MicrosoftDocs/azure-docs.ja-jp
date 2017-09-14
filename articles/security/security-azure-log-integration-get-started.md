---
title: "Azure ログ統合の使用 | Microsoft Docs"
description: "Azure ログ統合サービスをインストールし、Azure ストレージのログ、Azure 監査ログ、および Azure Security Center の警告を統合する方法について説明します。"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 07/26/2017
ms.author: TomSh
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: a5c51817688140cc2778602b4c1d5184ae4729a0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure 診断ログおよび Windows イベント転送による Azure ログ統合
Azure ログ統合 (AzLog) を使用すると、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できます。 この統合は、すべての資産に対するオンプレミスまたはクラウドの統合セキュリティ ダッシュボードを可能にします。これにより、アプリケーションに関連付けられているセキュリティ イベントの集計、関連付け、分析、警告が行えます。
>[!NOTE]
Azure ログ統合の詳細については、[Azure ログ統合の概要](https://docs.microsoft.com/azure/security/security-azure-log-integration-overview)を参照してください。

本記事は Azlog サービスのインストールに重点を置き、Azure 診断でサービスを統合することによって、Azure ログ統合を始める際に役立ちます。 Azure ログ統合サービスにより、Azure IaaS に展開された仮想マシンの Windows セキュリティ イベント チャネルから、Windows イベント ログ情報を収集することができます。 これはオンプレミスで利用される「イベント転送」と類似しています。

>[!NOTE]
>Azure ログ統合の出力を SIEM に統合する機能は、SIEM により提供されます。 詳細は、[オンプレミスの SIEM への Azure ログ統合](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)を参照してください。

Azure ログ統合サービスは、Windows Server 2008 R2 またはそれ以上の オペレーティング システム (Windows Server 2012 R2 または Windows Server 2016 を推奨) を使用する、物理または仮想コンピューター上で実行されます。

物理コンピューターはオンプレミス (またはホスト側サイト) で実行可能です。 仮想マシンで Azure ログ統合サービスを実行する場合、その仮想マシンをオンプレミスまたは Microsoft Azure などのパブリック クラウドに設置することができます。

物理または仮想マシンが Azure ログ統合サービスを実行するには、Azure パブリック クラウドへのネットワーク接続が必要です。 この記事では、構成に関する手順を詳しく説明します。

## <a name="prerequisites"></a>前提条件
AzLog のインストールには、少なくとも次が必要です。
* **Azure サブスクリプション**。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。
* Windows Azure 診断ログに使用可能な**ストレージ アカウント** (事前構成されたストレージ アカウントを使用するか、新規に作成できます。この記事の後半で、ストレージ アカウントを構成する方法を紹介します)。
  >[!NOTE]
  シナリオによっては、ストレージ アカウントが必要ない場合があります。 この記事で説明する Azure 診断シナリオの場合は必要です。
* **2 つのシステム**: Azure ログ統合サービスを実行するコンピューターと、Azlog サービス コンピューターに送信されるログ情報を持つ監視可能なコンピューター。
   * 監視対象のマシン - [Azure 仮想マシン](../virtual-machines/virtual-machines-windows-overview.md)として実行する VM
   * Azure ログ統合サービスを実行し、SIEM に後でインポートされるすべてのログ情報を収集するコンピューター。
    * このシステムは、オンプレミスまたは Microsoft Azure に設置可能です。  
    * Windows server 2008 R2 SP1 の x64 バージョンまたはそれ以上を実行し、.NET 4.5.1 がインストールされている必要があります。 [方法 : インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/library/hh925568)の記事に従って、インストールされている .NET のバージョンを確認することができます。  
    Azure 診断ログに使用する Azure Storage アカウントへの接続が必要です。 接続を確認する方法については、この記事の後半で説明します。

Azure Portal を使って仮想マシンを作成するプロセスの簡単なデモについては、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]



## <a name="deployment-considerations"></a>デプロイに関する考慮事項
Azure ログ統合のテスト中は、オペレーティング システムの最小要件を満たす任意のシステムを使用することができます。 ただし運用環境によっては、負荷のスケールアップまたはスケールアウトの検討が必要な場合もあります。

イベント量が高い場合は、Azure ログ統合サービスの複数インスタンス (物理または仮想マシンごとに 1 つのインスタンス) を実行できます。 また、Windows 用 Azure 診断 (WAD) ストレージ アカウントの負荷分散が可能です。インスタンスに提供するサブスクリプションの数は、容量をベースにする必要があります。
>[!NOTE]
この時点では、(Azure ログ統合サービスを実行する) Azure ログ統合マシンのインスタンスのスケールアウト、またはストレージ アカウントやサブスクリプションに対する具体的な推奨事項はありません。 拡大/縮小の決定は、これらの各領域におけるパフォーマンスの観測値に基づく必要があります。

またパフォーマンス向上のために、Azure ログ統合サービスをスケールアップすることもできます。 次のパフォーマンス メトリックは、Azure ログ統合サービスを実行するマシンのサイズ変更に役立ちます。
* 8 プロセッサ (コア) コンピューターでは、1 つの Azlog インテグレーター インスタンスが、1 日あたり 2,400 万 (1 時間あたり最大 100 万) のイベントを処理できます。

* 4 プロセッサ (コア) コンピューターでは、1 つの Azlog インテグレーター インスタンスが、1 日あたり 150 万 (1 時間あたり最大 62,500 ) のイベントを処理できます。

## <a name="install-azure-log-integration"></a>Azure ログ統合のインストール
Azure ログ統合のインストールには、[Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)インストール ファイルをダウンロードする必要があります。 セットアップ ルーチンを実行し、利用統計情報をマイクロソフトに提供するかを決定します。  

![インストール画面で利用統計情報チェック ボックスをオンに](./media/security-azure-log-integration-get-started/telemetry.png)

*
> [!NOTE]
> マイクロソフトによる利用統計情報の収集を許可することをお勧めします。 このオプションをオフにして、利用統計情報の収集を無効にすることができます。
>


Azure ログ統合サービスは、インストール先のマシンから利用統計情報を収集します。  

収集される利用統計情報を以下に示します。

* Azure ログ統合の実行中に発生する例外
* 処理されたクエリおよびイベントの数に関するメトリック
* 使用されている Azlog.exe コマンド ライン オプションについての統計情報

インストール プロセスについては、次のビデオをご覧ください。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]



## <a name="post-installation-and-validation-steps"></a>ポスト インストールおよび検証の手順
基本的なセットアップ ルーチンを完了すると、ポスト インストールおよび検証手順が実行できます。
1. 管理者特権の PowerShell ウィンドウを開き、**c:\Program Files\Microsoft Azure Log Integration** に移動します。
2. まず初めに Azlog コマンドレットをインポートします。 スクリプト **LoadAzlogModule.ps1** (次のコマンドでは “.\” に注意してください) を実行することでインポートが可能です。 **.\LoadAzlogModule.ps1** と入力し、**ENTER** をクリックします。  
次の図のように表示されます。 </br></br>
![インストール画面で利用統計情報チェック ボックスをオンに](./media/security-azure-log-integration-get-started/loaded-modules.png) </br></br>
3. 特定の Azure 環境を使用して AzLog を構成する必要があります。 「Azure 環境」とは、使用する Azure のクラウド データ センターの「種類」です。 この時点では複数の Azure 環境が存在しますが、現在対象となるオプションは **AzureCloud** または**AzureUSGovernment** のいずれかです。   管理者特権の PowerShell 環境では、**c:\program files \Microsoft Azure ログ統合\**内であることを確認します。 </br></br>
    確認したらコマンドを実行します。 </br>
    ``Set-AzlogAzureEnvironment -Name AzureCloud`` (Azure 商用)

      >[!NOTE]
      コマンドの成功時に、フィードバックは提供されません。  米国政府の Azure クラウドを使用する場合、米国政府クラウド向け **AzureUSGovernment** (Name 変数) が使用されます。 その他の Azure クラウドは、現時点ではサポートされていません。  
4. システムを監視するには、Azure 診断に使用されているストレージ アカウント名が必要です。  Azure ポータルで **[仮想マシン]** に移動し、監視対象の仮想マシンを検索します。 **[プロパティ]** セクションで、**[診断設定]** を選択します。  **[エージェント]** をクリックし、指定されたストレージ アカウント名をメモします。 このアカウント名は今後の手順で必要です。
![Azure 診断設定](./media/security-azure-log-integration-get-started/storage-account-large.png) </br></br>

      ![Azure 診断設定](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)
      >[!NOTE]
      仮想マシン作成中において監視が有効でない場合には、上記のオプションが選択できます。
5. ここで再び Azure ログ統合マシンについて説明します。 Azure ログ統合をインストールしたシステムと、ストレージ アカウント間の接続を確認する必要があります。 Azure ログ統合サービスを実行する物理コンピューターまたは仮想マシンは、ストレージ アカウントに接続し、各監視対象システムで構成されている Azure 診断が記録する情報を取得する必要があります。  
  1. Azure Storage エクスプローラーをダウンロードするには[ここ](http://storageexplorer.com/)をクリック。
  2. セットアップ ルーチンの実行
  3. インストールが完了したら **[次へ]** をクリックします。**[Microsoft Azure Storage エクスプローラーを起動]** チェック ボックスはチェックを入れたままにします。  
  4. Azure にログインします。
  5. Azure 診断用に構成したストレージ アカウントが表示できることを確認します。  
![ストレージ アカウント](./media/security-azure-log-integration-get-started/storage-account.jpg) </br></br>
   6. ストレージ アカウントには、オプションがいくつかありますのでご注意ください。 そのうちの 1 つは**テーブル**です。 **テーブル**には、**WADWindowsEventLogsTable** が表示されます。 </br></br>
   ![ストレージ アカウント](./media/security-azure-log-integration-get-started/storage-explorer.png) </br>

## <a name="integrate-azure-diagnostic-logging"></a>Azure 診断ログの統合
この手順では、Azure ログ統合サービスを実行しているコンピューターを構成し、ログ ファイルを含むストレージ アカウントに接続します。
この手順を完了するには、いくつかの事項が前もって必要です。  
* **FriendlyNameForSource:** Azure 診断から情報を格納するために仮想マシンを構成した、ストレージ アカウントに適用可能な表示名。
* **StorageAccountName:** Azure 診断を構成する際に指定したストレージ アカウント名。  
* **StorageKey:** 仮想マシン用に Azure 診断情報が格納されるストレージ アカウントに対するストレージ キー。  

ストレージ キーを取得するには、次の手順を実行します。
 1. [Azure ポータル](http://portal.azure.com)にアクセスします。
 2. Azure コンソールのナビゲーション ウィンドウで一番下までスクロールし、**その他のサービス**をクリックします。

 ![その他のサービス](./media/security-azure-log-integration-get-started/more-services.png)
 3. **[フィルター]** テキスト ボックスで、**ストレージ**を入力します。 **[ストレージ アカウント]** (**ストレージ**入力後に表示されます) をクリックします。

   ![フィルター ボックス](./media/security-azure-log-integration-get-started/filter.png)
 4. ストレージ アカウント一覧が表示されたら、ログの記憶域に割り当てられているアカウントをダブルクリックします。

   ![ストレージ アカウント一覧](./media/security-azure-log-integration-get-started/storage-accounts.png)
 5. **[設定]** セクションで **[アクセス キー]** をクリックします。

  ![アクセス キー](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 6. **key1** をコピーし、次の手順でアクセスできるよう安全な場所に格納します。

   ![2 つのアクセス キー](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)
 7. Azure ログ統合をインストールしたサーバーで、管理者特権のコマンド プロンプトを開きます (管理者特権の PowerShell コンソールではなく、管理者特権のコマンド プロンプト ウィンドウを必ず使用してください)。
 8. **c:\Program Files\Microsoft Azure Log Integration** に移動します。
 9. ``Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey> `` を実行します。 </br> たとえば、``Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``サブスクリプション ID をイベント XML に表示するには、サブスクリプション ID を表示名に追加します。``Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>``または``Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==``

>[!NOTE]  
60 分間待つと、ストレージ アカウントから取得されたイベントが表示されます。 表示するには、Azlog インテグレーターで **[イベント ビューアー] > [Windows ログ] > [転送されたイベント]** を開きます。

ここで説明した手順については、次のビデオをご覧ください。
> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>[転送されたイベント] フォルダーにデータが表示されない場合
1 時間経過しても、 **[転送されたイベント]** フォルダーにデータが表示されない場合は、次の操作を行います。

1. Azure ログ統合サービスを実行するコンピューターが、Azure にアクセスできることを確認します。 接続をテストするには、 [Azure ポータル](http://portal.azure.com) をブラウザーから開いてみます。
2. ユーザー アカウント **Azlog** に、**users\Azlog** フォルダーへの書き込みアクセス許可が付与されていることを確認します。
  <ol type="a">
   <li>**Windows エクスプローラー** を開く</li>
  <li> **c:\users** に移動</li>
  <li> **c:\users\Azlog** を右クリック</li>
  <li> **セキュリティ**  をクリック</li>
  <li> **NT Service\Azlog** をクリックし、アカウントのアクセス許可を確認します。 アカウントがタブに表示されない場合や、適切なアクセス許可が表示されない場合は、このタブでアカウント権限を付与することができます。</li>
  </ol>
3. **Azlog source list** コマンドを実行して、**Azlog source add** コマンドに追加されたストレージ アカウントを確認します。
4. **[イベント ビューアー] > [Windows ログ] > [アプリケーション]** の順にアクセスして、Azure ログ統合からエラーが報告されていないかどうかを確認します。


インストールおよび構成中に問題が発生した場合、[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を作成し、サポートを要求するサービスとして **[ログ統合]** を選択します。

別のオプションとして、[Azure ログ統合の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)も利用可能です。 このコミュニティでは、Azure ログ統合を最大限に活用する方法に関する質問、回答、ヒント、およびコツなどによる、相互サポート機能を提供しています。 さらに、Azure ログ統合チームがこのフォーラムを監視しており、可能なときにはいつでも支援を提供します。

## <a name="next-steps"></a>次のステップ
Azure ログの統合の詳細については、次のドキュメントを参照してください。

* [Azure ログ用の Microsoft Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の概要](security-azure-log-integration-overview.md) – このドキュメントでは、Azure ログ統合と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。 これは、SIEM コンポーネントを構成する方法に関する現在のガイダンスです。 詳細については、まず SIEM ベンダーに確認してください。
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure アクティビティ ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。
* [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – このブログ投稿では、Azure 監査ログと、Azure リソースの操作の洞察を得るのに役立つその他の機能を紹介します。

