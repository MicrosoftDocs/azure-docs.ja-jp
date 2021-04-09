---
title: 参照データ セットを環境に追加する方法 - Azure Time Series Insights | Microsoft Docs
description: この記事では、参照データ セットを追加して、Azure Time Series Insights 環境内のデータを増幅する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001832"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Azure portal を使用して Azure Time Series Insights Gen1 環境の参照データ セットを作成する

> [!CAUTION]
> これは Gen1 の記事です。

この記事では、Azure Time Series Insights 環境に参照データ セットを追加する方法について説明します。 参照データは、ソース データに結合することにより値を増幅するのに役立ちます。

参照データ セットは、イベント ソースからのイベントを増幅する項目の集まりです。 イベント ソースから受信した各イベントは、Azure Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の対応するデータ行と結合されます。 こうして増幅されたイベントをクエリで利用することができます。 この結合操作は、参照データ セットに定義されている主キー列に基づいて行われます。

参照データは、遡及的に結合されることはありません。 そのため、データが構成されてアップロードされると、現在および将来のイングレス データのみが対応付けられ、参照日付セットに結合されます。

## <a name="video"></a>ビデオ

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Time Series Insight の参照データ モデルについて説明します。</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>参照データ セットを追加する

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 既存の Azure Time Series Insights 環境を見つけます。 Azure Portal の左側のメニューにある **[すべてのリソース]** を選択します。 Azure Time Series Insights 環境を選択します。

1. **[概要]** ページを選択します。 ページの上部付近にある **[Essentials]** セクションを展開して、 **[Time Series Insights Explorer の URL]** を見つけ、リンクを開きます。  

   [![[Essentials] セクションを展開する](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   お使いの Azure Time Series Insights 環境に対するExplorer を表示します。

1. Azure Time Series Insights Explorer で環境セレクターを展開します。 アクティブな環境を選択します。 Explorer ページの右上にある参照データ アイコンを選択します。

   [![参照データの追加](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. **[+ データ セットの追加]** ボタンをクリックして、新しいデータ セットの追加を開始します。

   [![データ セットの追加](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. **[新しい参照データ セット]** ページで、データの形式を選択します。

   - **[CSV]** (コンマ区切りのデータ) を選択します。 最初の行は、ヘッダー行として扱われます。
   - JavaScript オブジェクト表記法 (JSON) 形式のデータの場合は **[JSON 配列]** を選択します。

   [![データ形式を選択する。](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. 次のいずれかの方法を使用して、データを指定します。

   - データをテキスト エディターに貼り付けます。 その後、 **[参照データの解析]** ボタンをクリックします。
   - **[ファイルの選択]** ボタンをクリックして、ローカル テキスト ファイルからデータを追加します。

   たとえば、CSV データを貼り付けます。[![貼り付けられた CSV データ](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   たとえば、JSON 配列データを貼り付けます。[![JSON データを貼り付ける](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   データ値の解析中にエラーが発生した場合は、ページの下部に赤色でエラーが表示されます (たとえば、`CSV parsing error, no rows extracted`)。

1. データが正常に解析されると、データ グリッドが表示され、データを表す行と列が表示されます。 データ グリッドをレビューし、内容が正しいこと確認してください。

   [![参照データをレビューする](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. 各列をレビューして、仮定されたデータ型を理解し、必要であればデータ型を変更します。  列見出しにあるデータ型のシンボルを選択します。 **#** はdouble (数値データ)、**T|F** はブール型、**Abc** は文字列を表します。

   [![列見出しでデータ型を選択する。](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. 必要であれば、列見出しの名前を変更します。 キー列の名前は、イベント ソース内の対応するプロパティに結合させるために必要です。

   > [!IMPORTANT]
   > 参照データのキー列名が、大文字小文字の区別も含め、受信データへのイベント名と正確に一致することを確認してください。 非キー列の名前は、受信データを対応する参照データの値で増幅するために使用されます。

1. 必要であれば、 **[Filter the rows...]\(行をフィルター...\)** フィールドに値を入力して、特定の行をレビューします。 フィルターはデータをレビューするのに便利ですが、データのアップロード中には適用されません。

1. データ グリッドの上の **[データ セット名]** フィールドに値を入力して、データ セットに名前を付けます。

    [![データ セットに名前を付ける。](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. データ グリッドの上にあるドロップダウン リストを選択して、データ セット内の **[主キー]** 列を指定します。

    [![キー列を選択する。](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(オプション)** **+** ボタンを選択して、セカンダリ キー列を複合主キーとして追加します。 選択を元に戻す必要がある場合は、ドロップダウン リストから空の値を選択して、セカンダリ キーを削除します。

1. データをアップロードするには、 **[アップロード]** ボタンをクリックします。

    [![行をアップロードしデータを確認します。](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    完了したアップロードが確認され、 **[データセットが正常にアップロードされました]** というメッセージが表示されます。

    > [!WARNING]
    > 参照データセット間で共有されている列またはプロパティは、**重複したプロパティ名** アップロードエラーが表示されます。 このエラーが発生すると、参照データセットを正常にアップロードすることはできません。 重複するプロパティ名を共有する行を結合することによって削除できます。

1. **[行の追加]** 、 **[行の一括インポート]** または **[列の追加]** を選択し、必要に応じて参照データの値をさらに追加します。

    [![行の追加、行の一括インポートまたは列の追加をします。](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > 一意のキーを別の行と共有する行は、その一意のキーを共有する最後に追加された行によってオーバーライドされる列を含みます。

   > [!NOTE]
   > 追加された行は、*長方形* である **必要** はありません - 参照データセット内の他のエントリの列の数が少なくなったり、大きくたり、変化したりする可能性があります。

## <a name="next-steps"></a>次のステップ

- プログラムで[参照データを管理](time-series-insights-manage-reference-data-csharp.md)する。

- 詳細な API リファレンスについては、[参照データ API](/rest/api/time-series-insights/gen1-reference-data-api) に関するドキュメントを参照してください。