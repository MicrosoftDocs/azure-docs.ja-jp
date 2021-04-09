---
title: Azure Maps Creator (プレビュー) で屋内マップを操作する
description: この記事では、Azure Maps Creator サービス (プレビュー) に適用される概念について説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4ab00317e71f832bb677c4c7587e2356a37cb7a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903566"
---
# <a name="creator-preview-for-indoor-maps"></a>屋内マップ用の Creator (プレビュー)


> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


この記事では、Azure Maps Creator に適用される概念とツールについて説明します。 Azure Maps Creator API と SDK を使い始める前に、この記事を読むことをお勧めします。

Creator を使用すると、屋内マップ データに基づくマップ地物を含むアプリケーションを開発できます。 この記事では、マップ データをアップロード、変換、作成、使用するプロセスについて説明します。 全体的なワークフローを次の図に示します。

![Creator のマップ データ ワークフロー](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator-preview"></a>Azure Maps Creator (プレビュー) を作成する 

Creator サービス (プレビュー) を使用するには、Azure Maps アカウントで Azure Maps Creator を作成する必要があります。 Azure Maps で Azure Maps Creator を作成する方法の詳細については、「[Azure Maps Creator を管理する](how-to-manage-creator.md)」を参照してください。

## <a name="upload-a-drawing-package"></a>Drawing パッケージをアップロードする

Creator (プレビュー) では、アップロードされた Drawing パッケージを変換することによって、屋内マップ データが収集されます。 Drawing パッケージでは、構築または再モデル化されたファシリティが表されています。 Drawing パッケージの要件については、「[Drawing パッケージの要件](drawing-requirements.md)」を参照してください。

Drawing パッケージをアップロードするには、[Azure Maps Data (プレビュー) Upload API](/rest/api/maps/data/uploadpreview) を使用します。  アップロードが正常に完了すると、Data Upload API からユーザー データ識別子 (`udid`) が返されます。 次のステップでは、`udid` を使用して、アップロードされたパッケージを屋内マップ データに変換します。

## <a name="convert-a-drawing-package"></a>Drawing パッケージを変換する

[Azure Maps Conversion サービス](/rest/api/maps/conversion)では、アップロードした Drawing パッケージが屋内マップ データに変換されます。 Conversion サービスでは、パッケージの検証も行われます。 検証の問題は、エラーと警告の 2 種類に分類されます。 エラーが検出されると、変換処理は失敗します。 警告が検出された場合は、変換は成功します。 ただし、すべての警告を確認して解決することをお勧めします。 警告は、変換の一部が無視されたか、自動的に修正されたことを意味します。 警告を解決しないと、後のプロセスでエラーが発生する可能性があります。 詳細については、[Drawing パッケージの警告とエラー](drawing-conversion-error-codes.md)に関するページを参照してください。

エラーが発生すると、Conversion サービスから、[Azure Maps Drawing Error Visualizer](drawing-error-visualizer.md) スタンドアロン Web アプリケーションへのリンクが提供されます。 Drawing Error Visualizer を使用すると、変換プロセスの間に発生した [Drawing パッケージの警告とエラー](drawing-conversion-error-codes.md)を調べることができます。 エラーを修正したら、パッケージのアップロードと変換を試みることができます。

## <a name="create-indoor-map-data"></a>屋内マップ データを作成する

Azure Maps Creator (プレビュー) では、次の 3 つのサービスが提供されています。

* [Dataset サービス](/rest/api/maps/dataset/createpreview)。
変換された Drawing パッケージ データからデータセットを作成するには、Dataset サービスを使用します。
* [Tileset サービス](/rest/api/maps/tileset/createpreview)。
データセットのベクター ベースの表現を作成するには、Tileset サービスを使用します。 アプリケーションでは、タイルセットを使用して、データセットの視覚的なタイルベースのビューを提供できます。
* [Feature State サービス](/rest/api/maps/featurestate)。動的なマップ スタイル設定をサポートするには、Feature State サービスを使用します。 動的なマップ スタイル設定を使用すると、アプリケーションで、IoT システムによって提供されるスペース上にリアルタイムのイベントを反映できます。

### <a name="datasets"></a>データセット

データセットは、屋内マップの地物のコレクションです。 屋内マップの地物は、変換された Drawing パッケージで定義されている設備を表します。 [Dataset サービス](/rest/api/maps/dataset/createpreview)を使用してデータセットを作成した後は、任意の数の[タイルセット](#tilesets)または[地物状態セット](#feature-statesets)を作成できます。

[Dataset サービス](/rest/api/maps/dataset/createpreview)を使用すると、開発者はいつでも既存のデータセットに対する設備の追加や削除を行うことができます。 API を使用して既存のデータセットを更新する方法の詳細については、[Dataset サービス](/rest/api/maps/dataset/createpreview)に関する記事で追加オプションを参照してください。 データセットを更新する方法の例については、「[データのメンテナンス](#data-maintenance)」を参照してください。

### <a name="tilesets"></a>タイルセット

タイルセットは、均一のグリッド タイルのセットを表すベクター データのコレクションです。 開発者は、[Tileset サービス](/rest/api/maps/tileset/createpreview)を使用して、データセットからタイルセットを作成できます。

さまざまなコンテンツ ステージを反映するため、同じデータセットから複数のタイルセットを作成できます。 たとえば、家具や機器を含むタイルセットと、家具や機器を含まない別のタイルセットを作成することができます。  最新のデータ更新を含むタイルセットと、最新のデータ更新を含まないタイルセットを生成することもできます。

タイルセットでは、ベクター データだけでなく、マップ レンダリング最適化のためのメタデータも提供されます。 たとえば、タイルセットのメタデータには、タイルセットの最小と最大のズーム レベルが含まれます。 また、メタデータでは、タイルセットの地理的範囲を定義する境界ボックスも提供されます。 境界ボックスを使用すると、アプリケーションのプログラムで正しい中心点を設定できます。 タイルセットのメタデータの詳細については、[Tileset List API](/rest/api/maps/tileset/listpreview)に関するページを参照してください。

タイルセットを作成したら、[Render V2 サービス](#render-v2-service)で取得できます。

タイルセットが古くなり、役に立たなくなった場合は、タイルセットを削除できます。 タイルセットを削除する方法の詳細については、「[データのメンテナンス](#data-maintenance)」を参照してください。

>[!NOTE]
>タイルセットは、作成元のデータセットから独立しています。 データセットからタイルセットを作成した場合、後でそのデータセットを更新しても、タイルセットは更新されません。 データセットの変更を反映するには、新しいタイルセットを作成する必要があります。 同様に、タイルセットを削除しても、データセットは影響を受けません。

### <a name="feature-statesets"></a>地物状態セット

地物状態セットは、部屋や機器といったデータセットの地物に割り当てられた動的なプロパティ ("*状態*") のコレクションです。 "*状態*" の例としては、温度や占有率などがあります。 各 "*状態*" は、プロパティの名前、値、および最終更新のタイムスタンプを含むキーと値のペアです。

[Feature State サービス](/rest/api/maps/featurestate/createstatesetpreview)を使用すると、データセットに対する地物状態セットを作成および管理できます。 状態セットは、1 つまたは複数の "*状態*" によって定義されます。 各地物 (部屋など) には、1 つの "*状態*" を関連付けることができます。

状態セット内の各 "*状態*" の値は、IoT デバイスや他のアプリケーションで更新または取得できます。  たとえば、[Feature State Update API](/rest/api/maps/featurestate/updatestatespreview) を使用すると、空間占有量を測定するデバイスで、部屋の状態の変化を体系的に送信できます。

アプリケーションでは、地物状態セットを使用することで、現在の状態とそれぞれのマップ スタイルに応じて、設備内の地物を動的にレンダリングできます。 地物状態セットを使用してレンダリング マップ内の地物のスタイルを設定する方法の詳細については、「[Indoor Web SDK モジュール](#indoor-maps-module)」を参照してください。

>[!NOTE]
>タイルセットと同様に、データセットを変更しても既存の地物状態セットに影響はなく、地物状態セットを削除しても、関連付けられているデータセットには影響しません。

## <a name="using-indoor-maps"></a>屋内マップの使用

### <a name="render-v2-service"></a>Render V2 サービス

Azure Maps の [Render V2 サービスの Get Map Tile API (プレビュー)](/rest/api/maps/renderv2/getmaptilepreview) は、Creator (プレビュー) のタイルセットをサポートするように拡張されています。

Render V2 サービスの Get Map State Tile API を使用すると、アプリケーションでタイルセットを要求できます。 その後、タイルセットをマップ コントロールまたは SDK に統合できます。 Render V2 サービスを使用するマップ コントロールの例については、「[Indoor Maps モジュール](#indoor-maps-module)」を参照してください。

### <a name="web-feature-service-api"></a>Web Feature Service API

[Web Feature Service (WFS) API](/rest/api/maps/wfs) を使用して、データセットのクエリを実行できます。 WFS は、[Open Geospatial Consortium API の機能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)に従います。 WFS API を使用すると、データセット自体の内部にある地物のクエリを実行できます。 たとえば、WFS を使用して、特定の施設と階層の中規模の会議室をすべて検索することができます。

### <a name="indoor-maps-module"></a>Indoor Maps モジュール

[Azure Maps Web SDK](./index.yml) には、Indoor Maps モジュールが含まれています。 このモジュールでは、Azure Maps の "*マップ コントロール*" ライブラリに対する拡張機能が提供されます。 Indoor Maps モジュールでは、Creator (プレビュー) で作成された屋内マップがレンダリングされます。 ユーザーが異なるフロアを視覚化できる "*フロア ピッカー*" などのウィジェットが統合されています。

Indoor Maps モジュールを使用すると、屋内マップ データを他の [Azure Maps サービス](./index.yml)と統合する Web アプリケーションを作成できます。 最も一般的なアプリケーションの設定としては、道路、画像、気象、交通などの他の地図から屋内マップへの知識の追加などがあります。

また、Indoor Maps モジュールでは動的なマップ スタイル設定もサポートされています。 アプリケーションで地物状態セットの動的なスタイル設定を実装する方法の詳細な手順については、[Indoor Map モジュールの使用方法](how-to-use-indoor-module.md)に関するページを参照してください

### <a name="azure-maps-integration"></a>Azure Maps の統合

屋内マップ用のソリューションの開発を始めたら、既存の Azure Maps の機能を統合する方法がわかります。 たとえば、[Azure Maps Geofence API](/rest/api/maps/spatial/postgeofence) と Creator の屋内マップを使用して、資産の追跡や安全性のシナリオを実装できます。 Geofence API を使用すると、たとえば、ワーカーが屋内の特定の区域に出入りしたかどうかを判断できます。 Azure Maps と IoT テレメトリを接続する方法の詳細については、[こちら](tutorial-iot-hub-maps.md)を参照してください。

### <a name="data-maintenance"></a>データのメンテナンス

 Azure Maps Creator (プレビュー) の List、Update、Delete API を使用すると、データセット、タイルセット、地物状態セットの一覧表示、更新、削除を行うことができます。

>[!NOTE]
>アイテムの一覧を確認して削除する場合は常に、すべての依存 API またはアプリケーションに対するその削除の影響を考慮する必要があります。 たとえば、アプリケーションによって現在使用されているタイルセットを、[Render V2 の Get Map Tile API](/rest/api/maps/renderv2/getmaptilepreview) を使用して削除する必要がある場合、そのタイルセットを削除すると、アプリケーションによるそのタイルセットのレンダリングが失敗するようになります。

### <a name="example-updating-a-dataset"></a>例:データセットの更新

次の例では、データセットの更新、新しいタイルセットの作成、古いタイルセットの削除を行う方法を示します。

1. 「[Drawing パッケージをアップロードする](#upload-a-drawing-package)」および「[Drawing パッケージを変換する](#convert-a-drawing-package)」セクションの手順に従い、新しい Drawing パッケージをアップロードして変換 ます。

2. [Dataset Create API](/rest/api/maps/dataset/createpreview) を使用して、変換されたデータを既存のデータセットに追加します。

3. [Tileset Create API](/rest/api/maps/tileset/createpreview) を使用して、更新されたデータセットから新しいタイルセットを生成します。 ステップ 4 のために、新しい tilesetId を保存します。

4. アプリケーションでタイルセット識別子を更新して、更新されたキャンパス データセットの視覚化を有効にします。 古いタイルセットを使用しなくなった場合は、削除することができます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル: Creator (プレビュー) の屋内マップを作成する](tutorial-creator-indoor-maps.md)