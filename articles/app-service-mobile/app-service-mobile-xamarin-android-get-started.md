---
title: Xamarin Android アプリに Azure Mobile Apps を使用する
description: 次のチュートリアルに従って、Xamarin Android 開発用の Azure Mobile Apps を使用します
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 29efa963a254913e3d4744ade1d161c5c8ce42e4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893352"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Xamarin Android アプリケーションにクラウドベースのバックエンド サービスを追加する方法を示します。 詳細については、「 [モバイル アプリとは](app-service-mobile-value-prop.md)」を参照してください。

完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

Xamarin Android アプリケーションの他のすべての Mobile Apps のチュートリアルを行う前に、このチュートリアルを完了してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手してください。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Visual Studio と Xamarin。 手順については、「 [セットアップとインストール](/visualstudio/cross-platform/setup-and-install) 」をご覧ください。

## <a name="create-an-azure-mobile-app-backend"></a>Azure モバイル アプリ バックエンドの作成
モバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。 次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Xamarin.Android アプリのダウンロードと実行
1. **[Download and run your Xamarin.Android project (Xamarin.Android プロジェクトをダウンロードして実行する)]** の **[ダウンロード]** ボタンをクリックします。

      圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。
2. **F5** キーを押してプロジェクトをビルドし、アプリケーションを開始します。
3. アプリで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、**[追加]** ボタンをクリックします。

    ![][10]

    要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

   > [!NOTE]
   > モバイル アプリ バックエンドにアクセスしてデータのクエリと挿入を行うコードを確認できます (ToDoActivity.cs C# ファイルにあります)。
   >
   >

## <a name="troubleshooting"></a>トラブルシューティング
ソリューションのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、`Xamarin.Android` サポート パッケージを更新します。 クイック スタート プロジェクトには、必ずしも最新バージョンが含まれていません。

プロジェクトで参照されるすべてのサポート パッケージのバージョンが同じである必要があることに注意してください。 [Azure Mobile Apps の NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)には、Android プラットフォームに関して `Xamarin.Android.Support.CustomTabs` の依存関係があるため、プロジェクトで新しいサポート パッケージを使用する場合は、必要なバージョンを使用してこのパッケージを直接インストールし、競合を回避する必要があります。

## <a name="next-steps"></a>次の手順
* [アプリへのオフライン同期の追加](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [アプリへの認証の追加](app-service-mobile-xamarin-android-get-started-users.md)
* [Xamarin.Android アプリへのプッシュ通知の追加](app-service-mobile-xamarin-android-get-started-push.md)
* [Azure Mobile Apps 用の管理されたクライアントの使用方法](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
