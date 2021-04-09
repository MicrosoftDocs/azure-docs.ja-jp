---
title: チュートリアル:AI Builder を使用してフォーム処理アプリを作成する - Form Recognizer
titleSuffix: Azure Cognitive Services
description: このチュートリアルでは、AI Builder を使用して、フォーム処理アプリケーションを作成し、トレーニングします。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103467121"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>チュートリアル:AI Builder を使用してフォーム処理アプリを作成する

[AI Builder](/ai-builder/overview) は Power Platform の機能であり、プロセスを自動化し、結果を予測して、ビジネスのパフォーマンスを向上させることができます。 AI Builder のフォーム処理を使用すると、フォーム ドキュメントからキーと値のペアおよびテーブル データを識別して抽出する AI モデルを作成できます。

> [!NOTE]
> このプロジェクトは、[Microsoft Learn モジュール](/learn/modules/get-started-with-form-processing/)としても利用できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * フォーム処理 AI モデルを作成する
> * モデルをトレーニングする
> * モデルを発行して Azure Power Apps または Power Automate で使用する

## <a name="prerequisites"></a>前提条件

* データのトレーニングとテストに使用するために、同じ種類の少なくとも 5 つのフォームのセット。 トレーニング データ セットをまとめるためのヒントとオプションについては、[トレーニング データ セットの作成](./build-training-data-set.md)に関するページを参照してください。 このクイックスタートでは、[サンプル データ セット](https://go.microsoft.com/fwlink/?linkid=2128080)の **Train** フォルダーにあるファイルを使用できます。
* Power Apps または Power Automate のライセンスについては、[ライセンス ガイド](https://go.microsoft.com/fwlink/?linkid=2085130)に関するドキュメントを参照してください。 ライセンスには、[Common Data Service](https://powerplatform.microsoft.com/common-data-service/) が含まれている必要があります。
* AI Builder の[アドオンまたは試用版](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409)。


## <a name="create-a-form-processing-project"></a>フォーム処理プロジェクトを作成する

1. [Power Apps](https://make.powerapps.com/) または [Power Automate](https://flow.microsoft.com/signin) に移動し、組織のアカウントでサインインします。
1. 左側のウィンドウで、 **[AI Builder]**  >  **[ビルド]** を選択します。
1. **[フォーム処理]** カードを選択します。
1. モデルの名前を入力します。
1. **［作成］** を選択します

## <a name="upload-and-analyze-documents"></a>ドキュメントをアップロードして分析する

**[ドキュメントの追加]** ページでは、情報を抽出するフォームの種類のモデルをトレーニングするためのサンプル ドキュメントを提供する必要があります。 ドキュメントをアップロードすると、AI Builder によりそれらが分析されて、モデルをトレーニングするのに十分であることが確認されます。

> [!NOTE]
> 現在、AI Builder では、次の種類のフォーム処理入力データはサポートされていません。
>
> - 複雑なテーブル (入れ子になったテーブル、マージされたヘッダーまたはセルなど)
> - チェック ボックスまたはラジオ ボタン
> - 50 ページを超える PDF ドキュメント
> - 入力可能な PDF
>
> 入力ドキュメントの要件の詳細については、「[入力の要件](./overview.md#input-requirements)」を参照してください。

### <a name="upload-your-documents"></a>ドキュメントをアップロードする

1. **[ドキュメントの追加]** を選択し、最低 5 つのドキュメントを選択して、 **[アップロード]** を選択します。
1. アップロードが完了したら、 **[閉じる]** を選択します。
1. 次に、 **[分析]** を選択します。

> [!NOTE] 
> これらのドキュメントをアップロードした後でも、ドキュメントの一部を削除したり、他のドキュメントをアップロードしたりすることができます。

> [!div class="mx-imgBorder"]
> ![[ドキュメントの追加] ボタンが強調表示されているスクリーンショット。](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>ドキュメントを分析する

分析ステップでは、アップロードしたドキュメントが AI Builder によって調べられ、フィールドとテーブルが検出されます。 この操作の完了に要する時間は、提供したドキュメントの数によって異なりますが、ほとんどの場合は数分で完了します。

分析が完了したら、サムネイルを選択してフィールド選択エクスペリエンスを開きます。

> [!IMPORTANT]
> 分析が失敗した場合は、AI Builder でドキュメント内の構造化テキストを検出できなかった可能性があります。 更新したドキュメントが[入力要件](./overview.md#input-requirements)に従っていることを確認します。

## <a name="select-your-form-fields"></a>フォームのフィールドを選択する

フィールド選択ページでは、重要な意味を持つフィールドを選択します。

1. フィールドを選択するには、ドキュメントで検出されたフィールドを示す四角形をクリックするか、クリックしてドラッグすることにより複数のフィールドを選択します。 右側のペインからフィールドを選択することもできます。
1. ニーズに合わせて名前を変更する場合や、抽出されたラベルを正規化する場合は、選択したフィールドの名前をクリックします。

    検出されたフィールドをクリックすると、次の情報が表示されます。

    - **フィールド名**: 検出されたフィールドのラベルの名前。
    - **フィールド値**: 検出されたフィールドの値。

> [!div class="mx-imgBorder"]
> ![タグ付けのページ](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>検出されないフィールドにラベルを付ける

ラベルを付けたいフィールドがモデルによって自動的に検出されなかった場合は、その値を囲むように四角形を描画し、表示されるダイアログにラベル名を入力できます。

## <a name="train-your-model"></a>モデルをトレーニングする

1. **[次へ]** を選択して、選択されたフォームのフィールドを確認します。 すべて問題がなければ、 **[トレーニング]** を選択してモデルをトレーニングします。

    > [!div class="mx-imgBorder"]
    > ![トレーニングのページ](./media/tutorial-ai-builder/summary-train-page.png)
1. トレーニングが完了したら、 **[トレーニングが完了しました]** 画面で **[詳細ページに移動する]** を選択します。
## <a name="quick-test-your-model"></a>モデルのクイック テストを行う

[詳細] ページでは、モデルを発行または使用する前にテストすることができます。

1. [詳細] ページで、 **[クイック テスト]** を選択します。
2. ドキュメントをドラッグ アンド ドロップするか、 **[デバイスからアップロード]** を選択して、テスト ファイルをアップロードすることができます。 クイック テストでは、結果が表示されるまでに数秒しかかかりません。
3. **[やり直す]** を選択して別のテストを実行することができ、終了した場合は **[閉じる]** を選択します。

### <a name="troubleshooting-tips"></a>トラブルシューティングのヒント

特定のフィールドの結果が悪かったり、信頼度が低い場合は、次のヒントを試してください。

- 各フィールドに異なる値が含まれたフォームを使用して再トレーニングします。
- より大きなトレーニング ドキュメントを使用して再トレーニングします。 タグ付けするドキュメントが多いほど、AI Builder によるフィールドの認識方法の学習が向上します。
- 特定のページだけを選択してトレーニングすることで、PDF ファイルを最適化することができます。 ドキュメント内の特定のページを選択するには、 **[印刷]** ** > [Print to PDF]\(PDF に印刷\)** オプションを使用します。

## <a name="publish-your-model"></a>モデルを発行する

モデルに問題がなければ、 **[発行]** を選択して発行します。 発行が完了すると、モデルは **[Published]\(発行済み\)** として昇格され、使用できるようになります。

> [!div class="mx-imgBorder"]
> ![発行モデルのページ](./media/tutorial-ai-builder/model-page.png)

フォーム処理モデルを発行した後は、[Power Apps キャンバス アプリ](/ai-builder/form-processor-component-in-powerapps)または [Power Automate](/ai-builder/form-processing-model-in-flow) でそれを使用できます。

## <a name="next-steps"></a>次のステップ

フォーム処理モデルを使用する方法については、AI Builder のドキュメントを参照してください。

* [Power Apps でフォーム プロセッサ コンポーネントを使用する](/ai-builder/form-processor-component-in-powerapps)
* [Power Automate でフォーム処理モデルを使用する](/ai-builder/form-processing-model-in-flow)