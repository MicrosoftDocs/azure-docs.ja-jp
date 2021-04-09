---
title: 'ML Studio (classic): Web サービス用の Excel アドイン - Azure'
description: コードを作成することなく、Excel から直接 Azure Machine Learning Web サービスを使用する方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 45d9e494b9f885cfa0680bec595aefcd4074d41e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520002"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-classic-web-services"></a>Azure Machine Learning Studio (クラシック) Web サービスの Excel アドイン

**適用対象:** ![適用対象: ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (classic)   ![適用対象外: ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Excel を使用すると、コードを作成することなく、直接 Web サービスを簡単に呼び出すことができます。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>ブックで既存の Web サービスを使用する手順

1. [サンプルの Excel ファイル](https://aka.ms/amlexcel-sample-2)を開きます。このファイルには、Excel アドインと、タイタニック号の乗客に関するデータが含まれます。 
 
    > [!NOTE]
    > - ファイルに関係する Web サービスの一覧が表示され、下部に [Auto-predict]\(自動予測\) のチェックボックスが表示されます。 自動予測を有効にすると、入力が変更されるたびに **すべての** サービスの予測が更新されます。 オフにした場合は、[Predict All]\(すべて予測\) をクリックして更新する必要があります。 サービス レベルで自動予測を有効にするには、手順 6 に移動します。
    > - Azure Machine Learning Excel アドインでは、読み込みのために Office アドイン ストアが呼び出されます。 組織で Office アドイン ストアへのアクセスが禁止されている場合、アドインの読み込み時にエラーが発生します。 この場合は、Microsoft 365 管理センターから Azure Machine Learning Excel アドインをデプロイしてください。 その後、アドインを呼び出し、URL と API キーを貼り付けて、Web サービスを手動で追加します。

 

2. 対象の Web サービスをクリックして選択します。この例では、「Titanic Survivor Predictor (Excel Add-in Sample) [Score]」を選択します。
   
    ![Web サービスの選択](./media/excel-add-in-for-web-services/image1.png)
3. この操作により、 **[予測]** セクションが表示されます。  このブックにはサンプル データが既に含まれていますが、空のブックで Excel のセルを選択して、 **[サンプル データを使用]** をクリックすることもできます。
4. ヘッダーを含めてデータを選択し、入力データ範囲のアイコンをクリックします。  [先頭行をデータの見出しとして使用する] ボックスを必ずオンにします。
5. **[出力]** で、データを出力するセル番号を入力します。この場合、「H1」などと入力します。
6. **[予測]** をクリックします。 [Auto-predict]\(自動予測\) チェックボックスを選択すると、予測ボタンを押さなくても、選択した領域 (入力として指定した領域) での任意の変更によって、出力セルの要求と更新がトリガーされます。
   
    ![[予測] セクション](./media/excel-add-in-for-web-services/image1.png)

Web サービスをデプロイするか、既存の Web サービスを使用します。 Web サービスのデプロイの詳細については、[チュートリアル 3: 信用リスク モデルのデプロイ](tutorial-part3-credit-risk-deploy.md)を参照してください。

Web サービスの API キーを取得します。 取得元は、クラシック Machine Learning Web サービスを発行したか、新しい Machine Learning Web サービスを発行したかによって異なります。

**クラシック Web サービスを使用する** 

1. Machine Learning Studio (クラシック) の左側の **[Web サービス]** セクションをクリックして、Web サービスを選択します。
   
    ![Studio での Web サービスの選択](./media/excel-add-in-for-web-services/image4.png)
2. Web サービスの API キーをコピーします。
   
    ![Studio での API キー](./media/excel-add-in-for-web-services/image5.png)
3. Web サービスの **[ダッシュボード]** タブで、 **[要求/応答]** をクリックします。
4. **[Request URI (要求 URI)]** セクションを探します。  URL をコピーして保存します。

> [!NOTE]
> これで、[Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインしてクラシック Machine Learning Web サービスの API キーを取得できます。
> 
> 

**新しい Web サービスを使用する**

1. [Azure Machine Learning Web サービス](https://services.azureml.net) ポータルで、 **[WEB サービス]** をクリックし、Web サービスを選択します。 
2. **[Consume (使用)]** をクリックします。
3. **[Basic consumption info (基本的な実行情報)]** を探します。 **主キー** と **要求応答** URL をコピーして保存します。

## <a name="steps-to-add-a-new-web-service"></a>新しい Web サービスを追加する手順

1. Web サービスをデプロイするか、既存の Web サービスを使用します。 Web サービスのデプロイの詳細については、[チュートリアル 3: 信用リスク モデルのデプロイ](tutorial-part3-credit-risk-deploy.md)を参照してください。
2. **[Consume (使用)]** をクリックします。
3. **[Basic consumption info (基本的な実行情報)]** を探します。 **主キー** と **要求応答** URL をコピーして保存します。
4. Excel で、 **[Web サービス]** セクションに移動します ( **[予測]** セクションにいる場合は、戻る矢印をクリックして Web サービスの一覧に移動します)。
   
    ![Web サービスの選択への移動](./media/excel-add-in-for-web-services/image3.png)
5. **[Web サービスの追加]** をクリックします。
6. **URL** というラベルが付いた、Excel アドインのテキスト ボックスに URL を貼り付けます。
7. **API キー** というラベルが付いたテキスト ボックスに API/主キーを貼り付けます。
8. **[追加]** をクリックします。
   
    ![クラシック Web サービスの URL と API キー。](./media/excel-add-in-for-web-services/image6.png)
9. Web サービスを使用するには、前述の「既存の Web サービスを使用する手順」に従います。

## <a name="sharing-your-workbook"></a>ブックを共有する
ブックを保存すると、追加した Web サービス用の API/主キーも保存されます。 このため、信頼できる個人とのみブックを共有することをお勧めします。

ご質問は、次のコメント セクション、または[フォーラム](/answers/topics/azure-machine-learning.html)までお寄せください。