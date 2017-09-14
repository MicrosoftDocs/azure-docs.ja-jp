---
title: "データ移動のための Data Management Gateway | Microsoft Docs"
description: "オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。 Azure Data Factory で Data Management Gateway を使用してデータを移動します。"
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9e40eba285aeb1cce6b77311d1b69a6b96967a0b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="data-management-gateway"></a>Data Management Gateway
データ管理ゲートウェイは、クラウドと、オンプレミスのデータ ストアとの間でデータをコピーするために、オンプレミスの環境にインストールする必要があるクライアント エージェントです。 Data Factory でサポートされているオンプレミスのデータ ストアは、 [サポートされるデータ ソース](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関するセクションに示されています。

この記事は、 [オンプレミスとクラウドのデータ ストアの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) ためのチュートリアルを補完します。 チュートリアルでは、ゲートウェイを使用して、オンプレミスの SQL Server データベースから Azure BLOB にデータを移動するパイプラインを作成します。 この記事では、データ管理ゲートウェイについて詳しく取り上げます。 

データ管理ゲートウェイは、複数のオンプレミス コンピューターをゲートウェイに関連付けることによって、スケールアウトできます。 1 つのノードで同時に実行できるデータ移動ジョブ数を増やすことで、スケールアップできます。 この機能は、単一のノードを持つ論理ゲートウェイでも使用できます。 詳細については、[Azure Data Factory でのデータ管理ゲートウェイのスケーリング](data-factory-data-management-gateway-high-availability-scalability.md)に関する記事をご覧ください。

> [!NOTE]
> 現時点では、ゲートウェイでサポートされるのは Data Factory でのコピー アクティビティとストアド プロシージャ アクティビティのみです。 カスタム アクティビティからゲートウェイを使用してオンプレミスのデータ ソースにアクセスすることはできません。      

## <a name="overview"></a>概要
### <a name="capabilities-of-data-management-gateway"></a>データ管理ゲートウェイの機能
データ管理ゲートウェイには次の機能があります。

* 同じ Data Factory 内にオンプレミスのデータ ソースとクラウドのデータ ソースをモデル化して、データを移動します。
* [データ ファクトリ] ページにゲートウェイの状態を表示して、一元的に監視および管理を行います。
* オンプレミスのデータ ソースへの安全なアクセスを管理します。
  * 企業のファイアウォールを変更する必要はありません。 Gateway は、インターネットを開くために送信 HTTP ベースの接続のみを行います。
  * 証明書でオンプレミスのデータ ストアの資格情報を暗号化します。
* データを効率的に移動します。データは並列に転送されます。自動再試行ロジックにより、断続的なネットワークの問題にも対応できます。

### <a name="command-flow-and-data-flow"></a>コマンド フローとデータ フロー
コピー アクティビティを使用してオンプレミスとクラウドの間でデータをコピーするとき、アクティビティはゲートウェイを使用して、オンプレミスのデータ ソースからクラウドに、またはその逆に、データを転送します。

データ ゲートウェイでのコピーのおおまかなデータ フローと手順の概要を次に示します。![ゲートウェイを使用したデータ フロー](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. データ開発者は、[Azure portal](https://portal.azure.com) または [PowerShell コマンドレット](https://msdn.microsoft.com/library/dn820234.aspx)を使用して Azure Data Factory 用のゲートウェイを作成します。
2. データ開発者は、ゲートウェイを指定することで、オンプレミスのデータ ストアに使用するリンクされたサービスを作成します。 リンクされたサービスの設定の一部として、データ開発者は、資格情報の設定アプリケーションを使用して認証の種類と資格情報を指定します。  資格情報の設定アプリケーションのダイアログは、データ ストアと通信して接続をテストし、ゲートウェイと通信して資格情報を保存します。
3. ゲートウェイは、資格情報をクラウドに保存する前に、(開発者によって提供された) ゲートウェイと関連付けられた証明書で資格情報を暗号化します。
4. Data Factory サービスは、共有 Azure Service Bus キューを使用する制御チャネルを介して、ジョブのスケジューリングと管理のためにゲートウェイと通信します。 コピー アクティビティ ジョブを開始する必要がある場合、Data Factory はリクエストと資格情報をキューに入れます。 ゲートウェイは、キューをポーリングした後でジョブを開始します。
5. ゲートウェイは、同じ証明書で資格情報を復号化し、適切な認証の種類と資格情報を使用してオンプレミスのデータ ストアに接続します。
6. ゲートウェイは、データ パイプラインでのコピー アクティビティの構成方法に応じて、オンプレミスのストアからクラウドのストレージに、またはその逆に、データをコピーします。 この手順では、ゲートウェイは、セキュリティで保護された (HTTPS) チャネルを使用して、クラウド ベースのストレージ サービス (Azure Blob Storage など) と直接通信します。

### <a name="considerations-for-using-gateway"></a>ゲートウェイの使用に関する注意点
* データ管理ゲートウェイの 1 つのインスタンスを、複数のオンプレミス データ ソースに使用できますが、 **1 つの Gateway インスタンスはただ 1 つの Azure Data Factory に関連付けられており**、別の Data Factory と共有することはできません。
* 1 台のコンピューターには、**データ管理ゲートウェイのインスタンスを 1 つだけ** インストールできます。 たとえば、オンプレミスのデータ ソースにアクセスする必要があるデータ ファクトリが 2 つあるとすると、オンプレミスのコンピューター 2 台にゲートウェイをインストールする必要があります。 つまり、ゲートウェイが、特定のデータ ファクトリに関連付けられています
* **ゲートウェイはデータ ソースと同じコンピューター上に存在する必要はありませんが**、 データ ソースの近くにあると、ゲートウェイがデータ ソースに接続するときの時間が短縮されます。 ゲートウェイは、オンプレミス データ ソースをホストするコンピューターとは異なるコンピューターにインストールすることをお勧めします。 ゲートウェイとデータ ソースが異なるコンピューターにあると、ゲートウェイと、データ ソースのリソースで競合が発生しません。
* **同じオンプレミス データ ソースに接続する異なるコンピューターで複数のゲートウェイを使用**できます。 たとえば、2 つのデータ ファクトリを提供する 2 つのゲートウェイがあり、どちらのデータ ファクトリにも同じオンプレミス データ ソースが登録されている場合があります。
* **Power BI** シナリオを提供するゲートウェイがコンピューターに既にインストールされている場合は、**Azure Data Factory 用のゲートウェイ**を別のコンピューターにインストールします。
* **ExpressRoute**を使用する場合でも、ゲートウェイを使用する必要があります。
* **ExpressRoute**を使用する場合であっても (ファイアウォールの背後にある) オンプレミスのデータ ソースとしてデータ ソースを扱います。 ゲートウェイを使用してサービスとデータ ソースの間の接続を確立します。
* データ ストアがクラウドの **Azure IaaS VM** に存在する場合でも、**ゲートウェイを使用す**る必要があります。

## <a name="installation"></a>インストール
### <a name="prerequisites"></a>前提条件
* サポートされている **オペレーティング システム** のバージョンは、Windows 7、Windows 8/8.1、Windows 10、Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 です。 ドメイン コントローラーへのデータ管理ゲートウェイのインストールは現在サポートされていません。
* .NET framework 4.5.1 以降が必要です。 Windows 7 コンピューターにゲートウェイをインストールする場合は、.NET Framework 4.5 以降をインストールします。 詳細については、「 [.NET Framework システム要件](https://msdn.microsoft.com/library/8z6watww.aspx) 」をご覧ください。
* ゲートウェイ コンピューターの推奨される最小限の **構成** は、2 GHz、4 コア、8 GB の RAM、80 GB のディスクです。
* ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答しません。 そのため、ゲートウェイをインストールする前に、コンピューターの適切な **電源プラン** を構成します。 コンピューターが休止状態に構成されている場合、ゲートウェイのインストール時にメッセージが表示されます。
* データ管理ゲートウェイを正常にインストールして構成するには、コンピューターの管理者である必要があります。 他のユーザーをローカル Windows グループの**データ管理ゲートウェイ ユーザー**に追加できます。 このグループのメンバーは、**Data Management Gateway 構成マネージャー** ツールを使用して、ゲートウェイを構成できます。

コピー アクティビティは特定の頻度で実行されるので、コンピューターのリソース (CPU、メモリ) 使用率も、ピーク時間とアイドル時間の同じパターンに従います。 リソース使用率はまた、移動されるデータの量に大きく依存します。 複数のコピー ジョブが進行中のときには、ピーク時にリソース使用率が上昇します。

### <a name="installation-options"></a>インストール オプション
データ管理ゲートウェイは、次の方法でインストールすることができます。

* [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードします。  MSI は、すべての設定を保持しながら、既存のデータ管理ゲートウェイを最新のバージョンにアップグレードするためにも使用できます。
* 手動セットアップで **[データ ゲートウェイのダウンロードとインストール]** のリンクをクリックするか、高速セットアップで **[このコンピューターに直接インストールします]** をクリックします。 高速セットアップを使う場合の詳細な手順については、 [オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事を参照してください。 手動セットアップでは、ダウンロード センターに移動します。  ダウンロード センターからゲートウェイをダウンロードしてインストールする手順については、次のセクションをご覧ください。

### <a name="installation-best-practices"></a>インストールのベスト プラクティス:
1. コンピューターが休止状態にならないように、ゲートウェイ用のホスト コンピューターの電源プランを構成します。 ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答しません。
2. ゲートウェイに関連付けられている証明書をバックアップします。

### <a name="install-the-gateway-from-download-center"></a>ダウンロード センターからゲートウェイをインストールする
1. [Microsoft Data Management Gateway のダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=39717)に移動します。
2. **[ダウンロード]** をクリックし、適切なバージョン (**32 ビット** または **64 ビット**) を選択して、**[次へ]** をクリックします。
3. **MSI** を直接実行するか、ハード ディスクに保存してから実行します。
4. **[ようこそ]** ページで **[言語]** を選択し、**[次へ]** をクリックします。
5. 使用許諾契約に**同意**し、**[次へ]** をクリックします。
6. ゲートウェイをインストールする**フォルダー**を選択し、**[次へ]** をクリックします。
7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。
8. **[完了]** をクリックしてインストールを完了します。
9. Azure Portal でキーを取得します。 手順の詳細については、次のセクションを参照してください。
10. コンピューターで実行されている **Data Management Gateway 構成マネージャー**の **[ゲートウェイの登録]** ページで、以下の手順を実行します。
    1. テキストにキーを貼り付けます。
    2. オプションとして、キー文字列を表示する場合は **[Show gateway key (ゲートウェイのキーを表示する)]** をクリックします。
    3. **[登録]**をクリックします。

### <a name="register-gateway-using-key"></a>キーを使用してゲートウェイを登録する
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>ポータルに論理ゲートウェイをまだ作成していない場合
ポータルにゲートウェイを作成し、**[構成]** ページからキーを取得するには、[オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に示されているチュートリアルの手順に従います。    

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>ポータルに論理ゲートウェイを既に作成している場合
1. Azure Portal で **[データ ファクトリ]** ページに移動し、**[リンクされたサービス]** タイルをクリックします。

    ![[データ ファクトリ] ページ](media/data-factory-data-management-gateway/data-factory-blade.png)
2. **[リンクされたサービス]** ページで、ポータルに作成した論理**ゲートウェイ**を選択します。

    ![論理ゲートウェイ](media/data-factory-data-management-gateway/data-factory-select-gateway.png)  
3. **[データ ゲートウェイ]** ページで、**[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。

    ![ポータルのダウンロード リンク](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)   
4. **[構成]** ページで、**[キーの再作成]** をクリックします。 警告メッセージを注意して読んでから、[はい] をクリックします。

    ![[キーの再作成]](media/data-factory-data-management-gateway/recreate-key-button.png)
5. キーの横にある [コピー] ボタンをクリックします。 キーがクリップボードにコピーされます。

    ![キーのコピー](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>システム トレイ アイコン/通知
次の図は、表示されるトレイ アイコンの一部を示しています。

![システム トレイのアイコン](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

システム トレイ アイコン/通知メッセージの上にカーソルを移動すると、ゲートウェイ/更新操作の状態の詳細がポップアップ ウィンドウに表示されます。

### <a name="ports-and-firewall"></a>ポートとファイアウォール
考慮する必要がある 2 つのファイアウォールがあります。組織の中央ルーターで実行している**企業ファイアウォール**と、ゲートウェイがインストールされているローカル コンピューター上のデーモンとして構成されている **Windows ファイアウォール**です。  

![ファイアウォール](./media/data-factory-data-management-gateway/firewalls2.png)

企業ファイアウォール レベルで、次のドメインと送信ポートを構成する必要があります。

| ドメイン名 | ポート | 説明 |
| --- | --- | --- |
| *.servicebus.windows.net |443、80 |Data Movement Service のバックエンドとの通信に使用 |
| *.core.windows.net |443 |Azure BLOB を使用した段階的なコピーに使用 (構成されている場合)|
| *. frontend.clouddatahub.net |443 |Data Movement Service のバックエンドとの通信に使用 |


Windows のファイアウォール レベルでは、通常これらの送信ポートが有効になっています。 有効でない場合は、ゲートウェイ コンピューターに応じたドメインとポートを構成することができます。

> [!NOTE]
> 1. お使いのソースやシンクに基づいて、追加のドメインと送信ポートを企業や Windows のファイアウォールのホワイトリストに追加する必要があります。
> 2. 一部のクラウド データベース ([Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings)、[Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access) など) では、そのファイアウォールの構成でゲートウェイ コンピューターの IP アドレスをホワイトリストに追加する必要があります。
>
>


#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>ソース データ ストアからシンク データ ストアへのデータのコピー
ファイアウォール ルールが企業ファイアウォール上で、Windows ファイアウォールがゲートウェイ コンピューター上で適切に有効化されていること、およびデータ ストア自体が適切に有効化されていることを確認します。 このルールを有効にすると、ゲートウェイは、ソースとシンクの両方に正常に接続されます。 コピー操作に関連するデータ ストアごとにルールを有効にしてください。

たとえば、 **Azure SQL Database シンクまたは Azure SQL Data Warehouse シンクにオンプレミスのデータ ストア**からコピーするには、以下の手順を実行します。

* 送信 **TCP** 通信を、Windows ファイアウォールと企業ファイアウォールの両方に対して、ポート **1433** 上で許可します。
* ゲートウェイ コンピューターの IP アドレスが、許可された IP アドレスの一覧に追加されるように、Azure SQL サーバーのファイアウォール設定を構成します。

> [!NOTE]
> ファイアウォールで送信ポート 1433 を許可していない場合、ゲートウェイは Azure SQL に直接アクセスすることはできません。 この場合は、SQL Azure データベースや SQL Azure DW に[ステージング コピー](https://docs.microsoft.com/azure/data-factory/data-factory-copy-activity-performance#staged-copy)を使用できます。 このシナリオでは、データ移動に HTTPS (ポート 443) が必要になるだけです。
>
>


### <a name="proxy-server-considerations"></a>プロキシ サーバーに関する考慮事項
企業ネットワーク環境でプロキシ サーバーを使用してインターネットにアクセスしている場合は、適切なプロキシ設定を使用するようにデータ管理ゲートウェイを構成します。 プロキシは、初期登録フェーズ中に設定できます。

![登録時にプロキシを設定](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

ゲートウェイは、プロキシ サーバーを使用してクラウド サービスに接続します。 初期セットアップする際に **[変更]** リンクをクリックします。 **[プロキシ設定]** ダイアログが表示されます。

![構成マネージャーを使用してプロキシを設定](media/data-factory-data-management-gateway/SetProxySettings.png)

3 つの構成オプションがあります。

* **プロキシを使用しない**: ゲートウェイは、クラウド サービスに接続するときにプロキシを明示的には使用しません。
* **システム プロキシを使用する**: ゲートウェイは、diahost.exe.config と diawp.exe.config で構成されているプロキシ設定を使用します。diahost.exe.config と diawp.exe.config でプロキシが構成されていない場合、ゲートウェイはプロキシを経由せず直接クラウド サービスに接続します。
* **カスタム プロキシを使用する**: diahost.exe.config と diawp.exe.config の構成は使用せず、HTTP プロキシ設定をゲートウェイ用に構成します。アドレスとポートが必要です。  ユーザー名とパスワードは、プロキシの認証設定によっては省略できます。  すべての設定が、ゲートウェイの資格情報証明書を使用して暗号化され、ゲートウェイ ホスト コンピューターにローカルで格納されます。

データ管理ゲートウェイのホスト サービスは、更新済みのプロキシ設定を保存した後に自動的に再起動されます。

ゲートウェイが正常に登録された後、プロキシ設定を表示または更新する必要がある場合は、Data Management Gateway 構成マネージャーを使用します。

1. **Data Management Gateway 構成マネージャー**を起動します。
2. **[設定]** タブに切り替えます。
3. **[HTTP プロキシ]** セクションの **[変更]** リンクをクリックして、**[HTTP プロキシを設定する]** ダイアログを開きます。  
4. **[次へ]** をクリックすると、プロキシ設定を保存して Gateway Host Service を再起動するためのアクセス許可を求める警告ダイアログが表示されます。

HTTP プロキシを表示して更新するには、構成マネージャー ツールを使用します。

![構成マネージャーを使用してプロキシを設定](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> NTLM 認証でプロキシ サーバーを設定する場合は、ドメイン アカウントで Gateway Host Service が実行されます。 ドメイン アカウントのパスワードを後で変更する場合は、忘れずにサービスの構成設定を更新し、必要に応じて再起動してください。 この要件のため、専用のドメイン アカウントを使用して、パスワードを頻繁に更新する必要がないプロキシ サーバーにアクセスすることをお勧めします。
>
>

### <a name="configure-proxy-server-settings"></a>プロキシ サーバーの設定を構成する
HTTP プロキシに対して **[システム プロキシを使用する]** 設定を選択すると、ゲートウェイは、diahost.exe.config と diawp.exe.config のプロキシ設定を使用します。diahost.exe.config と diawp.exe.config でプロキシが指定されていない場合、ゲートウェイはプロキシを経由せず直接クラウド サービスに接続します。 diahost.exe.config ファイルを更新する手順を次に示します。  

1. ファイル エクスプ ローラーで、C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config の安全なコピーを作成して、元のファイルをバックアップします。
2. 管理者として Notepad.exe を起動し、テキスト ファイル C:\Program Files\Microsoft Data Management Gateway\2.0\Shared\diahost.exe.config を開きます。次のコードに示されている system.net の既定のタグを確認します。

         <system.net>
             <defaultProxy useDefaultCredentials="true" />
         </system.net>    

   その後、次の例で示すように、プロキシ サーバーの詳細を追加できます。

         <system.net>
               <defaultProxy enabled="true">
                     <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
               </defaultProxy>
         </system.net>

   追加のプロパティは、プロキシ タグ内で scriptLocation のように必要な設定を指定することが許可されます。 構文については、「 [proxy 要素 (ネットワーク設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) 」を参照してください。

         <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
3. 構成ファイルを元の場所に保存し、Data Management Gateway ホスト サービスを再起動して、変更を取得します。 サービスを再起動するには、コントロール パネルからサービス アプレットを使用するか、**[Data Management Gateway 構成マネージャー]**、**[サービスの停止]**、**[サービスの開始]** の順にクリックします。 サービスが開始しない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

> [!IMPORTANT]
> diahost.exe.config と diawp.exe.config の**両方**を忘れずに更新してください。  


この点に加えて、Microsoft Azure が会社のホワイトリストにあることを確認する必要もあります。 有効な Microsoft Azure の IP アドレスの一覧は、 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>ファイアウォールとプロキシ サーバー関連の問題で発生する可能性がある症状
次のようなエラーが発生した場合は、ファイアウォールまたはプロキシ サーバーの不適切な構成が原因になっている可能性があります。構成が不適切だと、ゲートウェイが自身を認証するための Data Factory に接続できません。 ファイアウォールとプロキシ サーバーが正しく構成されていることを確認するには、前のセクションをご覧ください。

1. ゲートウェイを登録しようとすると次のエラーが発生します。「ゲートウェイのキーを登録できませんでした。 ゲートウェイのキーの登録を再試行する前に、Data Management Gateway が接続済み状態であり、Data Management Gateway Host Service が開始済みであることを確認してください。」
2. 構成マネージャーを開くと、ステータスとして "切断" または "接続中" と表示されます。 [イベント ビューアー]、[アプリケーションとサービス ログ]、[Data Management Gateway] の順に選択して Windows イベント ログを表示すると、次のようなエラーが表示されます。`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>資格情報の暗号化のためにポート 8050 を開く
Azure Portal でオンプレミスのリンクされたサービスを設定するとき、**資格情報の設**定アプリケーションでは、受信ポート **8050** を使って、資格情報がゲートウェイにリレーされます。 既定では、ゲートウェイのセットアップ中に、データ管理ゲートウェイのインストールによってこのポートがゲートウェイ コンピューターで開きます。

サードパーティ製のファイアウォールを使用する場合は、ポート 8050 を手動で開くことができます。 ゲートウェイの設定中にファイアウォールの問題が発生した場合は、次のコマンドを使用すると、ファイアウォールを構成せずにゲートウェイをインストールできます。

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

ゲートウェイ コンピューター上でポート 8050 を開かない場合は、 **資格情報の設定** アプリケーション以外のメカニズムを使用して、データ ストア資格情報を構成する必要があります。 たとえば、 [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell コマンドレットを使用できます。 データ ストア資格情報を設定する方法については、 [資格情報とセキュリティの設定](#set-credentials-and-securityy) に関するセクションを参照してください。

## <a name="update"></a>更新
既定では、データ管理ゲートウェイの新しいバージョンが利用可能になると、自動的に更新されます。 ゲートウェイは、すべてのスケジュールされたタスクが完了するまで、更新されません。 更新操作が完了するまで、ゲートウェイは追加のタスクを処理しません。 更新が失敗した場合、ゲートウェイは古いバージョンにロールバックします。

更新時刻のスケジュールは次の場所に表示されます。

* Azure Portal の [ゲートウェイ プロパティ] ページ。
* Data Management Gateway 構成マネージャーのホーム ページ
* システム トレイ通知メッセージ。

Data Management Gateway 構成マネージャーの [ホーム] タブには、更新スケジュールと、ゲートウェイが最後にインストール/更新された時刻が表示されます。

![更新のスケジュール](media/data-factory-data-management-gateway/UpdateSection.png)

今すぐ更新をインストールすることも、スケジュールされた時刻にゲートウェイが自動的に更新されるのを待つこともできます。 たとえば、次の図は Gateway 構成マネージャーに表示される通知メッセージと、すぐに更新をインストールする場合にクリックできる [更新] ボタンを示しています。

![DMG 構成マネージャーの更新](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

システム トレイの通知メッセージは、次の画像のようになります。

![システム トレイのメッセージ](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

システム トレイに更新操作 (手動または自動) の状態が表示されます。 Gateway 構成マネージャーを次回開いたときに、通知バーにゲートウェイが更新されたことを示すメッセージと、 [最新情報のトピック](data-factory-gateway-release-notes.md)へのリンクが表示されます。

### <a name="to-disableenable-auto-update-feature"></a>自動更新機能を無効/有効にするには
次の手順で、自動更新機能を無効/有効にすることができます。

[シングル ノード ゲートウェイ]
1. ゲートウェイ コンピューターで Windows PowerShell を起動します。
2. C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript フォルダーに切り替えます。
3. 自動更新機能をオフ (無効) にするには、次のコマンドを実行します。   

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off
    ```
4. オンに戻すには、次のコマンドを実行します。

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on  
    ```
[[高可用性とスケーラビリティを備えたマルチノード ゲートウェイ (プレビュー)](data-factory-data-management-gateway-high-availability-scalability.md)]
1. ゲートウェイ コンピューターで Windows PowerShell を起動します。
2. C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript フォルダーに切り替えます。
3. 自動更新機能をオフ (無効) にするには、次のコマンドを実行します。   

    高可用性機能を備えたゲートウェイ (プレビュー) については、別途 AuthKey パラメーターが必要となります。
    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -off -AuthKey <your auth key>
    ```
4. オンに戻すには、次のコマンドを実行します。

    ```PowerShell
    .\GatewayAutoUpdateToggle.ps1  -on -AuthKey <your auth key> 


## Configuration Manager
Once you install the gateway, you can launch Data Management Gateway Configuration Manager in one of the following ways:

1. In the **Search** window, type **Data Management Gateway** to access this utility.
2. Run the executable **ConfigManager.exe** in the folder: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

### Home page
The Home page allows you to do the following actions:

* View status of the gateway (connected to the cloud service etc.).
* **Register** using a key from the portal.
* **Stop** and start the **Data Management Gateway Host service** on the gateway machine.
* **Schedule updates** at a specific time of the days.
* View the date when the gateway was **last updated**.

### Settings page
The Settings page allows you to do the following actions:

* View, change, and export **certificate** used by the gateway. This certificate is used to encrypt data source credentials.
* Change **HTTPS port** for the endpoint. The gateway opens a port for setting the data source credentials.
* **Status** of the endpoint
* View **SSL certificate** is used for SSL communication between portal and the gateway to set credentials for data sources.  

### Diagnostics page
The Diagnostics page allows you to do the following actions:

* Enable verbose **logging**, view logs in event viewer, and send logs to Microsoft if there was a failure.
* **Test connection** to a data source.  

### Help page
The Help page displays the following information:  

* Brief description of the gateway
* Version number
* Links to online help, privacy statement, and license agreement.  

## Monitor gateway in the portal
In the Azure portal, you can view near-real time snapshot of resource utilization (CPU, memory, network(in/out), etc.) on a gateway machine.  

1. In Azure portal, navigate to the home page for your data factory, and click **Linked services** tile. 

    ![Data factory home page](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png) 
2. Select the **gateway** in the **Linked services** page.

    ![Linked services page](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. In the **Gateway** page, you can see the memory and CPU usage of the gateway.

    ![CPU and memory usage of gateway](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png) 
4. Enable **Advanced settings** to see more details such as network usage.
    
    ![Advanced monitoring of gateway](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

The following table provides descriptions of columns in the **Gateway Nodes** list:  

Monitoring Property | Description
:------------------ | :---------- 
Name | Name of the logical gateway and nodes associated with the gateway. Node is an on-premises Windows machine that has the gateway installed on it. For information on having more than one node (up to four nodes) in a single logical gateway, see [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md).    
Status | Status of the logical gateway and the gateway nodes. Example: Online/Offline/Limited/etc. For information about these statuses, See [Gateway status](#gateway-status) section. 
Version | Shows the version of the logical gateway and each gateway node. The version of the logical gateway is determined based on version of majority of nodes in the group. If there are nodes with different versions in the logical gateway setup, only the nodes with the same version number as the logical gateway function properly. Others are in the limited mode and need to be manually updated (only in case auto-update fails). 
Available memory | Available memory on a gateway node. This value is a near real-time snapshot. 
CPU utilization | CPU utilization of a gateway node. This value is a near real-time snapshot. 
Networking (In/Out) | Network utilization of a gateway node. This value is a near real-time snapshot. 
Concurrent Jobs (Running/ Limit) | Number of jobs or tasks running on each node. This value is a near real-time snapshot. Limit signifies the maximum concurrent jobs for each node. This value is defined based on the machine size. You can increase the limit to scale up concurrent job execution in advanced scenarios, where CPU/memory/network is under-utilized, but activities are timing out. This capability is also available with a single-node gateway (even when the scalability and availability feature is not enabled).  
Role | There are two types of roles in a multi-node gateway – Dispatcher and worker. All nodes are workers, which means they can all be used to execute jobs. There is only one dispatcher node, which is used to pull tasks/jobs from cloud services and dispatch them to different worker nodes (including itself).

In this page, you see some settings that make more sense when there are two or more nodes (scale out scenario) in the gateway. See [Data Management Gateway - high availability and scalability](data-factory-data-management-gateway-high-availability-scalability.md) for details about setting up a multi-node gateway.

### Gateway status
The following table provides possible statuses of a **gateway node**: 

Status  | Comments/Scenarios
:------- | :------------------
Online | Node connected to Data Factory service.
Offline | Node is offline.
Upgrading | The node is being auto-updated.
Limited | Due to Connectivity issue. May be due to HTTP port 8050 issue, service bus connectivity issue, or credential sync issue. 
Inactive | Node is in a configuration different from the configuration of other majority nodes.<br/><br/> A node can be inactive when it cannot connect to other nodes. 


The following table provides possible statuses of a **logical gateway**. The gateway status depends on statuses of the gateway nodes. 

Status | Comments
:----- | :-------
Needs Registration | No node is yet registered to this logical gateway
Online | Gateway Nodes are online
Offline | No node in online status.
Limited | Not all nodes in this gateway are in healthy state. This status is a warning that some node might be down! <br/><br/>Could be due to credential sync issue on dispatcher/worker node. 

## Scale up gateway
You can configure the number of **concurrent data movement jobs** that can run on a node to scale up the capability of moving data between on-premises and cloud data stores. 

When the available memory and CPU are not utilized well, but the idle capacity is 0, you should scale up by increasing the number of concurrent jobs that can run on a node. You may also want to scale up when activities are timing out because the gateway is overloaded. In the advanced settings of a gateway node, you can increase the maximum capacity for a node. 
  

## Troubleshooting gateway issues
See [Troubleshooting gateway issues](data-factory-troubleshoot-gateway-issues.md) article for information/tips for troubleshooting issues with using the data management gateway.  

## Move gateway from one machine to another
This section provides steps for moving gateway client from one machine to another machine.

1. In the portal, navigate to the **Data Factory home page**, and click the **Linked Services** tile.

    ![Data Gateways Link](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Select your gateway in the **DATA GATEWAYS** section of the **Linked Services** page.

    ![Linked Services page with gateway selected](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. In the **Data gateway** page, click **Download and install data gateway**.

    ![Download gateway link](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. In the **Configure** page, click **Download and install data gateway**, and follow instructions to install the data gateway on the machine.

    ![Configure page](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Keep the **Microsoft Data Management Gateway Configuration Manager** open.

    ![Configuration Manager](./media/data-factory-data-management-gateway/ConfigurationManager.png)    
6. In the **Configure** page in the portal, click **Recreate key** on the command bar, and click **Yes** for the warning message. Click **copy button** next to key text that copies the key to the clipboard. The gateway on the old machine stops functioning as soon you recreate the key.  

    ![Recreate key](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Paste the **key** into text box in the **Register Gateway** page of the **Data Management Gateway Configuration Manager** on your machine. (optional) Click **Show gateway key** check box to see the key text.

    ![Copy key and Register](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Click **Register** to register the gateway with the cloud service.
9. On the **Settings** tab, click **Change** to select the same certificate that was used with the old gateway, enter the **password**, and click **Finish**.

   ![Specify Certificate](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   You can export a certificate from the old gateway by doing the following steps: launch Data Management Gateway Configuration Manager on the old machine, switch to the **Certificate** tab, click **Export** button and follow the instructions.
10. After successful registration of the gateway, you should see the **Registration** set to **Registered** and **Status** set to **Started** on the Home page of the Gateway Configuration Manager.

## Encrypting credentials
To encrypt credentials in the Data Factory Editor, do the following steps:

1. Launch web browser on the **gateway machine**, navigate to [Azure portal](http://portal.azure.com). Search for your data factory if needed, open data factory in the **DATA FACTORY** page and then click **Author & Deploy** to launch Data Factory Editor.   
2. Click an existing **linked service** in the tree view to see its JSON definition or create a linked service that requires a data management gateway (for example: SQL Server or Oracle).
3. In the JSON editor, for the **gatewayName** property, enter the name of the gateway.
4. Enter server name for the **Data Source** property in the **connectionString**.
5. Enter database name for the **Initial Catalog** property in the **connectionString**.    
6. Click **Encrypt** button on the command bar that launches the click-once **Credential Manager** application. You should see the **Setting Credentials** dialog box.

    ![Setting credentials dialog](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. In the **Setting Credentials** dialog box, do the following steps:
   1. Select **authentication** that you want the Data Factory service to use to connect to the database.
   2. Enter name of the user who has access to the database for the **USERNAME** setting.
   3. Enter password for the user for the **PASSWORD** setting.  
   4. Click **OK** to encrypt credentials and close the dialog box.
8. You should see a **encryptedCredential** property in the **connectionString** now.

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
ゲートウェイ コンピューターとは異なるコンピューターからポータルにアクセスする場合は、資格情報マネージャー アプリケーションがゲートウェイ コンピューターに接続できることを確認する必要があります。 アプリケーションがゲートウェイ コンピューターに到達できない場合、データ ソースの資格情報を設定したり、データ ソースへの接続をテストしたりすることはできません。  

**資格情報の設定**アプリケーションを使用すると、ポータルでは、ゲートウェイ コンピューターの **Gateway 構成マネージャー**の **[証明書]** タブで指定された証明書を使用して、資格情報が暗号化されます。

API を使用して資格情報を暗号化した方がよい場合は、 [New-AzureRmDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) PowerShell コマンドレットを使用して資格情報を暗号化できます。 コマンドレットはゲートウェイに構成されている証明書を使用して資格情報を暗号化します。 暗号化された資格情報を、JSON の **connectionString** の **EncryptedCredential** 要素に追加します。 JSON は、 [New-AzureRmDataFactoryLinkedService](https://msdn.microsoft.com/library/mt603647.aspx) コマンドレットや Data Factory エディターで使用します。

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Data Factory エディターを使用して資格情報を設定するもう 1 つの方法があります。 エディターを使用して SQL Server のリンクされたサービスを作成し、プレーン テキストで資格情報を入力した場合、資格情報は、Data Factory が所有する証明書で暗号化されます。 ゲートウェイで使用するように構成されている証明書は使用されません。 この方法は、場合によっては少し高速ですが、セキュリティは低下します。 したがって、この方法は開発/テストの目的のみに使用することをお勧めします。

## <a name="powershell-cmdlets"></a>PowerShell コマンドレット
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。

1. 管理者モードで **Azure PowerShell** を起動します。
2. 次のコマンドを実行して Azure 資格情報を入力することにより、Azure アカウントにログインします。

    ```PowerShell
    Login-AzureRmAccount
    ```
3. **New-AzureRmDataFactoryGateway** コマンドレットを使用して、次のように論理ゲートウェイを作成します。

    ```PowerShell
    $MyDMG = New-AzureRmDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **コマンドと出力の例**:

    ```
    PS C:\> $MyDMG = New-AzureRmDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. Azure PowerShell で、**C:\Program Files\Microsoft Data Management Gateway\2.0\PowerShellScript\** フォルダーに移動します。 次のコマンドに示すように、ローカル変数 **$Key** に関連付けられた **RegisterGateway.ps1** スクリプトを実行します。 このスクリプトは、コンピューターにインストールされているクライアント エージェントを、前に作成した論理ゲートウェイに登録します。

    ```PowerShell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    IsRegisterOnRemoteMachine パラメーターを使用することで、リモート マシンにゲートウェイを登録できます。 例:

    ```PowerShell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. **Get-AzureRmDataFactoryGateway** コマンドレットを使用して、データ ファクトリ内のゲートウェイの一覧を取得できます。 **[状態]** に **[オンライン]** と表示されている場合、ゲートウェイをいつでも使用できることを意味します。

    ```PowerShell        
    Get-AzureRmDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
**Remove-AzureRmDataFactoryGateway** コマンドレットを使用して、ゲートウェイを削除できます。また、**Set-AzureRmDataFactoryGateway** コマンドレットを使用して、ゲートウェイの記述を更新できます。 これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。  

### <a name="list-gateways-using-powershell"></a>PowerShell を使用したゲートウェイの一覧表示

```PowerShell
Get-AzureRmDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>PowerShell を使用したゲートウェイの削除

```PowerShell
Remove-AzureRmDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```


## <a name="next-steps"></a>次のステップ
* [オンプレミスとクラウド データ ストアの間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md) に関するページを参照してください。 チュートリアルでは、ゲートウェイを使用して、オンプレミスの SQL Server データベースから Azure BLOB にデータを移動するパイプラインを作成します。  

