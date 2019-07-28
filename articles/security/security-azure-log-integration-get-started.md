---
title: Azure Log Integration の使用を開始する | Microsoft Docs
description: Azure Log Integration サービスをインストールし、Azure Storage のログ、Azure 監査ログ、および Azure Security Center のアラートを統合する方法について説明します。
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: b1df35c82ffb8b98f8b7bf86c21694b62b3804e4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298296"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Azure Log Integration と Azure Diagnostics ログおよび Windows イベント転送


>[!IMPORTANT]
> Azure ログの統合機能は、2019 年 6 月 15 日までに非推奨になる予定です。 AzLog ダウンロードは、2018 年 6 月 27 日に無効になりました。 今後必要な対応のガイダンスについては、[Azure 監視を使って SIEM ツールと統合する](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)方法に関する投稿を確認してください。 

セキュリティ情報イベント管理 (SIEM) ベンダーから [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) コネクタを入手できない場合は、Azure Log Integration しか使用できません。

Azure Log Integration により Azure のログが SIEM で使用可能になり、統合されたセキュリティ ダッシュボードを作成してすべてのアセットを管理できます。
Azure Monitor コネクタの状態に関する情報については、SIEM のベンダーにお問い合わせください。

> [!IMPORTANT]
> 主な関心が仮想マシン ログの収集である場合、ほとんどの SIEM ベンダーのソリューションにこのオプションが含まれています。 代替として SIEM ベンダーのコネクタを使用することは常に推奨されます。

この記事は、Azure Log Integration の使用を開始するうえで役立ちます。 この記事は、Azure Log Integration サービスのインストールと、Azure Diagnostics へのサービスの統合にフォーカスしています。 Azure Log Integration サービスにより、Azure IaaS (サービスとしてのインフラストラクチャ) に展開された仮想マシンの Windows セキュリティ イベント チャネルから、Windows イベント ログ情報を収集することができます。 これは、ユーザーがオンプレミス システムで使用する可能性のある "*イベント転送*" と同様です。

> [!NOTE]
> Azure Log Integration の出力と SIEM との統合は、SIEM で行われます。 詳細については、[AAzure Log Integration とオンプレミスの SIEM の統合](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)に関するページを参照してください。

Azure Log Integration サービスは、Windows Server 2008 R2 以降 (Windows Server 2016 または Windows Server 2012 R2 を推奨) が実行されている物理または仮想コンピューターで実行されます。

物理コンピューターは、オンプレミスまたはホストしているサイト上で実行できます。 仮想マシンで Azure Log Integration サービスを実行する場合、その仮想マシンをオンプレミスまたは Microsoft Azure などのパブリック クラウドに設置することができます。

物理または仮想マシンが Azure ログ統合サービスを実行するには、Azure パブリック クラウドへのネットワーク接続が必要です。 この記事には、必要な構成の詳細が記載されています。

## <a name="prerequisites"></a>前提条件

Azure Log Integration のインストールには、次の項目が最小限必要です。

* **Azure サブスクリプション**。 お持ちでない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップしてください。
* Microsoft Azure Diagnostics ログに使用できる**ストレージ アカウント**。 事前に構成されたストレージ アカウントを使用するか、または新しいストレージ アカウントを作成できます。 ストレージ アカウントの構成方法については、この記事の後の方で説明します。

  > [!NOTE]
  > シナリオによっては、ストレージ アカウントが不要な場合があります。 この記事で説明する Azure Diagnostics シナリオの場合は、ストレージ アカウントが必要です。

