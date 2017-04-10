---
title: "Azure Machine Learning Web サービスのパラメーターの使用 | Microsoft Docs"
description: "Azure Machine Learning Web サービスを使用して、Web サービス アクセス時のモデルの動作を変更する方法です。"
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c49187db-b976-4731-89d6-11a0bf653db1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: raymondl;garye
translationtype: Human Translation
ms.sourcegitcommit: 247d370c1f80729856e53690045991127ad54351
ms.openlocfilehash: 4c9f2bb57e3669327d8704b93fa56e2ace1e8b35
ms.lasthandoff: 03/02/2017


---
# <a name="use-azure-machine-learning-web-service-parameters"></a>Azure Machine Learning Web サービスのパラメーターの使用
Azure Machine Learning Web サービスは、変更可能なパラメーターを持つモジュールを含む実験を発行すると作成されます。 状況によっては、Web サービスの実行中にモジュールの動作変更が必要になる場合がありますが、 *Web サービスのパラメーター*を使えばこのタスクを実行できます。 

一般的な例として、発行された Web サービスのユーザーが、Web サービスにアクセスしたときに別のデータ ソースを指定できるように[データのインポート][reader] モジュールを設定する場合があります。 または、別の宛先を指定できるように[データのインポート][writer] モジュールを構成します。 その他の例としては、[特徴ハッシュ][feature-hashing] モジュールのビット数の変更や、[フィルターに基づく特徴の選択][filter-based-feature-selection]モジュールにおける目的の特徴の数の変更があります。 

Web サービスのパラメーターを設定し、実験の&1; つまたは複数のモジュール パラメーターに関連付けて、必須か任意かを指定することができます。 Web サービスのユーザーは、Web サービスの呼び出し時にこれらのパラメーターの値を指定できます。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-to-set-and-use-web-service-parameters"></a>Web サービスのパラメーターを設定して使用する方法
Web サービスのパラメーターを定義するには、モジュールのパラメーターの横にあるアイコンをクリックして、[Set as web service parameter] を選択します。 すると、新しい Web サービスのパラメーターが作成されて、モジュール パラメーターにつながります。 これで、Web サービスにアクセスしたときにユーザーが Web サービスのパラメーターの値を指定できるようになり、それがモジュール パラメーターに適用されます。

Web サービスのパラメーターを&1; 回定義すれば、その実験の他のモジュール パラメーターでも使用できます。 1 つのモジュールのパラメーターに関連付けられている Web サービスのパラメーターを定義する場合、Web サービスのパラメーターに同じ種類の値が想定されるのであれば、その同じパラメーターを他のモジュールでも使用できます。 たとえば、Web サービスのパラメーターが数値の場合、数値が想定されるモジュール パラメーターでのみ使用できます。 Web サービスのパラメーターの値を設定すると、すべての関連付けられたモジュール パラメーターに適用されます。

Web サービスのパラメーターの既定値を指定するかどうかを設定できます。 指定した場合、Web サービスのユーザー向けのパラメーターはオプションとなります。 既定値を指定しない場合、ユーザーは Web サービスにアクセスしたときに値の入力を求められます。

Web サービスの API ドキュメントには、Web サービス アクセス時にプログラムで Web サービスのパラメーターを指定する方法に関する Web サービス ユーザー向けの情報が含まれます。

> [!NOTE]
> 従来の Web サービスの API ドキュメントは、Machine Learning Studio の Web サービス **ダッシュボード**にある **API ヘルプ ページ** リンクから入手できます。 新しい Web サービスの API ドキュメントは、Web サービスの **[Consume (使用)]** ページと **[Swagger API]** ページにある [Azure Machine Learning Web サービス](https://services.azureml.net/Quickstart) ポータルから入手できます。
> 
> 

## <a name="example"></a>例
たとえば、Azure BLOB ストレージに情報を送信する[データのエクスポート][writer] モジュールの実験があるとします。 Web サービスのユーザーがサービスにアクセスしたときに BLOB ストレージへのパスを変更できるようにする "Blob path" という名前の Web サービスのパラメーターを定義しましょう。

1. Machine Learning Studio で[データのエクスポート][writer] モジュールをクリックして選択します。 実験キャンバスの右側の [プロパティ] ウィンドウにプロパティが表示されます。
2. ストレージの種類を指定します。
   
   * **[Please specify data destination]**で [Azure Blob Storage] を選択します。
   * **[Please specify authentication type]**の下の [アカウント] を選択します。
   * Azure BLOB ストレージのアカウント情報を入力します。 
     <p />
3. **[Path to blob beginning with container parameter]**の右にあるアイコンをクリックします。 次のように表示されています。
   
   ![Web サービスのパラメーター アイコン][icon]
   
   [Set as web service parameter] を選択します。
   
   [プロパティ] ウィンドウの下部にある **[Web Service Parameters]** の下に [Path to blob beginning with container] という名前のエントリが追加されます。 これは、この[データのエクスポート][writer] モジュール パラメーターに現在関連付けられている Web サービスのパラメーターです。
4. Web サービスのパラメーターの名前を変更するには、名前をクリックし、「Blob path」と入力して **Enter** キーを押します。 
5. Web サービスのパラメーターの既定値を指定するには、名前の右側にあるアイコンをクリックして、[Provide default value] を選択して値を入力し (たとえば 「container1/output1.csv」)、 **Enter** キーを押します。
   
   ![Web サービスのパラメーター][parameter]
6. **[実行]**をクリックします。 
7. **[Web サービスのデプロイ]** をクリックし、**[Deploy Web Service [Classic] \(Web サービスのデプロイ [従来])]** または **[Deploy Web Service [New] \(Web サービスのデプロイ [新規])]** を選択して、Web サービスをデプロイします。

> [!NOTE] 
> 新しい Web サービスをデプロイするには、Web サービスのデプロイ先となるサブスクリプションで十分なアクセス許可を持っている必要があります。 詳しくは、「[Azure Machine Learning Web サービス ポータルを使用して Web サービスを管理する](machine-learning-manage-new-webservice.md)」をご覧ください。 

これで Web サービスのユーザーが Web サービス アクセス時に[データのエクスポート][writer] モジュールの新しい宛先を指定できるようになります。

## <a name="more-information"></a>詳細情報
詳細については、[Machine Learning Blog (Machine Learning ブログ)](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) の「[Web サービスのパラメーター (ブログの投稿)](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx)」をご覧ください。

Machine Learning Web サービスへのアクセスの詳細については、 [「発行済みの Machine Learning Web サービスを使用する方法」](machine-learning-consume-web-services.md)をご覧ください。

<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/


