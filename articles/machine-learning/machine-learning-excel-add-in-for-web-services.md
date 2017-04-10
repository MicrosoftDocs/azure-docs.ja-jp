---
title: "Machine Learning Web サービスの Excel アドイン | Microsoft Docs"
description: "コードを作成することなく、Excel から直接 Azure Machine Learning Web サービスを使用する方法。"
services: machine-learning
documentationcenter: 
author: tedway
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/14/2017
ms.author: tedway;garye
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: 814decc2aace0379332af229d232eef1a26939de
ms.lasthandoff: 03/02/2017


---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Azure Machine Learning Web サービスの Excel アドイン
Excel を使用すると、コードを作成することなく、直接 Web サービスを簡単に呼び出すことができます。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>ブックで既存の Web サービスを使用する手順

1. [サンプルの Excel ファイル](http://aka.ms/amlexcel-sample-2)を開きます。このファイルには、Excel アドインと、タイタニック号の乗客に関するデータが含まれます。
2. 対象の Web サービスをクリックして選択します。この例では、「Titanic Survivor Predictor (Excel Add-in Sample) [Score]」を選択します。
   
    ![Web サービスの選択][01]
3. この操作により、**[予測]** セクションが表示されます。  このブックにはサンプル データが既に含まれていますが、空のブックで Excel のセルを選択して、 **[サンプル データを使用]**をクリックすることもできます。
4. ヘッダーを含めてデータを選択し、入力データ範囲のアイコンをクリックします。  [先頭行をデータの見出しとして使用する] ボックスを必ずオンにします。
5. **[出力]** で、データを出力するセル番号を入力します。この場合、「H1」などと入力します。
6. **[予測]**をクリックします。
   
    ![[予測] セクション][02]

Web サービスをデプロイするか、既存の Web サービスを使用します。 Web サービスのデプロイについて詳しくは、「[チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)」をご覧ください。

Web サービスの API キーを取得します。 取得元は、クラシック Machine Learning Web サービスを発行したか、新しい Machine Learning Web サービスを発行したかによって異なります。

**クラシック Web サービスを使用する** 

1. Machine Learning Studio の左側の **[Web サービス]** セクションをクリックして、Web サービスを選択します。
   
    ![Studio での Web サービスの選択][04]
2. Web サービスの API キーをコピーします。
   
    ![Studio での API キー][05]
3. Web サービスの **[ダッシュボード]** タブで、**[要求/応答]** をクリックします。
4. **[Request URI (要求 URI)]** セクションを探します。  URL をコピーして保存します。

> [!NOTE]
> これで、[Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインしてクラシック Machine Learning Web サービスの API キーを取得できます。
> 
> 

**新しい Web サービスを使用する**

1. [Azure Machine Learning Web サービス](https://services.azureml.net) ポータルで、**[WEB サービス]** をクリックし、Web サービスを選択します。 
2. **[Consume (使用)]**をクリックします。
3. **[Basic consumption info (基本的な実行情報)]** を探します。 **主キー**と**要求応答** URL をコピーして保存します。

## <a name="steps-to-add-a-new-web-service"></a>新しい Web サービスを追加する手順

1. Web サービスをデプロイするか、既存の Web サービスを使用します。 Web サービスのデプロイについて詳しくは、「[チュートリアル手順 5: Azure Machine Learning Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)」をご覧ください。
2. **[Consume (使用)]**をクリックします。
3. **[Basic consumption info (基本的な実行情報)]** を探します。 **主キー**と**要求応答** URL をコピーして保存します。
4. Excel で、**[Web サービス]** セクションに移動します (**[予測]** セクションにいる場合は、戻る矢印をクリックして Web サービスの一覧に移動します)。
   
    ![Web サービスの選択への移動][03]
5. **[Web サービスの追加]**をクリックします。
6. **URL**というラベルが付いた、Excell アドインのテキスト ボックスに URL を貼り付けます。
7. **API キー**というラベルが付いたテキスト ボックスに API/主キーを貼り付けます。
8. **[追加]**をクリックします。
   
    ![クラシック Web サービスの URL と API キー。][06]
9. Web サービスを使用するには、前述の「既存の Web サービスを使用する手順」に従います。

## <a name="sharing-your-workbook"></a>ブックを共有する
ブックを保存すると、追加した Web サービス用の API/主キーも保存されます。 このため、信頼できる個人とのみブックを共有することをお勧めします。

ご質問は、次のコメント セクション、または[フォーラム](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)までお寄せください。

[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png
[05]: ./media/machine-learning-excel-add-in-for-web-services/image5.png
[06]: ./media/machine-learning-excel-add-in-for-web-services/image6.png

