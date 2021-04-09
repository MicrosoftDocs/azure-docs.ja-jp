---
title: Azure Maps Drawing Error Visualizer を使用する
description: この記事では、Creator (プレビュー) Conversion API から返された警告とエラーを視覚化する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: db88e347e12783205ea8c31fed0bb374fccb4736
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903583"
---
# <a name="using-the-azure-maps-drawing-error-visualizer-with-creator-preview"></a>Creator (プレビュー) での Azure Maps Drawing Error Visualizer の使用

> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


Drawing Error Visualizer は、変換プロセス中に検出された [Drawing パッケージの警告とエラー](drawing-conversion-error-codes.md)を表示するスタンドアロンの Web アプリケーションです。 Error Visualizer Web アプリケーションは、インターネットに接続せずに使用できる静的ページで構成されています。  Error Visualizer を使用すると、[Drawing パッケージの要件](drawing-requirements.md)に従ってエラーと警告を修正できます。 [Azure Maps Conversion API](/rest/api/maps/conversion) からは、エラーが検出された場合にのみ、Error Visualizer のリンクを含む応答が返されます。

## <a name="prerequisites"></a>前提条件

Drawing Error Visualizer をダウンロードする前に、次のことを行う必要があります。

1. [Azure Maps アカウントを作成します](quick-demo-map-app.md#create-an-azure-maps-account)
2. [プライマリ サブスクリプション キー (主キーまたはサブスクリプション キーとも呼ばれます) を取得します](quick-demo-map-app.md#get-the-primary-key-for-your-account)。
3. [Creator (プレビュー) リソースを作成します](how-to-manage-creator.md)

このチュートリアルでは [Postman](https://www.postman.com/) アプリケーションを使用していますが、別の API 開発環境を選択することもできます。

## <a name="download"></a>ダウンロード

1. Drawing パッケージを Azure Maps Creator サービス (プレビュー) にアップロードして、アップロードされたパッケージの `udid` を取得します。 パッケージをアップロードする手順については、「[Drawing パッケージのアップロード](tutorial-creator-indoor-maps.md#upload-a-drawing-package)」を参照してください。

2. 図面パッケージがアップロードされたので、アップロードされたパッケージに `udid` を使用して、パッケージをマップ データに変換します。 パッケージを変換する手順については、「[Drawing パッケージの変換](tutorial-creator-indoor-maps.md#convert-a-drawing-package)」を参照してください。

    >[!NOTE]
    >変換処理が成功した場合、Error Visualizer ツールのリンクは表示されません。

3. Postman アプリケーションの応答の **[Headers]\(ヘッダー\)** タブで、Conversion API から返された `diagnosticPackageLocation` プロパティを探します。 応答は次のような JSON です。

    ```json
    {
        "operationId": "77dc9262-d3b8-4e32-b65d-74d785b53504",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Failed",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {
            "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
        }
    }
    ```

4. `diagnosticPackageLocation` URL に対して `HTTP-GET` 要求を実行して、Drawing パッケージの Error Visualizer をダウンロードします。

## <a name="setup"></a>セットアップ

`diagnosticPackageLocation` リンクからダウンロードした zip パッケージ内には 2 つのファイルがあります。

* _VisualizationTool.zip_:Drawing Error Visualizer のソース コード、メディア、および Web ページが含まれています。
* _ConversionWarningsAndErrors.json_:Drawing Error Visualizer によって使用される警告、エラー、その他の詳細の書式設定された一覧が含まれています。

_VisualizationTool.zip_ フォルダーを展開します。 次の項目が含まれています。

* _assets_ フォルダー: 画像とメディア ファイルが含まれています
* _static_ フォルダー: ソース コード
* _index.html_ ファイル: Web アプリケーション。

以下の各バージョン番号のブラウザーを使用して、_index.html_ ファイルを開きます。 記載されているバージョンと同等の互換性のある動作を提供するバージョンであれば、別のバージョンを使用できます。

* Microsoft Edge 80
* Safari 13
* Chrome 80
* Firefox 74

## <a name="using-the-drawing-error-visualizer-tool"></a>Drawing Error Visualizer ツールの使用

Drawing Error Visualizer ツールを起動すると、アップロード ページが表示されます。 アップロード ページには、ドラッグ アンド ドロップ ボックスがあります。 ドラッグ アンド ドロップ ボックスは、エクスプローラー ダイアログを起動するボタンとしても機能します。

:::image type="content" source="./media/drawing-errors-visualizer/start-page.png" alt-text="Drawing Error Visualizer アプリ - スタート ページ":::

_ConversionWarningsAndErrors.json_ ファイルは、ダウンロードしたディレクトリのルートに配置されています。 _ConversionWarningsAndErrors.json_ を読み込むには、ファイルをボックスにドラッグ アンド ドロップするか、ボックスをクリックして、エクスプローラー ダイアログでファイルを見つけ、ファイルをアップロードします。

:::image type="content" source="./media/drawing-errors-visualizer/loading-data.gif" alt-text="Drawing Error Visualizer アプリ - ドラッグ アンド ドロップでデータを読み込む":::

_ConversionWarningsAndErrors.json_ ファイルが読み込まれると、Drawing パッケージのエラーと警告の一覧が表示されます。 各エラーまたは警告には、レイヤー、レベル、および詳細メッセージが指定されています。 エラーまたは警告に関する詳細情報を表示するには、 **[Details]\(詳細\)** リンクをクリックします。 操作できないセクションが一覧の下に表示されます。 各エラーに移動して、エラーの解決方法の詳細を確認できます。

:::image type="content" source="./media/drawing-errors-visualizer/errors.png" alt-text="Drawing Error Visualizer アプリ - エラーと警告":::

## <a name="next-steps"></a>次のステップ

[Drawing パッケージが要件を満たしたら](drawing-requirements.md)、[Azure Maps Dataset サービス](/rest/api/maps/conversion)を使用して、Drawing パッケージをデータセットに変換できます。 その後、屋内マップ Web モジュールを使用してアプリケーションを開発できます。 詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Drawing Conversion のエラー コード](drawing-conversion-error-codes.md)

> [!div class="nextstepaction"]
> [屋内マップ用の Creator (プレビュー)](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Indoor Maps モジュールを使用する](how-to-use-indoor-module.md)

> [!div class="nextstepaction"]
> [屋内マップの動的スタイル設定を実装する](indoor-map-dynamic-styling.md)