---
title: ML Studio (classic):Excel で Web サービスを使用する - Azure
description: Azure Machine Learning Studio (クラシック) では、Excel から直接 Web サービスを簡単に呼び出すことができ、コードを作成する必要がありません。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: e8e7404a2b643cf90866253e5a7453381b66f73e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517860"
---
# <a name="consuming-an-azure-machine-learning-studio-classic-web-service-from-excel"></a>Excel からの Azure Machine Learning Studio (クラシック) Web サービスの使用

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Azure Machine Learning Studio (クラシック) では、Excel から直接 Web サービスを簡単に呼び出すことができ、コードを作成する必要がありません。

Excel 2013 (またはそれ以降) または Excel Online を使用している場合は、Excel の [Excel アドイン](excel-add-in-for-web-services.md)を使用することをお勧めします。



## <a name="steps"></a>手順
Web サービスを発行します。 [チュートリアル 3: 信用リスク モデルのデプロイ](tutorial-part3-credit-risk-deploy.md)でその方法を説明しています。 現在、Excel ブック機能は、1 つの出力を持つ要求/応答サービス (つまり 1 つのスコア付けラベル) のみサポートしています。 

Web サービスを作成したら、Studio の左側の **[WEB サービス]** セクションをクリックして、Excel から使用する Web サービスを選択します。

**従来の Web サービス**

1. Web サービスの **[ダッシュボード]** タブに、**要求/応答** サービスの行があります。 このサービスに 1 つの出力がある場合は、その行の **[Excel ブックのダウンロード]** のリンクを確認してください。

    ![Studio (クラシック) Web サービス ポータルを使用して Excel ブックをダウンロードする](./media/consuming-from-excel/excellink.png)
2. **[Excel ブックのダウンロード]** をクリックします。

**新しい Web サービス**

1. Azure Machine Learning Web サービス ポータルで **[Consume (使用)]** を選択します。
2. [Consume (使用)] ページの **[Web service consumption options (Web サービスの使用オプション)]** セクションで、[Excel] アイコンをクリックします。

**ブックを使用する**

1. ブックを開きます。
2. セキュリティの警告が表示されます。 **[編集を有効にする]** ボタンをクリックします。

    ![編集を有効にして、保護されたビューのセキュリティ警告を削除する](./media/consuming-from-excel/enableeditting.png)
3. セキュリティの警告が表示されます。 **[コンテンツの有効化]** ボタンをクリックして、スプレッドシートのマクロを実行します。

    ![コンテンツを有効にして、マクロが無効になっているというセキュリティ警告を無視する](./media/consuming-from-excel/enablecontent.png)
4. マクロが有効になると、テーブルが生成されます。 青の列は RRS Web サービス、または **PARAMETERS** への入力として必要です。 RRS サービス **PREDICTED VALUES** の出力は緑色で表示されます。 特定の行のすべての列がいっぱいになると、ブックは自動的に、スコア付け API を呼び出し、スコア付けされた結果を表示します。

    ![パラメーターの入力と結果の予測値の表](./media/consuming-from-excel/sampletable.png)
5. 複数の行のスコア付けを実行するには、2 番目の行にデータを入力すると、予測値が生成されます。 複数の行を一度に貼り付けることもできます。

これで、Excel 機能 (グラフ、Power Map、条件付き書式など) と予測値を使用して、データの視覚化に役立てることができます。

## <a name="sharing-your-workbook"></a>ブックを共有する
マクロが機能するには、API キーがスプレッドシートの一部である必要があります。 つまり、信頼するエンティティ/個人とだけ、ブックを共有する必要があります。

## <a name="automatic-updates"></a>自動更新
RRS 呼び出しは、次の 2 つの状況で発生します。

1. 初めて行のすべての **PARAMETERS**
2. すべての **PARAMETERS** に入力されている行の任意の **PARAMETERS** が変更されたとき。