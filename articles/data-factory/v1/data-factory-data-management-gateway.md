---
title: Data Factory の Data Management Gateway
description: オンプレミスとクラウドの間でデータを移動するためのデータ ゲートウェイを設定します。 Azure Data Factory で Data Management Gateway を使用してデータを移動します。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: b9084537-2e1c-4e96-b5bc-0e2044388ffd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 9d86fa9bfe9c17867b8a30519b79d9ee8c5af363
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932003"
---
# <a name="data-management-gateway"></a>Data Management Gateway
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 Data Factory サービスの現在のバージョンを使用している場合は、[セルフホステッド IR](../create-self-hosted-integration-runtime.md) に関するページを参照してください。

> [!NOTE]
> Data Management Gateway は、セルフホステッド Integration Runtime に名前が変わりました。

データ管理ゲートウェイは、クラウドと、オンプレミスのデータ ストアとの間でデータをコピーするために、オンプレミスの環境にインストールする必要があるクライアント エージェントです。 Data Factory でサポートされているオンプレミスのデータ ストアは、 [サポートされるデータ ソース](data-factory-data-movement-activities.md#supported-data-stores-and-formats) に関するセクションに示されています。

この記事は、 [オンプレミスとクラウドのデータ ストアの間でデータを移動する](data-factory-move-data-between-onprem-and-cloud.md) ためのチュートリアルを補完します。 チュートリアルでは、ゲートウェイを使用して、オンプレミスの SQL Server データベースから Azure BLOB にデータを移動するパイプラインを作成します。 この記事では、データ管理ゲートウェイについて詳しく取り上げます。

データ管理ゲートウェイは、複数のオンプレミス コンピューターをゲートウェイに関連付けることによって、スケールアウトできます。 1 つのノードで同時に実行できるデータ移動ジョブ数を増やすことで、スケールアップできます。 この機能は、単一のノードを持つ論理ゲートウェイでも使用できます。 詳細については、[Azure Data Factory でのデータ管理ゲートウェイのスケーリング](data-factory-data-management-gateway-high-availability-scalability.md)に関する記事をご覧ください。

> [!NOTE]
> 現時点では、ゲートウェイでサポートされるのは Data Factory でのコピー アクティビティとストアド プロシージャ アクティビティのみです。 カスタム アクティビティからゲートウェイを使用してオンプレミスのデータ ソースにアクセスすることはできません。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

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

1. データ開発者は、[Azure portal](https://portal.azure.com) または [PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/az.datafactory/)を使用して Azure Data Factory 用のゲートウェイを作成します。
2. データ開発者は、ゲートウェイを指定することで、オンプレミスのデータ ストアに使用するリンクされたサービスを作成します。 リンクされたサービスの設定の一部として、データ開発者は、資格情報の設定アプリケーションを使用して認証の種類と資格情報を指定します。 資格情報の設定アプリケーションのダイアログは、データ ストアと通信して接続をテストし、ゲートウェイと通信して資格情報を保存します。
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

* [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=39717)から MSI セットアップ パッケージをダウンロードします。 MSI は、すべての設定を保持しながら、既存のデータ管理ゲートウェイを最新のバージョンにアップグレードするためにも使用できます。
* 手動セットアップで **[データ ゲートウェイのダウンロードとインストール]** のリンクをクリックするか、高速セットアップで **[このコンピューターに直接インストールします]** をクリックします。 高速セットアップを使う場合の詳細な手順については、 [オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md) に関する記事を参照してください。 手動セットアップでは、ダウンロード センターに移動します。 ダウンロード センターからゲートウェイをダウンロードしてインストールする手順については、次のセクションをご覧ください。

### <a name="installation-best-practices"></a>インストールのベスト プラクティス:
1. コンピューターが休止状態にならないように、ゲートウェイ用のホスト コンピューターの電源プランを構成します。 ホスト コンピューターが休止状態の場合、ゲートウェイはデータ要求に応答しません。
2. ゲートウェイに関連付けられている証明書をバックアップします。

### <a name="install-the-gateway-from-download-center"></a>ダウンロード センターからゲートウェイをインストールする
1. [Microsoft Data Management Gateway のダウンロード ページ](https://www.microsoft.com/download/details.aspx?id=39717)に移動します。
2. **[ダウンロード]** をクリックし、**64 ビット** バージョン (32 ビットはサポートされていません) を選択し、 **[次へ]** を選択します。
3. **MSI** を直接実行するか、ハード ディスクに保存してから実行します。
4. **[ようこそ]** ページで **[言語]** を選択し、 **[次へ]** をクリックします。
5. 使用許諾契約に**同意**し、 **[次へ]** をクリックします。
6. ゲートウェイをインストールする**フォルダー**を選択し、 **[次へ]** をクリックします。
7. **[インストールの準備完了]** ページで **[インストール]** をクリックします。
8. **[完了]** をクリックしてインストールを完了します。
9. Azure ポータルでキーを取得します。 手順の詳細については、次のセクションを参照してください。
10. コンピューターで実行されている **Data Management Gateway 構成マネージャー**の **[ゲートウェイの登録]** ページで、以下の手順を実行します。
    1. テキストにキーを貼り付けます。
    2. オプションとして、キー文字列を表示する場合は **[Show gateway key (ゲートウェイのキーを表示する)]** をクリックします。
    3. **[登録]** をクリックします。

### <a name="register-gateway-using-key"></a>キーを使用してゲートウェイを登録する
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>ポータルに論理ゲートウェイをまだ作成していない場合
ポータルにゲートウェイを作成し、 **[構成]** ページからキーを取得するには、[オンプレミスとクラウドの間でのデータ移動](data-factory-move-data-between-onprem-and-cloud.md)に関する記事に示されているチュートリアルの手順に従います。

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>ポータルに論理ゲートウェイを既に作成している場合
1. Azure Portal で **[データ ファクトリ]** ページに移動し、 **[リンクされたサービス]** タイルをクリックします。

    ![[データ ファクトリ] ページ](media/data-factory-data-management-gateway/data-factory-blade.png)
2. **[リンクされたサービス]** ページで、ポータルに作成した論理**ゲートウェイ**を選択します。

    ![論理ゲートウェイ](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. **[データ ゲートウェイ]** ページで、 **[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。

    ![ポータルのダウンロード リンク](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. **[構成]** ページで、 **[キーの再作成]** をクリックします。 警告メッセージを注意して読んでから、[はい] をクリックします。

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

| ドメイン名 | Port | 説明 |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Data Movement Service のバックエンドとの通信に使用 |
| *.core.windows.net |443 |Azure BLOB を使用した段階的なコピーに使用 (構成されている場合)|
| *. frontend.clouddatahub.net |443 |Data Movement Service のバックエンドとの通信に使用 |
| *.servicebus.windows.net |9350-9354, 5671 |コピー ウィザードで使用される TCP 経由でのオプションの Service Bus Relay |

Windows のファイアウォール レベルでは、通常これらの送信ポートが有効になっています。 有効でない場合は、ゲートウェイ コンピューターに応じたドメインとポートを構成することができます。

> [!NOTE]
> 1. お使いのソースやシンクに基づいて、追加のドメインと送信ポートを企業や Windows のファイアウォールのホワイトリストに追加する必要があります。
> 2. 一部のクラウド データベース (例: [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-configure-firewall-settings)、[Azure Data Lake](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data#set-ip-address-range-for-data-access) など) では、そのファイアウォールの構成でゲートウェイ コンピューターの IP アドレスをホワイトリストに追加する必要があります。
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

* **プロキシを使用しない**:ゲートウェイは、クラウド サービスに接続するときにプロキシを明示的には使用しません。
* **システム プロキシを使用する**:ゲートウェイは、diahost.exe.config と diawp.exe.config で構成されているプロキシ設定を使用します。diahost.exe.config と diawp.exe.config でプロキシが構成されていない場合、ゲートウェイはプロキシを経由せず直接クラウド サービスに接続します。
* **カスタム プロキシを使用する**:diahost.exe.config と diawp.exe.config の構成は使用せず、HTTP プロキシ設定をゲートウェイ用に構成します。アドレスとポートが必要です。 ユーザー名とパスワードは、プロキシの認証設定によっては省略できます。 すべての設定が、ゲートウェイの資格情報証明書を使用して暗号化され、ゲートウェイ ホスト コンピューターにローカルで格納されます。

データ管理ゲートウェイのホスト サービスは、更新済みのプロキシ設定を保存した後に自動的に再起動されます。

ゲートウェイが正常に登録された後、プロキシ設定を表示または更新する必要がある場合は、Data Management Gateway 構成マネージャーを使用します。

1. **Data Management Gateway 構成マネージャー**を起動します。
2. **[設定]** タブに切り替えます。
3. **[HTTP プロキシ]** セクションの **[変更]** リンクをクリックして、 **[HTTP プロキシを設定する]** ダイアログを開きます。
4. **[次へ]** をクリックすると、プロキシ設定を保存して Gateway Host Service を再起動するためのアクセス許可を求める警告ダイアログが表示されます。

HTTP プロキシを表示して更新するには、構成マネージャー ツールを使用します。

![構成マネージャーを使用してプロキシを設定](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> NTLM 認証でプロキシ サーバーを設定する場合は、ドメイン アカウントで Gateway Host Service が実行されます。 ドメイン アカウントのパスワードを後で変更する場合は、忘れずにサービスの構成設定を更新し、必要に応じて再起動してください。 この要件のため、専用のドメイン アカウントを使用して、パスワードを頻繁に更新する必要がないプロキシ サーバーにアクセスすることをお勧めします。
>
>

### <a name="configure-proxy-server-settings"></a>プロキシ サーバーの設定を構成する
HTTP プロキシに対して **[システム プロキシを使用する]** 設定を選択すると、ゲートウェイは、diahost.exe.config と diawp.exe.config のプロキシ設定を使用します。diahost.exe.config と diawp.exe.config でプロキシが指定されていない場合、ゲートウェイはプロキシを経由せず直接クラウド サービスに接続します。 diahost.exe.config ファイルを更新する手順を次に示します。

1. ファイル エクスプローラーで、*C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config* の安全なコピーを作成して、元のファイルをバックアップします。
2. 管理者として Notepad.exe を起動し、テキスト ファイル *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared\\diahost.exe.config* を開きます。次のコードに示されている system.net の既定のタグを確認します。

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    その後、次の例で示すように、プロキシ サーバーの詳細を追加できます。

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    追加のプロパティは、プロキシ タグ内で scriptLocation のように必要な設定を指定することが許可されます。 構文については、「 [proxy 要素 (ネットワーク設定)](https://msdn.microsoft.com/library/sa91de1e.aspx) 」を参照してください。

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. 構成ファイルを元の場所に保存し、Data Management Gateway ホスト サービスを再起動して、変更を取得します。 サービスを再起動するには、コントロール パネルからサービス アプレットを使用するか、 **[Data Management Gateway 構成マネージャー]** 、 **[サービスの停止]** 、 **[サービスの開始]** の順にクリックします。 サービスが開始しない場合は、不正確な XML タグ構文が編集済みのアプリケーション構成ファイルに追加されている可能性があります。

> [!IMPORTANT]
> diahost.exe.config と diawp.exe.config の**両方**を忘れずに更新してください。

この点に加えて、Microsoft Azure が会社のホワイトリストにあることを確認する必要もあります。 有効な Microsoft Azure の IP アドレスの一覧は、 [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=41653)からダウンロードできます。

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>ファイアウォールとプロキシ サーバー関連の問題で発生する可能性がある症状
次のようなエラーが発生した場合は、ファイアウォールまたはプロキシ サーバーの不適切な構成が原因になっている可能性があります。構成が不適切だと、ゲートウェイが自身を認証するための Data Factory に接続できません。 ファイアウォールとプロキシ サーバーが正しく構成されていることを確認するには、前のセクションをご覧ください。

1. ゲートウェイを登録しようとすると次のエラーが発生します。「ゲートウェイのキーを登録できませんでした。 ゲートウェイのキーの登録を再試行する前に、Data Management Gateway が接続済み状態であり、Data Management Gateway Host Service が開始済みであることを確認してください。」
2. 構成マネージャーを開くと、ステータスとして "切断" または "接続中" と表示されます。 [イベント ビューアー] > [アプリケーションとサービス ログ] > [Data Management Gateway] の順に選択して Windows イベント ログを表示すると、次のようなエラーが表示されます。`Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>資格情報の暗号化のためにポート 8050 を開く
Azure ポータルでオンプレミスのリンクされたサービスを設定するとき、**資格情報の設**定アプリケーションでは、受信ポート **8050** を使って、資格情報がゲートウェイにリレーされます。 既定では、ゲートウェイのセットアップ中に、データ管理ゲートウェイのインストールによってこのポートがゲートウェイ コンピューターで開きます。

サードパーティ製のファイアウォールを使用する場合は、ポート 8050 を手動で開くことができます。 ゲートウェイの設定中にファイアウォールの問題が発生した場合は、次のコマンドを使用すると、ファイアウォールを構成せずにゲートウェイをインストールできます。

    msiexec /q /i DataManagementGateway.msi NOFIREWALL=1

ゲートウェイ コンピューター上でポート 8050 を開かない場合は、 **資格情報の設定** アプリケーション以外のメカニズムを使用して、データ ストア資格情報を構成する必要があります。 たとえば、[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell コマンドレットを使用できます。 データ ストア資格情報を設定する方法については、「資格情報とセキュリティの設定」に関するセクションを参照してください。

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
2. *C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\* フォルダーに切り替えます。
3. 自動更新機能をオフ (無効) にするには、次のコマンドを実行します。

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. オンに戻すには、次のコマンドを実行します。

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [高可用性とスケーラビリティを備えたマルチノード ゲートウェイ](data-factory-data-management-gateway-high-availability-scalability.md)
1. ゲートウェイ コンピューターで Windows PowerShell を起動します。
2. *C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\* フォルダーに切り替えます。
3. 自動更新機能をオフ (無効) にするには、次のコマンドを実行します。

    高可用性機能を備えたゲートウェイについては、別途 AuthKey パラメーターが必要となります。
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. オンに戻すには、次のコマンドを実行します。

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>構成マネージャー
ゲートウェイをインストールした後、次のどれかの方法で Data Management Gateway 構成マネージャーを起動できます。

1. **[検索]** ウィンドウに、このユーティリティにアクセスする **Data Management Gateway** を入力します。
2. 次のフォルダー内にある実行可能な *ConfigManager.exe* を実行します: *C:\\\\Program Files\\Microsoft Data Management Gateway\\2.0\\Shared*。

### <a name="home-page"></a>ホーム ページ
ホーム ページでは、次の操作を行うことができます。

* (クラウド サービスなどに接続されている) ゲートウェイの状態を表示する。
* **[登録]** する。
* ゲートウェイ コンピューターで **Data Management Gateway ホスト サービス**を**停止**および開始する。
* **更新のスケジュール** を特定の時刻に設定する。
* ゲートウェイの **最終更新日時**を確認する。

### <a name="settings-page"></a>[設定] ページ
[設定] ページでは、次の操作を行うことができます。

* ゲートウェイで使われる **証明書** を表示、変更、エクスポートする。 この証明書は、データ ソースの資格情報の暗号化に使用されます。
* エンドポイントの **HTTPS ポート** を変更する。 ゲートウェイでデータ ソースの資格情報を設定するためにポートが開かれます。
* **状態**
* **SSL 証明書**を表示する。この証明書は、データ ソースの資格情報を設定するためにポータルとゲートウェイ間の SSL 通信に使用されます。

### <a name="remote-access-from-intranet"></a>イントラネットからのリモート アクセス
この機能は今後有効になります。 今後の更新プログラム (v3.4 以降) で、資格情報を暗号化するために PowerShell または資格情報マネージャー アプリケーションを使用しながら、ポート 8050 (前のセクションを参照) を使用して現在発生しているすべてのリモート接続を有効または無効にできます。

### <a name="diagnostics-page"></a>[診断] ページ
[診断] ページでは、次の操作を行うことができます。

* 詳細 **ログ**を有効にして、イベント ビューアーでログを表示し、障害が発生した場合は Microsoft にログを送信する。
* **接続をテスト** する。

### <a name="help-page"></a>Help page
[ヘルプ] ページには、次の情報が表示されます。

* ゲートウェイの簡単な説明
* バージョン番号
* オンライン ヘルプ、プライバシーに関する声明、使用許諾契約書へのリンク。

## <a name="monitor-gateway-in-the-portal"></a>ポータルでのゲートウェイの監視
Azure Portal では、ゲートウェイ コンピューターでのリソース使用状況 (CPU、メモリ、ネットワーク(入力/出力) など) のほぼリアルタイムのスナップショットを表示できます。

1. Azure Portal で、データ ファクトリのホーム ページに移動して、 **[リンクされたサービス]** タイルをクリックします。

    ![データ ファクトリのホーム ページ](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. **[リンクされたサービス]** ページで、**ゲートウェイ**を選択します。

    ![[リンクされたサービス] ページ](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. **[ゲートウェイ]** ページで、ゲートウェイのメモリと CPU の使用率を確認できます。

    ![ゲートウェイの CPU とメモリの使用率](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. **[詳細設定]** を有効にして、ネットワーク使用率などの詳細を確認します。
    
    ![ゲートウェイの詳細な監視](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

次の表に、**ゲートウェイ ノード**一覧の列の説明を示します。

監視のプロパティ | 説明
:------------------ | :----------
名前 | ゲートウェイに関連付けられている論理ゲートウェイとノードの名前です。 ノードとは、ゲートウェイがインストールされているオンプレミスの Windows コンピューターです。 1 つの論理ゲートウェイ内に複数のノード (最大 4 つのノード) を含める方法の詳細については、「[Data Management Gateway - 高可用性とスケーラビリティ](data-factory-data-management-gateway-high-availability-scalability.md)」をご覧ください。
[Status] | 論理ゲートウェイとゲートウェイ ノードの状態です 例:オンライン、オフライン、制限あり、など。これらの状態の詳細については、「[ゲートウェイの状態](#gateway-status)」セクションをご覧ください。
Version | 論理ゲートウェイと各ゲートウェイ ノードのバージョンを示します。 論理ゲートウェイのバージョンは、グループ内のノードで多数を占めるバージョンに基づいて決定されます。 論理ゲートウェイの設定の中にさまざまなバージョンのノードがある場合は、論理ゲートウェイと同じバージョン番号を持つノードのみが適切に機能します。 それ以外のノードは制限モードであり、手動で更新する必要があります (自動更新に失敗する場合のみ)。
使用可能なメモリ | ゲートウェイ ノードで使用可能なメモリです。 この値は、ほぼリアルタイムのスナップショットです。
CPU 使用率 | ゲートウェイ ノードの CPU 使用率。 この値は、ほぼリアルタイムのスナップショットです。
ネットワーク (入力/出力) | ゲートウェイ ノードのネットワーク使用率。 この値は、ほぼリアルタイムのスナップショットです。
同時実行ジョブ (実行中/制限) | 各ノードで実行されるタスクまたはジョブの数。 この値は、ほぼリアルタイムのスナップショットです。 上限は、各ノードの最大の同時実行ジョブ数を表します。 この値は、マシンのサイズに基づいて定義されます。 CPU/ メモリ/ ネットワークは使用率を超えておらず、アクティビティがタイムアウトになっている高度なシナリオでは、上限を引き上げて、同時実行ジョブの実行回数をスケールアップできます。この機能は、単一ノードのゲートウェイでも利用できます (スケーラビリティおよび可用性の機能が無効になっている場合でも、利用できます)。
Role | マルチノード ゲートウェイには、ディスパッチャーとワーカーという 2 つのタイプの役割があります。 ノードはすべてワーカーであり、全部がジョブの実行に使用できることを意味します。 ディスパッチャー ノードは 1 つだけです。このノードは、クラウド サービスからタスク/ジョブをプルし、異なるワーカー ノード (自身を含む) にディスパッチするために使用されます。

このページでは、ゲートウェイに 2 つ以上のノード (スケールアウト シナリオ) がある場合により有意義ないくつかの設定を確認します。 マルチノード ゲートウェイの設定に関する詳細については、「 [Data Management Gateway - 高可用性とスケーラビリティ](data-factory-data-management-gateway-high-availability-scalability.md)」をご覧ください。

### <a name="gateway-status"></a>ゲートウェイの状態
次の表は、**ゲートウェイ ノード**の状態を示しています。

Status  | コメント/シナリオ
:------- | :------------------
オンライン | ノードはデータ ファクトリ サービスに接続されています。
オフライン | ノードはオフラインです。
アップグレード中 | ノードは自動でアップデートされています。
制限あり | 接続の問題に起因します。 HTTP ポート 8050 の問題、サービス バス接続の問題、または資格情報の同期の問題に起因する可能性があります。
非アクティブ | ノードは、多数を占める他のノードの構成とは違う構成になっています。<br/><br/> 他のノードに接続できない場合、ノードが非アクティブになることがあります。

次の表は、**論理ゲートウェイ**の状態を示しています。 ゲートウェイの状態は、ゲートウェイ ノードの状態に依存します。

Status | 説明
:----- | :-------
Needs Registration\(登録が必要\) | この論理ゲートウェイには、まだ登録されたノードがありません。
オンライン | ゲートウェイ ノードはオンラインです。
オフライン | オンライン状態のノードはありません。
制限あり | このゲートウェイに、正常な状態ではないノードが含まれています。 この状態は、一部のノードがダウンしている可能性を警告します。 <br/><br/>ディスパッチャー/ワーカー ノードでは、資格情報の同期の問題に起因する可能性があります。

## <a name="scale-up-gateway"></a>ゲートウェイのスケールアップ
オンプレミスとクラウドのデータ ストア間のデータ移動の機能をスケールアップするために、1 つのノードで実行できる**同時実行のデータ移動ジョブ**の数を構成することができます。

使用可能なメモリと CPU が効果的に活用されていないが、アイドル容量は 0 になっている場合は、ノードで実行できる同時実行ジョブの数を増やすことで、スケールアップを行うことができます。 また、ゲートウェイが過負荷になっているために、アクティビティがタイムアウトしている場合も、スケールアップを検討します。 ゲートウェイ ノードの詳細設定で、ノードの最大容量を増やすことができます。

## <a name="troubleshooting-gateway-issues"></a>ゲートウェイの問題のトラブルシューティング
データ管理ゲートウェイの使用に関連する問題のトラブルシューティングの情報やヒントについては、[ゲートウェイの問題のトラブルシューティング](data-factory-troubleshoot-gateway-issues.md)に関するの記事を参照してください。

## <a name="move-gateway-from-one-machine-to-another"></a>1 つのコンピューターから別のコンピューターへのゲートウェイの移動
このセクションでは、1 台のコンピューターから別のコンピューターにゲートウェイ クライアントを移動する手順を説明します。

1. ポータルで **Data Factory ホーム ページ**に移動し、 **[リンクされたサービス]** タイルをクリックします。

    ![データ ゲートウェイ リンク](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. **[リンクされたサービス]** ページの **[データ ゲートウェイ]** セクションでゲートウェイを選択します。

    ![ゲートウェイが選択された状態の [リンクされたサービス] ページ](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. **[データ ゲートウェイ]** ページで **[データ ゲートウェイをダウンロードしてインストールする]** をクリックします。

    ![ダウンロード ゲートウェイ リンク](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. **[構成]** ページで **[データ ゲートウェイのダウンロードとインストール]** をクリックし、指示に従ってコンピューターにデータ ゲートウェイをインストールします。

    ![[構成] ページ](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. **Microsoft Data Management Gateway Configuration Manager** を開いたままにします。

    ![構成マネージャー](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. ポータルの **[構成]** ページでコマンド バーの **[キーの再作成]** をクリックし、警告メッセージで **[はい]** をクリックします。 キー文字列の横にある **コピー ボタン** をクリックして、キーをクリップボードにコピーします。 古いコンピューターのゲートウェイは、キーが再作成されるとすぐに機能しなくなります。

    ![[キーの再作成]](./media/data-factory-data-management-gateway/RecreateKey.png)
7. コンピューター上の **Data Management Gateway 構成マネージャー**の **[ゲートウェイの登録]** ページで、**キー**を貼り付けます。 (省略可能) **[ゲートウェイのキーを表示する]** チェック ボックスをオンにしてキー文字列を表示します。

    ![キーのコピーと登録](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. **[登録]** をクリックして、ゲートウェイをクラウド サービスに登録します。
9. **[設定]** タブで **[変更]** をクリックして以前のゲートウェイで使用されていたのと同じ証明書を選択し、 **[パスワード]** を入力して **[完了]** をクリックします。

   ![証明書の指定](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   古いゲートウェイから証明書をエクスポートするには、古いコンピューターで Data Management Gateway 構成マネージャーを起動し、 **[証明書]** タブに切り替え、 **[エクスポート]** をクリックして指示に従います。
10. ゲートウェイの登録が成功したら、Gateway Configuration Manager のホーム ページで、 **[登録]** が **[登録済み]** に、 **[状態]** が **[開始]** に設定されていることを確認する必要があります。

## <a name="encrypting-credentials"></a>資格情報の暗号化
Data Factory エディターで資格情報を暗号化するには、以下の手順を実行します。

1. **ゲートウェイ コンピューター**上で Web ブラウザーを起動し、 [Azure ポータル](https://portal.azure.com)に移動します。 必要に応じてデータ ファクトリを検索して **[データ ファクトリ]** ページで開き、 **[作成およびデプロイ]** をクリックして Data Factory エディターを起動します。
2. ツリー ビューの既存の **リンクされたサービス** をクリックしてその JSON 定義を参照するか、データ管理ゲートウェイを必要とするリンクされたサービス (SQL Server や Oracle など) を作成します。
3. JSON エディターで、 **gatewayName** プロパティにゲートウェイの名前を入力します。
4. **connectionString** の **[データ ソース]** プロパティにサーバー名を入力します。
5. **connectionString** の **[初期カタログ]** プロパティにデータベース名を入力します。
6. コマンド バーの **[暗号化]** ボタンをクリックして、ClickOnce **資格情報マネージャー** アプリケーションを起動します。 **[資格情報の設定]** ダイアログ ボックスが表示されます。

    ![[資格情報の設定] ダイアログ](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. **[資格情報の設定]** ダイアログ ボックスで、以下の手順を実行します。
   1. Data Factory サービスがデータベースへの接続に使用する **認証** を選択します。
   2. **[ユーザー名]** の設定に、データベースへのアクセス権を持つユーザーの名前を入力します。
   3. **[パスワード]** の設定に、ユーザーのパスワードを入力します。
   4. **[OK]** をクリックすると、資格情報が暗号化され、ダイアログ ボックスが閉じます。
8. これで、**connectionString** 内に **encryptedCredential** プロパティが表示されます。

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

API を使用して資格情報を暗号化した方がよい場合は、[New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) PowerShell コマンドレットを使用して資格情報を暗号化できます。 コマンドレットはゲートウェイに構成されている証明書を使用して資格情報を暗号化します。 暗号化された資格情報を、JSON の **connectionString** の **EncryptedCredential** 要素に追加します。 JSON は、[New-AzDataFactoryLinkedService](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactorylinkedservice) コマンドレットや Data Factory エディターで使用します。

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Data Factory エディターを使用して資格情報を設定するもう 1 つの方法があります。 エディターを使用して SQL Server のリンクされたサービスを作成し、プレーン テキストで資格情報を入力した場合、資格情報は、Data Factory が所有する証明書で暗号化されます。 ゲートウェイで使用するように構成されている証明書は使用されません。 この方法は、場合によっては少し高速ですが、セキュリティは低下します。 したがって、この方法は開発/テストの目的のみに使用することをお勧めします。

## <a name="powershell-cmdlets"></a>PowerShell コマンドレット
このセクションでは、Azure PowerShell コマンドレットを使用して、ゲートウェイを作成し登録する方法について説明します。

1. 管理者モードで **Azure PowerShell** を起動します。
2. 次のコマンドを実行して Azure 資格情報を入力することにより、Azure アカウントにログインします。

    ```powershell
    Connect-AzAccount
    ```
3. **New-AzDataFactoryGateway** コマンドレットを使用して、次のように論理ゲートウェイを作成します。

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **コマンドと出力の例**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

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

1. Azure PowerShell で、*C:\\\\Program Files\\Microsoft Integration Runtime\\3.0\\PowerShellScript\\* フォルダーに切り替えます。 次のコマンドに示すように、ローカル変数 **$Key** に関連付けられた *RegisterGateway.ps1* スクリプトを実行します。 このスクリプトは、コンピューターにインストールされているクライアント エージェントを、前に作成した論理ゲートウェイに登録します。

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    IsRegisterOnRemoteMachine パラメーターを使用することで、リモート マシンにゲートウェイを登録できます。 例:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. **Get-AzDataFactoryGateway** コマンドレットを使用して、データ ファクトリ内のゲートウェイの一覧を取得できます。 **[状態]** に **[オンライン]** と表示されている場合、ゲートウェイをいつでも使用できることを意味します。

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   **Remove-AzDataFactoryGateway** コマンドレットを使用して、ゲートウェイを削除できます。また、**Set-AzDataFactoryGateway** コマンドレットを使用して、ゲートウェイの記述を更新できます。 これらのコマンドレットの文法やその他の詳細については「Data Factory Cmdlet Reference (Data Factory コマンドレット リファレンス)」を参照してください。  

### <a name="list-gateways-using-powershell"></a>PowerShell を使用したゲートウェイの一覧表示

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>PowerShell を使用したゲートウェイの削除

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>次の手順
* [オンプレミスとクラウド データ ストアの間でのデータの移動](data-factory-move-data-between-onprem-and-cloud.md) に関するページを参照してください。 チュートリアルでは、ゲートウェイを使用して、オンプレミスの SQL Server データベースから Azure BLOB にデータを移動するパイプラインを作成します。
