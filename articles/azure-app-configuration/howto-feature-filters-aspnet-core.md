---
title: 機能フィルターを使用してユーザーのサブセットに対して機能を有効にする
titleSuffix: Azure App Configuration
description: 機能フィルターを使用してユーザーのサブセットに対して機能を有効にする方法を説明します
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206758"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>機能フィルターを使用してユーザーのサブセットに対して機能を有効にする

機能フラグを使用すると、アプリケーションの機能をアクティブ化または非アクティブ化することができます。 単純な機能フラグは、オンまたはオフのいずれかです。 アプリケーションは常に同じように動作します。 たとえば、機能フラグを使用して新しい機能をロールアウトします。 機能フラグを有効にすると、すべてのユーザーに新しい機能が表示されます。 機能フラグを無効にすると、新しい機能は表示されなくなります。

これに対し、"_条件付き機能フラグ_" を使用すると、機能フラグを動的に有効または無効にすることができます。 機能フラグの条件によっては、アプリケーションの動作が異なる場合があります。 最初に、ユーザーの小さなサブセットに新しい機能を表示するとします。 条件付き機能フラグを使用すると、一部のユーザーに対しては機能フラグを有効にし、他のユーザーに対しては無効にすることができます。 "_機能フィルター_" では、評価のたびに機能フラグの状態が判断されます。

`Microsoft.FeatureManagement` ライブラリには、次の 2 つの機能フィルターがあります。

- `PercentageFilter` では、パーセンテージに基づいて機能フラグが有効にされます。
- `TimeWindowFilter` では、指定した時間帯に機能フラグが有効にされます。

また、[Microsoft.FeatureManagement.IFeatureFilter インターフェイス](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)を実装する独自の機能フィルターを作成することもできます。

## <a name="registering-a-feature-filter"></a>機能フィルターの登録

機能フィルターを登録するには、機能フィルターの名前を指定して `AddFeatureFilter` メソッドを呼び出します。 たとえば、次のコードでは `PercentageFilter` が登録されます。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Azure App Configuration での機能フィルターの構成

一部の機能フィルターには追加の設定があります。 たとえば、`PercentageFilter` ではパーセンテージに基づいて機能がアクティブ化されます。 使用するパーセンテージを定義する設定があります。

定義されている機能フラグに対するこれらの設定は、Azure App Configuration で構成できます。 たとえば、`PercentageFilter` を使用して、Web アプリに対する要求の 50% について機能フラグを有効にするには、次の手順のようにします。

1. 「[クイックスタート: ASP.NET Core アプリに機能フラグを追加する](./quickstart-feature-flag-aspnet-core.md)」の手順に従って、機能フラグを使用して Web アプリを作成します。

1. Azure portal で、お使いの構成ストアにアクセスし、 **[機能マネージャー]** をクリックします。

1. クイックスタートで作成した *Beta* 機能フラグのコンテキスト メニューをクリックします。 **[編集]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![Beta 機能フラグを編集する](./media/edit-beta-feature-flag.png)

1. **[編集]** 画面で、 **[オン]** ラジオ ボタンがまだ選択されていない場合はオンにします。 次に、 **[フィルターの追加]** ボタンをクリックします。 ( **[オン]** ラジオ ボタンのラベルが **[条件付き]** に変わります)。

1. **[キー]** フィールドに、「*Microsoft.Percentage*」と入力します。

    > [!div class="mx-imgBorder"]
    > ![機能フィルターを追加する](./media/feature-flag-add-filter.png)

1. 機能フィルター キーの横にあるコンテキスト メニューをクリックします。 **[パラメーターの編集]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![機能フィルターのパラメーターを編集する](./media/feature-flag-edit-filter-parameters.png)

1. **[名前]** ヘッダーの下をポイントして、グリッドにテキスト ボックスを表示します。 **[名前]** に「*値*」と入力し、 **[値]** に「50」と入力します。 **[値]** フィールドは、機能フィルターを有効にする要求の割合を示します。

    > [!div class="mx-imgBorder"]
    > ![機能フィルターのパラメーターを設定する](./media/feature-flag-set-filter-parameters.png)

1. **[適用]** をクリックして、 **[機能フラグの編集]** 画面に戻ります。 次に、 **[適用]** を再びクリックして、機能フラグの設定を保存します。

1. 機能フラグの **[状態]** が、 *[条件付き]* と表示されるようになります。 この状態は、機能フィルターによって適用される条件に基づいて、機能フラグが要求ごとに有効または無効になることを示します。

    > [!div class="mx-imgBorder"]
    > ![条件付き機能フラグ](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>機能フィルターの動作

この機能フラグの効果を確認するには、アプリケーションを起動し、ブラウザーの **[更新]** ボタンを何回かクリックします。 約 50% の時間は、ツール バーに *Beta* と表示されます。 要求のサブセットに対する *Beta* 機能は `PercentageFilter` によって非アクティブ化されるため、残りの時間は表示されません。 次のビデオでは、この動作を示します。

> [!div class="mx-imgBorder"]
> ![PercentageFilter の動作](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [機能管理の概要](./concept-feature-management.md)
