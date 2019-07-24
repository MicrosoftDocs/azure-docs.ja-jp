---
title: Xamarin.iOS アプリに Azure App Service Mobile Apps を使用する | Microsoft Doc
description: このチュートリアルに従って、Xamarin.iOS 開発用の Azure Mobile Apps を使用します。
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 03fb286bd24bb12f3a1e508627a2de156e185568
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892434"
---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS アプリを作成する
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Azure Mobile Apps バックエンドを使用して Xamarin.iOS モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。  新しいモバイル アプリ バックエンドと、アプリのデータを Azure に保存する簡単な *Todo list* Xaamrin.iOS アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Xamarin.iOS チュートリアルを実行する前に完了しておく必要があります。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手します。このアプリは評価終了後も使用できます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Visual Studio と Xamarin。 手順については、「 [セットアップとインストール](/visualstudio/cross-platform/setup-and-install) 」をご覧ください。
* Xcode v7.0 以降と Xamarin Studio Community がインストールされた Mac。 [Visual Studio と Xamarin を対象にしたセットアップとインストール](/visualstudio/cross-platform/setup-and-install)に関するページと「[Mac ユーザー向けのセットアップ、インストール、および検証](/visualstudio/cross-platform/setup-install-and-verifications-for-mac-users)」 (MSDN) を参照してください。

## <a name="create-an-azure-mobile-app-backend"></a>Azure モバイル アプリ バックエンドの作成
モバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成
これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。 次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

次の手順に従って、Node.js または .NET バックエンドを使用するようにサーバー プロジェクトを構成します。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Xamarin.iOS アプリのダウンロードと実行
1. [Azure Portal] をブラウザー ウィンドウで開きます。
2. モバイル アプリの [設定] ブレードで、**[開始]**  >  **[Xamarin.iOS]** の順にクリックします。 手順 3 で、まだ選択されていない場合は **[新しいアプリを作成する]** をクリックします。  次に、 **[ダウンロード]** ボタンをクリックします。

      モバイル バックエンドに接続されているクライアント アプリケーションがダウンロードされます。 圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。
3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio または Visual Studio で開きます。

    ![][9]

    ![][8]
4. F5 キーを押してプロジェクトをビルドし、iPhone エミュレーターでアプリを起動します。
5. アプリで、意味のあるテキスト (「"*Xamarin の学習*"」など) を入力し、**+** ボタンをクリックします。

    ![][10]

    要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> モバイル アプリ バックエンドにアクセスして QSTodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。
>
>

## <a name="next-steps"></a>次の手順
* [アプリへのオフライン同期の追加](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [アプリへの認証の追加](app-service-mobile-xamarin-ios-get-started-users.md)
* [Xamarin.Android アプリへのプッシュ通知の追加](app-service-mobile-xamarin-ios-get-started-push.md)
* [Azure Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
