---
title: "Azure Data Factory での自己ホスト型統合ランタイムの作成 | Microsoft Docs"
description: "Azure Data Factory で自己ホスト型統合ランタイムを作成する方法について説明します。これにより、データ ファクトリがプライベート ネットワーク内のデータ ストアにアクセスすることができます。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 18f5aea960bca34699d2d265d4801797291a3e3a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成し構成する方法
統合ランタイム (IR) は、異なるネットワーク環境間でデータ統合機能を提供するために Azure Data Factory によって使用されるコンピューティング インフラストラクチャです。 IR に関する詳細については、[ランタイム統合の概要](concepts-integration-runtime.md)を参照してください。 

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-introduction.md)を参照してください。

自己ホスト型統合ランタイムでは、クラウド データストアとプライベート ネットワーク内のデータストアとの間でコピー アクティビティを実行し、変換アクティビティをオンプレミスのコンピューティング リソース、または Azure Virtual Network 上のコンピューティング リソースにディスパッチすることができます。 自己ホスト型統合ランタイムのインストールには、オンプレミスのマシン、またはプライベート ネットワーク内の仮想マシンが必要です。  

このドキュメントでは、自己ホスト型 IR を作成し構成する方法について説明します。

## <a name="high-level-steps-to-install-self-hosted-ir"></a>自己ホスト型 IR をインストールする手順の概要
1.  自己ホスト型統合ランタイムを作成します。 PowerShell の例を次に示します。 

    ```powershell
    New-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2.  自己ホスト型統合ランタイムをダウンロードし (ローカル コンピューターに) インストールします。
3.  認証キーを取得し、そのキーを使用して自己ホスト型統合ランタイムを登録します。 PowerShell の例を次に示します。 

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```
    
## <a name="command-flow-and-data-flow"></a>コマンド フローとデータ フロー
オンプレミスとクラウドの間でデータを移動するとき、アクティビティは自己ホスト型統合ランタイムを使用して、オンプレミスのデータ ソースからクラウドに、またはその逆に、データを転送します。 

自己ホスト型の IR でコピーする手順の概要についてのデータ フローを次に示します。