* **2 つのシステム**: 
  * Azure Log Integration サービスを実行するマシン。 このマシンは、後に SIEM にインポートされるすべてのログ情報を収集します。 次のようなシステムです。
    * オンプレミスまたは Microsoft Azure にホストできるシステムのいずれか。  
    * Windows server 2008 R2 SP1 の x64 バージョンまたはそれ以上を実行し、Microsoft .NET 4.5.1 がインストールされている必要がある。 インストールされている .NET のバージョンを確認するには、[インストールされている .NET Framework バージョンを確認する方法](https://msdn.microsoft.com/library/hh925568)に関するページを参照してください。  
    * Azure Diagnostics ログに使用する Azure Storage アカウントに接続されている必要がある。 接続の確認方法については、この記事の後の方で説明します。
  * 監視対象のマシン。 これは、[Azure 仮想マシン](../virtual-machines/virtual-machines-windows-overview.md)として実行されている仮想マシンです。 このマシンからのログ情報が Azure Log Integration サービスのマシンに送信されます。

Azure Portal を使用して仮想マシンを作成する方法の簡単なデモについては、次のビデオをご覧ください。<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

テスト中、オペレーティング システムの最小要件を満たす任意のシステムを使用できます。 運用環境の場合は、負荷のスケールアップまたはスケールアウトの計画が必要になることがあります。

Azure ログ統合サービスの複数のインスタンスを実行できます。 ただし、物理マシンまたは仮想マシンごとにサービスの 1 つのインスタンスのみを実行できます。 さらに、Microsoft Azure Diagnostics の Azure Diagnostics ストレージ アカウントを負荷分散できます。 インスタンスに提供するサブスクリプションの数は、容量に基づきます。

> [!NOTE]
> 現時点では、Azure Log Integration (つまり、Azure Log Integration サービスを実行しているマシン) のインスタンスをいつスケールアウトするかや、ストレージ アカウントまたはサブスクリプションに関する具体的な推奨事項はありません。 拡大/縮小は、これらの各領域におけるパフォーマンスの観測値に基づいて判断します。

また、パフォーマンス向上のために、Azure Log Integration サービスをスケールアップすることもできます。 次のパフォーマンス メトリックは、Azure Log Integration を実行するマシンのサイズ変更に役立ちます。

* 8 プロセッサ (コア) マシンでは、Azure Log Integration の 1 つのインスタンスで 1 日あたり約 2,400 万イベント (約 100 万イベント/時間) を処理できます。
* 4 プロセッサ (コア) マシンでは、Azure Log Integration の 1 つのインスタンスで 1 日あたり約 150 万イベント (約 62,500 イベント/時間) を処理できます。

## <a name="install-azure-log-integration"></a>Azure Log Integration のインストール

セットアップ ルーチンを実行します。 利用統計情報をマイクロソフトに提供するかどうかを選択します。

Azure Log Integration サービスは、インストール先のマシンから利用統計情報データを収集します。  

収集される利用統計情報データは、次のとおりです。

* Azure Log Integration の実行中に発生する例外。
* 処理されたクエリおよびイベントの数に関するメトリック。
* 使用されている Azlog.exe コマンド ライン オプションについての統計情報。 

> [!NOTE]
> マイクロソフトによる利用統計情報の収集を許可することをお勧めします。 利用統計情報データの収集は、 **[Allow Microsoft to collect telemetry data]\(マイクロソフトによる利用統計情報データの収集を許可する\)** チェック ボックスをオフにすることで停止できます。
>

![利用統計情報のチェック ボックスがオンになっている状態のインストール ウィンドウのスクリーンショット](./media/security-azure-log-integration-get-started/telemetry.png)


インストール プロセスについては、次のビデオをご覧ください。<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>ポスト インストールおよび検証の手順

基本的なセットアップを完了すると、ポスト インストールおよび検証の手順が実行できます。

1. PowerShell を管理者として開きます。 次に、C:\Program Files\Microsoft Azure Log Integration に移動します。
2. Azure Log Integration のコマンドレットをインポートします。 コマンドレットをインポートするには、`LoadAzlogModule.ps1` のスクリプトを実行します。 `.\LoadAzlogModule.ps1` と入力し、Enter キーを押します (このコマンドの **.\\** の使用に注意してください)。 次の図のように表示されます。

   ![LoadAzlogModule.ps1 コマンドの出力のスクリーン ショット](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. 次に、特定の Azure 環境を使用するように Azure Log Integration を構成します。 "*Azure 環境*" とは、使用する Azure のクラウド データ センターの種類です。 複数の Azure 環境が存在しますが、現在対象となるオプションは **AzureCloud** または**AzureUSGovernment** のいずれかです。 PowerShell を管理者として実行し、C:\Program Files\Microsoft Azure Log Integration\ 内であることを確認します。 その後、次のコマンドを実行します。

   `Set-AzlogAzureEnvironment -Name AzureCloud` (**AzureCloud** の場合)
  
   米国政府の Azure クラウドを使用する場合、 **-Name** 変数に **AzureUSGovernment** を使用します。 現時点では、Azure のその他のクラウドはサポートされていません。  

   > [!NOTE]
   > コマンドが成功するとフィードバックを受信しません。 

4. システムを監視するには、Azure Diagnostics に使用されているストレージ アカウントの名前が必要です。 Azure Portal で **[仮想マシン]** に移動します。 監視する Windows 仮想マシンを検索します。 **[プロパティ]** セクションで、 **[診断設定]** を選択します。  次に、 **[エージェント]** を選択します。 指定されたストレージ アカウント名をメモします。 このアカウント名は、後の手順で必要になります。

   ![Azure の [診断設定] ウィンドウのスクリーンショット](./media/security-azure-log-integration-get-started/storage-account-large.png) 

   ![[ゲスト レベルの監視を有効にする] ボタンのスクリーンショット](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > 仮想マシンが作成されたときに監視が有効になっていなかった場合、前の図に示すように有効にできます。

5. ここで、Azure Log Integration マシンに戻ります。 Azure Log Integration をインストールしたシステムと、ストレージ アカウント間の接続を確認します。 Azure Log Integration サービスを実行しているコンピューターは、監視対象の各システム上で構成されている Azure Diagnostics によって記録された情報を取得するために、ストレージ アカウントへのアクセスが必要です。 接続は、次のように確認します。 
   1. [Azure Storage Explorer をダウンロード](https://storageexplorer.com/)します。
   2. セットアップを完了します。
   3. インストールが完了したら、 **[次へ]** を選択します。 **[Launch Microsoft Azure Storage Explorer]\(Microsoft Azure Storage Explorer を起動する\)** チェック ボックスはオンのままにします。  
   4. Azure にサインインします。
   5. Azure Diagnostics 用に構成したストレージ アカウントが表示されることを確認します。 

   ![Storage Explorer のストレージ アカウントのスクリーンショット](./media/security-azure-log-integration-get-started/storage-explorer.png)

   1. ストレージ アカウントの下に、オプションがいくつか表示されます。 **[テーブル]** の下に、**WADWindowsEventLogsTable** というテーブルが表示されます。

   仮想マシンが作成されたときに監視が有効になっていなかった場合、前に説明したように有効にできます。


## <a name="integrate-windows-vm-logs"></a>Windows VM のログを統合する

この手順では、Azure Log Integration サービスを実行しているコンピューターを構成し、ログ ファイルを含むストレージ アカウントに接続します。

この手順を完了するには、次の設定が必要です。  
* **FriendlyNameForSource**:Azure Diagnostics から情報を格納するために仮想マシンを構成した、ストレージ アカウントに適用可能な表示名。
* **StorageAccountName**:Azure Diagnostics を構成するときに指定したストレージ アカウントの名前。  
* **StorageKey**:この仮想マシン用に Azure Diagnostics 情報が格納されるストレージ アカウントに対するストレージ キー。  

ストレージ キーを取得するには、次の手順を実行します。
1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. ナビゲーション ウィンドウで、で、 **[すべてのサービス]** を選択します。
3. **[フィルター]** テキスト ボックスで、「**ストレージ**」と入力します。 **[ストレージ アカウント]** を選択します。

   ![[すべてのサービス] でのストレージ アカウントを示すスクリーンショット](./media/security-azure-log-integration-get-started/filter.png)

4. ストレージ アカウントの一覧が表示されます。 ストレージのログを記録するように割り当てたアカウントをダブルクリックします。

   ![ストレージ アカウントの一覧を示すスクリーンショット](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. **[設定]** で **[アクセス キー]** を選択します。

   ![メニューの [アクセス キー] オプションを示すスクリーンショット](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. **key1** をコピーし、次の手順でアクセスできるように安全な場所に格納します。
7. Azure Log Integration をインストールしたサーバーで、管理者としてコマンド プロンプト ウィンドウを開きます (管理者として、PowerShell ではなく、コマンド プロンプト ウィンドウを開いてください)。
8. C:\Program Files\Microsoft Azure Log Integration に移動します。
9. 次のコマンドを実行します。`Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`
 
   例:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   サブスクリプション ID がイベント XML に表示されるようにするには、サブスクリプション ID を表示名に追加します。

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   例:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> 60 分間待ち、ストレージ アカウントから取得されたイベントを確認します。 イベントを表示するには、Azure Log Integration で、 **[イベント ビューアー]**  >  **[Windows ログ]**  >  **[転送されたイベント]** と選択します。

次のビデオは、前の手順の内容を示します。<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>[転送されたイベント] フォルダーにデータが表示されない場合
1 時間経過しても、[転送されたイベント] フォルダーにデータが表示されない場合は、次の操作を行います。

1. Azure Log Integration サービスを実行するマシンを確認します。 Azure にアクセスできることを確認します。 接続をテストするには、ブラウザーで、[Azure Portal](https://portal.azure.com) に移動してみてください。
2. ユーザー アカウント AzLog にフォルダー users\Azlog に対する書き込みアクセス許可があることを確認します。
   1. エクスプローラーを開きます。
   2. C:\users に移動します。
   3. C:\users\Azlog を右クリックします。
   4. **[セキュリティ]** を選択します。
   5. **NT Service\Azlog** を選択します。 アカウントのアクセス許可を確認します。 アカウントがタブに表示されない場合や、適切なアクセス許可が表示されない場合は、このタブでアカウント権限を付与することができます。
3. コマンド `Azlog source list` を実行するときに、コマンド `Azlog source add` に追加したストレージ アカウントが出力に列挙されていることを確認してください。
4. Azure Log Integration からエラーが報告されていないかどうかを確認するには、 **[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** と移動します。

インストールおよび構成中に問題が起きた場合は、[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を作成できます。 サービスに、 **[Log Integration]** を選択します。

別のオプションとして、[Azure Log Integration の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration)も利用可能です。 MSDN フォーラムで、コミュニティは質問に回答し、Azure Log Integration を最大限活用するためのヒントやコツを共有することでサポートを提供します。 このフォーラムは Azure Log Integration チームも監視しています。 サポートを提供できるときはいつでも提供します。

## <a name="integrate-azure-activity-logs"></a>Azure Activity Logs を統合する

Azure アクティビティ ログは、Azure で発生したサブスクリプション レベルのイベントの分析に利用できるサブスクリプション ログです。 たとえば、Azure Resource Manager の運用データから、サービスの正常性イベントまでの範囲のデータが含まれています。 Azure Security Center アラートは、このログにも含まれます。
> [!NOTE]
> [作業の開始](security-azure-log-integration-get-started.md)に関する記事を読み、手順を完了してから、この記事の手順を試してください。

### <a name="steps-to-integrate-azure-activity-logs"></a>Azure Activity Logs を統合する手順

1. コマンド プロンプトを開き、コマンド ```cd c:\Program Files\Microsoft Azure Log Integration``` を実行します。
2. コマンド ```azlog createazureid``` を実行します。

    このコマンドは Azure ログインを要求します。 このコマンドは、管理者、共同管理者、または所有者としてログインし、Azure サブスクリプションをホストする Azure AD テナントに、Azure Active Directory サービス プリンシパルを作成します。 単なる Guest ユーザーとして Azure AD テナントにログインしている場合、コマンドは失敗します。 Azure への認証は、Azure AD によって行われます。 Azure ログ統合のサービス プリンシパルを作成すると、Azure AD の ID が作成され、Azure サブスクリプションからの読み取りアクセス許可が付与されます。
3. 前の手順で作成した Azure Log Integration サービス プリンシパルを承認するために次のコマンドを実行してアクセスし、サブスクリプションの Activity Log を読み取ります。 コマンドを実行するには、サブスクリプションの所有者である必要があります。

   ```Azlog.exe authorize subscriptionId``` 例:

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. 次のフォルダーを調べて、Azure Active Directory 監査ログの JSON ファイルが作成されていることを確認します。
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> JSON ファイルの情報を Security Information and Event Management (SIEM) システムに移行する具体的な手順については、お使いの SIEM のベンダーに問い合わせてください。

[Azure ログ統合に関する MSDN フォーラム](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration)を通して、コミュニティの支援を受けることができます。 このフォーラムで、Azure ログ統合コミュニティのメンバーは、質問、回答、ヒント、コツなどによって相互にサポートを提供しています。 さらに、Azure ログ統合チームがこのフォーラムを監視しており、可能なときにはいつでも支援を提供します。

[サポート要求](../azure-supportability/how-to-create-azure-support-request.md)を出すこともできます。 サポートを依頼しようとしているサービスとして [ログ統合] を選択します。

## <a name="next-steps"></a>次の手順

Azure Log Integration の詳細については、次の記事を参照してください。作業の開始に関する記事を読み、手順を完了してから、この記事の手順を試してください。

* [Azure Log Integration の概要](security-azure-log-integration-overview.md)。 この記事では、Azure Log Integration と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/)。 このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure Log Integration を構成する方法について説明します。 ここには SIEM コンポーネントを構成する方法に関する現時点でのガイダンスが記載されています。 追加の詳細については、SIEM ベンダーに確認してください。
* [Azure Log Integration のよくあるご質問 (FAQ)](security-azure-log-integration-faq.md)。 この FAQ は、Azure Log Integration について寄せられる質問とその回答です。
* [Azure Security Center のアラートと Azure Log Integration の統合](../security-center/security-center-integrating-alerts-with-log-integration.md)。 この記事では、Azure Diagnostics と Azure のアクティビティ ログによって収集される、Security Center のアラートと仮想マシンのセキュリティ イベントを同期する方法について説明します。 ログを同期するには、Azure Monitor ログまたは SIEM ソリューションを使用します。
* [Azure Diagnostics と Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/)。 このブログ投稿では、Azure 監査ログと、Azure リソースの操作に関する分析情報を得るのに役立つその他の機能を紹介します。
