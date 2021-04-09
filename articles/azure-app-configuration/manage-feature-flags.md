---
title: チュートリアル:Azure App Configuration を使用して機能フラグを管理する
titleSuffix: Azure App Configuration
description: このチュートリアルでは、Azure App Configuration を使用して、お使いのアプリケーションとは別に機能フラグを管理する方法について説明します。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 0410a1cde12b9ef762d348a286d78b35f7b14bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932304"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>チュートリアル:Azure App Configuration で機能フラグを管理する

すべての機能フラグを Azure App Configuration に保存して、1 つの場所から管理できます。 App Configuration には、機能フラグ専用に設計された、**機能マネージャー** と呼ばれるポータル UI があります。 さらに、App Configuration は .NET Core 機能フラグのデータ スキーマをネイティブにサポートしています。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration で機能フラグを定義および管理する。
> * お使いのアプリケーションから機能フラグにアクセスする。

## <a name="create-feature-flags"></a>機能フラグの作成

Azure portal 内の App Configuration 用の機能マネージャーは、お使いのアプリケーションで使用する機能フラグを作成および管理するための UI を提供します。

新しい機能フラグを追加するには次のようにします。

1. **[機能マネージャー]**  >  **[+追加]** を選択して、機能フラグを追加します。

    ![機能フラグの一覧](./media/azure-app-configuration-feature-flags.png)

1. 機能フラグの一意のキー名を入力します。 コード内のフラグを参照するには、この名前が必要です。

1. 必要な場合は、機能フラグの説明を指定します。

1. 機能フラグの初期状態を設定します。 この状態は、通常、 *[オフ]* または *[オン]* です。 機能フラグにフィルターを追加すると、 *[オン]* 状態は *[条件付き]* に変わります。

    ![機能フラグの作成](./media/azure-app-configuration-feature-flag-create.png)

1. 状態が *[オン]* の場合は、 **[+フィルターの追加]** を選択して、状態を限定する追加条件を指定します。 組み込みまたはカスタムのフィルター キーを入力し、次に **[+パラメーターの追加]** を選択して、1 つ以上のパラメーターをフィルターに関連付けます。 組み込みフィルターには次のものがあります。

    | Key | JSON パラメーター |
    |---|---|
    | Microsoft.Percentage | {"Value":0 - 100 パーセント} |
    | Microsoft.TimeWindow | {"Start":UTC 時間, "End":UTC 時間} |
    | Microsoft.Targeting | { "Audience": ユーザー、グループ、ロールアウトの割合を定義する JSON BLOB。 [こちらの設定ファイル](https://github.com/microsoft/FeatureManagement-Dotnet/blob/master/examples/FeatureFlagDemo/appsettings.json)の `EnabledFor` 要素の下の例を参照してください}

    ![機能フラグのフィルター](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>機能フラグの状態の更新

機能フラグの状態の値を変更するには、次のようにします。

1. **[Feature Manager]\(機能マネージャー\)** を選択します。

1. 変更する機能フラグの右側にある省略記号 (**...**) を選択し、次に **[編集]** を選択します。

1. 機能フラグの新しい状態を設定します。

## <a name="access-feature-flags"></a>機能フラグへのアクセス

機能マネージャーで作成された機能フラグは、通常のキー値として格納および取得されます。 それらは、特別な名前空間プレフィックス `.appconfig.featureflag` の下に保持されます。 基のキー値を表示するには、Configuration Explorer を使用します。 お使いのアプリケーションは、App Configuration の構成プロバイダー、SDK、コマンドライン拡張機能、および REST API を使用してこれらの値を取得できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration を使用して機能フラグとその状態を管理する方法について説明しました。 App Configuration と ASP.NET Core での機能管理サポートの詳細については、次の記事をご覧ください。

* [.NET Core アプリ内で機能フラグを使用する](./use-feature-flags-dotnet-core.md)
