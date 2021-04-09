---
title: 機能フィルターを使用して条件付き機能フラグを有効にする
titleSuffix: Azure App Configuration
description: 機能フィルターを使用して条件付き機能フラグを有効にする方法について説明します。
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602265"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>機能フィルターを使用して条件付き機能フラグを有効にする

機能フラグを使用すると、アプリケーションの機能をアクティブ化または非アクティブ化することができます。 単純な機能フラグは、オンまたはオフのいずれかです。 アプリケーションは常に同じように動作します。 たとえば、機能フラグを使用して新しい機能をロールアウトします。 機能フラグを有効にすると、すべてのユーザーに新しい機能が表示されます。 機能フラグを無効にすると、新しい機能は表示されなくなります。

これに対し、"_条件付き機能フラグ_" を使用すると、機能フラグを動的に有効または無効にすることができます。 機能フラグの条件によっては、アプリケーションの動作が異なる場合があります。 最初に、ユーザーの小さなサブセットに新しい機能を表示するとします。 条件付き機能フラグを使用すると、一部のユーザーに対しては機能フラグを有効にし、他のユーザーに対しては無効にすることができます。 "_機能フィルター_" では、評価のたびに機能フラグの状態が判断されます。

`Microsoft.FeatureManagement` ライブラリには、3 つの機能フィルターがあります。

- `PercentageFilter` では、パーセンテージに基づいて機能フラグが有効にされます。
- `TimeWindowFilter` では、指定した時間帯に機能フラグが有効にされます。
- `TargetingFilter` では、指定したユーザーとグループに対して機能フラグが有効にされます。

また、[Microsoft.FeatureManagement.IFeatureFilter インターフェイス](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)を実装する独自の機能フィルターを作成することもできます。

## <a name="registering-a-feature-filter"></a>機能フィルターの登録

機能フィルターを登録するには、目的の機能フィルターの種類名を指定して `AddFeatureFilter` メソッドを呼び出します。 たとえば、次のコードでは `PercentageFilter` が登録されます。

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

1. **[編集]** 画面で、 **[機能フラグを有効にする]** チェックボックスがまだオンになっていない場合はオンにします。 次に、 **[機能フィルターを使用する]** チェックボックスをオンにし、 **[カスタム]** を選択します。 

1. **[名前]** フィールドで *[Microsoft.Percentage]* を選択します。

    > [!div class="mx-imgBorder"]
    > ![機能フィルターを追加する](./media/feature-flag-add-filter.png)

1. 機能フィルター名の横にあるコンテキスト メニューをクリックします。 **[フィルター パラメーターの編集]** をクリックします。

    > [!div class="mx-imgBorder"]
    > ![機能フィルターのパラメーターを編集する](./media/feature-flags-edit-filter-parameters.png)

1. **[名前]** に「*値*」と入力し、 **[値]** に「50」と入力します。 **[値]** フィールドは、機能フィルターを有効にする要求の割合を示します。

    > [!div class="mx-imgBorder"]
    > ![機能フィルターのパラメーターを設定する](./media/feature-flag-set-filter-parameters.png)

1. **[適用]** をクリックして、 **[機能フラグの編集]** 画面に戻ります。 次に、 **[適用]** を再びクリックして、機能フラグの設定を保存します。

1. **[機能マネージャー]** ページで、機能フラグに *[カスタム]* の **[機能フィルター]** 値が設定されました。 

    > [!div class="mx-imgBorder"]
    > ![機能フィルターの値に "カスタム" と表示されている機能フラグ](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>機能フィルターの動作

この機能フラグの効果を確認するには、アプリケーションを起動し、ブラウザーの **[更新]** ボタンを何回かクリックします。 約 50% の時間は、ツール バーに *Beta* と表示されます。 要求のサブセットに対する *Beta* 機能は `PercentageFilter` によって非アクティブ化されるため、残りの時間は表示されません。 次のビデオでは、この動作を示します。

> [!div class="mx-imgBorder"]
> ![TargetingFilter の動作](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [対象ユーザーに対して機能の段階的なロールアウトを有効にする](./howto-targetingfilter-aspnet-core.md)
