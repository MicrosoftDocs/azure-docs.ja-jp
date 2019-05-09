---
title: Azure Mobile Apps で使用するユニバーサル Windows プラットフォーム (UWP) を作成する | Microsoft Docs
description: このチュートリアルに従って、Azure モバイル アプリ バックエンドを使用して、C#、Visual Basic、または JavaScript で Universal Windows Platform (UWP) アプリの開発を開始します。
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 289d50fc9d71ee5ea8960fd077e91ef6f68027cb
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771312"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Azure バックエンドを使用して Windows アプリを作成する

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要

このチュートリアルでは、Universal Windows Platform (UWP) アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 詳細については、「 [モバイル アプリとは](app-service-mobile-value-prop.md)」を参照してください。 次に示すのは、完了したアプリの画面キャプチャです。

![Completed desktop app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

UWP アプリの他のすべての Mobile Apps チュートリアルを行う前に、このチュートリアルを完了する必要があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Windows 10。
* [Visual Studio Community 2017]。
* UWP アプリ開発に関する知識。 UWP アプリをビルドするための[セットアップ](https://docs.microsoft.com/windows/uwp/get-started/get-set-up)方法については、[UWP のドキュメント](https://docs.microsoft.com/windows/uwp/)を参照してください。

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure Mobile App バックエンドの作成

新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。 次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>クライアント プロジェクトのダウンロードおよび実行

モバイル アプリ バックエンドを構成した後は、新しいクライアント アプリを作成したり、既存のアプリを Azure に接続するように変更したりできます。 このセクションでは、モバイル アプリ バックエンドに接続するようにカスタマイズされた UWP サンプル アプリ プロジェクトをダウンロードします。

1. モバイル アプリ バックエンドの **[クイック スタート]** ブレードに戻り、**[新しいアプリの作成]**  >  **[ダウンロード]** の順にクリックして、圧縮されたプロジェクト ファイルをローカル コンピューターに展開します。

    ![Download Windows quickstart project](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

2. UWP プロジェクトを開き、F5 キーを押してアプリをデプロイ、実行します。
3. アプリで、**[Insert a TodoItem (TodoItem の挿入)]** ボックスに意味のあるテキスト (たとえば、「"*チュートリアルの完了*"」) を入力し、**[保存]** をクリックします。

    ![Windows quickstart complete desktop](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。

> [!TIP]
> .NET バックエンドを使用している場合、サーバー プロジェクトと同じソリューションに UWP アプリ プロジェクトを追加できます。 これにより、同じ Visual Studio ソリューションでアプリとバックエンドの両方をより簡単にデバッグおよびテストできるようになります。 UWP アプリ プロジェクトをバックエンド ソリューションに追加するには、Visual Studio 2017 を使用する必要があります。

## <a name="next-steps"></a>次の手順

* [アプリへの認証の追加](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリへのプッシュ通知の追加](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  モバイル アプリ バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://go.microsoft.com/fwLink/p/?LinkID=534203
