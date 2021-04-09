---
title: App Service プランの管理
description: 作成、移動、拡張、削除など、App Service プランを管理するためのさまざまなタスクを実行する方法について説明します。
keywords: App Service, Azure App Service, スケール, App Service プラン, 変更する, 作成する, 管理する, 管理
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 58ddf60a842a9583eae25a875f55227cee772a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100582534"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Azure で App Service プランを管理する

[Azure App Service プラン](overview-hosting-plans.md)は、App Service アプリの実行に必要なリソースを提供します。 このガイドでは、App Service プランを管理する方法を示します。

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

> [!TIP]
> App Service Environment がある場合は、 「[App Service Environment で App Service プランを作成する](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)」をご覧ください。

空の App Service プランを作成したり、またはアプリ作成の一部としてプランを作成したりすることができます。

1. [Azure Portal](https://portal.azure.com) で、 **[リソースの作成]** を選択します。

   ![Azure portal でリソースを作成します。][createResource] 

1. **[新規]**  >  **[Web アプリ]** または別の種類の App Service アプリを選択します。

   ![Azure Portal でアプリを作成します。][createWebApp] 

2. App Service プランを構成する前に、 **[インスタンスの詳細]** セクションを構成します。 **[発行]** や **[オペレーティング システム]** などの設定により、App Service プランの利用可能な価格レベルを変更できます。 App Service プランを作成する場所は、 **[リージョン]** によって決まります。 
   
3. **[App Service プラン]** セクションで、既存のプランを選択するか、 **[新規作成]** を選択してプランを作成します。

   ![Create an App Service plan.][createASP] 

4. プランを作成するときに、新しいプランの価格レベルを選択できます。 **[SKU とサイズ]** で、 **[サイズの変更]** を選択して、価格レベルを変更します。 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>アプリを別の App Service プランに移動する

別の App Service プランへのアプリの移動は、移動元プランと移動先プランが "_同じリソース グループおよび同じ地理的リージョン_" に存在している場合に限り可能です。

> [!NOTE]
> Azure では、新しい App Service プランはそれぞれ、内部的に Web スペースと呼ばれるデプロイ ユニットにデプロイされます。 各リージョンには多数の Web スペースが存在できますが、アプリは同じ Web スペース内に作成されているプラン間でのみ移動できます。 App Service Environment は分離された Web スペースであるため、アプリは同じ App Service Environment 内のプラン間で移動できますが、異なる App Service Environment 内のプラン間では移動できません。
>
> プランを作成するときに Web スペースを指定することはできませんが、あるプランが既存のプランと確実に同じ Web スペース内に作成されるようにすることは可能です。 簡単に言えば、同じリソース グループとリージョンの組み合わせで作成されるプランはすべて同じ Web スペースにデプロイされます。 たとえば、リソース グループ A とリージョン B 内にプランを作成した場合は、その後でリソース グループ A とリージョン B 内に作成するすべてのプランが同じ Web スペースにデプロイされます。 プランは作成された後に Web スペースを移動できないため、あるプランを別のリソース グループに移動することによって、別のプランと "同じ Web スペース" に移動することはできないことに注意してください。
> 

1. [Azure portal](https://portal.azure.com) で、**App Services** を検索して選択し、移動するアプリを選択します。

2. 左側のメニューから、 **[App Service プランの変更]** を選択します。

3. **[App Service プラン]** ドロップダウンで、このアプリの移動先の既存プランを選択します。 ドロップダウンには、現在の App Service プランと同じリソース グループと地理的リージョン内にあるプランのみが表示されます。 そのようなプランが存在しない場合は、既定でプランを作成できます。 **[新規作成]** を選択することで、手動で新しいプランを作成することもできます。

4. プランを作成する場合は、新しいプランの価格レベルを選択できます。 **[価格レベル]** で、既存のレベルを選択して変更します。 
   
   > [!IMPORTANT]
   > 上位レベルのプランから下位レベルのプランにアプリを移動する (たとえば **D1** から **F1** に移動する) 場合、アプリで、ターゲット プランの特定の機能が失われる可能性があります。 たとえば、アプリで TLS/SSL 証明書を使っている場合は、次のようなエラー メッセージが表示されることがあります。
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. 終わったら、 **[OK]** を選択します。
   
   ![App Service plan selector.][change] 

## <a name="move-an-app-to-a-different-region"></a>アプリを異なるリージョンに移動する

アプリが実行されるリージョンは、アプリを含む App Service プランのリージョンです。 ただし、App Service プランのリージョンを変更することはできません。 アプリを異なるリージョンに移動する必要がある場合の 1 つの方法として、アプリの複製の作成があります。 複製によって、任意のリージョンの新規または既存の App Service プランにアプリをコピーすることができます。

メニューの **[開発ツール]** セクションに **[アプリの複製]** があります。

> [!IMPORTANT]
> 複製にはいくつかの制限があります。 これらの制限については、[Azure App Service アプリの複製](app-service-web-app-cloning.md)に関する記事を参照してください。

## <a name="scale-an-app-service-plan"></a>App Service プランのスケーリング

App Service プランの価格レベルをスケールアップする方法については、「[Azure でのアプリのスケールアップ](manage-scale-up.md)」を参照してください。

アプリのインスタンスの数をスケールアウトする方法については、「[手動または自動によるインスタンス数のスケール変更](../azure-monitor/autoscale/autoscale-get-started.md)」をご覧ください。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service プランを削除する

予期しない課金を避けるため、App Service プランの最後のアプリを削除すると、プランも既定で削除されます。 プランを削除せずに残しておく場合は、課金されないように、プランを **Free** レベルに変更する必要があります。

> [!IMPORTANT]
> App Service プランにアプリが関連付けられていない場合でも、構成済みの VM インスタンスが引き続き確保されるため、料金が発生します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure でのアプリのスケールアップ](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
