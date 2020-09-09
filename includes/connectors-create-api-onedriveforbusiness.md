---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102800"
---
## <a name="prerequisites"></a>前提条件

* [OneDrive for Business](https://OneDrive.com) アカウント 

Logic Apps で OneDrive for Business アカウントを使用するには、Azure portal で、OneDrive for Business アカウントへの接続を Logic Apps に承認する必要があります。

以下の手順に従って、Logic Apps に OneDrive for Business アカウントへの接続を承認します。  

1. Azure portal にサインインします。 

1. **[Azure サービス]** で、 **[Logic Apps]** を選択します。 次に、一覧からロジック アプリの名前を選択します。

1. ロジック アプリのメニューで、 **[開発ツール]** の **[ロジック アプリ デザイナー]** を選択します。

1. Logic Apps デザイナーで、ドロップダウン リストから **[Microsoft のマネージド API を表示]** を選択し、検索ボックスに「*OneDrive for Business*」と入力します。 使用するトリガーまたはアクションを選択します。  

   ![OneDrive for Business API アクションによる繰り返しトリガーを示す Logic Apps デザイナーのスクリーンショット。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. これまでに OneDrive for Business への接続を作成していない場合は、プロンプトに従って、OneDrive for Business の資格情報を指定します。 この資格情報を使用して、ロジック アプリに OneDrive for Business アカウントのデータへのアクセスが承認されます。  

   ![OneDrive for Business のサインイン プロンプトを示す Logic Apps デザイナーのスクリーンショット。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. OneDrive for Business のユーザー名とパスワードを入力して、ロジック アプリを承認します。  

   ![サインイン プロンプトを示す OneDrive for Business サインイン ページのスクリーンショット。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. これで手順に接続が表示されます。 [保存] を選択し、ロジック アプリの作成に進みます。 

   ![OneDrive for Business 接続が一覧表示されたトリガーを示す Logic Apps デザイナーのスクリーンショット。](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
