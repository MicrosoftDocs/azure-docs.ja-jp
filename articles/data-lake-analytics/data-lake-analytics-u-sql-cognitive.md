---
title: Azure Data Lake Analytics の U-SQL Cognitive 機能
description: U-SQL の Cognitive 機能のインテリジェンスを使用する方法について説明します。 このコード サンプルは使用開始時に役立ちます。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/05/2018
ms.openlocfilehash: 19d947b8b595107c76c1201d05e4d5ade3d7a092
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220059"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>U-SQL の Cognitive 機能を使い始める

## <a name="overview"></a>概要
U-SQL の Cognitive 機能により、開発者はビッグ データ プログラムにインテリジェンスを配置できます。 

Cognitive 機能を使用するには、次のサンプルを利用できます。
* イメージング: [顔を検出する](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* イメージング: [感情を検出する](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* イメージング: [オブジェクトを検出する (タグ付け)](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* イメージング: [OCR (光学式文字認識)](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* テキスト: [キー フレーズ抽出と感情分析](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>U-SQL での Cognitive 拡張機能の登録
開始する前に、「[U-SQL での Cognitive 拡張機能の登録](/u-sql/objects-and-extensions/cognitive-capabilities-in#registeringExtensions)」の記事の手順に従い、U-SQL で Cognitive 拡張機能を登録します。

## <a name="next-steps"></a>次のステップ
* [U-SQL Cognitive のサンプル](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics ジョブに U-SQL ウインドウ関数を使用する](./data-lake-analytics-u-sql-get-started.md)