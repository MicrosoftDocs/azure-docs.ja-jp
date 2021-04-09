---
title: 対象ユーザーに対する機能の段階的なロールアウトを有効にする
titleSuffix: Azure App Configuration
description: 対象ユーザーに対する機能の段階的なロールアウトを有効にする方法について説明します。
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929686"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>対象ユーザーに対する機能の段階的なロールアウトを有効にする

機能フラグを使用すると、アプリケーションの機能を動的にアクティブ化または非アクティブ化できます。 機能フィルターにより、評価のたびに機能フラグの状態が確認されます。 `Microsoft.FeatureManagement` ライブラリには `TargetingFilter` が含まれます。これは、指定したユーザーとグループの一覧に対して、または指定した割合のユーザーに対して機能フラグを有効にします。 `TargetingFilter` は "粘着性" です。 つまり、個々のユーザーが機能を受け取ると、以後のすべての要求でそのユーザーにその機能が引き続き表示されます。 `TargetingFilter` を使用すると、デモ中に特定のアカウントに対して機能を有効にしたり、さまざまなグループや "リング" の中のユーザーに対して新しい機能を段階的にロールアウトしたりすることができます。

この記事では、Azure App Configuration で `TargetingFilter` を使用して、ASP.NET Core Web アプリケーションの新しい機能を指定したユーザーとグループにロールアウトする方法について説明します。

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>機能フラグと認証を使用する Web アプリケーションを作成する

ユーザーとグループに基づいて機能をロールアウトするには、ユーザーがサインインできるようにする Web アプリケーションが必要です。

1. 次のコマンドを使用して、ローカル データベースに対して認証を行う Web アプリケーションを作成します。

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. ビルドして実行し、その後、右上隅にある **[登録]** リンクを選択して、新しいユーザー アカウントを作成します。 `test@contoso.com` のメール アドレスを使用します。 **[Register Confirmation]\(登録の確認\)** 画面で、 **[Click here to confirm your account]\(ここをクリックしてアカウントを確認\)** を選択します。

1. 「[クイックスタート: ASP.NET Core アプリに機能フラグを追加する](./quickstart-feature-flag-aspnet-core.md)」の手順に従って、機能フラグを新しい Web アプリケーションに追加します。

1. App Configuration で機能フラグを切り替えます。 この操作によってナビゲーション バーの **[Beta]** 項目の表示が制御されることを確認します。

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>TargetingFilter を使用するように Web アプリケーション コードを更新する

この時点で、機能フラグを使用して、すべてのユーザーに対して `Beta` 機能を有効または無効にすることができます。 一部のユーザーに対して機能フラグを有効にし、他のユーザーに対して無効にするには、`TargetingFilter` を使用するようにコードを更新します。 この例では、サインイン ユーザーのメール アドレスをユーザー ID として使用し、メール アドレスのドメイン名部分をグループとして使用しています。 `TargetingContext` にユーザーとグループを追加します。 `TargetingFilter` は、このコンテキストを使用して、要求ごとに機能フラグの状態を確認します。

1. `Microsoft.FeatureManagement.AspNetCore` パッケージの最新バージョンに更新します。

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. *TestTargetingContextAccessor.cs* ファイルを追加します。

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. *Startup.cs* で、*Microsoft.FeatureManagement.FeatureFilters* 名前空間への参照を追加します。

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. `AddFeatureManagement()` の呼び出しに続き、`TargetingFilter` を登録するように *ConfigureServices* メソッドを更新します。

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. 前の手順で作成した `TestTargetingContextAccessor` をサービス コレクションに追加するように *ConfigureServices* メソッドを更新します。 *TargetingFilter* は、これを使用して、機能フラグが評価されるたびにターゲットのコンテキストを特定します。

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

*ConfigureServices* メソッド全体は次のようになります。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>TargetingFilter を使用するように機能フラグを更新する

1. Azure portal で、App Configuration ストアに移動し、 **[機能マネージャー]** を選択します。

1. クイックスタートで作成した *[Beta]* 機能フラグのコンテキスト メニューを選択します。 **[Edit]\(編集\)** を選択します。

    > [!div class="mx-imgBorder"]
    > ![Beta 機能フラグを編集する](./media/edit-beta-feature-flag.png)

1. **[編集]** 画面で、 **[機能フラグを有効にする]** チェックボックスがまだオンになっていない場合はオンにします。 次に、 **[機能フィルターを使用する]** チェックボックスをオンします。

1. **[ターゲット]** ラジオ ボタンを選択します。

1. 次のオプションを選択します。

    - **既定のパーセンテージ**: 0
    - **グループ**: **[名前]** に「_contoso.com_」と入力し、 **[パーセンテージ]** に「_50_」と入力します。
    - **ユーザー**: `test@contoso.com`

    機能フィルター画面はこのようになります。

    > [!div class="mx-imgBorder"]
    > ![条件付き機能フラグ](./media/feature-flag-filter-enabled.png)

    これらの設定により、次の動作が発生します。

    - 機能フラグは、ユーザー `test@contoso.com` に対して常に有効になります。これは、`test@contoso.com` が _[ユーザー]_ セクションにリストされているためです。
    - 機能フラグは、_contoso.com_ グループ内の他のユーザーの 50% に対して有効になります。これは、_contoso.com_ が _[グループ]_ セクションにリストされており、 _[パーセンテージ]_ が _50_ に設定されているためです。
    - 機能は、他のすべてのユーザーに対して常に無効になります。これは、 _[既定のパーセンテージ]_ が _0_ に設定されているためです。

1. **[適用]** を選択してこれらの設定を保存し、 **[機能マネージャー]** 画面に戻ります。

1. これで、機能フラグの **[機能フィルター]** が *[ターゲット]* として表示されるようになりました。 この状態は、 *[ターゲット]* 機能フィルターによって適用される条件に基づいて、機能フラグが要求ごとに有効または無効になることを示します。

## <a name="targetingfilter-in-action"></a>TargetingFilter の動作

この機能フラグの効果を確認するには、アプリケーションをビルドして実行します。 _[既定のパーセンテージ]_ オプションが 0 に設定されているため、初期状態では *[Beta]* 項目はツールバーに表示されません。

次に、登録時に設定したパスワードを使用して `test@contoso.com` としてサインインします。 `test@contoso.com` がターゲット ユーザーとして指定されているため、 *[Beta]* 項目がツールバーに表示されるようになりました。

次のビデオでは、この動作を示します。

> [!div class="mx-imgBorder"]
> ![TargetingFilter の動作](./media/feature-flags-targetingfilter.gif)

`@contoso.com` のメール アドレスを使用して追加のユーザーを作成し、グループ設定の動作を確認できます。 これらのユーザーの 50% に *[Beta]* 項目が表示されます。 他の 50% には *[Beta]* 項目が表示されません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [機能管理の概要](./concept-feature-management.md)