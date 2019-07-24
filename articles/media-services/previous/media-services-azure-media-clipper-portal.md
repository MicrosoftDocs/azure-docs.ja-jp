---
title: ポータルでの Azure Media Clipper の使用 | Microsoft Docs
description: Azure Portal から Azure Media Clipper を使用してクリップを作成します
services: media-services
keywords: クリップ;サブクリップ;エンコード;メディア
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8c88caefb0909da55de87116a23fa520c1679cc2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57990606"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>ポータルで Azure Media Clipper を使用してクリップを作成する  

ポータルで Azure Media Clipper を使用すると、メディア サービス アカウント内のアセットからクリップを作成できます。 作成を開始するには、Azure Portal でメディア サービス アカウントに移動します。 次に、**[サブクリップ]** タブを選択します。

**[サブクリップ]** タブでは、クリップの作成を開始できます。 ポータルで Clipper は、有効なストリーミング ロケーターに公開されている単一ビットレートの MP4、マルチビット レートの MP4、ライブ アーカイブを読み込みます。 公開されていないアセットは読み込まれません。

現在、Clipper はパブリック プレビュー段階にあります。 Azure Portal で Clipper にアクセスするには、こちらの[パブリック プレビュー ページ](https://portal.azure.com/?feature.subclipper=true)に移動します。

次の図は、メディア サービス アカウントでの Clipper のランディング ページを示しています。![Azure Portal での Azure Media Clipper](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>クリップの生成
クリップを作成するには、アセットをドラッグしてクリップ インターフェイスにドロップします。 マークする時点がわかっている場合は、手動でこれらの時点をインターフェイスに入力することができます。 わからない場合は、アセットを再生するか再生ヘッドをドラッグして、目的のマーク開始時点とマーク終了時点を見つけます。 マーク開始時点とマーク終了時点を指定しないと、クリップは入力アセットの最初から開始され、入力アセットの終了まで継続されます。

フレームまたは GOP の精度で移動するには、フレーム送り/GOP 送りまたはフレーム戻し/GOP 戻しのボタンを使用します。 複数のアセットに対してクリップする場合は、複数のアセットをアセット選択パネルからドラッグしてクリップ インターフェイスにドロップします。 インターフェイス内のアセットは、選択して目的の順序に変更できます。 アセット選択パネルには、各アセットのアセット期間、種類、および解像度メタデータが表示されます。 複数のアセットを連結するときは、各入力ファイルのソースの解像度を考慮してください。 ソースの解像度が異なる場合は、低いほうの解像度入力が、最高解像度のアセットの解像度に合わせてアップスケールされます。 クリッピング ジョブの出力をプレビューするには、プレビュー ボタンを選択します。すると、選択したマーク時点からクリップが再生されます。

## <a name="producing-dynamic-manifest-filters"></a>動的マニフェスト フィルターの生成
[動的マニフェスト フィルター](https://azure.microsoft.com/blog/dynamic-manifest/)では、マニフェスト属性とアセットのタイムラインに基づく一連のルールを記述します。 これらのルールで、ストリーミング エンドポイントが、出力プレイリストを操作する方法 (マニフェスト) が決定されます。 このフィルターを使用すると、再生でストリーミングするセグメントを変更できます。 Clipper によって生成されたフィルターは、ローカルのフィルターであり、ソース アセットに固有のものです。 レンダリングされたクリップとは異なり、フィルターは新しいアセットではなく、生成するためにエンコード ジョブは必要ありません。 [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) または [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest) を使用して簡単に作成することができますが、精度は GOP までです。 通常、ストリーミング用にエンコードされたアセットの GOP サイズは 2 秒です。

動的なマニフェスト フィルターを作成するには、**[アセット]** タブに移動して目的のアセットを選びます。 上部メニューの **[サブクリップ]** ボタンを選びます。 [詳細設定] メニューからクリッピング モードとして動的マニフェスト フィルターを選びます。 レンダリングされたクリップを生成するのと同じ手順で、フィルターを作成できます。 フィルターは、1 つのアセットからのみ生成することができます。

次の図は、Azure Portal で動的マニフェスト フィルター モードにある Clipper を示しています。![Azure Portal で動的マニフェスト フィルター モードにある Azure Media Clipper](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>クリッピング ジョブの送信
クリップの作成が終了したら、対応するクリッピング ジョブまたは動的マニフェスト呼び出しを開始する [ジョブの送信] ボタンを選択します。

## <a name="next-steps"></a>次の手順
Azure Media Clipper の使用を開始するには、[作業の開始](media-services-azure-media-clipper-getting-started.md)に関する記事から、ウィジェットを展開する方法の詳細についてお読みください。
