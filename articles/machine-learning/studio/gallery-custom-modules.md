---
title: "Cortana Intelligence ギャラリー カスタム モジュール | Microsoft Docs"
description: "Cortana Intelligence ギャラリーでカスタム機械学習モジュールを発見します。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 16037a84-dad0-4a8c-9874-a1d3bd551cf0
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: roopalik;garye
ms.openlocfilehash: 4bab94c04f09261eaa88b9e6a225c05f57992ab0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="discover-custom-machine-learning-modules-in-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーでカスタム機械学習モジュールを発見する
[!INCLUDE [machine-learning-gallery-item-selector](../../../includes/machine-learning-gallery-item-selector.md)]

## <a name="custom-modules-for-machine-learning-studio"></a>Machine Learning Studio 向けカスタムモジュール
Cortana Intelligence ギャラリーには、Azure Machine Learning Studio の機能を拡張するいくつかの[カスタム モジュール](https://gallery.cortanaintelligence.com/customModules)が用意されています。 これらのモジュールをインポートして実験で使用し、さらに高度な予測分析ソリューションを開発することができます。

現在、ギャラリーには、*時系列分析*、*関連付けルール*、*クラスタリング アルゴリズム* (k-means 法以降)、*視覚化*、およびその他の役立つユーティリティ モジュールが用意されています。


## <a name="discover"></a>発見
[ギャラリーで](http://gallery.cortanaintelligence.com)カスタム モジュールを参照するには、**[More (詳細)]** で **[Custom Modules (カスタム モジュール)]** を選択します。

![ギャラリー ホーム ページでカスタム モジュールを選択する](./media/gallery-custom-modules/select-custom-modules-in-gallery.png)

**[[カスタム モジュール]](https://gallery.cortanaintelligence.com/customModules)** ページには、最近追加されたモジュールや人気のあるモジュールの一覧が表示されます。 すべてのカスタム モジュールを表示するには、**[See all (すべて表示)]** ボタンを選択します。 特定のカスタム モジュールを検索するには、**[See all (すべて表示)]** を選択してから、フィルター条件を選択します。 ギャラリー ページの上部にある **[Search (検索)]** ボックスに検索語句を入力することもできます。

![[See all (すべて表示)] を選択してすべてのカスタム モジュールを参照する](./media/gallery-custom-modules/click-see-all-for-all-custom-modules.png)

### <a name="understand"></a>理解

公開されたカスタム モジュールの機能を理解するには、カスタム モジュールを選択してモジュールの詳細ページを開きます。 詳細ページでは、一貫性のある情報豊かな学習エクスペリエンスが提供されます。 たとえば、詳細ページではモジュールの目的が強調表示され、期待される入力、出力、パラメーターの一覧が表示されます。 詳細ページには、基になるソース コードへのリンクもあり、ソース コードを調べたりカスタマイズしたりできます。

### <a name="comment-and-share"></a>コメントと共有
カスタム モジュールの詳細ページの **[Comments (コメント)]** セクションで、モジュールに関するコメント、フィードバックの提供、または質問を投稿することができます。 モジュールは、Twitter や LinkedIn で友人や同僚と共有することもできます。 モジュールの詳細ページへのリンクを電子メールで送信し、他のユーザーを招待してそのページを見せることもできます。

![この項目を友人と共有する](./media/gallery-how-to-use-contribute-publish/share-links.png)

![独自のコメントを追加する](./media/gallery-how-to-use-contribute-publish/comments.png)

## <a name="import"></a>インポート
任意のカスタム モジュールをギャラリーから独自の実験にインポートできます。

Cortana Intelligence ギャラリーには、モジュールのコピーをインポートする方法が 2 つ用意されています。

* **ギャラリーから**。 ギャラリーからカスタム モジュールをインポートするときに、モジュールの使用方法の例を示すサンプル実験も取得します。
* **Machine Learning Studio 内から**。 Machine Learning Studio で作業しているときに、任意のカスタム モジュールをインポートできます (その場合、サンプル実験は取得しません)。

### <a name="from-the-gallery"></a>ギャラリーから

1. ギャラリーで、モジュールの詳細ページを開きます。 
2. **[Studio で開く]** を選択します。
   
    ![ギャラリーからカスタム モジュールを開く](./media/gallery-custom-modules/open-custom-module-from-gallery.png)
   
各カスタム モジュールには、モジュールを使用する方法を示したサンプル実験が含まれています。 **[Studio で開く]** を選択すると、Machine Learning Studio ワークスペースでサンプル実験が開きます。 (Studio にまだサインインしていない場合は、最初に Microsoft アカウントを使用してサインインするように求められます。)

サンプル実験に加えて、カスタム モジュールがワークスペースにコピーされます。 また、カスタム モジュールは、すべての組み込みまたはカスタム Machine Learning Studio モジュールと共に、モジュール パレットにも配置されます。 ワークスペース内の他のモジュールと同様に、独自の実験で使用できるようになります。

### <a name="from-within-machine-learning-studio"></a>Machine Learning Studio 内から

1. Machine Learning Studio で **[新規]** を選択します。
2. **[Module (モジュール)]** を選択します。 ギャラリーのモジュールの一覧から選択するか、**[検索]** ボックスを使用して特定のモジュールを検索します。
3. マウスでモジュールをポイントし、**[Import Module (モジュールのインポート)]** を選択します。 (モジュールに関する情報を取得するには、**[ギャラリーで表示]** を選択します。 この操作で、ギャラリー内のモジュールの詳細ページに移動します。)
   
    ![Machine Learning Studio にカスタム モジュールをインポートする](./media/gallery-custom-modules/add-custom-module-in-studio.png)

カスタム モジュールは、ワークスペースにコピーされ、組み込みまたはカスタム Machine Learning Studio モジュールと共に、モジュール パレットに配置されます。 ワークスペース内の他のモジュールと同様に、独自の実験で使用できるようになります。

## <a name="use"></a>用途

カスタム モジュールをインポートするときに選択する方法に関係なく、モジュールをインポートすると、そのモジュールは Machine Learning Studio のモジュール パレットに配置されます。 カスタム モジュールは、他のモジュールと同じように、ワークスペース内のすべての実験でモジュール パレットから使用できます。

インポートしたモジュールを使用するには、次の手順に従って操作します。

1. 実験を作成するか、既存の実験を開きます。
2. ワークスペース内のカスタム モジュールの一覧を展開するには、モジュール パレットで **[Custom (カスタム)]** を選択します。 モジュール パレットは、実験キャンバスの左側にあります。
   
    ![Studio パレットのカスタム モジュール一覧](./media/gallery-custom-modules/custom-module-in-studio-palette.png)
3. インポートしたモジュールを選択し、実験までドラッグします。


**[ギャラリーに移動する](http://gallery.cortanaintelligence.com)**

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

