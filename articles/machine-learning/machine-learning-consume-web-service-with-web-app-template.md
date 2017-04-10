---
title: "Web アプリ テンプレートによる Azure Machine Learning Web サービスの使用 | Microsoft Docs"
description: "Azure Marketplace の Web アプリ テンプレートを利用して、Azure Machine Learning で予測 Web サービスを使用します。"
keywords: "Web サービス,運用,REST API,Machine Learning"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 5d587618dd885ec30e35b13c5fd543c1a8853200
ms.lasthandoff: 03/22/2017


---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Web アプリケーション テンプレートによる Azure Machine Learning Web サービスの使用

予測モデルを開発し、Machine Learning Studio を使用するか、R または Python などのツールを使用してそれを Azure Web サービスとしてデプロイしたら、REST API を使用して運用可能モデルにアクセスできます。

REST API を使用して Web サービスにアクセスする方法は、いろいろあります。 たとえば、Web サービスをデプロイしたときに生成されたサンプル コード ([Machine Learning Web サービス ポータル](https://services.azureml.net/quickstart)や Machine Learning Studio の Web サービス ダッシュボードで利用可能) を使用して、C#、R、または Python でアプリケーションを記述できます。 または同時に作成された Microsoft Excel ブックのサンプルを使用することもできます。

しかし、Web サービスにアクセスするための最もすばやくて簡単な方法は、 [Azure Web アプリケーション Marketplace](https://azure.microsoft.com/marketplace/web-applications/all/)で入手できる Web アプリ テンプレートを利用する方法です。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Azure Machine Learning Web アプリ テンプレート
Azure Marketplace で入手できる Web アプリ テンプレートを使用すると、Web サービスの入力データと予想される結果を認識するカスタム Web アプリを構築できます。 必要な操作は、Web アプリに Web サービスおよびデータへのアクセスを許可することだけで、後の処理はテンプレートによって行われます。

使用できるテンプレートは、次の 2 つです。

* [Azure ML Request-Response Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

各テンプレートは、Web サービスの API URI とキーを使用してサンプル ASP.NET アプリケーションを作成し、Web サイトとして Azure にデプロイします。 Request-Response Service (RRS) テンプレートは、1 つの結果を取得するために Web サービスに 1 行のデータを送信できる Web アプリケーションを作成します。 Batch Execution Service (BES) テンプレートは、複数の結果を取得するために多くの行のデータを送信できる Web アプリケーションを作成します。

これらのテンプレートは、コードを記述せずに使用できます。 API キーと URI だけを指定すれば、テンプレートによってアプリケーションが構築されます。

Web サービスの API キーと要求 URI を取得する手順は次のとおりです。

1. 新しい Web サービスの [Web サービス ポータル](https://services.azureml.net/quickstart)で、上部の **[Web サービス]** をクリックします。 クラシック Web サービスの場合は、**[Classic Web Services]\(クラシック Web サービス)** をクリックします。
2. アクセスする Web サービスをクリックします。
3. クラシック Web サービスの場合は、アクセスするエンドポイントをクリックします。
4. 上部の **[使用]** をクリックします。
5. **プライマリ**または**セカンダリ キー**をコピーし、保存します。
6. 要求応答サービス (RRS) テンプレートを作成する場合は、**要求-応答** URI をコピーして保存します。 バッチ実行サービス (BES) テンプレートを作成する場合は、**バッチ要求** URI をコピーして保存します。


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Request-Response Service (RRS) テンプレートの使用方法
次の図に示すように、RRS Web アプリ テンプレートを使用する手順に従います。

![RRS Web テンプレートを使用する手順][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. [Azure Portal](https://portal.azure.com) に移動して**ログイン**し、**[新規]** をクリックします。**[Azure ML Request-Response Service Web App]** を探して選択し、**[作成]** をクリックします。 
   
   * Web アプリケーションに一意の名前を付けます。 Web アプリの URL は、この名前の後に `.azurewebsites.net.` を付けたものです。たとえば、`http://carprediction.azurewebsites.net.` のようになります。
   * 実行している Web サービスの Azure サブスクリプションとサービスを選択します。
   * **[作成]**をクリックします。
     
     ![[Web アプリを作成]][image5]

4. Azure による Web アプリのデプロイが完了したら、Azure で Web アプリ設定ページの **[URL]** をクリックするか、Web ブラウザーで URL を入力します。 たとえば、`http://carprediction.azurewebsites.net.` のように指定します。
5. 初めて Web アプリを実行すると、**API ポスト URL** と **API キー**を指定するように求められます。
   前に保存した値 (**要求 URI** と **API キー**) をそれぞれ入力します。
     
     **[Submit]**をクリックします。
     
     ![ポスト URI と API キーの入力][image6]

6. **Web アプリの構成** ページに、現在の Web サービス設定が表示されます。 ここで、Web アプリで使用される設定を変更できます。
   
   > [!NOTE]
   > ここでの設定の変更は、この Web アプリだけに適用されます。 Web サービスの既定の設定は変更されません。 たとえば、ここで **説明** を変更した場合、Machine Learning Studio の Web サービス ダッシュボードに表示される説明は変更されません。
   > 
   > 
   
    設定し終わったら、**[変更を保存]** をクリックし、**[ホーム ページに移動]** をクリックします。

7. Web サービスに送信する値をホーム ページで入力できます。 入力を完了し **[送信]** をクリックすると、結果が返されます。

**構成**ページに戻るには、Web アプリの `setting.aspx` ページに移動します。 たとえば、`http://carprediction.azurewebsites.net/setting.aspx.` ページに移動します。もう一度 API キーを入力するように求められます。ページにアクセスして設定を更新するには、キーの入力が必要です。

他の Web アプリと同様に、Azure ポータルでこの Web アプリの停止、再開、または削除を行うことができます。 実行中は、ホーム Web アドレスに移動して、新しい値を入力することができます。

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Batch Execution Service (BES) テンプレートの使用方法
BES Web アプリ テンプレートは、RRS テンプレートと同じように使用できます。ただし、作成される Web アプリで複数行のデータを送信し、複数の結果を受け取ることができる点が異なります。

バッチ実行 Web サービスの入力値は Azure Storage から取得することも、ローカル ファイルから取得することもでき、結果は Azure Storage コンテナーに格納されます。
そのため、Web アプリから返される結果を保持するための Azure ストレージ コンテナーが必要であり、入力データも準備する必要があります。

![BES Web テンプレートを使用する手順][image2]

1. [Azure ML Batch Execution Service Web App Template](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) に移動して、Azure Marketplace で BES テンプレートを開き、**[Web アプリを作成]** をクリックすること以外は、RRS テンプレートと同じ手順に従って BES Web アプリを作成します。

2. 結果の格納先を指定するには、Web アプリのホーム ページで格納先コンテナーの情報を入力します。 Web アプリが入力値を取得する場所として、ローカル ファイルまたは Azure ストレージ コンテナーも指定します。
   **[Submit]**をクリックします。
   
    ![ストレージ情報][image7]

ページが表示され、ジョブの状態が示されます。
ジョブが完了すると、Azure BLOB ストレージ内の結果の場所が表示されます。 結果をローカル ファイルにダウンロードするためのオプションも表示されます。

## <a name="for-more-information"></a>BLOB の詳細
詳細については、以下のページをご覧ください。

* Machine Learning Studio で Machine Learning の実験を作成する方法については、「 [Azure Machine Learning Studio で初めての実験を作成する](machine-learning-create-experiment.md)
* Machine Learning の実験を Web サービスとしてデプロイする方法については、「 [Azure Machine Learning Web サービスをデプロイする](machine-learning-publish-a-machine-learning-web-service.md)
* Web サービスにアクセスするための別の方法については、「 [Azure Machine Learning Web サービスを使用する方法](machine-learning-consume-web-services.md)

[image1]: media/machine-learning-consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/machine-learning-consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/machine-learning-consume-web-service-with-web-app-template/api-key.png
[image4]: media/machine-learning-consume-web-service-with-web-app-template/post-uri.png
[image5]: media/machine-learning-consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/machine-learning-consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/machine-learning-consume-web-service-with-web-app-template/storage.png

