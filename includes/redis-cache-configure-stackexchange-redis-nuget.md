---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 8ebf5ddfa118e0aeadeab0c00a981871a4b5708e
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429997"
---
.NET アプリケーションでは **StackExchange.Redis** キャッシュ クライアントを使用できます。キャッシュ クライアント アプリケーションの構成は、Visual Studio で NuGet パッケージを使用すると簡単です。 

> [!NOTE]
> 詳細については、GitHub の [StackExchange.Redis](http://github.com/StackExchange/StackExchange.Redis) に関するページと [StackExchange.Azure Cache for Redis クライアントのドキュメント](http://github.com/StackExchange/StackExchange.Redis#documentation)を参照してください。
>
>

Visual Studio で StackExchange.Redis NuGet パッケージを使用してクライアント アプリケーションを構成するには、**ソリューション エクスプローラー**でプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 

![Manage NuGet packages](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

検索ボックスに「**StackExchange.Redis**」または「**StackExchange.Redis.StrongName**」と入力し、結果の中から必要なバージョンを選択して、**[インストール]** をクリックします。

> [!NOTE]
> 厳密な名前を持つバージョンの **StackExchange.Redis** クライアント ライブラリを希望する場合は、**[StackExchange.Redis.StrongName]** を選択してください。それ以外の場合は、**[StackExchange.Redis]** を選択します。
>
>

![StackExchange.Redis NuGet package](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

クライアント アプリケーションから StackExchange.Azure Cache for Redis クライアントを使用して Azure Cache for Redis にアクセスするために必要なアセンブリ参照が NuGet パッケージによってダウンロードされ追加されます。

> [!NOTE]
> StackExchange.Redis を使用するようにプロジェクトを構成していた場合、 **NuGet パッケージ マネージャー**から、そのパッケージへの更新プログラムを確認できます。 StackExchange.Redis NuGet パッケージの更新版を確認してインストールするには、**[NuGet パッケージ マネージャー]** ウィンドウで **[更新プログラム]** をクリックします。 StackExchange.Redis NuGet パッケージの更新プログラムがある場合は、新しいバージョンを使用するためにプロジェクトを更新できます。
>
>

このほか、**[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックし、**[パッケージ マネージャー コンソール]** ウィンドウで次のコマンドを実行する方法でも、StackExchange.Redis NuGet パッケージをインストールできます。

```
Install-Package StackExchange.Redis
```
