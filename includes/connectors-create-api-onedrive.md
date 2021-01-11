---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040254"
---
## <a name="prerequisites"></a>前提条件

* [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) アカウント 

Logic Apps で OneDrive アカウントを使用するには、OneDrive アカウントへの接続を、あらかじめ Azure portal で Logic Apps に承認する必要があります。

Logic Apps に OneDrive アカウントへの接続を承認するには、次の手順に従います。  

1. Azure portal にサインインします。 

1. **[Azure サービス]** で、 **[Logic Apps]** を選択します。 次に、目的のロジック アプリの名前を一覧から選択します。

1. ロジック アプリのメニューで、 **[開発ツール]** の **[ロジック アプリ デザイナー]** を選択します。

1. Logic Apps デザイナーで、ドロップダウン リストから **[Microsoft のマネージド API を表示]** を選択し、検索ボックスに「*OneDrive*」と入力します。 使用するトリガーまたはアクションを選択します。

   ![追加する OneDrive API アクションの一覧を表示する Logic Apps デザイナーのスクリーンショット。](./media/connectors-create-api-onedrive/onedrive-1.png)

2. これまでに OneDrive への接続を作成したことがない場合は、プロンプトに従い、OneDrive の資格情報を使用してサインインします。  

   ![OneDrive API のサインイン プロンプトを表示する Logic Apps デザイナーのスクリーンショット。](./media/connectors-create-api-onedrive/onedrive-2.png)

3. **[サインイン]** を選択し、ユーザー名とパスワードを入力します。 **[サインイン]** をクリックします。 

   ![OneDrive API 承認に使用される Microsoft アカウント サインイン ページのスクリーンショット。](./media/connectors-create-api-onedrive/onedrive-3.png)   

    この資格情報は、OneDrive アカウントにあるデータへのアクセスをロジック アプリに承認する目的で使用されます。 

4. **[はい]** を選択して、OneDrive アカウントの使用をロジック アプリに承認します。  

   ![Logic Apps に許可されるアクションを示す Microsoft アカウントの承認のスクリーンショット。](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. これで手順に接続が表示されます。 [保存] を選択し、ロジック アプリの作成に進みます。 

   ![アクション エディターと OneDrive API 接続を表示する Logic Apps デザイナーのスクリーンショット。](./media/connectors-create-api-onedrive/onedrive-5.png)
