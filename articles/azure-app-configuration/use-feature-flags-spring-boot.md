---
title: Spring Boot アプリで機能フラグを使用するためのチュートリアル - Azure App Configuration | Microsoft Docs
description: このチュートリアルでは、Spring Boot アプリで機能フラグを実装する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 0dcf85add4e1c9f1d701c4ce4a122c8d6b31382a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077832"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>チュートリアル: Spring Boot アプリ内で機能フラグを使用する

Spring Boot Core 機能管理ライブラリでは、Spring Boot アプリケーションで機能フラグを実装するためのサポートが提供されます。 これらのライブラリにより、機能フラグを宣言的にコードに追加できます。

機能管理ライブラリは、バックグラウンドで機能フラグのライフサイクルも管理します。 たとえば、ライブラリは、フラグの状態を更新およびキャッシュしたり、要求の呼び出し中にフラグの状態が不変であることを保証したりします。 さらに、Spring Boot ライブラリには、MVC コントローラーのアクション、ルート、およびミドルウェアを含む統合が用意されています。

[Spring Boot アプリに機能フラグを追加するクイックスタート](./quickstart-feature-flag-spring-boot.md)では、Spring Boot アプリケーションに機能フラグを追加する複数の方法が示されています。 このチュートリアルでは、それらの方法についてより詳細に説明します。

このチュートリアルでは、次の内容を学習します。

> [!div class="checklist"]
> * アプリケーションの主要部分に機能フラグを追加して、機能の可用性を制御します。
> * 機能フラグを管理するのに App Configuration を使用する場合は、それと統合します。

## <a name="set-up-feature-management"></a>機能管理を設定する

Spring Boot 機能マネージャー `FeatureManager` は、フレームワークのネイティブ構成システムから機能フラグを取得します。 その結果、Spring Boot がサポートする任意の構成ソース (ローカルの *bootstrap.yml* ファイルや環境変数など) を使用して、アプリケーションの機能フラグを定義できます。 `FeatureManager` は、依存関係の挿入を拠り所としています。 機能管理サービスは、標準の規則を使用して登録できます。

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

機能フラグはアプリケーションの外部で保持し、別々に管理することをお勧めします。 そうすることで、フラグの状態をいつでも変更して、アプリケーションにその変更をすぐに反映させることができます。 App Configuration は、専用のポータル UI を介してすべての機能フラグを一元的に整理および制御するための場所を提供します。 また、App Configuration は、その Spring Boot クライアント ライブラリを介してお使いのアプリケーションに直接フラグを配信します。

App Configuration に Spring Boot アプリケーションを接続する最も簡単な方法は、構成プロバイダーを使用することです。

### <a name="spring-cloud-11x"></a>Spring Cloud 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Spring Cloud 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>機能フラグの宣言

各機能フラグには 2 つの部分があります。1 つは名前で、もう 1 つは、機能の状態が "*オン*" である (つまり、その値が `True` である) かどうかの評価に使用される 1 つ以上のフィルターの一覧です。 フィルターは、機能をオンにするタイミングに関するユース ケースを定義します。

機能フラグに複数のフィルターがある場合は、フィルターのいずれかが、機能を有効にする必要があると判断するまで、フィルター一覧が順番に走査されます。 その時点で、機能フラグが "*オン*" になり、残りのフィルターの結果はすべてスキップされます。 どのフィルターも、機能を有効にする必要があると示していない場合、機能フラグは "*オフ*" になります。

機能マネージャーは、*application.yml* を機能フラグの構成ソースとしてサポートしています。 次の例は、YAML ファイルでの機能フラグの設定方法を示しています。

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

慣例により、この YML ドキュメントの `feature-management` セクションが機能フラグの設定に使用されます。 前述の例は、`EnabledFor` プロパティにフィルターが定義されている 3 つの機能フラグを示しています。

* `feature-a` は "*オン*" です。
* `feature-b` は "*オフ*" です。
* `feature-c` は、`parameters` プロパティとともに `Percentage` という名前のフィルターを指定します。 `Percentage` は、構成可能なフィルターです。 この例で、`Percentage` は、`feature-c` フラグが "*オン*" になる確率を 50% に指定しています。

## <a name="feature-flag-checks"></a>機能フラグのチェック

機能管理の基本的なパターンでは、最初に機能フラグが "*オン*" に設定されているかどうかをチェックします。 そうなっている場合は、機能マネージャーが、機能に含まれているアクションを実行します。 次に例を示します。

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>依存関係の挿入

Spring Boot では、依存関係の挿入を介して機能マネージャー `FeatureManager` にアクセスできます。

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>コントローラー アクション

MVC コントローラーでは、`@FeatureGate` 属性を使用して、特定のアクションを有効にするかどうかを制御できます。 次の `Index` アクションを実行するには、`feature-a` が "*オン*" になっている必要があります。

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

制御する機能フラグが "*オフ*" であるために MVC コントローラーまたはアクションがブロックされている場合、登録されている `IDisabledFeaturesHandler` インターフェイスが呼び出されます。 既定の `IDisabledFeaturesHandler` インターフェイスは、応答本文なしで 404 状態コードをクライアントに返します。

## <a name="mvc-filters"></a>MVC フィルター

MVC フィルターは、機能フラグの状態に基づいてアクティブになるように設定できます。 以下のコードは、`FeatureFlagFilter` という名前の MVC フィルターを追加します。 このフィルターは、`feature-a` が有効になっている場合のみ、MVC パイプライン内でトリガーされます。

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>ルート

機能フラグを使用して、ルートをリダイレクトすることができます。 次のコードでは、`feature-a` が有効になっている場合にユーザーがリダイレクトされます。

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Spring Boot アプリケーションで `spring-cloud-azure-feature-management-web` ライブラリを使用して機能フラグを実装する方法について説明しました。 Spring Boot と App Configuration での機能管理サポートの詳細については、次のリソースを参照してください。

* [Spring Boot の機能フラグのサンプル コード](./quickstart-feature-flag-spring-boot.md)
* [機能フラグを管理する](./manage-feature-flags.md)