![概要](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. データの開発者は、PowerShell コマンドレットを使用して Azure データ ファクトリ内で自己ホスト型統合ランタイムを作成します。 現時点では、Azure ポータルはこの機能をサポートしていません。
2. データの開発者は、データ ストアへの接続に使用する自己ホスト型統合ランタイムのインスタンスを指定することによって、オンプレミスのデータ ストアへのリンクされたサービスを作成します。 リンクされたサービスの設定の一部として、データの開発者は「資格情報マネージャー」アプリケーション (現時点ではサポートされていません) を使用して、認証のタイプと資格情報を設定します。 資格情報マネージャー アプリケーションのダイアログは、データ ストアと通信して接続をテストし、自己ホスト型統合ランタイムと通信して資格情報を保存します。
4.  自己ホスト型統合ランタイム ノードは、Windows データ保護 API を使用して資格情報を暗号化し、ローカルに保存します。 高可用性を目的として複数ノードが設定されている場合、資格情報はさらに他のノード間で同期されます。 各ノードは、DPAPI を使用して暗号化し、ローカルに格納します。 資格情報の同期は、データの開発者に透過的であり、自己ホスト型 IR によって処理されます。    
5.  Data Factory サービスは、共有 Azure Service Bus キューを使用する**制御チャネル**を介して、ジョブのスケジューリングと管理のために自己ホスト型統合ランタイムと通信します。 アクティビティ ジョブを実行する必要がある場合、Data Factory は、その要求を資格情報と共に (資格情報が自己ホスト型統合ランタイムに保存されていない場合) キューに入れます。 自己ホスト型統合ランタイムは、そのキューをポーリングした後、ジョブを起動します。
6.  自己ホスト型統合ランタイムは、データ パイプラインでのコピー アクティビティの構成方法に応じて、オンプレミスのストアからクラウドのストレージに、またはその逆に、データをコピーします。 この手順では、自己ホスト型統合ランタイムは、セキュリティで保護された (HTTPS) チャネルを使用して、クラウド ベースのストレージ サービス (Azure Blob Storage など) と直接通信します。

## <a name="considerations-for-using-self-hosted-ir"></a>自己ホスト型 IR の使用に関する注意点

- 1 つの自己ホスト型統合ランタイムは、複数のオンプレミス データ ソースで使用できます。 ただし、**1 つの自己ホスト型統合ランタイムはただ 1 つの Azure Data Factory に関連付けられており**、別の Data Factory と共有することはできません。
- 1 台のコンピューターには、**自己ホスト型統合ランタイムのインスタンスを 1 つだけ** インストールできます。 たとえば、オンプレミスのデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、オンプレミスのコンピューター 2 台に自己ホスト型統合ランタイムをインストールする必要があります。 つまり、自己ホスト型統合ランタイムは、特定のデータ ファクトリに関連付けられています。
- **自己ホスト型統合ランタイムはデータ ソースと同じコンピューター上に存在する必要はありません**。 ただし、自己ホスト型統合ランタイムをデータ ソースの近くに配置することにより、自己ホスト型統合ランタイムがデータ ソースに接続する時間が短縮されます。 自己ホスト型統合ランタイムは、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにインストールすることをお勧めします。 自己ホスト型統合ランタイムとデータ ソースが別のコンピューターにある場合、自己ホスト型統合ランタイムはデータ ソースとリソースの競合を起こしません。
- **同じオンプレミス データ ソースに接続する異なるコンピューターで複数のゲートウェイ自己ホスト型統合ランタイムを使用**できます。 たとえば、2 つのデータ ファクトリを提供する 2 つの自己ホスト型統合ランタイムがあり、どちらのデータ ファクトリにも同じオンプレミス データ ソースが登録されている場合があります。
- **Power BI** シナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、**Azure Data Factory 用の自己ホスト型統合ランタイム**を別のコンピューターにインストールします。
- 自己ホスト型統合ランタイムは、Azure 仮想ネットワーク内のデータの統合をサポートするために使用する必要があります。
- **ExpressRoute**を使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを扱います。 自己ホスト型統合ランタイムを使用してサービスとデータ ソースの間の接続を確立します。
- データ ストアが**Azure IaaS 仮想マシン**上のクラウド内にある場合でも、自己ホスト型統合ランタイムを使用する必要があります。

## <a name="prerequisites"></a>前提条件 

- サポートされている **オペレーティング システム** のバージョンは、Windows 7、Windows 8/8.1、Windows 10、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 です。 自己ホスト型統合ランタイムの**ドメイン コントローラーへのインストールはサポートされていません**。
- **.NET Framework 4.6.1 以降**が必要です。 Windows 7 コンピューターに自己ホスト型統合ランタイムをインストールする場合は、.NET Framework 4.6.1 以降をインストールします。 詳細については、「 [.NET Framework システム要件](/dotnet/framework/get-started/system-requirements) 」をご覧ください。
- 自己ホスト型統合ランタイム コンピューターの推奨される最小限の **構成** は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
- ホスト コンピューターが休止状態の場合、自己ホスト型統合ランタイムはデータ要求に応答しません。 そのため、自己ホスト型統合ランタイムをインストールする前に、コンピューターの適切な源プランを構成します。 コンピューターが休止状態に入るよう構成されている場合、自己ホスト型統合ランタイムのインストール時にメッセージが出力されます。
- 自己ホスト型統合ランタイムを正常にインストールして構成するには、コンピューターの管理者である必要があります。
- コピー アクティビティは特定の頻度で実行されるので、コンピューターのリソース (CPU、メモリ) 使用率も、ピーク時間とアイドル時間の同じパターンに従います。 リソース使用率はまた、移動されるデータの量に大きく依存します。 複数のコピー ジョブが進行中のときには、ピーク時にリソース使用率が上昇します。

## <a name="installation-best-practices"></a>インストールのベスト プラクティス
自己ホスト型統合ランタイムは、MSI セットアップ パッケージを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)からダウンロードしてインストールできます。 詳細な手順については、[オンプレミスとクラウドの間でのデータ移動](tutorial-hybrid-copy-powershell.md)に関する記事を参照してください。
  
- コンピューターが休止状態にならないように、自己ホスト型統合ランタイム用のホスト コンピューターの電源プランを構成します。 ホスト コンピューターが休止状態になると、自己ホスト型統合ランタイムはオフラインになります。
- 定期的に、自己ホスト型統合ランタイムに関連付けられている資格情報をバックアップします。

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>ダウンロード センターからの自己ホスト型 IR のインストールと登録

