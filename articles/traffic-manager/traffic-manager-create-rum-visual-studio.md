---
title: Visual Studio Mobile Center での Real User Measurements - Azure Traffic Manager
description: Real User Measurements を Traffic Manager に送信するように、Visual Studio Mobile Center を使用して開発されたモバイル アプリケーションを設定します
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: devx-track-js
ms.openlocfilehash: f9e8cdd3eb5c9f441444683fb5efaccc880b2757
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184613"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Visual Studio Mobile Center で Real User Measurements を Traffic Manager に送信する方法

Real User Measurements を Traffic Manager に送信するように、Visual Studio Mobile Center を使用して開発されたモバイル アプリケーションを設定するには、次の手順に従います。

>[!NOTE]
> 現在、Traffic Manager への Real User Measurements の送信は Android でのみサポートされています。

Real User Measurements を構成するには、キーを取得し、RUM パッケージでアプリをインストルメント化する必要があります。

## <a name="step-1-obtain-a-key"></a>手順 1: キーを取得する
    
測定値を取得し、クライアント アプリケーションから Traffic Manager に送信されると、その測定値は、Real User Measurements (RUM) キーと呼ばれる一意の文字列を使用して、サービスによって識別されます。 RUM キーを取得するには、Azure Portal、REST API、または PowerShell/CLI インターフェイスを使用します。

Azure Portal を使用して RUM キーを取得するには、次の手順を実行します。
1. ブラウザーから Azure Portal にサインインします。 まだアカウントを持っていない場合は、1 か月間の無料試用版にサインアップできます。
2. ポータルの検索バーで、変更の対象となる Traffic Manager プロファイル名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. Traffic Manager プロファイル ページで、**[設定]** で **[Real User Measurements]** をクリックします。
4. **[キーの生成]** をクリックして、新しい RUM キーを作成します。
        
   ![Real User Measurements キーの生成](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **図 1: Real User Measurements キーの生成**

5. ページには、生成された RUM キーと、HTML ページに埋め込む必要がある JavaScript コード スニペットが表示されます。
 
   ![Real User Measurements キーの JavaScript コード](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **図 2: Real User Measurements キーと測定 JavaScript**
 
6. **[コピー]** をクリックして、RUM キーをコピーします。 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>手順 2: Mobile Center SDK の RUM パッケージでアプリをインストルメント化する

Visual Studio Mobile Center を初めてご利用になる場合は、[Web サイト](https://mobile.azure.com)を参照してください。 SDK 統合手順の詳細については、「[Getting Started with the Android SDK (Android SDK の概要)](/mobile-center/sdk/getting-started/Android)」を参照してください。

Real User Measurements を使用するには、次の手順を実行します。

1.  SDK をプロジェクトに追加します

    ATM RUM SDK のプレビュー期間中は、パッケージ リポジトリを明示的に参照する必要があります。

    **app/build.gradle** ファイルで、次の行を追加します。

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    **app/build.gradle** ファイルで、次の行を追加します。

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. SDK を開始します

    アプリのメイン アクティビティ クラスを開いて、次の import ステートメントを追加します。

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    同じファイルで `onCreate` コールバックを検索し、次のコードを追加します。

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>次のステップ
- [Real User Measurements](traffic-manager-rum-overview.md) について確認する
- [Traffic Manager のしくみ](traffic-manager-overview.md)
- [Mobile Center](/mobile-center/) について確認する
- Mobile Center に[サインアップ](https://mobile.azure.com)する
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)