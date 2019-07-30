---
title: Xamarin Android アプリに Azure Mobile Apps を使用する
description: 次のチュートリアルに従って、Xamarin Android 開発用の Azure Mobile Apps を使用します
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: eed900ee54f62056eceeb35a43a4ba6526b049ca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447084"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android アプリの作成
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started) を今すぐチェックしてください。
>

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

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Xamarin.Android アプリを実行する
1. Xamarin.Android プロジェクトを開きます。

2. [Azure Portal](https://portal.azure.com/) にアクセスし、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

3. このフォルダー内のファイル `ToDoActivity.cs` (xamarin.android/ZUMOAPPNAME/ToDoActivity.cs) を開きます。 アプリケーション名は `ZUMOAPPNAME` です。

4. `ToDoActivity` クラスで、`ZUMOAPPURL` 変数を上のパブリック エンドポイントに置き換えます。

    `const string applicationURL = @"ZUMOAPPURL";`

    →
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. F5 キーを押して、アプリをデプロイおよび実行します。

6. アプリで、意味のあるテキスト (たとえば、「*チュートリアルの完了*」) を入力し、 **[追加]** ボタンをクリックします。

    ![][10]

    要求のデータは TodoItem テーブルに挿入されます。 テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

   > [!NOTE]
   > モバイル アプリ バックエンドにアクセスしてデータのクエリと挿入を行うコードを確認できます (ToDoActivity.cs C# ファイルにあります)。
   
## <a name="troubleshooting"></a>トラブルシューティング
ソリューションのビルドで問題が発生した場合は、NuGet パッケージ マネージャーを実行し、`Xamarin.Android` サポート パッケージを更新します。 クイック スタート プロジェクトには、必ずしも最新バージョンが含まれていません。

プロジェクトで参照されるすべてのサポート パッケージのバージョンが同じである必要があることに注意してください。 [Azure Mobile Apps の NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)には、Android プラットフォームに関して `Xamarin.Android.Support.CustomTabs` の依存関係があるため、プロジェクトで新しいサポート パッケージを使用する場合は、必要なバージョンを使用してこのパッケージを直接インストールし、競合を回避する必要があります。

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
