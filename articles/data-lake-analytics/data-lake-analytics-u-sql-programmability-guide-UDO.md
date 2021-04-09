---
title: Azure Data Lake の U-SQL UDO プログラミング ガイド
description: 優れた USQL スクリプトを作成できるように、Azure Data Lake Analytics での U-SQL UDO プログラミングについて説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 02360c68e5e830ceee69075fd5532b126d85bec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512468"
---
# <a name="u-sql-user-defined-objects-overview"></a>U-SQL ユーザー定義オブジェクトの概要


## <a name="u-sql-user-defined-objects-udo"></a>U-SQL: ユーザー定義オブジェクト:UDO
U-SQL を使うと、ユーザー定義オブジェクト (UDO) と呼ばれるカスタム プログラミング オブジェクトを定義できます。

以下は、U-SQL の UDO の一覧です。

* ユーザー定義エクストラクター
    * 行ごとに抽出
    * カスタム構造化ファイルからのデータ抽出の実装に使用

* ユーザー定義アウトプッター
    * 行ごとに出力
    * カスタム データ型またはカスタム ファイル形式への出力に使用

* ユーザー定義プロセッサ
    * 1 つの行を取得し、1 つの行を生成
    * 列の数を減らしたり、既存の列セットから派生した値を含む列を新しく生成したりする際に使用

* ユーザー定義アプライヤー
    * 1 つの行を取得し、0 ～ n 個の行を生成
    * OUTER/CROSS APPLY と併用

* ユーザー定義コンバイナー
    * 行セットを結合 (ユーザー定義の JOIN)

* ユーザー定義レジューサー
    * n 個の行を取得し、1 つの行を生成
    * 行の数を減らす際に使用

UDO は通常、U-SQL スクリプトの以下の U-SQL ステートメントの中で明示的に呼び出されます。

* EXTRACT
* OUTPUT
* PROCESS
* COMBINE
* REDUCE

> [!NOTE]  
> UDO の消費メモリは 0.5Gb に制限されています。  このメモリ制限は、ローカルの実行には適用されません。

## <a name="next-steps"></a>次のステップ
* [U-SQL プログラミング ガイド - 概要](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL プログラミング ガイド - UDT および UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)