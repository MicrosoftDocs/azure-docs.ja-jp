---
title: 制限事項
description: SDK 機能のコードに関する制限事項
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: af935aeaaeee11ab50b327b7f7b5b77246cb974b
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566048"
---
# <a name="limitations"></a>制限事項

多くの機能には、サイズや数などの制限があります。

## <a name="azure-frontend"></a>Azure フロントエンド

* プロセスあたりの AzureFrontend インスタンスの合計数: 16.
* AzureFrontend あたりの AzureSession インスタンスの合計数: 16.

## <a name="objects"></a>Objects

* オブジェクトの 1 つの型 (Entity、CutPlaneComponent など) の許容される合計数: 16,777,215。
* 許容されるアクティブな切断面の合計数: 8.

## <a name="geometry"></a>ジオメトリ

* 1 つの資産内で許容される素材の合計数: 65,535。
* 1 つのテクスチャの最大寸法: 16,384 x 16,384。 これより大きいソース テクスチャは、変換プロセスによって縮小されます。

## <a name="overall-number-of-polygons"></a>多角形の全体数

読み込まれたすべてのモデルに対して許容される多角形の数は、[セッション管理 REST API](../how-tos/session-rest-api.md#create-a-session) に渡される VM のサイズによって異なります。

| サーバーのサイズ | 多角形の最大数 |
|:--------|:------------------|
|standard| 2,000 万 |
|Premium| 制限なし |

この制限の詳細については、[サーバーのサイズ](../reference/vm-sizes.md)に関する章を参照してください。

## <a name="platform-limitations"></a>プラットフォームの制限事項

**Windows 10 デスクトップ**

* Win32/x64 は、サポートされている唯一の Win32 プラットフォームです。 Win32/x86 はサポートされていません。

**Hololens 2**

* [PV カメラからのレンダリング](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in)機能はサポートされていません。
