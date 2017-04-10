---
title: "Mobile Apps で使用する Universal Windows Platform (UWP) を作成する | Microsoft Doc"
description: "このチュートリアルに従って、Azure モバイル アプリ バックエンドを使用して、C#、Visual Basic、または JavaScript で Universal Windows Platform (UWP) アプリの開発を開始します。"
services: app-service\mobile
documentationcenter: windows
author: adrianhall
manager: adrianha
editor: 
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 1a031c4858bcbc75ee807ba520e1b22c89471498
ms.lasthandoff: 01/20/2017


---
# <a name="create-a-windows-app"></a>Create a Windows app (Windows アプリの作成)
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Universal Windows Platform (UWP) アプリにクラウドベースのバックエンド サービスを追加する方法を示します。 詳細については、「 [モバイル アプリとは](app-service-mobile-value-prop.md)」を参照してください。 次に示すのは、完了したアプリの画面キャプチャです。

![Completed desktop app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)   
デスクトップで実行されています。 

![Completed phone app](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)  
スマート フォンで実行されています。

UWP アプリの他のすべての Mobile Apps チュートリアルを行う前に、このチュートリアルを完了する必要があります。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大&10; 件の無料モバイル アプリを入手できます。 このアプリは評価終了後も使用できます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* [Visual Studio Community 2015] 以降のバージョン。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service を開始する場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/mobile/)に関するページにアクセスしてください。 有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="create-a-new-azure-mobile-app-backend"></a>新しい Azure Mobile App バックエンドの作成
新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。 次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

## <a name="configure-the-server-project"></a>サーバー プロジェクトの構成
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>クライアント プロジェクトのダウンロードおよび実行
モバイル アプリ バックエンドを構成した後は、新しいクライアント アプリを作成したり、既存のアプリを Azure に接続するように変更したりできます。 このセクションでは、モバイル アプリ バックエンドに接続するようにカスタマイズされた UWP アプリ テンプレート プロジェクトをダウンロードします。

1. モバイル アプリ バックエンドの **[クイック スタート]** ブレードに戻り、**[新しいアプリの作成]**  >  **[ダウンロード]** の順にクリックして、圧縮されたプロジェクト ファイルをローカル コンピューターに展開します。
   
    ![Download Windows quickstart project](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)
2. (省略可能) サーバー プロジェクトと同じソリューションに UWP アプリ プロジェクトを追加します。 これにより、必要であれば、同じ Visual Studio ソリューションでアプリとバックエンドの両方をより簡単にデバッグおよびテストできるようになります。 UWP アプリ プロジェクトをソリューションに追加するには、Visual Studio 2015 以降のバージョンを使用している必要があります。
3. UWP アプリをスタートアップ プロジェクトにして F5 キーを押し、アプリをデプロイおよび実行します。
4. アプリで、**[Insert a TodoItem (TodoItem の挿入)]** ボックスに意味のあるテキスト (たとえば、「"*チュートリアルの完了*"」) を入力し、**[保存]** をクリックします。
   
    ![Windows quickstart complete desktop](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)
   
    これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。
5. (省略可能) アプリを停止し、別のデバイスまたはモバイル エミュレーターで再開します。
   
    ![Windows quickstart complete phone](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)
   
    UWP アプリの開始後、前の手順で保存したデータが Azure から読み込まれる点に注目してください。 

## <a name="next-steps"></a>次のステップ
* [アプリへの認証の追加](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリへのプッシュ通知の追加](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  アプリにプッシュ通知のサポートを追加して、Azure Notification Hubs を使ってプッシュ通知を送信するようにモバイル アプリ バックエンドを構成する方法について説明します。
* [アプリのオフライン同期の有効化](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  モバイル アプリ バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203

