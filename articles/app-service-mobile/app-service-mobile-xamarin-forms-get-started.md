---
title: Xamarin.Forms を使用して Mobile Apps を使用する
description: Xamarin.Forms 開発用の Mobile Apps を初めて使用する場合は、このチュートリアルに従ってください。
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 2e0a4b51e9140b9b3c0032980b7c6cd5a4d55638
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761397"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Azure での Xamarin.Forms アプリの作成

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

このチュートリアルでは、Azure App Service の Mobile Apps 機能をバックエンドとして使用して、Xamarin.Forms モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 新しい Mobile Apps バックエンドと、アプリのデータを Azure に格納する簡単な To Do リスト Xamarin.Forms アプリの両方を作成します。

Xamarin.Forms の他のすべての Azure Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* Visual Studio 2017 または Visual Studio for Mac に含まれる Visual Studio Tools for Xamarin。 手順については、[Xamarin のインストール ページ][Install Xamarin]をご覧ください。

* (省略可能) iOS アプリをビルドするには、Xcode 9.0 以降がインストールされた Mac が必要です。 Visual Studio for Mac を使用して iOS アプリを開発するか、Visual Studio 2017 を使用することができます (Mac がネットワーク上で使用可能な場合)。

## <a name="create-a-new-mobile-apps-back-end"></a>新しい Mobile Apps バックエンドの作成

新しい Mobile Apps バックエンドを作成するには、次の手順を実行します。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

以上で、モバイル アプリから使用できる Mobile Apps バックエンドのセットアップが完了しました。 次は、簡単な To Do リスト バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成

Node.js または .NET バックエンドを使用するようにサーバー プロジェクトを構成するには、次の手順を実行します。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms ソリューションのダウンロードと実行

ソリューションを開くには Visual Studio Tools for Xamarin が必要です。[Xamarin のインストール手順][Install Xamarin]をご覧ください。 ツールが既にインストールされている場合は、以下の手順に従ってソリューションをダウンロードし、開きます。

### <a name="visual-studio"></a>Visual Studio

1. [Azure Portal]にアクセスします。

2. モバイル アプリの [設定] ブレードで、**[クイックスタート]** ([デプロイメント] の下) > **[Xamarin.Forms]** の順にクリックします。 手順 3 で、まだ選択されていない場合は **[新しいアプリの作成]** をクリックします。  次に、 **[ダウンロード]** ボタンをクリックします。

   この操作により、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. ダウンロードしたプロジェクトを抽出し、Visual Studio 2017 で開きます。

   ![Visual Studio で抽出されたプロジェクト][8]

4. Android または Windows のプロジェクトを実行するには、以下の手順に従います。また、ネットワーク接続された Mac コンピューターが使用可能な場合は、iOS プロジェクトを実行できます。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. [Azure Portal]にアクセスします。

2. モバイル アプリの [設定] ブレードで、**[クイックスタート]** ([デプロイメント] の下) > **[Xamarin.Forms]** の順にクリックします。 手順 3 で、まだ選択されていない場合は **[新しいアプリの作成]** をクリックします。  次に、 **[ダウンロード]** ボタンをクリックします。

   この操作により、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. ダウンロードしたプロジェクトを抽出し、Visual Studio for Mac で開きます。

   ![Visual Studio for Mac で抽出されたプロジェクト][9]

4. Android または iOS プロジェクトを実行するには、以下の手順に従います。



## <a name="optional-run-the-android-project"></a>(省略可能) Android プロジェクトの実行

このセクションでは、Xamarin.Android プロジェクトを実行します。 Android デバイスを使用していない場合は、このセクションを省略できます。

### <a name="visual-studio"></a>Visual Studio

1. Android (Droid) プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、Android プロジェクトの横の **[ビルド]** と **[デプロイ]** チェック ボックスをオンにし、共有コード プロジェクトの **[ビルド]** ボックスがオンになっていることを確認します。

4. プロジェクトをビルドし、Android エミュレーターでアプリを起動するには、**F5** キーを押すか、**[開始]** をクリックします。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. Android プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. プロジェクトをビルドして、アプリを Android エミュレーターで起動するには、**[実行]** メニューを選択してから **[デバッグの開始]** を選択します。



アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 (**+**) を選択します。

![Android の To Do アプリ][11]

この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> Mobile Apps バックエンドにアクセスするコードは、ソリューションの共有コード プロジェクトの **TodoItemManager.cs** C# ファイルにあります。
>

## <a name="optional-run-the-ios-project"></a>(省略可能) iOS プロジェクトの実行

このセクションでは、iOS デバイス用の Xamarin.iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

### <a name="visual-studio"></a>Visual Studio

1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、iOS プロジェクトの横の **[ビルド]** と **[デプロイ]** チェック ボックスをオンにし、共有コード プロジェクトの **[ビルド]** ボックスがオンになっていることを確認します。

4. プロジェクトをビルドし、iPhone エミュレーターでアプリを起動するには、**F5** キーを押します。

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[実行]** メニューで **[デバッグの開始]** を選択し、プロジェクトをビルドして、アプリを iPhone エミュレーターで起動します。



アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 (**+**) を選択します。

![iOS の To Do アプリ][10]

この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> Mobile Apps バックエンドにアクセスするコードは、ソリューションの共有コード プロジェクトの **TodoItemManager.cs** C# ファイルにあります。
>

## <a name="optional-run-the-windows-project"></a>(省略可能) Windows プロジェクトの実行

このセクションでは、Windows デバイス用の Xamarin.Forms Universal Windows Platform (UWP) プロジェクトを実行します。 Windows デバイスを使用していない場合は、このセクションを省略できます。

### <a name="visual-studio"></a>Visual Studio

1. UWP プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、選択した Windows プロジェクトの横の **[ビルド]** と **[デプロイ]** チェック ボックスをオンにし、共有コード プロジェクトの **[ビルド]** ボックスがオンになっていることを確認します。

4. プロジェクトをビルドし、Windows エミュレーターでアプリを起動するには、**F5** キーを押すか、**[開始]** をクリックします (「**ローカル コンピューター**」と表示されています)。

> [!NOTE]
> macOS で Windows プロジェクトを実行することはできません。



アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 (**+**) を選択します。

この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

![UWP の To Do アプリ][12]

> [!NOTE]
> ソリューションのポータブル クラス ライブラリ プロジェクトの **TodoItemManager.cs** C# ファイルに、Mobile Apps バックエンドにアクセスするコードが表示されます。
>

## <a name="troubleshooting"></a>トラブルシューティング

ソリューションのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、最新バージョンの `Xamarin.Forms` に更新します。また、Android プロジェクトで `Xamarin.Android` サポート パッケージを更新します。 クイック スタート プロジェクトには、必ずしも最新バージョンが含まれていません。

Android プロジェクトで参照されるすべてのサポート パッケージのバージョンが同じである必要があることに注意してください。 [Azure Mobile Apps の NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)には、Android プラットフォームに関して `Xamarin.Android.Support.CustomTabs` の依存関係があるため、プロジェクトで新しいサポート パッケージを使用する場合は、必要なバージョンを使用してこのパッケージを直接インストールし、競合を回避する必要があります。

## <a name="next-steps"></a>次の手順

* [アプリに認証を追加する](app-service-mobile-xamarin-forms-get-started-users.md) ID プロバイダーを使用してアプリのユーザーを認証する方法を学習します。

* [アプリにプッシュ通知を追加する](app-service-mobile-xamarin-forms-get-started-push.md): アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するように Mobile Apps バックエンドを構成する方法を学習します。

* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md): Mobile Apps バックエンドを使用して、アプリにオフライン サポートを追加する方法を学習します。 オフラインの同期を使用すれば、ネットワーク接続が得られない状況でも、モバイル アプリケーションのデータを表示、追加、または変更することができます。

* [Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md): Xamarin アプリでの管理されたクライアント SDK の操作方法について学習します。

* [Xamarin.Forms で他の Azure サービスを使用する](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/): 検索、格納、認知サービスなどの追加の Azure 機能を Xamarin.Forms アプリに追加します。

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
