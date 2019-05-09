---
title: PowerApps から関数を呼び出す | Microsoft Docs
description: カスタム コネクタを作成し、そのコネクタを使用して関数を呼び出します。
services: functions
keywords: クラウド アプリ, クラウド サービス, PowerApps, ビジネス プロセス, ビジネス アプリケーション
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: afb97c1c14422f15a3fc2c00b2101c4a36675556
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002485"
---
# <a name="call-a-function-from-powerapps"></a>PowerApps から関数を呼び出す
[PowerApps](https://powerapps.microsoft.com) プラットフォームは、ビジネス エキスパート向けに設計されており、従来のアプリケーション コードを使用せずにアプリを作成することができます。 プロフェッショナルの開発者は Azure Functions を使用して PowerApps の機能を拡張できます。PowerApps アプリ ビルダーが技術詳細を把握している必要はありません。

このトピックでは、風力タービンのメンテナンス シナリオに基づいて、アプリを作成します。 ここで説明するのは、「[関数の OpenAPI 定義の作成](functions-openapi-definition.md)」で定義した関数を呼び出す方法です。 この関数によって、風力タービンの応急処理がコスト効率に優れているかどうかを確認します。

![PowerApps で完成したアプリ](media/functions-powerapps-scenario/finished-app.png)

Microsoft Flow から同じ関数を呼び出す方法の詳細については、「[Microsoft Flow から関数を呼び出す](functions-flow-scenario.md)」をご覧ください。

このトピックでは、次の方法について説明します。

> [!div class="checklist"]
> * Excel でサンプル データを準備する。
> * API 定義をエクスポートする。
> * 文字列を API に追加する。
> * アプリを作成し、データ ソースを追加する。
> * アプリでデータを表示するコントロールを追加する。
> * 関数を呼び出すコントロールを追加し、データを表示する。
> * アプリを実行して、修復がコスト効率に優れているかどうかを確認する。

> [!IMPORTANT]
> OpenAPI 機能は現在プレビュー段階であり、Azure Functions Runtime のバージョン 1.x でのみ使用できます。

## <a name="prerequisites"></a>前提条件

+ Azure アカウントと同じサインイン資格情報を使用しているアクティブな [PowerApps アカウント](https://docs.microsoft.com/powerapps/maker/signup-for-powerapps)。 
+ Excel と、アプリのデータ ソースとして使用する [Excel サンプル ファイル](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx)。
+ 「[関数の OpenAPI 定義の作成](functions-openapi-definition.md)」を完了している。

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>文字列を API に追加する
カスタム API (カスタム コネクタとも呼ばれます) は PowerApps で使用できますが、アプリで使用するには、その API に接続しておく必要があります。

1. [web.powerapps.com](https://web.powerapps.com) で、**[接続]** をクリックします。

    ![PowerApps 接続](media/functions-powerapps-scenario/powerapps-connections.png)

1. **[新しい接続]** をクリックし、下へスクロールして "**タービン修復**" コネクタを選択し、クリックします。

    ![新しい接続](media/functions-powerapps-scenario/new-connection.png)

1. API キーを入力し、**[作成]** をクリックします。

    ![接続を作成する](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> 他のユーザーとアプリを共有する場合は、アプリを操作または使用する各ユーザーが API キーを入力して、API に接続する必要があります。 この動作は将来変更される可能性があります。このトピックは、その変更に合わせて更新されます。

## <a name="create-an-app-and-add-data-sources"></a>アプリを作成し、データ ソースを追加する
これで PowerApps でアプリを作成し、Excel データとカスタム API を、アプリのデータ ソースとして追加することができます。

1. [web.powerapps.com](https://web.powerapps.com) で、**[空白から開始]** > ![電話アプリ アイコン](media/functions-powerapps-scenario/icon-phone-app.png) (電話) > **[このアプリの作成]** の順に選択します。

    ![[空白から開始] - 電話アプリ](media/functions-powerapps-scenario/create-phone-app.png)

    Web 用 PowerApps Studio でアプリが開きます。 次のイメージは、PowerApps Studio のさまざまな部分を示しています。

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) 左側のナビゲーション バー**。各画面のコントロールがすべて階層表示されます

    **(B) 中央のウィンドウ**。作業画面です

    **(C) 右側のウィンドウ**。レイアウト、データ ソースなどのオプションを設定します

    **(D) プロパティ** ドロップダウン リスト。数式を適用するプロパティを選択します

    **(E) 数式バー**。(Excel などと同じように) アプリの動作を定義する数式を追加します
    
    **(F) リボン**。コントロールを追加し、デザイン要素をカスタマイズします

1. Excel ファイルをデータ ソースとして追加します。

    インポートするデータは次のようになります。

    ![インポートする Excel データ](media/functions-powerapps-scenario/excel-table.png)

    1. アプリ キャンバスで、**[データに接続]** を選択します。

    1. **[データ]** パネルで、**[静的データをアプリに追加します]** をクリックします。

        ![データ ソースを追加する](media/functions-powerapps-scenario/add-static-data.png)

        通常は外部ソースのデータに対して読み取りおよび書き込みを行いますが、これはサンプルなので、Excel データを静的データとして追加します。

    1. 保存した Excel ファイルに移動し、"**タービン**" テーブルを選択して、**[接続]** をクリックします。

        ![データ ソースを追加する](media/functions-powerapps-scenario/choose-table.png)


1. カスタム API をデータ ソースとして追加します。

    1. **[データ]** パネルで、**[データ ソースの追加]** をクリックします。

    1. **[Turbine Repair]\(タービン修復\)** をクリックします。

        ![タービン修復コネクタ](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>アプリでデータを表示するコントロールを追加する
データ ソースがアプリで使用できるようになりました。次は、タービンのデータを表示できる画面をアプリに追加します。

1. **[ホーム]** タブで、**[新しい画面]** > **[リスト画面]** をクリックします。

    ![リスト画面](media/functions-powerapps-scenario/list-screen.png)

    PowerApps によって、項目を表示するための "*ギャラリー*" を含む画面と、検索、並べ替え、およびフィルター処理を実行するためのその他のコントロールが追加されます。

1. タイトル バーを `Turbine Repair` に変更し、ギャラリーのサイズを変更して、その他のコントロールをその下に表示できるようにします。

    ![タイトルとギャラリーのサイズを変更する](media/functions-powerapps-scenario/gallery-title.png)

1. ギャラリーを選択して、右ウィンドウの **[プロパティ]** で **CustomGallerySample** をクリックします。

    ![データ ソースを変更する](media/functions-powerapps-scenario/change-data-source.png)

1. **[データ]** パネルで、一覧から **Turbines** を選択します。

    ![データ ソースの選択](media/functions-powerapps-scenario/select-data-source.png)

    データ セットにはイメージが含まれていないため、次は、データに合わせてレイアウトを変更します。 

1. 引き続き **[データ]** パネルで、**[レイアウト]** を **[タイトル、サブタイトル、本文]** に変更します。

    ![ギャラリーのレイアウトを変更する](media/functions-powerapps-scenario/change-layout.png)

1. **[データ]** パネルでの最後の手順として、ギャラリーに表示されるフィールドを変更します。

    ![ギャラリーのフィールドを変更する](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** = Title 

1. ギャラリーが選択されている状態で、**TemplateFill** プロパティを数式 `If(ThisItem.IsSelected, Orange, White)` に設定します。

    ![テンプレート入力数式](media/functions-powerapps-scenario/formula-fill.png)

    どのギャラリー アイテムが選択されているかが確認しやすくなりました。

    ![選択されている項目](media/functions-powerapps-scenario/selected-item.png)

1. アプリの元の画面は必要ありません。 左側のウィンドウで、**[Screen1]** をポイントし、**[...]**、**[削除]** の順にクリックします。

    ![画面を削除する](media/functions-powerapps-scenario/delete-screen.png)

1. **[ファイル]** をクリックし、アプリに名前を付けます。 左側のメニューで **[保存]** をクリックし、右下隅にある **[保存]** をクリックします。

運用アプリでは、通常、他にもたくさんの書式設定を行いますが、ここで、このシナリオの重要な操作 "関数の呼び出し" に進みます。

## <a name="add-controls-to-call-the-function-and-display-data"></a>関数を呼び出すコントロールを追加し、データを表示する
アプリでタービンごとに概要データが表示されるようになりました。次は、作成した関数を呼び出すコントロールを追加し、返されたデータを表示します。 OpenAPI 定義で指定した方法に基づいて、関数にアクセスします。ここでは `TurbineRepair.CalculateCosts()` です。

1. リボンの **[挿入]** タブで、**[ボタン]** をクリックします。 次に、同じタブをクリックし、**[ラベル]** をクリックします

    ![ボタンとラベルを挿入する](media/functions-powerapps-scenario/insert-controls.png)

1. ギャラリーの下にボタンとラベルをドラッグし、ラベルのサイズを変更します。 

1. ボタンのテキストを選択し、`Calculate costs` に変更します。 アプリは次のイメージのようになります。

    ![ボタンが含まれるアプリ](media/functions-powerapps-scenario/move-button-label.png)

1. ボタンを選択し、ボタンの **OnSelect** プロパティで次の数式を入力します。

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    この数式は、ボタンがクリックされたときに実行され、選択したギャラリー アイテムの **ServiceRequired** 値が `Yes` の場合に次の処理を実行します。

    + *collection* `DetermineRepair` をクリアして、以前の呼び出しからデータを削除する。 コレクションは表形式の変数です。

    + `TurbineRepair.CalculateCosts()` 関数の呼び出しによって返されたデータをコレクションに割り当てる。 
    
        関数に渡される値は、ギャラリーで選択した項目の **EstimatedEffort** フィールドおよび **MaxOutput** フィールドの値です。 このフィールドはギャラリーに表示されませんが、数式で使用できます。

1. ラベルを選択し、ラベルの **Text** プロパティで次の数式を入力します。

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    この数式では、`First()` 関数を使用して `DetermineRepair` コレクションの先頭行 (のみ) にアクセスします。 その後、この数式は `message`、`costToFix`、`revenueOpportunity` の 3 つの値を表示します。 これらの値は、アプリが最初に実行されるまで空です。

    完成アプリは、次のイメージのようになります。

    ![実行前の完成アプリ](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>アプリの実行
アプリが完成しました。 次はこれを実行し、関数によってアクションが呼び出されるかどうかを確認します。

1. PowerApps Studio の右上にある実行ボタンをクリックします:  ![アプリケーションの実行ボタン](media/functions-powerapps-scenario/f5-arrow-sm.png)。

1. **ServiceRequired** の値が `Yes` のタービンを選択し、**[Calculate costs]\(コストを計算\)** ボタンをクリックします。 次のイメージような結果が表示されます。

    ![PowerApps で完成したアプリ](media/functions-powerapps-scenario/finished-app.png)

1. 他のタービンで、関数によって返される内容を確認します。

## <a name="next-steps"></a>次の手順
このトピックで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Excel でサンプル データを準備する。
> * API 定義をエクスポートする。
> * 文字列を API に追加する。
> * アプリを作成し、データ ソースを追加する。
> * アプリでデータを表示するコントロールを追加する。
> * 関数を呼び出すコントロールを追加し、データを表示する
> * アプリを実行して、修復がコスト効率に優れているかどうかを確認する。

PowerApps の詳細については、「[PowerApps の概要](https://powerapps.microsoft.com/tutorials/getting-started/)」を参照してください。

Azure Functions を使用する他の興味深いシナリオについては、「[Microsoft Flow から関数を呼び出す](functions-flow-scenario.md)」および「[Azure Logic Apps と統合される関数を作成する](functions-twitter-email.md)」をご覧ください。
