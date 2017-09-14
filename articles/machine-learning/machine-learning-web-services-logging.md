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
ms.date: 06/15/2017
ms.author: raymondl;garye
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 7d0b2db01427430d6b0a317cdfefc265dd4b06e2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---
# <a name="enable-logging-for-machine-learning-web-services"></a>Machine Learning Web サービスのログ記録の有効化
このドキュメントは、Machine Learning Web サービスのログ記録機能について説明します。 ログ機能を使用すると、エラー番号とメッセージの他に追加の情報を得ることができ、Machine Learning API の呼び出しをトラブルシューティングするときに役立ちます。  

## <a name="how-to-enable-logging-for-a-web-service"></a>Web サービスでのログ記録を有効にする方法

[Azure Machine Learning Web Services](https://services.azureml.net) ポータルからログ記録を有効にすることができます。 

1. Azure Machine Learning Web サービス ポータル ([https://services.azureml.net](https://services.azureml.net)) にサインインします。 クラシック Web サービスの場合は、Machine Learning Studio の Machine Learning Web サービス ページで **[New Web Services Experience] \(新しい Web サービス エクスペリエンス\)** をクリックします。

   ![[New Web Services Experience] \(新しい Web サービス エクスペリエンス\) リンク](media/machine-learning-web-services-logging/new-web-services-experience-link.png)

2. メニュー バーで、新しい Web サービスの **[Web サービス]** をクリックします。クラシック Web サービスの場合は **[Classic Web Services (クラシック Web サービス)]** をクリックします。

   ![新しい Web サービスまたはクラシック Web サービスの選択](media/machine-learning-web-services-logging/select-web-service.png)

3. 新しい Web サービスの場合は、Web サービス名をクリックします。 クラシック Web サービスの場合は、Web サービス名をクリックし、次のページで適切なエンドポイントをクリックします。

4. 上部のメニュー バーで **[構成]** をクリックします。

5. エラーのみをログに記録する場合は **[ログを有効にする]** オプションを *[エラー]* に設定し、すべてログに記録するには *[すべて]* に設定します。

   ![ログ記録レベルの選択](media/machine-learning-web-services-logging/enable-logging.png)

6. [ **Save**] をクリックします。

7. クラシック Web サービスの場合は、**ml-diagnostics** コンテナーを作成します。

   すべての Web サービス ログは、Web サービスに関連付けられているストレージ アカウントの **ml-diagnostics** という BLOB コンテナーに保存されます。 新しい Web サービスの場合、Web サービスに初めてアクセスしたときに、このコンテナーが作成されます。 クラシック Web サービスの場合、コンテナーが存在しない場合は作成する必要があります。 

   1. [Azure ポータル](https://portal.azure.com)で、Web サービスに関連付けられているストレージ アカウントに移動します。

   2. **[Blob service]** の **[コンテナー]** をクリックします。

   3. コンテナー **ml-diagnostics** が存在しない場合は、**[ コンテナー]** をクリックし、コンテナーに "ml-diagnostics" という名前を付け、**[アクセスの種類]** を "BLOB" に設定します。 **[OK]**をクリックします。

      ![ログ記録レベルの選択](media/machine-learning-web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> クラシック Web サービスの場合、Machine Learning Studio の Web サービスのダッシュボードにも、ログ機能を有効にするスイッチがあります。 ただし、ログ機能は Web サービス ポータルで管理されるようになったため、この記事で説明しているように、ポータルでログ機能を有効にする必要があります。 Studio で既にログ記録を有効にしている場合は、Web サービス ポータルでログ記録を無効にしてから、再び有効にします。


## <a name="the-effects-of-enabling-logging"></a>ログ記録の有効化による効果
ログ記録が有効な場合、Web サービス エンドポイントの診断とエラーのログは、ユーザーのワークスペースにリンクされている Azure ストレージ アカウントの **ml-diagnostics** BLOB コンテナーに記録されます。 このコンテナーには、このストレージ アカウントに関連付けられているすべてのワークスペースの Web サービス エンドポイントの診断情報が保持されています。

Azure のストレージ アカウントを探索できる複数のツールを使って、ログを表示できます。 最も簡単な方法は、Azure ポータルでストレージ アカウントに移動し、**[コンテナー]** をクリックし、コンテナー **ml-diagnostics** をクリックすることです。  

## <a name="log-blob-detail-information"></a>ログ BLOB の詳細情報
コンテナーの各 BLOB には、次のアクションの 1 つだけについて診断情報が保持されています。

* バッチ実行メソッドの実行  
* 要求応答メソッドの実行  
* 要求応答コンテナーの初期化

各 BLOB の名前には次の形式の接頭辞が付きます。 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


_ログの種類_は次のいずれかの値です。  

* batch (バッチ)  
* score/requests (スコア/要求)  
* score/init (スコア/初期化)  


