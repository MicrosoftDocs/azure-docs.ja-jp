---
title: "Xamarin.Forms を使用して Mobile Apps を使用する"
description: "次のチュートリアルに従って、Xamarin.Forms 開発用の Azure Mobile Apps を使用します"
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 6b8c5c2ac2e721c4d6f73c7c17f34eadc041e0c9
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-xamarinforms-app"></a>Xamarin.Forms アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Mobile App バックエンドを使用して Xamarin.Forms モバイル アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。 新しい Mobile App バックエンドと、アプリのデータを Azure に格納する簡単な *Todo list* Xamarin.Forms アプリの両方を作成します。

Xamarin.Forms の他のすべての Azure Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大&10; 件の無料 Mobile Apps を入手できます。 このアプリは評価終了後も使用できます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Visual Studio と Xamarin。 手順については、「 [セットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx) 」をご覧ください。 
* Xcode v7.0 以降と Xamarin Studio Community がインストールされた Mac。 [Visual Studio と Xamarin を対象にしたセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx)に関するページと「[Mac ユーザー向けのセットアップ、インストール、および検証](https://msdn.microsoft.com/library/mt488770.aspx)」 (MSDN) を参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](https://azure.microsoft.com/try/app-service/mobile/)に関するページにアクセスしてください。App Service で、有効期限付きのスターター モバイル アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure Mobile App バックエンドの作成
新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。 次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成
以下の手順に従って、Node.js または .NET バックエンドを使用するようにサーバー プロジェクトを構成します。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms ソリューションのダウンロードと実行
いくつかのオプションがあります。 ネットワーク接続された Mac を使用して iOS アプリを構築すれば、Mac にソリューションをダウンロードして Xamarin Studio で開くことも、ソリューションを Windows コンピューターにダウンロードして Visual Studio で開くこともできます。 Xamarin のセットアップのシナリオについてさらに詳細な手順が必要な場合は、 [Visual Studio と Xamarin を対象にしたセットアップとインストール](https://msdn.microsoft.com/library/mt613162.aspx) に関するページを参照してください。

先に進みます。

1. Mac または Windows コンピューターで、 [Azure ポータル] をブラウザー ウィンドウで開きます。
2. モバイル アプリの [設定] ブレードで、[モバイル] の **[開始]** > **[Xamarin.Forms]** の順にクリックします。 手順 3 で、まだ選択されていない場合は **[新しいアプリの作成]** をクリックします。  次に、 **[ダウンロード]** ボタンをクリックします。
   
   これにより、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。
3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio または Visual Studio でそれを開きます。
   
   ![][9]
   
   ![][8]

## <a name="optional-run-the-ios-project"></a>(省略可能) iOS プロジェクトの実行
このセクションでは、iOS デバイス用の Xamarin iOS プロジェクトを実行します。 iOS デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-xamarin-studio"></a>Xamarin Studio で使用する
1. iOS プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]**をクリックします。
2. **[実行]** メニューで **[デバッグの開始]** をクリックし、プロジェクトをビルドして、アプリを iPhone エミュレーターで起動します。

#### <a name="in-visual-studio"></a>Visual Studio で使用する
1. iOS プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]**をクリックします。
2. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。
3. **[構成マネージャー]** ダイアログ ボックスで、iOS プロジェクトの **[ビルド]** と **[デプロイ]** チェックボックスをオンにします。
4. **F5** キーを押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動します。
   
   > [!NOTE]
   > ビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、Xamarin サポート パッケージを最新バージョンに更新します。 クイック スタート プロジェクトでは、最新バージョンへの更新が遅れる場合があります。    
   > 
   > 

アプリで、意味のあるテキスト (「"*Xamarin の学習*"」など) を入力し、**+** ボタンをクリックします。

![][10]

これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs C# ファイルに、モバイル アプリ バックエンドにアクセスするコードが表示されます。
> 
> 

## <a name="optional-run-the-android-project"></a>(省略可能) Android プロジェクトの実行
このセクションでは、Android 用の Xamarin Android プロジェクトを実行します。 Android デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-xamarin-studio"></a>Xamarin Studio で使用する
1. Android プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]**をクリックします。
2. **[実行]** メニューで **[デバッグの開始]** をクリックし、プロジェクトをビルドして、アプリを Android エミュレーターで起動します。

#### <a name="in-visual-studio"></a>Visual Studio で使用する
1. Android (Droid) プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]**をクリックします。
2. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。
3. **[構成マネージャー]** ダイアログ ボックスで、Android プロジェクトの **[ビルド]** と **[デプロイ]** チェックボックスを選択します。
4. **F5** キーを押してプロジェクトをビルドし、Android エミュレーターでアプリケーションを起動します。
   
   > [!NOTE]
   > ビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、Xamarin サポート パッケージを最新バージョンに更新します。 クイック スタート プロジェクトでは、最新バージョンへの更新が遅れる場合があります。    
   > 
   > 

アプリで、意味のあるテキスト (「"*Xamarin の学習*"」など) を入力し、**+** ボタンをクリックします。

![][11]

これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs C# ファイルに、モバイル アプリ バックエンドにアクセスするコードが表示されます。
> 
> 

## <a name="optional-run-the-windows-project"></a>(省略可能) Windows プロジェクトの実行
このセクションでは、Windows デバイス用の Xamarin WinApp プロジェクトを実行します。 Windows デバイスを使用していない場合は、このセクションを省略できます。

#### <a name="in-visual-studio"></a>Visual Studio で使用する
1. いずれかの Windows プロジェクトを右クリックし、 **[スタート アップ プロジェクトに設定]**をクリックします。
2. **[ビルド]** メニューの **[構成マネージャー]** をクリックします。
3. **[構成マネージャー]** ダイアログ ボックスで、選択した Windows プロジェクトの **[ビルド]** と **[デプロイ]** チェックボックスを選択します。
4. **F5** キーを押してプロジェクトをビルドし、 Windows エミュレーターでアプリケーションを起動します。
   
   > [!NOTE]
   > ビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、Xamarin サポート パッケージを最新バージョンに更新します。 クイック スタート プロジェクトでは、最新バージョンへの更新が遅れる場合があります。    
   > 
   > 

アプリで、意味のあるテキスト (「"*Xamarin の学習*"」など) を入力し、**+** ボタンをクリックします。

これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。 要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

![][12]

> [!NOTE]
> ソリューションのポータブル クラス ライブラリ プロジェクトの TodoItemManager.cs C# ファイルに、モバイル アプリ バックエンドにアクセスするコードが表示されます。
> 
> 

## <a name="next-steps"></a>次のステップ
* [アプリへの認証の追加](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリへのプッシュ通知の追加](app-service-mobile-xamarin-forms-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  モバイル アプリ バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。
* [Azure Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md)  
  Xamarin アプリでの管理されたクライアント SDK の操作方法について説明します。 

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


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


