---
title: "Machine Learning Web サービスのログ記録 | Microsoft Docs"
description: "Machine Learning Web サービスのログ記録を有効にする方法について説明します。 ログ記録では、API のトラブルシューティングに役立つ追加情報を提供します。"
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: raymondl;garye
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f307a26bfbb55b395f4073f4368432ae69b867ae


---
# <a name="enable-logging-for-machine-learning-web-services"></a>Machine Learning Web サービスのログ記録の有効化
このドキュメントは、クラシック Web サービスのログ記録機能について説明します。 Web サービスでのログ記録を有効にすると、エラー番号とメッセージの他に追加の情報を得ることができ、。Machine Learning APIs の呼び出しをトラブルシューティングするときに役立ちます。  

Azure クラシック ポータルで Web サービスのログ記録を有効化するには:   

1. [Azure クラシック ポータル](https://manage.windowsazure.com/)にサインインします。
2. 左側の機能の列で、**[MACHINE LEARNING]** をクリックします。
3. ワークスペース、**[Web サービス]** の順にクリックします。
4. Web サービスの一覧で、Web サービスの名前をクリックします。
5. エンドポイントの一覧で、エンドポイントの名前をクリックします。
6. **[構成]**をクリックします。
7. **[診断トレース レベル]** を *[エラー]* または *[すべて]* に設定し、**[保存]** をクリックします。

Azure Machine Learning Web Services ポータルでのログ記録を有効にするには:

1. [Azure Machine Learning Web サービス ポータル](https://services.azureml.net)にサインインします。
2. [Classic Web Services (クラシック Web サービス)] をクリックします。
3. Web サービスの一覧で、Web サービスの名前をクリックします。
4. エンドポイントの一覧で、エンドポイントの名前をクリックします。
5. **[構成]**をクリックします。
6. **ログ** を *[エラー]* または *[すべて]* に設定し、**[保存]** をクリックします。

## <a name="the-effects-of-enabling-logging"></a>ログ記録の有効化による効果
ログ記録が有効な場合、選択したエンドポイントのすべての診断とエラーが、ユーザーのワークスペースにリンクされている Azure ストレージ アカウントに記録されます。 このストレージ アカウントは、ワークスペースの Azure クラシック ポータルの [ダッシュボード] ビュー ([概要] セクションの下部) で確認できます。  

Azure のストレージ アカウントを探索できる複数のツールを使って、ログを表示できます。 最も簡単な方法は、Azure クラシック ポータルで [ストレージ アカウント] に移動し、**[コンテナー]** をクリックすることです。 そうすることで、 **"ml-diagnostics"**という名前のコンテナーを確認できます。 このコンテナーには、このストレージ アカウントに関連付けられているすべてのワークスペースの Web サービス エンドポイントの診断情報が保持されています。 

## <a name="log-blob-detail-information"></a>ログ BLO の詳細情報
コンテナーの各 BLOB には、次の診断情報が 1 つだけ保持されています。

* バッチ実行メソッドの実行  
* 要求応答メソッドの実行  
* 要求応答コンテナーの初期化

各 BLOB の名前には次の形式の接頭辞が付きます。 

{ワークスペース ID}-{Web サービス ID}-{エンドポイント ID}/{ログの種類}  

ログの種類は次のいずれかの値です。  

* batch (バッチ)  
* score/requests (スコア/要求)  
* score/init (スコア/初期化)  




<!--HONumber=Nov16_HO3-->


