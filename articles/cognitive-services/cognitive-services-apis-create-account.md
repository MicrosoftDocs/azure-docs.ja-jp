---
title: "Azure Portal で Cognitive Services APIs アカウントを作成する | Microsoft Docs"
description: "Azure Portal で Microsoft Cognitive Services APIs アカウントを作成する方法。"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Azure Portal で Cognitive Services APIs アカウントを作成する

Microsoft Cognitive Service API を使用するには、まず、Azure Portal でアカウントを作成する必要があります。

1. [Azure Portal](http://portal.azure.com) にサインインします。

2. **[+ 新規]** をクリックします。

3. **[インテリジェンス + 分析]** を選択し、**[Cognitive Services API (プレビュー)]** を選択します。

    ![Cognitive Services APIs の選択](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. **[作成]** ページで、次の情報を指定します。

    -   **アカウント名:** アカウントの名前。 わかりやすい名前を使用することをお勧します (*AFaceAPIAccount* など)。

    -   **サブスクリプション:** 使用可能な Azure サブスクリプションの中から、少なくとも共同作業者アクセス許可を持っているサブスクリプションを選択します。

    -   **API の種類:** 使用する Cognitive Services API を選択します。 使用できるさまざまな Cognitive Services APIs の詳細については、[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) のサイトを参照してください。

    ![API の種類の選択](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **価格レベル:** Cognitive Services アカウントのコストは、実際の使用量と選択しているオプションによって異なります。 各 API の価格の詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/cognitive-services/)を参照してください。

    -   **リソース グループ :** リソース グループとは、同じライフサイクル、アクセス許可、ポリシーを共有するリソースの集まりです。 リソース グループの詳細については、「[ポータルを使用した Azure リソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)」を参照してください。

    -   **リソース グループの場所:** これは、選択されている API がグローバルである (特定の場所にバインドされていない) 場合にのみ必要です。 API がグローバルであり、特定の場所にバインドされていない場合は、Cognitive Services API アカウントに関連付けられたメタデータが配置されるリソース グループの場所を指定する必要があります。 この場所は、アカウントの実行時の可用性に影響しません。 リソース グループの詳細については、「[ポータルを使用した Azure リソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)」を参照してください。

    -   **API 設定:** 既定では、アカウントの作成は、[Azure アカウント管理者](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles)がそれを明示的に有効にするまでは無効になっています。

        この設定の変更は、現在選択されている API の種類と場所、または左側のパネルに表示されているリソース グループの場所にのみ適用されます。

        ![Cognitive Services APIs アカウントの作成](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > 更新の設定が失敗したことを示す通知が表示された場合は、[アカウント管理者](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts)としてログインしていません。 アカウント管理者は、前述の手順に従って、作成を有効にする必要があります。
        >
        > ![設定の更新が失敗したことを示すメッセージ](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        場合によっては、アカウント管理者は、サブスクリプションへのアクセス権を持っていないことがあります。 この場合は、サービス管理者に「[サブスクリプションまたはサービスを管理する Azure 管理者ロールを追加または変更する](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator)」の手順を実行することを依頼してください。
        
        サブスクリプションのアカウント管理者またはサービス管理者を見つけるには、[Azure Portal](https://portal.azure.com) でサブスクリプションを選択し、__[プロパティ]__ を選択します。 [プロパティ] ブレードの下部に__アカウント管理者__と__サービス管理者__の情報が表示されます。
        
        ![サブスクリプションのプロパティ](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Cognitive Services に送信したデータは、マイクロソフトの製品とサービスを向上させるためにマイクロソフトによって使用される場合があります。 詳細については、オンライン サービスの使用条件の[Microsoft Cognitive Services に関するセクション](http://www.microsoft.com/Licensing/product-licensing/products.aspx)
   を参照してください。

5. Azure Portal ダッシュボードにアカウントをピン留めするには、**[ダッシュボードにピン留めする]** をクリックします。

6. **[作成]** をクリックしてアカウントを作成します。

Cognitive Services アカウントが正常にデプロイされたら、ダッシュボードのタイルをクリックしてアカウント情報を表示します。

**[概要]** セクションの **[エンドポイント URL]** と **[キー]** セクションのキーを使用して、アプリケーションでの API の呼び出しを開始できます。

![アカウント情報の表示](media/cognitive-services-apis-create-account/display-account.png)

![アカウント キーの表示](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>次のステップ

- 使用できるすべての Microsoft Cognitive Services の詳細については、「[Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)」を参照してください。

- サンプルの Cognitive Services APIs を使用するためのクイック スタート ガイドについては、以下を参照してください。
    - [テキスト分析 API を使ってセンチメント、キー フレーズ、トピック、および言語を検出してみる](cognitive-services-text-analytics-quick-start.md)
    - [Cognitive Services の Recommendations API のクイック スタート ガイド](cognitive-services-recommendations-quick-start.md)
