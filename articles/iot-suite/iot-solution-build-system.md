---
title: "MyDriving Azure IoT の例: ビルドする |Microsoft Docs"
description: "Microsoft Azure を使用して、Stream Analytics、Machine Learning、Event Hubs などの IoT システムを設計する方法を総合的に示すアプリを構築します。"
services: 
documentationcenter: .net
suite: 
author: harikmenon
manager: douge
ms.assetid: c2fcd6ee-3bbe-43d1-a066-dce52cc3a53d
ms.service: multiple
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2016
ms.author: harikm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 012bfc7d8431e2edb2b1056fb465421fad58193a


---
# <a name="build-and-deploy-the-mydriving-solution-to-your-environment"></a>MyDriving ソリューションを自分の環境でビルドおよびデプロイする
MyDriving は、車からデータを収集するモノのインターネット (IoT) ソリューションです。Machine Learning を使用して処理し、収集したデータを携帯電話に表示します。 これのバック エンドは、Microsoft Azure のさまざまなサービスで構成されます。 クライアントには、Android、iOS、または Windows 10 のスマートフォンが可能です。

当社は独自の IoT システムを作成するきっかけを提供するために、MyDriving ソリューションを作成しました。 このバック エンド アーキテクチャをご自分の Azure アカウントにデプロイするには、Azure Resource Manager のスクリプトを [GitHub の MyDriving リポジトリ](https://github.com/Azure-Samples/MyDriving)から取得します。 それから、さまざまなサービスを再構成したり、独自のデータに合うようにクエリを変更したりすることができます。 これらのスクリプトは、モバイル アプリ、Azure App Service API プロジェクトなどのコードと共に MyDriving リポジトリにあります。

まだアプリを試していない場合は、「 [ファースト ステップ ガイド](iot-solution-get-started.md)」を参照してください。

「 [MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」にアーキテクチャの詳細なアカウントがあります。 要約すると、当社がセットアップする構成と、同様のプロジェクトを作成するためにお客様がセットアップする構成があります。

* **クライアント アプリ** は、Android、iOS および Windows 10 のスマートフォンで実行します。 Xamarin プラットフォームを使用して、 `src/MobileApp`の GitHub に格納されている多くのコードを共有しています。 アプリでは、実際に次の 2 つの異なる機能を実行します。
  * オンボード診断 (OBD) デバイスおよびその独自のロケーション サービスからシステムのクラウド バックエンドにテレメトリをリレーします。
  * これは、ユーザーが記録された乗車のクエリを実行するユーザー インターフェイスです。
* **クラウド サービス** では、乗車データをリアルタイムで取り込み、処理します。 このサービスを作成するための主な作業は、多様な Azure サービスの選択、カスタマイズ、および接続です。 一部のパーツには、受信データをフィルターおよび処理するためにスクリプトが必要です。 すべてのパーツを構成するには、Azure Resource Manager テンプレートを使用します。
* **モバイル サービス アプリ** は、デバイス アプリのユーザー インターフェイス部分の背後にある Web サービスです。 この主な仕事は、格納された処理済みデータのデータベースにクエリを行うことです。 そのコードは、 `src/MobileAppService`下の GitHub にあります。
* **Visual Studio with Xamarin** は当社の開発環境です。 Visual Studio のコンポーネントおよびスタンドアロン統合開発環境 (IDE) の両方として存在する Xamarin は、クロスプラットフォーム デバイス コードをビルドするために使用します。 iOS コードをビルドするには、OS X コンピューター上で Xamarin のインスタンスが実行されている必要があります。 必要に応じて、Visual Studio で管理されるエージェントとして実行できます。
* **単体テスト** は、Xamarin Test Cloud で実行されます。
* **GitHub** は、すべてのコード、スクリプト、およびテンプレートを保存するリポジトリです。
* **Visual Studio Team Services** は、Web サービスとデバイス アプリの継続的なビルドとテストを管理するために使用するクラウド サービスです。
* **HockeyApp** は、デバイス コードのリリースを配布するために使用されます。 また、クラッシュおよび使用量レポート、ユーザーからのフィードバックを収集します。
* **Visual Studio Application Insights** は、モバイル Web サービスを監視します。

それでは、すべての設定方法を見てみましょう。 手順の多くは省略可能であることに注意してください。

## <a name="sign-up-for-accounts"></a>アカウントにサインアップ
* [Visual Studio Dev Essentials](https://www.visualstudio.com/products/visual-studio-dev-essentials-vs.aspx)。 この無料プログラムでは、Visual Studio、Visual Studio Team Services、および Azure を含む、多くの開発者ツールやサービスに簡単にアクセスできます。 これでは、月額 25 ドルの Azure クレジットを 12 か月間利用できます。 これには、Pluralsight トレーニングと Xamarin University のサブスクリプションも含まれます。 [Azure](https://azure.com) と [Visual Studio Team Services](https://www.visualstudio.com/products/visual-studio-team-services-vs.aspx) の Free レベルに別々にサインアップすることもできますが、これには Azure クレジットはありません。
* [HockeyApp](https://rink.hockeyapp.net/) (省略可能) は、モバイル アプリのテスト配布の管理とテレメトリの収集用です。
* [Xamarin](https://xamarin.com/) (必須) は、モバイル アプリのビルドと、[Xamarin Test Cloud](https://xamarin.com/test-cloud) でのデバッグとテストの実行用です。
* [GitHub](https://github.com/Azure-Samples/MyDriving/) (省略可能) では、独自のコード用に無料のパブリック リポジトリを作成します (プライベート リポジトリは有料です)。 プライベート リポジトリが必要な場合には、Visual Studio Team Services の基本プランを使用することもできます。
* [Power BI](https://powerbi.microsoft.com/) (省略可能) は、システム全体のデータを詳細に視覚化するために使用します。

> [!NOTE]
> [GitHub の MyDriving リポジトリ](https://github.com/Azure-Samples/MyDriving)にアクセスするために、GitHub アカウントは必要ありません。
> 
> 

## <a name="install-development-tools"></a>開発ツールのインストール
次の設定は、Azure バックエンドを使用する iOS、Android および Windows 10 モバイル クロスプラットフォーム アプリを含む、完全なソリューションを開発するために行います。

または、Azure バックエンドで作業を行っていない場合は、モバイル アプリを開発するために、Mac または Windows のいずれかで Xamarin Studio を使用できます。

[この設定の詳細については、こちら](https://msdn.microsoft.com/library/mt613162.aspx)を参照してください。

### <a name="windows-development-machine"></a>Windows の開発用コンピューター
Windows 上の中心的なツールは Visual Studio で、Android および Windows 用の MyDriving アプリ、App Service API プロジェクト、およびマイクロサービスの拡張機能と共に動作します。

Xamarin、Git、エミュレーターおよびその他の役立つコンポーネントは、すべて Visual Studio に統合されています。

インストール:

* [Visual Studio 2015 with Xamarin](https://www.visualstudio.com/products/visual-studio-community-vs) (任意のエディション – Community は無料)。
* [ユニバーサル Windows プラットフォーム用 SQLite](https://visualstudiogallery.msdn.microsoft.com/4913e7d5-96c9-4dde-a1a1-69820d615936)。 Windows 10 Mobile コードのビルドに必要です。
* [Azure SDK for Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)。 Azure でアプリを実行するための SDK と、Azure を管理するためにコマンド ライン ツールを提供します。
* [Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric)。 [マイクロ サービス](../service-fabric/service-fabric-get-started.md) の拡張機能のビルドに必要です。

また、正しい Visual Studio の拡張機能があることを確認します。 **[ツール]** の下に、**[Android]、[iOS]、[Xamarin]** などがあることを確認します。 表示されない場合は、[コントロール パネル]、**[プログラムと機能]** > **[Microsoft]** > **[Visual Studio 2015]** > **[変更]** の順に開きます。 **[クロスプラットフォーム開発]** で、**[C#/.Net (Xamarin)]\# を選択します**。 そこで、 **Git for Windows** がインストールされていることを確認します。

### <a name="mac-development-machine"></a>Mac の開発用コンピューター
iOS の開発を行う場合は、Mac (Yosemite 以降) が必要です。 Windows ではすべてのコードの開発と管理を行うために、Visual Studio with Xamarin を使用していますが、Xamarin では iOS コードをビルドおよび署名するために、Mac にインストールされているエージェントを使用します。

![Windows で開発し Mac でビルドする](./media/iot-solution-build-system/image1.png)

(または、クロスプラットフォーム アプリを開発するために、Mac 上で直接 Xamarin Studio を使用できます。)

ターゲット プラットフォームとして iOS を含めない場合、Mac は必要ありません。

インストール:

* [Xamarin Studio for iOS](https://developer.xamarin.com/guides/ios/getting_started/installation/mac/)。 Windows 仮想マシンを実行している Mac 上で、Visual Studio および Xamarin を設定することもできます。 MSDN の「 [Mac ユーザーのセットアップ、インストール、および検証](https://msdn.microsoft.com/library/mt488770.aspx) 」を参照してください。
* [Azure 開発ツール](https://azure.microsoft.com/downloads/) (省略可能)。

Mac 上でリモート ログインを有効にします。 **[システム環境設定]** > **[共有]** の順に開いて、**[リモート ログイン]** を選択します。

Windows の Visual Studio で iOS プロジェクトを開くと、Xamarin プラグインから Mac の ID を要求するプロンプトが表示されます。

## <a name="fetch-the-github-repository"></a>Github リポジトリのフェッチ
GitHub、Visual Studio、または別の Git クライアント上の [[ZIP をダウンロード]](https://github.com/Azure-Samples/MyDriving) ボタンを使用して、 **GitHub MyDriving リポジトリ** のローカル コピーをフェッチします。

C:\\code など、短いパス名のフォルダーにファイルを解凍します。

または、最新の状態に保持するか、コードを投稿する場合は、次のようにリポジトリを複製します。

**git clone https://github.com/Azure-Samples/MyDriving.git**

## <a name="get-a-bing-maps-api-key"></a>Bing マップ API キーの取得
[Bing マップ API キーを登録します](https://msdn.microsoft.com/library/ff428642.aspx)。

`src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs`の 22 行目をこのキーに置き換える必要があります。

## <a name="build-the-demo-app"></a>デモ アプリのビルド
Visual Studio で次のソリューションを開きます。

* src\MobileApps\MyDriving.sln
* src\MobileAppService\MyDrivingService.sln
* src\Extensions\ServiceFabric\VINLookUpApplication\VINLookUpApplication.sln

次を求めるプロンプトが表示されます。

* 信頼できない可能性があるいくつかのプロジェクトを信頼します。 先に進める場合は、これらのプロジェクトを開くことを選択します。
* 新しい Windows 10 コンピューターで作業する場合は、開発者モードを設定します。
* Xamarin の資格情報を入力します。
* Xamarin Mac に接続します。 Mac を所有していない場合は、Visual Studio で iOS プロジェクトを右クリックして、 **[プロジェクトのアンロード]**を選択します。

ソリューションをリビルドします。

ビルドで問題がある場合は、次の解決策を試してください。

* *VINLookupApplication プロジェクトが読み込まれない*: [Azure SDK for Visual Studio 2015](https://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)をインストールしていることを確認します。
* *Service Fabric プロジェクトをビルドできない*: 最初にインターフェイス プロジェクトをビルドし、Service Fabric SDK をインストールしていることを確認します。
* *Android アプリをビルドできない*:
  
  * **[ツール]** > **[Android]** > **[Android SDK Manager]** の順に開き、Android 6 (API 23)/SDK プラットフォームがインストールされていることを確認します。
  * このディレクトリを削除して、次をリビルドします。<br/>
    `%LocalAppData%\Xamarin\zips`

## <a name="get-to-know-the-code"></a>コードについて
ソリューションには、以下があります。

* Azure 拡張機能: Service Fabric。
* Azure HDInsight: Azure で乗車データを処理するためのスクリプト。
* Mobile Apps: デバイス アプリ。
* MobileAppsService/MyDrivingService: Web バックエンド。
* Power BI: レポートの定義。
* Scripts:
  
  * Resource Manager: Azure リソースをビルドするためのテンプレート。
  * PowerShell: Resource Manager テンプレートを実行するためのスクリプト。
  * Azure SQL Database: データベースのデバッグ。
* SQL Database: CreateTables: スキーマの定義。
* Azure Stream Analytics: 受信データ ストリームを変換するクエリ。

## <a name="run-the-apps-in-development-mode"></a>開発モードでアプリを実行する
使用しているデバイスでアプリを実行するアクションを実行します。

* バック エンド: MyDrivingService をスタートアップ プロジェクトとして設定し、F5 キーを押して、バックエンド Web サービスを実行します。 API の一覧のブラウザー ビューが開きます。
* モバイル クライアント: [モバイル アプリは Xamarin で開発されます](https://developer.xamarin.com/guides/cross-platform/deployment,_testing,_and_metrics/debugging_with_xamarin/)。
  
  * Android: 詳細については、「 [Xamarin での Android のデバッグ](http://developer.xamarin.com/guides/android/deployment,_testing,_and_metrics/debugging_with_xamarin_android/)」を参照してください。
  * iOS: 詳細については、「 [iOS でのデバッグ](http://developer.xamarin.com/guides/ios/deployment,_testing,_and_metrics/debugging_in_xamarin_ios/)」を参照してください。
  * Windows Phone: 詳細については、「 [Xamarin + Windows Phone](https://developer.xamarin.com/guides/cross-platform/windows/phone/)」を参照してください。

## <a name="upload-the-mobile-app-to-hockeyapp"></a>HockeyApp へモバイル アプリのアップロード
HockeyApp では、新しいリリースのユーザーへの通知や、Android、iOS や Windows アプリの配布を管理できます。 また、有用なクラッシュ レポート、スクリーン ショット付きのユーザーからのフィードバック、および使用状況メトリックも収集します。

ビルド アプリを[アップロードして開始します](http://support.hockeyapp.net/kb/app-management-2/how-to-create-a-new-app)。 次に、開発用コンピューターから [HockeyApp](https://rink.hockeyapp.net) にサインインします。 開発者ダッシュボードで、**[新しいアプリ]** をクリックして、ビルド ファイルをウィンドウにドラッグします。 (後では、ビルド サービスを自動化してこれを実行できます。)

アプリのダッシュ ボードが開きました。

![アプリのダッシュボードの [概要] タブ](./media/iot-solution-build-system/image2.png)

アプリを実行する各プラットフォームで、このプロセスを繰り返します。 次に、以下を実行できます。

* ダッシュボードの [アプリ ID](http://support.hockeyapp.net/kb/app-management-2/how-to-find-the-app-id) を使用して、アプリからクラッシュ データとフィードバックを送信します。 MyDriving で、src/MobileApps/MyDriving/MyDriving.Utils/Logger.cs の ID を更新します。
* [テスト ユーザーを招待します](http://support.hockeyapp.net/kb/app-management-2/how-to-invite-beta-testers)。 URL を取得して、テスト担当者のユーザーを募集します。 テスト ユーザーはチームにサインアップして、アプリケーションをダウンロードし、フィードバックを送信することができます。
* ベータ リリースをよりオープンにする場合は、配布をパブリックに設定します。 **[アプリの管理]** > **[配布]** > **[ダウンロード = パブリック]** の順にクリックします。 これで、すべてのユーザーがアプリをダウンロードし、フィードバックを送信できます。また、新しいバージョンを投稿したときに、ユーザーに通知が表示されます。 ユーザーからクラッシュ レポートを受信する場合もあります。
  
   ![ダッシュボード上のチーム](./media/iot-solution-build-system/image3.png)
* [クラッシュ レポートと Visual Studio Team Services をリンクさせます](http://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)。 **[アプリの管理]** > **[Visual Studio Team Services]** の順にクリックします。 クラッシュ レポートがある場合や、フィードバックを受信した場合、HockeyApp では Team Services に自動的に作業項目を作成できます。

詳細については、 [HockeyApp のサイト](https://hockeyapp.net)を参照してください。

## <a name="test-the-mobile-app-on-xamarin-test-cloud"></a>Xamarin Test Cloud でのモバイル アプリのテスト
[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud/introduction-to-test-cloud/) では、クラウド内の実際のデバイス上で UI テストを自動化します。 NUnit フレームワークを使用して、ユーザー インターフェイスからアプリケーションを実行するテストを作成します。

Xamarin を使用するには、[Xamarin.UITests](https://developer.xamarin.com/guides/testcloud/uitest/intro-to-uitest/) SDK をアプリに組み込みます。これは、NuGet パッケージとして提供されます。 デモ アプリで見つけることができ、Xamarin テンプレートで新しいテスト プロジェクトを作成するときに含まれます。

![インターフェイスでのクロス プラットフォーム SDK の入手先](./media/iot-solution-build-system/image4.png)

テスト プロジェクトの例は、リポジトリのアプリに付属します。 [MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService) 下の [src](https://github.com/Azure-Samples/MyDriving/tree/master/src)/MobileApps/[MyDriving](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileApps/MyDriving)/MyDriving.UITests/ を参照してください。

Visual Studio Team Services のビルドを使用する場合、簡単に Xamarin UI 単体テストを作成し、ビルドの一部として実行できます。

## <a name="deploy-azure-services"></a>Azure サービスのデプロイ
Azure サービスと Team Services Build サービスの自動デプロイを実行するには、 **scripts/README.md**の詳細な手順を参照してください。

Microsoft Azure では、クラウド アプリケーションのビルドに使用できるさまざまなサービスを提供します。 多くは個別に使用できますが (App Service、Web Apps など)、MyDriving で使用するなど、統合システムを形成するために相互接続すると、最適な状態で使用できます。

Azure サービスを手動で作成および相互接続することができますが、Azure Resource Manager テンプレートを使用すると、より早く信頼性が向上します。 [Resource Manager](../azure-resource-manager/resource-group-overview.md) では、ソリューションのリソースのデプロイとリソース間の相互接続を自動化します。

[Scripts/ARM](https://github.com/Azure-Samples/MyDriving/tree/master/scripts/ARM)の GitHub リポジトリで、MyDriving システムのテンプレートを見つけます。 このテンプレートでは、アーキテクチャのさまざまなサービスがどのように相互接続されているかについて、包括的かつ簡潔なビューを提供します。 これらすべての詳細については、「 [MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」で説明していますが、テンプレート自体を読むだけで多くを把握することができます。

> [!NOTE]
> ほとんどの Azure サービスには、価格レベルに応じて関連するコストがあります。 Azure を初めて使用する場合、 [無料で試す](https://azure.microsoft.com/free/)ことができます。 ただし、MyDriving システムで特定のコンポーネントを使用する予定がない場合は、コストが発生することを回避するために、それらのコンポーネントを削除してください。 この記事で後述する「運用コストを見積もり」のセクションでは、一般的なサービス コストの概要を説明しています。
> 
> 

### <a name="edit-the-template"></a>テンプレートの編集
デプロイをカスタマイズするために、不要なコンポーネントを削除したり、その他を追加したりするには、変更を加える scenario\_complete.params.json と cenario\_complete.json のコピーを最初に作成します。

scenario\_complete.params.json ファイルでは、次の表に示すようなサービス SKU またはストレージ レプリケーションの種類など、さまざまな既定値を上書きできます。 既定値は、最低コストのオプションが選択されます。

| **パラメーター** | **説明** | **既定値** |
| --- | --- | --- |
| IoT Hub SKU |Azure IoT Hub サービスのレベル |F1 |
| Storage Account Type |ストレージ レプリケーションの種類 |標準 LRS |
| SQL Service Objective |使用される同時実行スロット数 |DW100 |
| Hosting Plan SKU |App Service のサービス プラン |F1 |

scenario\_complete.json:

* "baseName" を検索し、希望の名前に変更します。
* "Create" を検索します。 これらの各セクションでリソースが作成されます。
* sqlServerAdminLogin および sqlServerAdminPassword に適した値を設定します。
* リソースを作成するセクションを削除する前に、その名前がファイル内の別の場所に存在しないか検索し、依存関係があるかどうかを確認します。 サービスを作成する各セクションには、依存関係を一覧する *dependsOn* セクションが含まれることに注意してください。

テンプレートでは次が構成されます。 詳細については、「 [リファレンス ガイド](http://aka.ms/mydrivingdocs)」を参照してください。

| **サービス** | **説明と詳細** |
| --- | --- |
| ストレージ アカウント |テンプレートでは、次の 3 つのアカウントを作成します。 |
| -   Stream Analytics から集計されたテレメトリを受信し、API エンドポイントを使用してこのデータを公開する Azure App Service テーブルのバッキング ストアとして機能する SQL database。 | |
| -   HDInsight によって処理される、別の Stream Analytics ジョブから履歴データを蓄積する Blob storage。 | |
| -   Power BI で使用する HDInsight で処理された結果を受信する SQL database。 | |
| Azure IoT Hub |接続されている各デバイスとの双方向接続を確立します。 MyDriving ソリューションで、モバイル アプリは Azure IoT Hub にデータを送信するフィールド ゲートウェイとして機能します。 その後、Azure IoT Hub は Stream Analytics への入力として機能します。 |
| Azure Event Hubs |出力を Azure Service Fabric で作成しされた拡張機能のキューに登録する Stream Analytics ジョブの出力。 |
| Azure SQL Data Warehouse | |
| Stream Analytics ジョブ |App Service API、Azure Machine Learning、拡張機能、および Power BI のリアルタイムと履歴データの両方を集計するために使用するクエリでの入力と出力の接続。 |
| Machine Learning ワークスペース |実験、R コード、API サービスが含まれます。 |
| Azure Data Factory |スケジュールされた Machine Learning の再トレーニング。 |
| Service Fabric ホスティング プラン |拡張機能に使用します。 |
| App Service (“モバイル アプリ”) |モバイル アプリのエンドポイントを提供する Mobile Apps API プロジェクトをホストします。 API コードは、Visual Studio から App Service にデプロイされる必要があります。 |
| アラート ルール |アプリの応答でエラーが示される場合、電子メールが送信されます。 |
| アプリケーション インサイト |App Service で API のパフォーマンスを監視します。 Visual Studio での接続を構成する必要があります。 |
| Azure Key Vault |Web サービス クラスターの証明書を保存します。 |

### <a name="run-the-template"></a>テンプレートの実行
テンプレートの実行方法の詳細は、 **scripts/README.md**に記載されています。

このスクリプトを使用して自分の Azure アカウントのすべてのサービスをプロビジョニングするには、次のいずれかの操作を行います。

* PowerShell の使用:
  
  ```
  
  cd scripts/PowerShell;
  deploy.ps1 *location* *resourceGroupName*
  ```
  
  * *location* は、`North Europe` や `West US` などの [Azure の場所](https://azure.microsoft.com/regions/)です。 `Get-AzureLocation` を使用して、利用可能な場所の一覧を検索します。
  * *resourceGroupName* は、すべてのリソースが属するグループを指定する名前です。 リソースが完了したときに、このグループを削除すると、それらをすべてまとめて削除することができます。
* Bash で DeploymentScripts/Bash/deploy.sh を実行します。
* Visual Studio ソリューションの DeploymentScripts/VS/DeployARM.sln を開き、ビルドします。

テンプレートを実行するたびに、一連の新しいリソースが新しい名前で作成されることに注意してください。 リソースを削除するには、ポータルに移動して、リソース グループを削除します。

スクリプトが何らかの理由で失敗した場合、もう一度実行することができます。

スクリプトでは、Visual Studio Team Services の継続的な統合を構成するオプションを提供しています。 Team Services プロジェクトを設定した場合、URL (https://yourAccountName.visualstudio.com) があります。 要求されたら、完全な URL を入力します。 Team Services プロジェクトに新規または既存の名前を付けることができます。

## <a name="set-up-build-and-test-definitions-in-visual-studio-team-services"></a>Visual Studio Team Services でのビルドの設定と定義のテスト
ほとんどの場合、当社では機能をビルドおよびテストするために、このプロジェクトの Team Services を使用します。 ただし、かんばんボードのあるタスク管理、タスクやソース管理と統合されたコード レビュー、ゲート ビルドなど、コラボレーション機能も提供されます。 これは、GitHub、Xamarin、HockeyApp、および Visual Studio などのその他のツールとうまく統合されます。 いつでも Web インターフェイスまたは Visual Studio のいずれかより便利な方を使用して、Team Services にアクセスできます。

ビルドとリリースの定義の手順では、Team Services [Marketplace](https://marketplace.visualstudio.com/VSTS)で利用可能なさまざまなプラグイン サービスを使用します。 コマンドラインの実行やファイルのコピーを行う基本的なユーティリティだけでなく、Xamarin、Android およびその他のベンダーによるビルドを呼び出したり、HockeyApp と接続したりするサービスがあります。

![チーム サービスでのオプションの構築](./media/iot-solution-build-system/image5.png)

### <a name="build-definitions"></a>ビルド定義
メイン ターゲットそれぞれにビルド定義があります。 機能と回帰テストにも、バリエーションがあります。 次が提供されます。

* MyDriving.Services (モバイル アプリ用バックエンド Web アプリ)
* MyDriving.Xamarin.Android
  
  * MyDriving.Xamarin.Android-Feature
  * MyDriving.Xamarin.Android-Regression
* MyDriving.Xamarin.iOS
  
  * MyDriving.Xamarin.iOS-Feature
  * MyDriving.Xamarin.iOS-Regression
* MyDriving.Xamarin.UWP
  
  * MyDriving.Xamarin.UWP-Feature
  * MyDriving.Xamarin.UWP-Regression

この構成の完全な詳細情報を表示する場合は、「 [MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」のセクション 4.7、「ビルドとリリースの構成」を参照してください。 これらは、次の同じ一般的なパターンに従います。 スクリプト:

1. NuGet パッケージを復元します。 リポジトリにコンパイルされたコードを保持しないため、各ビルドの最初の手順で必要な NuGet パッケージを復元します。
2. ライセンスをアクティブ化します。 ビルドはクラウドで実行されるため、ライセンスが必要な場合は (特に、Xamarin ビルド サービス)、現在のビルド コンピューターでライセンスをアクティブ化する必要があります。 その後、別のコンピューターで使用できるように、当社がすぐにライセンスを非アクティブ化します。
3. 適切なサービスを使用してビルドする。 モバイル アプリには Xamarin ビルドを、バックエンド Web サービスには Visual Studio ビルドを使用します。
4. テストを作成します。
5. テストを実行します。 Xamarin Test Cloud でモバイル アプリ テストを実行します。
6. ビルドの結果を格納場所に発行します。

メイン ビルドのトリガーが、継続的インテグレーションに設定されます。 つまり、コードがマスター分岐にチェックインするたびに、ビルドが実行されます。

![トリガーが継続的インテグレーションに設定されているインターフェイス](./media/iot-solution-build-system/image6.png)

### <a name="release-definitions"></a>リリース定義
リリース定義は、ほぼ同じように設定されます。

Web サービスでは、Azure Web アプリとしてデプロイを設定します。

![Azure の Web アプリとしてデプロイを設定するためのインターフェイス](./media/iot-solution-build-system/image7.png)

リリース トリガーを継続的なデプロイを設定します。 つまり、チェックインの後にビルドが成功するたびに、Web アプリが更新されます。

![リリースのトリガーを継続的なデプロイに設定するためのインターフェイス](./media/iot-solution-build-system/image8.png)

モバイル アプリの場合、HockeyApp にデプロイします。

![モバイル アプリを HockeyApp にデプロイするためのインターフェイス](./media/iot-solution-build-system/image9.png)

## <a name="explore-telemetry-by-using-application-insights"></a>Application Insights を使用したテレメトリの調査
[Application Insights](../application-insights/app-insights-overview.md) では、パフォーマンスと Web サービスの使用状況に関するテレメトリを収集します。 Application Insights SDK では、サービスから Azure の Application Insights リソースにテレメトリを送信します。

テンプレートで設定する Application Insights リソースを参照します。 そこで、[Mobile App Service プロジェクト](https://github.com/Azure-Samples/MyDriving/tree/master/src/MobileAppService)のパフォーマンスのグラフを調べることができます。 グラフには、サーバーの要求と応答時間、エラー、および例外の数が表示されます。 依存関係の応答時間のグラフもあります。つまり、Machine Learning としてデータベースと REST API への呼び出しがあります。 パフォーマンスに問題がある場合は、問題の原因となっているシステムの部分を表示することができます。

![パフォーマンス グラフの例](./media/iot-solution-build-system/image11.png)

手動で設定した Web サービスがある場合は、同じグラフを簡単に取得できます。 Web サービス ブレードで、**[ツール]** > **[拡張機能]** > **[追加]** の順に選択します。 **[Application Insights]**を選択します。

![グラフを取得する Application Insights を選択するためのインターフェイス](./media/iot-solution-build-system/image12.png)

この機能は、Application Insights SDK を使用してアプリケーションをインストルメント化して動作します。

開発時に [Application Insights SDK を追加して](../application-insights/app-insights-asp-net.md) 、カスタム テレメトリを追加できます (または、Azure の外部で実行されているアプリケーションをインストルメント化できます)。 これは、ユーザーの平均乗車距離や合計走行距離など、アプリケーションによって異なるメトリックを記録するために便利です。 Visual Studio でプロジェクトを右クリックし、 **[Application Insights の追加]**を選択します

![カスタム テレメトリを追加する [Application Insights の追加] を選択するためのインターフェイス](./media/iot-solution-build-system/image10.png)

異常な数のエラー応答がある場合に、Application Insights はアラート メールを送信します。 また、応答時間などのさまざまなメトリックに独自のアラートを設定することもできます。

Web サービスが常に起動および実行されるようにするために、 [可用性テスト](../application-insights/app-insights-monitor-web-app-availability.md)を設定できます。 これらのテストは世界各地のさまざまな場所から 15 分ごとにサイトを ping します。 ここでも、問題がある可能性がある場合は、電子メールを受信します。

## <a name="estimate-operational-costs"></a>運用コストの見積もり
このようなアプリの小規模の実行は、非常に低価格で行うことができます。 ほとんどのサービスにはエントリ レベルの Free レベルがあるため、開発と小規模の運用コストはほんのわずかです。 当然ながら、自分のアプリで MyDriving で示したすべての機能を使用する必要はありません。

MyDriving の開発構成を設定するコストの大まかな見積もりを、次に示します。 使用し *なかった* 代替手段についても示します。 この情報は、自分のコストを見積もるとき役立ちます。

次を想定しています。

* 5 チーム以下 (これに加え、観察する利害関係者)。
* 約 1 か月の実行。
* 1 日あたり 4 回乗車するユーザー 100 人。

> [!NOTE]
> Azure を初めて使用する場合、 [無料アカウント](https://azure.microsoft.com/free/)があります。
> 
> 

| **サービス/コンポーネント** | **メモ** | **コスト/月** |
| --- | --- | --- |
| [Visual Studio 2015 Community](https://www.visualstudio.com/products/visual-studio-community-vs) with [Xamarin](https://visualstudiogallery.msdn.microsoft.com/dcd5b7bd-48f0-4245-80b6-002d22ea6eee) <br/>クロスプラットフォームの開発環境 |Visual Studio Community。 (単一のコード ベースからクロスプラットフォームを設計するには、[Xamarin.Forms](https://xamarin.com/forms) に [Visual Studio Professional](https://www.visualstudio.com/vs-2015-product-editions) が必要です)。 |0 ドル |
| [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) <br/>デバイスへの双方向データ接続 |8,000 メッセージ + 0.5 KB/メッセージが無料。 |0 ドル |
| [Stream Analytics](https://azure.microsoft.com/pricing/details/stream-analytics/)  <br/>    大量のストリーム データ処理 |有効な場合に、1 時間あたりのストリーミング ユニットごとに 0.031 ドルを課金します。 必要なストリーミング ユニット数を選択します。さらに必要な場合はスケールアップします。 |23 ドル |
| [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)<br/>  適応型の応答 |10 ドル/シート/月。 <br/>                                                                                                                                                                                 + 3 時間の実験 \* 1 ドル / 実験時間 <br/>                                                                                                                                                           + 3.5 時間 API CPU \* 2 ドル/運用 CPU 時間。 <br/>                                                                                                                                                          API CPU 時間は 5 分/日の再トレーニングを想定していますが、入力データが増えると上昇します。                   <br/>                                                                                                                                                                     + 400 回の乗車/日の処理に 2 分/日のスコアリング。 |20 ドル |
| [App Service](https://azure.microsoft.com/pricing/details/app-service/)  <br/>  モバイル バックエンド用のホスト |レベル B1 – 運用 Web アプリ。 |56 ドル |
| [Visual Studio Team Services ](https://azure.microsoft.com/pricing/details/visual-studio-team-services/)  <br/>  ビルド、単体テストおよびリリースの管理、タスク管理 |プライベート Agents、ユーザー 5 人。 |0 ドル |
| [アプリケーション インサイト](https://azure.microsoft.com/pricing/details/application-insights/) <br/>Web サービスとサイトのパフォーマンスと使用状況の監視 |Free レベル。 |0 ドル |
| [HockeyApp](http://hockeyapp.net/pricing/) <br/>  ベータ アプリの配布、フィードバック、使用量とクラッシュ データの収集 |新規ユーザー用に 2 つの無料アプリ。<br/> その後は 30 ドル/月。 |0 ドル |
| [Xamarin](https://store.xamarin.com/)<br/>  複数のデバイスの統合されたプラットフォーム上のコード |無料試用版。 <br/>その後は 25 ドル/月。 |0 ドル |
| [SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)  |Basic レベル、Single Database モデル。 |5 ドル |
| [Service Fabric](https://azure.microsoft.com/pricing/details/service-fabric/) (省略可能) |ローカル クラスターを実行する。 |0 ドル |
| [Power BI](https://powerbi.microsoft.com/pricing/)<br/>  多用途のディスプレイおよびストリーミングされたデータと静的データの調査 |Free レベル: 1GB、10,000 行/時間、毎日更新。 <br/> [上限引き上げ](https://powerbi.microsoft.com/documentation/powerbi-power-bi-pro-content-what-is-it/)の場合は 10 ドル/ユーザー/月、接続オプションの追加、コラボレーション。 |0 ドル |
| [Storage](https://azure.microsoft.com/pricing/details/storage/) |L (ローカル冗長) &lt; 100 G 0.024 ドル/GB. |3 ドル |
| [Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) |0.60 ドル/アクティビティ \* (8 - 5 FOC)。 |2 ドル |
| [HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) <br/>   毎日の再トレーニングに使用するオンデマンド クラスター |毎日 1 時間 X 31 日の場合、0.32 ドル/時間で A3 ノード 3 個。 |30 ドル |
| [Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) |11 ドル/月のスループット ユニットの基本 + 0.028 ドルのイングレス。 |11 ドル |
| OBD ドングル | |12 ドル |
| **合計** | |**157 ドル** |

詳細については、次を参照してください。

*  [Azure サービスのクォータと制限](../azure-subscription-service-limits.md#iot-hub-limits)
* [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

## <a name="send-us-your-feedback"></a>フィードバックを送信します
MyDriving は、独自の IoT システムの開発を支援するために作成されました。そのため、このアプリの動作に関するコメントは大歓迎です。 次のような場合は、ぜひお知らせください。

* 問題または課題が生じた場合。
* シナリオに合わせて拡張できる点がある場合。
* より効率的に特定のニーズを達成する方法を見つけた場合。
* MyDriving やこのドキュメントの改善につながるご提案がある場合。

フィードバックを送るには、[GitHub 上の問題] を作成するか、以下にコメントを残してください (en-us 版)。

皆様のご意見をお待ちしております。

## <a name="next-steps"></a>次のステップ
システムとそのコンポーネントの設計の包括的な説明である「 [MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs)」をお勧めします。




<!--HONumber=Nov16_HO3-->


