---
title: "Xamarin.Forms を使用して Mobile Apps を使用する"
description: "Xamarin.Forms 開発用の Mobile Apps を初めて使用する場合は、このチュートリアルに従ってください。"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: syntaxc4
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: ee12caaad4095cff6dae3282f747ae804f93db81
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure App Service の Mobile Apps 機能をバックエンドとして使用して、Xamarin.Forms モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 新しい Mobile Apps バックエンドと、アプリのデータを Azure に格納する簡単な To Do リスト Xamarin.Forms アプリの両方を作成します。

Xamarin.Forms の他のすべての Azure Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、[Azure 無料試用版](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。

* Visual Studio と Xamarin。 詳しくは、[Visual Studio と Xamarin のセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx)に関するページを参照してください。

* Xcode v7.0 以降と Xamarin Studio Community がインストールされた Mac。 詳しくは、[Visual Studio と Xamarin のセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx)に関するページと「[Mac ユーザー向けのセットアップ、インストール、および検証](https://msdn.microsoft.com/library/mt488770.aspx)」(MSDN) を参照してください。

## <a name="create-a-new-mobile-apps-back-end"></a>新しい Mobile Apps バックエンドの作成

新しい Mobile Apps バックエンドを作成するには、次の手順を実行します。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

以上で、モバイル クライアント アプリケーションから使用できる Mobile Apps バックエンドのセットアップが完了しました。 次は、簡単な To Do リスト バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成

Node.js または .NET バックエンドを使用するようにサーバー プロジェクトを構成するには、次の手順を実行します。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms ソリューションのダウンロードと実行

ソリューションは 2 とおりの方法でダウンロードできます。 ソリューションを Mac にダウンロードして Xamarin Studio で開くか、Windows コンピューターにダウンロードして Visual Studio で開きます (ネットワーク接続された Mac を iOS アプリの作成に使用)。 詳細については、[Visual Studio と Xamarin のセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx)に関するページを参照してください。

Mac または Windows コンピューターで、次の手順を実行します。

1. [Azure ポータル]にアクセスします。

2. モバイル アプリの **[設定]** ブレードで、**[モバイル]** の **[開始]** > **[Xamarin.Forms]** の順に選択します。 **ステップ 3** で **[新しいアプリの作成]** を選択し、**[ダウンロード]** を選択します。

   この操作により、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。

3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio (Mac) または Visual Studio (Windows) で開きます。

   ![Xamarin Studio で抽出されたプロジェクト][9]

   ![Visual Studio で抽出されたプロジェクト][8]

## <a name="optional-run-the-ios-project"></a>(省略可能) iOS プロジェクトの実行
このセクションでは、iOS デバイス用の Xamarin iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-xamarin-studio"></a>Xamarin Studio で使用する
1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[実行]** メニューで **[デバッグの開始]** を選択し、プロジェクトをビルドして、アプリを iPhone エミュレーターで起動します。

#### <a name="in-visual-studio"></a>Visual Studio で使用する
1. iOS プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、iOS プロジェクトの **[ビルド]** と **[デプロイ]** チェック ボックスをオンにします。

4. プロジェクトをビルドし、iPhone エミュレーターでアプリを起動するには、**F5** キーを押します。

   > [!NOTE]
   > プロジェクトのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、Xamarin サポート パッケージを最新バージョンに更新します。 クイックスタート プロジェクトは、最新バージョンへの更新に時間がかかる場合があります。    
   >
   >

5. アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 (**+**) を選択します。

    ![][10]

    この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。

    > [!NOTE]
    > ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs C# ファイルに、Mobile Apps バックエンドにアクセスするコードが表示されます。
    >
    >

## <a name="optional-run-the-android-project"></a>(省略可能) Android プロジェクトの実行
このセクションでは、Android 用の Xamarin droid プロジェクトを実行します。 Android デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-xamarin-studio"></a>Xamarin Studio で使用する

1. Android プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. プロジェクトをビルドして、アプリを Android エミュレーターで起動するには、**[実行]** メニューで **[デバッグの開始]** を選択します。

#### <a name="in-visual-studio"></a>Visual Studio で使用する

1. Android (Droid) プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、Android プロジェクトの **[ビルド]** と **[デプロイ]** チェック ボックスをオンにします。

4. プロジェクトをビルドして Android エミュレーターでアプリを起動するには、**F5** キーを押します。

   > [!NOTE]
   > プロジェクトのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、Xamarin サポート パッケージを最新バージョンに更新します。 クイックスタート プロジェクトは、最新バージョンへの更新に時間がかかる場合があります。    
   >
   >

5. アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 (**+**) を選択します。

    ![][11]
    
    この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。
    
    > [!NOTE]
    > ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs C# ファイルに、Mobile Apps バックエンドにアクセスするコードが表示されます。
    >
    >

## <a name="optional-run-the-windows-project"></a>(省略可能) Windows プロジェクトの実行

このセクションでは、Windows デバイス用の Xamarin WinApp プロジェクトを実行します。 Windows デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-visual-studio"></a>Visual Studio で使用する

1. いずれかの Windows プロジェクトを右クリックし、**[スタート アップ プロジェクトに設定]** を選択します。

2. **[ビルド]** メニューの **[構成マネージャー]** を選択します。

3. **[構成マネージャー]** ダイアログ ボックスで、選択した Windows プロジェクトの横にある **[ビルド]** と **[デプロイ]** チェック ボックスを選択します。

4. プロジェクトをビルドして Windows エミュレーターでアプリを起動するには、**F5** キーを押します。

   > [!NOTE]
   > プロジェクトのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、Xamarin サポート パッケージを最新バージョンに更新します。 クイックスタート プロジェクトは、最新バージョンへの更新に時間がかかる場合があります。    
   >
   >

5. アプリで、意味のあるテキスト ("*Xamarin の学習*" など) を入力し、正符号 (**+**) を選択します。

    この操作で、Azure でホストされている新しい Mobile Apps バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目が Mobile Apps バックエンドによって返され、データが一覧に表示されます。
    
    ![][12]
    
    > [!NOTE]
    > ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs C# ファイルに、Mobile Apps バックエンドにアクセスするコードが表示されます。
    >
    >

## <a name="next-steps"></a>次のステップ

* [アプリへの認証の追加](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。

* [アプリへのプッシュ通知の追加](app-service-mobile-xamarin-forms-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するように Mobile Apps バックエンドを構成する方法について説明します。

* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフラインの同期を使用すれば、ネットワーク接続が得られない状況でも、モバイル アプリケーションのデータを表示、追加、または変更することができます。

* [Mobile Apps 用の管理されたクライアントの使用](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Xamarin アプリでの管理されたクライアント SDK の操作方法について説明します。

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
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure ポータル]: https://portal.azure.com/

