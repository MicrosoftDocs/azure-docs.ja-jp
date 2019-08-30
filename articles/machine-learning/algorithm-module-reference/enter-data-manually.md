---
title: 'データの手動入力: モジュール リファレンス'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning service でデータの手動入力モジュールを使用し、値を入力することによって、小さなデータセットを作成する方法について説明します。 データセットには、複数の列を含めることができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: d7e6cede12f5a348f59db83b31b19c89266dfdf7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128835"
---
# <a name="enter-data-manually-module"></a>データの手動入力モジュール

この記事では、Azure Machine Learning service のビジュアル インターフェイス (プレビュー) のモジュールについて説明します。

値を入力して小さなデータセットを作成するには、このモジュールを使用します。 データセットには、複数の列を含めることができます。
  
このモジュールは、次のような用途に使用できます。  
  
- テスト用に少数の値のセットを生成する  
  
- ラベルの簡単なリストを作成する
  
- データセットに挿入する列名のリストを入力する

## <a name="enter-data-manually"></a>データの手動入力 
  
1.  [データの手動入力](./enter-data-manually.md)モジュールを実験に追加します。 このモジュールは、Azure Machine Learning の **[Data Input and Output]\(データの入力と出力\)** カテゴリにあります。 
  
2.  **[DataFormat]** で、次のいずれかのオプションを選択します。 指定したデータの解析方法は、これらのオプションによって決まります。 それぞれの形式で要件が大きく異なるため、関連するトピックに必ず目を通してください。  
  
    -   **ARFF**: Attribute-Relation File Format の略。Weka で使用されます。   
  
    -   **CSV**: コンマ区切り値形式。 詳細については、「[Convert to CSV (CSV への変換)](./convert-to-csv.md)」を参照してください。  
  
    -   **SVMLight**: Vowpal Wabbit などの機械学習フレームワークで使用される形式。  
  
    -   **TSV**: タブ区切り値形式。

     形式を選択したにもかかわらず、その形式の仕様を満たしたデータを入力しなかった場合、実行時エラーが発生します。
  
3.  **[Data]\(データ\)** ボックス内をクリックして、データの入力を開始します。 次の形式には、特別な注意が必要です。  
  
    - **CSV**: 複数の列を作成するには、コンマ区切りテキストを貼り付けるか、フィールド間にコンマを使用して複数の列を入力します。
  
        **HasHeader** オプションをオンにすると、値の先頭行を列見出しとして使用できます。  
  
        このオプションをオフにした場合は、Col1、Col2... という列名が使用されます。 列名は、後から [[メタデータの編集]](./edit-metadata.md) を使用して追加したり変更したりできます。  
  
    - **TSV**: 複数の列を作成するには、タブ区切りテキストを貼り付けるか、フィールド間にタブを使用して複数の列を入力します。  
  
        **HasHeader** オプションをオンにすると、値の先頭行を列見出しとして使用できます。  
  
        このオプションをオフにした場合は、Col1、Col2... という列名が使用されます。 列名は、後から [[メタデータの編集]](./edit-metadata.md) を使用して追加したり変更したりできます。  
  
    -   **ARFF**: 既存の ARFF 形式のファイルを貼り付けます。 値を直接入力する場合は、データの先頭にヘッダー (省略可能) と必須の属性フィールドを追加してください。 
    
        たとえば、単純なリストに次のヘッダーと属性行を追加できます。 この場合、列見出しは `SampleText` となります。
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: SVMLight 形式を使用して値を入力するか貼り付けます。  
  
        たとえば次のサンプルは、SVMight 形式の献血データセットの最初の 2 行を表したものです。  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        [データの手動入力](./enter-data-manually.md)モジュールを実行すると、これらの行が列とインデックス値のデータセットに変換されます。その例を次に示します。  
  
        |Col1|Col2|Col3|Col4|ラベル|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  それぞれの行の後に Enter キーを押して改行します。  
  
     **最後の行の後で必ず Enter キーを押してください。** 
     
     複数回 Enter キーを押して空の後続行を複数追加した場合、最後の空行は削除されてトリミングされますが、他の空行は欠損値として扱われます。  
  
     欠損値を含んだ行を作成した場合、それらの行は、後からいつでもフィルターで除去できます。  
  
5.  モジュールを右クリックし、 **[Run selected]\(選択項目の実行\)** を選択すると、データが解析されて、データセットとしてワークスペースに読み込まれます。  
  
     データセットを表示するには、出力ポートをクリックして **[Visualize]\(可視化\)** を選択します。  
## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 