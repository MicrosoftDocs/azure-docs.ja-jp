---
title: Azure Mobile Apps で使用するユニバーサル Windows プラットフォーム (UWP) を作成する | Microsoft Docs
description: このチュートリアルに従って、Azure モバイル アプリ バックエンドを使用して、C#、Visual Basic、または JavaScript で Universal Windows Platform (UWP) アプリの開発を開始します。
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 2b718a000bb5805cc615a59aebb2d5dcca6906a4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440205"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Azure バックエンドを使用して Windows アプリを作成する

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started) を今すぐチェックしてください。
>

## <a name="overview"></a>概要

このチュートリアルでは、Universal Windows Platform (UWP) アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 詳細については、「 [モバイル アプリとは](app-service-mobile-value-prop.md)」を参照してください。 次に示すのは、完了したアプリの画面キャプチャです。

![Completed desktop app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

UWP アプリの他のすべての Mobile Apps チュートリアルを行う前に、このチュートリアルを完了する必要があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Windows 10。
* Visual Studio Community 2017。
* UWP アプリ開発に関する知識。 UWP アプリをビルドするための[セットアップ](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)方法については、[UWP のドキュメント](https://docs.microsoft.com/windows/uwp/)を参照してください。

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure Mobile App バックエンドの作成

新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>データベース接続を作成し、クライアントとサーバー プロジェクトを構成する
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>クライアント プロジェクトを実行する

1. UWP プロジェクトを開きます。

2. [Azure Portal](https://portal.azure.com/) にアクセスし、作成したモバイル アプリに移動します。 `Overview` ブレードで、モバイル アプリのパブリック エンドポイントである URL を探します。 例 - アプリ名 "test123" のサイト名は https://test123.azurewebsites.net になります。

3. このフォルダー内のファイル `App.xaml.cs` (windows-uwp-cs/ZUMOAPPNAME/) を開きます。 アプリケーション名は `ZUMOAPPNAME` です。

4. `App` クラスで、`ZUMOAPPURL` パラメーターを上のパブリック エンドポイントに置き換えます。

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    →
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. F5 キーを押して、アプリをデプロイおよび実行します。

6. アプリで、 **[Insert a TodoItem (TodoItem の挿入)]** ボックスに意味のあるテキスト (たとえば、「"*チュートリアルの完了*"」) を入力し、 **[保存]** をクリックします。

    ![Windows quickstart complete desktop](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。