1. [Microsoft 統合ランタイム ダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=39717)に移動します。
2. **[ダウンロード]** をクリックし、適切なバージョン (**32 ビット** または **64 ビット**) を選択して、**[次へ]** をクリックします。
3. **MSI** を直接実行するか、ハード ディスクに保存してから実行します。
4. **[ようこそ]** ページで **[言語]** を選択し、**[次へ]** をクリックします。
5. 使用許諾契約に**同意**し、**[次へ]** をクリックします。
6. **フォルダー**を選択して自己ホスト型統合ランタイムをインストールし、**次**をクリックします。
7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。
8. **[完了]** をクリックしてインストールを完了します。
9. Azure PowerShell を使用して認証キーを取得します。 認証キーを取得するための PowerShell の例:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. コンピューターで実行している Microsoft 統合ランタイム構成マネージャーの**統合ランタイム (自己ホスト型) 登録**ページで、次の手順を実行します。
    1. **認証キー**をテキスト領域に貼り付けます。
    2. オプションとして、キー文字列を表示する場合は **[Show gateway key (ゲートウェイのキーを表示する)]** をクリックします。
    3. **[登録]**をクリックします。


## <a name="high-availability-and-scalability"></a>高可用性とスケーラビリティ
自己ホスト型統合ランタイムは、複数のオンプレミス コンピューターに関連付けできます。 これらのコンピューターは、ノードと呼ばれます。 最大で 4 つのノードをを 1 つの自己ホスト型統合ランタイムに関連付けできます。 1 つの論理ゲートウェイと複数のノード (ゲートウェイがインストールされているオンプレミス コンピューター) を関連付ける利点は次のとおりです。 
1. 自己ホスト型統合ランタイムの可用性が向上したことによって、ビッグ データ ソリューションや Azure Data Factory を使用したクラウド データ統合において、単一の障害発生点となることはなくなりました。最大 4 つのノードによって、継続性を確保しています。
2. オンプレミスとクラウド データ ストアとの間のデータ移動は、パフォーマンスとスループットが向上しました。 詳しくは[パフォーマンス比較](copy-activity-performance.md)を参照してください。

[ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から自己ホスト型統合ランタイム ソフトウェアをインストールして、新しい AzureRmDataFactoryV2IntegrationRuntimeKey コマンドレットのいずれかによって登録するだけで、複数のノードを関連付けることができます。[チュートリアル](tutorial-hybrid-copy-powershell.md)に説明があります。 

> [!NOTE]
> 各ノードを関連付けるために新しい自己ホスト型統合ランタイムを作成する必要はありません。 

## <a name="system-tray-icons-notifications"></a>システム トレイ アイコン/通知
システム トレイ アイコン/通知メッセージの上にカーソルを移動すると、自己ホスト型統合ランタイムの状態の詳細が表示されます。

![システム トレイ通知メッセージ](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>ポートとファイアウォール
考慮する必要がある 2 つのファイアウォールがあります。組織の中央ルーターで実行している**企業ファイアウォール**と、自己ホスト型統合ランタイムがインストールされているローカル コンピューター上のデーモンとして構成されている **Windows ファイアウォール**です。

![ファイアウォール](media\create-self-hosted-integration-runtime\firewall.png)

**企業ファイアウォール**レベルで、次のドメインと送信ポートを構成する必要があります。

ドメイン名 | ポート | 説明
------------ | ----- | ------------
*.servicebus.windows.net | 443、80 | Data Movement Service のバックエンドとの通信に使用
*.core.windows.net | 443 | Azure BLOB を使用した段階的なコピーに使用 (構成されている場合)
*. frontend.clouddatahub.net | 443 | Data Movement Service のバックエンドとの通信に使用

**Windows ファイアウォール**のレベル (コンピューター レベル) では、通常これらの送信ポートが有効になっています。 有効でない場合は、自己ホスト型統合ランタイム コンピューターに応じたドメインとポートを構成することができます。

> [!NOTE]
> お使いのソースやシンクに基づいて、追加のドメインと送信ポートを企業や Windows のファイアウォールのホワイトリストに追加する必要があります。
>
> 一部のクラウド データベース (Azure SQL Database、Azure Data Lake など) では、そのファイアウォールの構成で自己ホスト型統合ランタイム コンピューターの IP アドレスをホワイトリストに追加する必要があります。

### <a name="copy-data-from-a-source-to-a-sink"></a>ソースからシンクへのデータのコピー 
ファイアウォール ルールが企業ファイアウォール上で、Windows ファイアウォールが自己ホスト型統合ランタイム コンピューター上で適切に有効化されていること、およびデータ ストア自体が適切に有効化されていることを確認します。 このルールを有効にすると、自己ホスト型統合ランタイムは、ソースとシンクの両方に正常に接続されます。 コピー操作に関連するデータ ストアごとにルールを有効にしてください。

たとえば、**オンプレミスのデータ ストアから Azure SQL Database シンク、または Azure SQL Data Warehouse シンクへ**コピーするには、次の手順を実行します。

- 送信 **TCP** 通信を、Windows ファイアウォールと企業ファイアウォールの両方に対して、ポート **1433** 上で許可します。
- Azure SQL server のファイアウォール設定を、自己ホスト型統合ランタイム コンピューターの IP アドレスを許可された IP アドレスのリストに追加するよう構成します。

> [!NOTE]
> ファイアウォールで送信ポート 1433 を許可していない場合、自己ホスト型統合ランタイムは Azure SQL に直接アクセスすることはできません。 この場合は、SQL Azure データベースや SQL Azure DW に[ステージング コピー](copy-activity-performance.md)を使用できます。 このシナリオでは、データ移動に HTTPS (ポート 443) が必要になるだけです。


## <a name="proxy-server-considerations"></a>プロキシ サーバーに関する考慮事項
企業ネットワーク環境でプロキシ サーバーを使用してインターネットにアクセスしている場合は、適切なプロキシ設定を使用するように自己ホスト型統合ランタイムを構成します。 プロキシは、初期登録フェーズ中に設定できます。

![プロキシの指定](media\create-self-hosted-integration-runtime\specify-proxy.png)

自己ホスト型統合ランタイムは、プロキシ サーバーを使用してクラウド サービスに接続します。 初期セットアップする際に [変更] リンクをクリックします。 [プロキシ設定] ダイアログが表示されます。

![プロキシの設定](media\create-self-hosted-integration-runtime\set-http-proxy.png)

3 つの構成オプションがあります。

- **プロキシを使用しない**: 自己ホスト型統合ランタイムは、クラウド サービスに接続するときにプロキシを明示的には使用しません。
- **システム プロキシを使用する**: 自己ホスト型統合ランタイムは、diahost.exe.config と diawp.exe.config で構成されているプロキシ設定を使用します。diahost.exe.config と diawp.exe.config でプロキシが構成されていない場合、自己ホスト型統合ランタイムはプロキシを経由せず直接クラウド サービスに接続します。
- **カスタム プロキシを使用する**: diahost.exe.config と diawp.exe.config の構成は使用せず、HTTP プロキシ設定を自己ホスト型統合ランタイム用に構成します。アドレスとポートが必要です。 ユーザー名とパスワードは、プロキシの認証設定によっては省略できます。 すべての設定は Windows DPAPI を使用して、自己ホスト型統合ランタイム上で暗号化され、コンピューターにローカルに格納されます。

統合ランタイムのホスト サービスは、更新済みのプロキシ設定を保存した後に自動的に再起動されます。

自己ホスト型統合ランタイムが正常に登録された後、プロキシ設定を表示または更新する必要がある場合は、Integration Runtime Configuration Manager を使用します。

1.  **Microsoft Integration Runtime Configuration Manager** を起動します。
2.  **[設定]** タブに切り替えます。
3.  **[HTTP プロキシ]** セクションの **[変更]** リンクをクリックして、**[HTTP プロキシを設定する]** ダイアログを開きます。
4.  **[次へ]** をクリックすると、プロキシ設定を保存して Integration Runtime Host Service を再起動するためのアクセス許可を求める警告ダイアログが表示されます。

HTTP プロキシを表示して更新するには、構成マネージャー ツールを使用します。

![プロキシの表示](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> NTLM 認証でプロキシ サーバーを設定する場合は、ドメイン アカウントで Integration runtime Host Service が実行されます。 ドメイン アカウントのパスワードを後で変更する場合は、忘れずにサービスの構成設定を更新し、必要に応じて再起動してください。 この要件のため、専用のドメイン アカウントを使用して、パスワードを頻繁に更新する必要がないプロキシ サーバーにアクセスすることをお勧めします。

### <a name="configure-proxy-server-settings"></a>プロキシ サーバーの設定を構成する

HTTP プロキシに対して **[システム プロキシを使用する]** 設定を選択すると、自己ホスト型統合ランタイムは、diahost.exe.config と diawp.exe.config のプロキシ設定を使用します。diahost.exe.config と diawp.exe.config でプロキシが指定されていない場合、自己ホスト型統合ランタイムはプロキシを経由せず直接クラウド サービスに接続します。 diahost.exe.config ファイルを更新する手順を次に示します。

1. ファイル エクスプ ローラーで、C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config の安全なコピーを作成して、元のファイルをバックアップします。
2. 管理者として Notepad.exe を起動し、テキスト ファイル C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config を開きます。次のコードに示されている system.net の既定のタグを確認します。

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    その後、次の例で示すように、プロキシ サーバーの詳細を追加できます。

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ``` 
    
    追加のプロパティは、プロキシ タグ内で scriptLocation のように必要な設定を指定することが許可されます。 構文については、[proxy 要素 (ネットワーク設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) を参照してください。

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 構成ファイルを元の場所に保存し、Self-hosted Integration Runtime ホスト サービスを再起動して、変更を取得します。 サービスを再起動するには、コントロール パネルからサービス アプレットを使用するか、**[Integration Runtime Configuration Manager]**、**[サービスの停止]**、**[サービスの開始]** の順にクリックします。 サービスが開始しない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

> [!IMPORTANT]
> diahost.exe.config と diawp.exe.config の両方を忘れずに更新してください。

この点に加えて、Microsoft Azure が会社のホワイトリストにあることを確認する必要もあります。 有効な Microsoft Azure の IP アドレスの一覧は、 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>ファイアウォールとプロキシ サーバー関連の問題で発生する可能性がある症状
次のようなエラーが発生した場合は、ファイアウォールまたはプロキシ サーバーの不適切な構成が原因になっている可能性があります。構成が不適切だと、自己ホスト型統合ランタイムが自身を認証するための Data Factory に接続できません。 ファイアウォールとプロキシ サーバーが正しく構成されていることを確認するには、前のセクションをご覧ください。

1.  自己ホスト型統合ランタイムを登録するときに、次のエラーを受け取ります。:「この統合ランタイム ノードの登録に失敗しました。」 認証キーが有効であること、およびこのマシンで Integration Service Host Service が実行されていることを確認します。 "
2.  Integration Runtime Configuration Manager を開くと、ステータスとして “**切断**” または “**接続中**” と表示されます。 [イベント ビューアー]、[アプリケーションとサービス ログ]、[Microsoft Integration Runtime] の順に選択して Windows イベント ログを表示すると、次のようなエラーが表示されます。

    ```
    Unable to connect to the remote server 
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="open-port-8060-for-credential-encryption"></a>資格情報の暗号化のためにポート 8060 を開く
Azure ポータルでオンプレミスのリンクされたサービスを設定するとき、**資格情報の設定**アプリケーション (現在サポートされていません) では、受信ポート 8060 を使って、資格情報が自己ホスト型統合ランタイムにリレーされます。 自己ホスト型統合ランタイムのセットアップ中に、既定では、自己ホスト型統合ランタイムのインストールが、自己ホスト型統合ランタイム コンピューター上でこのポートを開きます。

サードパーティ製のファイアウォールを使用する場合は、ポート 8050 を手動で開くことができます。 自己ホスト型統合ランタイムの設定中にファイアウォールの問題が発生した場合は、次のコマンドを使用すると、ファイアウォールを構成せずに自己ホスト型統合ランタイムをインストールできます。

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

自己ホスト型統合ランタイム コンピューター上でポート 8060 を開かない場合は、「資格情報の設定」アプリケーション以外のメカニズムを使用して、データ ストア資格情報を構成する必要があります。 たとえば、 New-AzureRmDataFactoryV2LinkedServiceEncryptCredential PowerShell コマンドレットを使用できます。 データ ストア資格情報を設定する方法については、「資格情報とセキュリティの設定」に関するセクションを参照してください。


## <a name="next-steps"></a>次のステップ
詳細な手順については、[チュートリアル: オンプレミスのデータをクラウドにコピー](tutorial-hybrid-copy-powershell.md)のチュートリアルを参照してください。

