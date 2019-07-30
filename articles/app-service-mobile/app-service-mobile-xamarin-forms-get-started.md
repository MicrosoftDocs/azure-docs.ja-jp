---
title: Xamarin.Forms を使用して Mobile Apps を使用する
description: Xamarin.Forms 開発用の Mobile Apps を初めて使用する場合は、このチュートリアルに従ってください。
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: bca0f0de7de321060635459c4435525f650c7467
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446319"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Azure での Xamarin.Forms アプリの作成

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このチュートリアルでは、Azure App Service の Mobile Apps 機能をバックエンドとして使用して、Xamarin.Forms モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 新しい Mobile Apps バックエンドと、アプリのデータを Azure に格納する簡単な To Do リスト Xamarin.Forms アプリの両方を作成します。

Xamarin.Forms の他のすべての Azure Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* Visual Studio 2017 以降または Visual Studio for Mac に含まれる Visual Studio Tools for Xamarin。 手順については、[Xamarin のインストール ページ][Install Xamarin]を参照してください。

* (省略可能) iOS アプリをビルドするには、Xcode 9.0 以降がインストールされた Mac が必要です。 Visual Studio for Mac を使用して iOS アプリを開発するか、Visual Studio 2017 以降を使用することができます (Mac がネットワーク上で使用可能な場合)。

## <a name="create-a-new-mobile-apps-back-end"></a>新しい Mobile Apps バックエンドの作成
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Xamarin.Forms ソリューションを実行する

ソリューションを開くには Visual Studio Tools for Xamarin が必要です。[Xamarin のインストール手順][Install Xamarin]を参照してください。 ツールが既にインストールされている場合は、以下の手順に従ってソリューションをダウンロードし、開きます。

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows と Mac)

1. [Azure portal](https://portal.azure.com/) に移動し、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

2. このフォルダー内のファイル `Constants.cs` (xamarin.forms/ZUMOAPPNAME) を開きます。 アプリケーション名は `ZUMOAPPNAME` です。

3. `Constants.cs` クラスで、`ZUMOAPPURL` 変数を上のパブリック エンドポイントに置き換えます。

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    →

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Android または Windows のプロジェクトを実行するには、以下の手順に従います。また、ネットワーク接続された Mac コンピューターが使用可能な場合は、iOS プロジェクトを実行できます。

## <a name="optional-run-the-android-project"></a>(省略可能) Android プロジェクトの実行

このセクションでは、Xamarin.Android プロジェクトを実行します。 Android デバイスを使用していない場合は、このセクションを省略できます。

### <a name="visual-studio"></a>Visual Studio

1. Android (Droid) プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、Android プロジェクトの横の **[ビルド]** と **[デプロイ]** チェック ボックスをオンにし、共有コード プロジェクトの **[ビルド]** ボックスがオンになっていることを確認します。

4. プロジェクトをビルドし、Android エミュレーターでアプリを起動するには、**F5** キーを押すか、 **[開始]** をクリックします。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. Android プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

2. プロジェクトをビルドして、アプリを Android エミュレーターで起動するには、 **[実行]** メニューを選択してから **[デバッグの開始]** を選択します。

アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 ( **+** ) を選択します。

![Android の To Do アプリ][11]

この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> Mobile Apps バックエンドにアクセスするコードは、ソリューションの共有コード プロジェクトの **TodoItemManager.cs** C# ファイルにあります。
>

## <a name="optional-run-the-ios-project"></a>(省略可能) iOS プロジェクトの実行

このセクションでは、iOS デバイス用の Xamarin.iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

### <a name="visual-studio"></a>Visual Studio

1. iOS プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、iOS プロジェクトの横の **[ビルド]** と **[デプロイ]** チェック ボックスをオンにし、共有コード プロジェクトの **[ビルド]** ボックスがオンになっていることを確認します。

4. プロジェクトをビルドし、iPhone エミュレーターでアプリを起動するには、**F5** キーを押します。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. iOS プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

2. **[実行]** メニューで **[デバッグの開始]** を選択し、プロジェクトをビルドして、アプリを iPhone エミュレーターで起動します。

アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 ( **+** ) を選択します。

![iOS の To Do アプリ][10]

この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> Mobile Apps バックエンドにアクセスするコードは、ソリューションの共有コード プロジェクトの **TodoItemManager.cs** C# ファイルにあります。
>

## <a name="optional-run-the-windows-project"></a>(省略可能) Windows プロジェクトの実行

このセクションでは、Windows デバイス用の Xamarin.Forms Universal Windows Platform (UWP) プロジェクトを実行します。 Windows デバイスを使用していない場合は、このセクションを省略できます。

### <a name="visual-studio"></a>Visual Studio

1. UWP プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、選択した Windows プロジェクトの横の **[ビルド]** と **[デプロイ]** チェック ボックスをオンにし、共有コード プロジェクトの **[ビルド]** ボックスがオンになっていることを確認します。

4. プロジェクトをビルドし、Windows エミュレーターでアプリを起動するには、**F5** キーを押すか、 **[開始]** をクリックします (「**ローカル コンピューター**」と表示されています)。

> [!NOTE]
> macOS で Windows プロジェクトを実行することはできません。

アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 ( **+** ) を選択します。

この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

![UWP の To Do アプリ][12]

> [!NOTE]
> ソリューションのポータブル クラス ライブラリ プロジェクトの **TodoItemManager.cs** C# ファイルに、Mobile Apps バックエンドにアクセスするコードが表示されます。
>

## <a name="troubleshooting"></a>トラブルシューティング

ソリューションのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、最新バージョンの `Xamarin.Forms` に更新します。また、Android プロジェクトで `Xamarin.Android` サポート パッケージを更新します。 クイック スタート プロジェクトには、必ずしも最新バージョンが含まれていません。

Android プロジェクトで参照されるすべてのサポート パッケージのバージョンが同じである必要があることに注意してください。 [Azure Mobile Apps の NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)には、Android プラットフォームに関して `Xamarin.Android.Support.CustomTabs` の依存関係があるため、プロジェクトで新しいサポート パッケージを使用する場合は、必要なバージョンを使用してこのパッケージを直接インストールし、競合を回避する必要があります。

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
