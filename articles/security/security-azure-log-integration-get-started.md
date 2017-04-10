---
title: "Azure ログ統合の使用 | Microsoft Docs"
description: "Azure ログ統合サービスをインストールし、Azure ストレージのログ、Azure 監査ログ、および Azure Security Center の警告を統合する方法について説明します。"
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 03/07/2017
ms.author: TomSh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: f5f5597e09128236fd659b68c70d587d87a0832a
ms.lasthandoff: 03/08/2017


---
# <a name="get-started-with-azure-log-integration"></a>Azure ログ統合の使用
Azure ログ統合を使用すると、未加工のログを、Azure リソースからオンプレミスのセキュリティ情報/イベント管理 (SIEM) システムに統合できます。 この統合は、すべての資産に対してオンプレミスまたはクラウドの統合ダッシュボードを提供します。これにより、アプリケーションに関連付けられているセキュリティ イベントの集計、関連付け、分析を実行し、警告を生成できます。

このチュートリアルでは、Azure ログ統合をインストールし、Azure ストレージのログ、Azure 監査ログ、および Azure Security Center の警告を統合する方法について説明します。 このチュートリアルの推定所要時間は&1; 時間です。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* Azure ログ統合サービスをインストールするコンピューター (オンプレミスまたはクラウド)。 64 ビット Windows OS が実行され、.Net 4.5.1 がインストールされている必要があります。 このコンピューターは、 **Azlog インテグレーター**と呼ばれます。
* として機能します。 このサブスクリプションがない場合は、 [無料アカウント](https://azure.microsoft.com/free/)にサインアップできます。
* Azure 仮想マシン (VM) が有効になっている Azure 診断。 Cloud Services に対する診断の有効化については、「 [Azure Cloud Services での Azure 診断の有効化](../cloud-services/cloud-services-dotnet-diagnostics.md)」をご覧ください。 Windows を実行している Azure VM に対する診断の有効化については、「[PowerShell を使用して Windows を実行している仮想マシンで Azure 診断を有効にする](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
* Azlog インテグレーターから Azure ストレージへの接続、および Azure サブスクリプションに対する認証と承認。
* Azure VM ログについては、SIEM エージェント (Splunk Universal Forwarder、HP ArcSight Windows Event Collector エージェント、IBM QRadar WinCollect など) を Azlog インテグレーターにインストールする必要があります。

## <a name="deployment-considerations"></a>デプロイメントに関する考慮事項
大量のイベントがある場合は、複数の Azlog インテグレーター インスタンスを実行できます。 Windows 用 Azure 診断 *(WAD)* ストレージ アカウントの負荷分散と、インスタンスに提供するサブスクリプションの数は、容量をベースにする必要があります。

8 プロセッサ (コア) コンピューターでは、1 つの Azlog インテグレーター インスタンスが、1 日あたり 2,400 万 (1 時間あたり最大 100 万) のイベントを処理できます。

4 プロセッサ (コア) コンピューターでは、1 つの Azlog インテグレーター インスタンスが、1 日あたり 150 万 (1 時間あたり最大 62,500 ) のイベントを処理できます。

## <a name="install-azure-log-integration"></a>Azure ログ統合のインストール
[Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)をダウンロードします。

Azure ログ統合サービスは、インストール先のマシンから利用統計情報を収集します。  収集される利用統計情報を以下に示します。

* Azure ログ統合の実行中に発生する例外
* 処理されたクエリおよびイベントの数に関するメトリック
* 使用されている Azlog.exe コマンド ライン オプションについての統計情報

> [!NOTE]
> このオプションをオフにして、利用統計情報の収集を無効にすることができます。
>
>


## <a name="set-your-azure-environment"></a>Azure 環境を設定する
1. PowerShell コンソールを管理者として開き、**cd** を **c:\Program Files\Microsoft Azure Log Integration** にします。
2. コマンド Set-AzLogAzureEnvironment -Name <Cloud> を実行します。

       Replace the Cloud with any of the following
       AzureCloud
       AzureUSGovernment

       Note that at this time, an Azlog integrator only supports integrating logs from a cloud that you choose to integrate.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Azure 診断ストレージ アカウントからの Azure VM ログの統合
1. 上記の前提条件をチェックして、Azure ログ統合を続行する前に、WAD ストレージ アカウントがログを収集していることを確認します。 WAD ストレージ アカウントがログを収集していない場合は、以下の手順を実行しないでください。
2. コマンド プロンプトを開き、**cd** により、現在のディレクトリを **C:\Program Files\Microsoft Azure Log Integration** に変更します。
3. コマンドを実行します

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      StorageAccountName を、VM から診断イベントを受信するように構成された Azure ストレージ アカウントの名前に置き換えます。

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      サブスクリプション ID がイベント XML に表示されるようにするには、サブスクリプション ID を表示名に追加します。

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>
4. 30 ～ 60 分間待って (1 時間かかる場合があります)、ストレージ アカウントから取得されたイベントを表示します。 表示するには、Azlog インテグレーターで **[イベント ビューアー] > [Windows ログ] > [転送されたイベント]** を開きます。
5. コンピューターにインストールされている標準 SIEM コネクタが、 **[転送されたイベント]** フォルダーからイベントを選択し、SIEM インスタンスにパイプするように構成されていることを確認します。 SIEM 固有の構成を確認してログ統合を構成し、表示します。

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>[転送されたイベント] フォルダーにデータが表示されない場合
1 時間経過しても、 **[転送されたイベント]** フォルダーにデータが表示されない場合は、次の操作を行います。

1. コンピューターを確認し、Azure にアクセスできることを確かめます。 接続をテストするには、 [Azure ポータル](http://portal.azure.com) をブラウザーから開いてみます。
2. ユーザー アカウント **azlog** に、**users\azlog** フォルダーに対する書き込みアクセス許可が付与されていることを確認します。
3. **azlog source list** コマンドを実行して、**azlog source add** コマンドに追加されたストレージ アカウントが表示されることを確認します。
4. **[イベント ビューアー] > [Windows ログ] > [アプリケーション]** の順にアクセスして、Azure ログ統合からエラーが報告されていないかどうかを確認します。

イベントがまだ表示されない場合は、次の操作を行います。

1. [Microsoft Azure Storage Explorer](http://storageexplorer.com/)をダウンロードします。
2. **azlog source add**コマンドに追加されたストレージ アカウントに接続します。
3. Microsoft Azure Storage Explorer で **WADWindowsEventLogsTable** テーブルに移動して、データがあるかどうかを確認します。 データがない場合は、VM で診断が正しく構成されていません。

## <a name="integrate-azure-activity-logs-and-security-center-alerts"></a>Azure アクティビティ ログと Security Center の警告の統合
1. コマンド プロンプトを開き、**cd** により、現在のディレクトリを **C:\Program Files\Microsoft Azure Log Integration** に変更します。
2. コマンドを実行します

        azlog createazureid

      このコマンドは Azure ログインを要求します。 このコマンドは、管理者、共同管理者、または所有者としてログインしている、Azure サブスクリプションをホストする Azure AD テナントに [Azure Active Directory サービス プリンシパル](../active-directory/active-directory-application-objects.md) を作成します。 単なる Guest ユーザーとして Azure AD テナントにログインしている場合、コマンドは失敗します。 Azure への認証は、Azure Active Directory (AD) によって行われます。  Azlog 統合のサービス プリンシパルを作成すると、Azure AD の ID が作成され、Azure サブスクリプションからの読み取りアクセス許可が付与されます。
3. コマンドを実行します

        azlog authorize <SubscriptionID>

      これにより、サブスクリプションの閲覧者アクセス許可が、手順 2. で作成されたサービス プリンシパルに割り当てられます。 SubscriptionID を指定しないと、このコマンドは、サービス プリンシパルの閲覧者ロールを、アクセスできるすべてのサブスクリプションに割り当てようとします。

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

   > [!NOTE]
   > **createazureid** コマンドの実行後すぐに **authorize** コマンドを実行すると、警告が表示される可能性があります。 Azure AD アカウントが作成されてから、そのアカウントが使用できるようになるまで、少し時間がかかります。 **createazureid** コマンドを実行した後で約 10 秒間待ってから **authorize** コマンドを実行すれば、この警告が表示されることはありません。
   >
   >
4. 次のフォルダーを調べて、監査ログの JSON ファイルがあることを確認します。

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. 次のフォルダーを調べて、Security Center のアラートが存在することを確認します。

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. 標準的な SIEM ファイル フォワーダー コネクタで、SIEM インスタンスにデータをパイプ処理する適切なフォルダーをポイントします。 使用している SIEM 製品に基づいて、フィールド マッピングが必要になる可能性があります。

## <a name="integrate-azure-active-directory-audit-logs"></a>Azure Active Directory と監査ログの統合
1. コマンド プロンプトと **cd** を **c:\Program Files\Microsoft Azure Log Integration** に開きます。
2. tenantID を提供するコマンドを実行します。 コマンドを実行するには、テナント管理者ロールのメンバーである必要があります。

AZLOG.exe authorizedirectoryreader tenantId

サンプル -

AZLOG.exe authorizedirectoryreader ba2c0023-d24b-4f4e-92b1-48c4469999


3. 次のフォルダーを調べて、Azure Active Directory 監査ログの JSON ファイルが作成されていることを確認します。
* **C:\Users\azlog\AzureActiveDirectoryJson**  
* **C:\Users\azlog\AzureActiveDirectoryJsonLD**

4. 標準的な SIEM ファイル フォワーダー コネクタで、SIEM インスタンスにデータをパイプ処理する適切なフォルダーをポイントします。 使用している SIEM 製品に基づいて、フィールド マッピングが必要になる可能性があります。

[Azure Active Directory で監査イベントとして現在記録されるイベントの一覧](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events)

インストールおよび構成中に問題が発生した場合、[サポート要求](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request)を作成し、サポートを要求するサービスとして 'Log Integration' を選択します。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure ログ統合をインストールし、Azure ストレージからログを統合する方法について説明しました。 詳細については、次の記事を参照してください。

* [Azure ログ用の Microsoft Azure ログ統合 (プレビュー)](https://www.microsoft.com/download/details.aspx?id=53324) – Azure ログ統合の詳細情報、システム要件、およびインストール手順のダウンロード センター。
* [Azure ログ統合の概要](security-azure-log-integration-overview.md) – このドキュメントでは、Azure ログ統合と、その主な機能およびしくみについて紹介します。
* [パートナーの構成手順](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – このブログ投稿では、Splunk、HP ArcSight、IBM QRadar などのパートナー ソリューションを使用できるように、Azure ログ統合を構成する方法について説明します。
* [Azure ログ統合のよく寄せられる質問 (FAQ)](security-azure-log-integration-faq.md) – この FAQ は、Azure ログ統合について寄せられる質問とその回答です。
* [Azure ログ統合への Security Center の警告の統合](../security-center/security-center-integrating-alerts-with-log-integration.md) – このドキュメントでは、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションと同期させる方法について説明します。
* [Azure 診断および Azure 監査ログの新機能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – このブログ投稿では、Azure 監査ログと、Azure リソースの操作の洞察を得るのに役立つその他の機能を紹介します。

