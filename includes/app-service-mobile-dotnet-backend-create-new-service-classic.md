---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729409"
---
1. [Azure Portal] にサインインします。
2. **[+ 新規]** > **[Web + モバイル]** > **[モバイル アプリ]** の順に選択し、Mobile Apps バックエンドの名前を入力します。
3. **[リソース グループ]** で、既存のリソース グループを選択するか、新しく作成します (アプリと同じ名前を使用)。 
4. **[App Service プラン]** では、既定のプラン ([Standard レベル](https://azure.microsoft.com/pricing/details/app-service/)) が選択されています。 別のプランを選択することも、[新しいプランを作成](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan)することもできます。 

   App Service プランの設定により、アプリに関連付けられる[場所、機能、コスト、コンピューティング リソース](https://azure.microsoft.com/pricing/details/app-service/)が決まります。 App Services プランの詳細と、さまざまな価格レベルおよび目的の場所で新しいプランを作成する方法については、「[Azure App Service プランの概要](../articles/app-service/overview-hosting-plans.md)」を参照してください。
   
5. **作成**を選択します。 この手順により、Mobile Apps バックエンドが作成されます。 
6. 新しい Mobile Apps バックエンドの **[設定]** ウィンドウで、**[クイック スタート]** > お使いのクライアント アプリ プラットフォーム > **[データベースの接続]** の順に選択します。 
   
   ![データベース接続のための選択](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. **[データ接続の追加]** ウィンドウで、**[SQL Database]** > **[新しいデータベースの作成]** の順に選択します。 データベース名を入力し、価格レベルを選択して、**[サーバー]** を選択します。 この新しいデータベースは再利用できます。 同じ場所に既にデータベースを所有している場合は、代わりに **[既存のデータベースの使用]** を選択することもできます。 別の場所にあるデータベースを使用することはお勧めしません。このようなデータベースを使用すると、帯域幅コストと待機時間が増加します。
   
   ![データベースの選択](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. **[新しいサーバー]** ウィンドウで、**[サーバー名]** ボックスに一意のサーバー名を入力し、ログインとパスワードを指定して、**[Azure サービスにサーバーへのアクセスを許可する]**、**[OK]** の順に選択します。 この手順により、新しいデータベースが作成されます。
9. **[データ接続の追加]** ウィンドウに戻り、**[接続文字列]** を選択し、データベースのログインとパスワードの値を入力して、**[OK]** を選択します。 

   データベースが正常にデプロイされるまで数分待ってから、次の手順に進んでください。

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
