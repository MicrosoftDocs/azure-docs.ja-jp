---
title: Azure Data Factory Mapping Data Flow の条件分割変換
description: Azure Data Factory Data Flow の条件分割変換
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727810"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Azure Data Factory Mapping Data Flow の条件分割変換

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

条件分割変換では、データ行をデータの内容に応じた別のストリームにルーティングできます。 条件分割変換の実装は、プログラミング言語の CASE 決定構造と同様です。 この変換では式を評価し、その結果に基づいて、データ行を指定されたストリームに送ります。 この変換には既定の出力も用意されているので、行が式に一致しない場合は既定の出力に送られます。

![条件分割変換](media/data-flow/cd1.png "条件分割変換")

条件をさらに追加するには、下部の構成ウィンドウで [ストリームの追加] を選択し、式ビルダーのテキスト ボックス内をクリックして式を作成します。
