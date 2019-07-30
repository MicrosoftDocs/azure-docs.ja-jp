---
title: Join Data (データの結合\):モジュール リファレンス
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service で Join Data (データの結合\) モジュールを使用してデータセットをマージする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1022bdc26a340b6b54ad840d1fe47674509fa865
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871667"
---
# <a name="join-data"></a>データの結合

この記事では、Azure Machine Learning service のビジュアル インターフェイスで **Join Data (データの結合\)** モジュールを使用し、データベーススタイルの結合操作を使用して 2 つのデータセットをマージする方法について説明します。  

## <a name="how-to-configure-join-data"></a>[Join Data]\(データの結合\) を構成する方法

2 つのデータセットに対して結合を実行するには、それらをキー列で関連付ける必要があります。 複数の列を使用した複合キーもサポートされています。 

1. 結合するデータセットを追加してから、 **[Join Data]\(データの結合\)** モジュールを実験にドラッグします。 

    このモジュールは、 **[操作]** の下の **[データ変換]** カテゴリ内にあります。

1. データセットを **[Join Data]\(データの結合\)** モジュールに接続します。 
 
1. **[Launch column selector]\(列セレクターの起動\)** を選択してキー列を選択します。 必ず左右両方の入力に列を選択してください。

    単一キーの場合:

    両方の入力に対して単一のキー列を選択します。
    
    複合キーの場合:

    左の入力と右の入力のすべてのキー列を同じ順序で選択します。 すべてのキー列が一致すると、 **[Join Data]\(データの結合\)** モジュールによってテーブルが結合されます。 列の順序が元のテーブルと同じでない場合は、オプション **[Allow duplicates and preserve column order in selection]\(重複を許可し、選択内容の列の順序を維持する\)** をオンにします。 

    ![列セレクター](media/module/join-data-column-selector.png)


1. テキスト列の結合で大文字と小文字の区別を維持する場合は、 **[大文字と小文字を区別する]** オプションをオンにします。 
   
1. **[結合の種類]** ドロップ ダウン リストを使用してデータセットの結合方法を指定します。  
  
    * **内部結合**:"*内部結合*" は最も一般的な結合操作です。 キー列の値が一致する場合にのみ、結合された行が返されます。  
  
    * **左外部結合**:"*左外部結合*" では、左側のテーブルのすべての行について結合された行が返されます。 左側のテーブルの行に右側のテーブルの一致する行がない場合、返される行には、右側のテーブルのすべての列の欠損値が含まれます。 欠損値の代わりの値を指定することもできます。  
  
    * **完全外部結合**:"*完全外部結合*" では、左側のテーブル (**table1**) と右側のテーブル (**table2**) のすべての行が返されます。  
  
         いずれかのテーブルの各行に、もう一方のテーブルと一致する行がない場合、結果には欠損値を含む行が含まれます。  
  
    * **左半結合**:"*左半結合*" では、キー列の値が一致すると、左側のテーブルの値のみが返されます。  

1. オプション **[Keep right key columns in joined table]\(結合テーブルの右側のキー列を維持する\)** の場合:

    * 両方の入力テーブルのキーを表示するには、このオプションを選択します。
    * 左側の入力のキー列のみを返すには、オフにします。

1. 実験を実行するか、[Join Data]\(データの結合\) モジュールを選択して **[Run Selected]\(選択項目の実行\)** を選択して結合を実行します。

1. 結果を表示するには、 **[Join Data]\(データの結合\)**  >  **[Results dataset]\(結果データセット\)**  >  **[可視化]** を右クリックします。

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 