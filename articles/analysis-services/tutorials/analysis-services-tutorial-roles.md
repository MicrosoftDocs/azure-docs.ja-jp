---
title: チュートリアル - Azure Analysis Services の管理者とユーザーのロールを構成する | Microsoft Docs
description: Azure Analysis Services ロールを構成する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4c1a3f52c37dcaad4bc2f84d6d2fa04b61376cf1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188778"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>チュートリアル:サーバー管理者とユーザー ロールを構成する

 このチュートリアルでは、SQL Server Management Studio (SSMS) を使用して Azure 内のサーバーに接続し、サーバー管理者とモデル データベースのロールを構成します。 また、[Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) についても紹介します。 TMSL は、1200 以上の互換性レベルの表形式モデル用 JSON ベース スクリプト言語です。 多くの表形式モデリング タスクの自動化に使用できます。 TMSL は PowerShell でよく使用されますが、このチュートリアルでは SSMS で XMLA クエリ エディターを使用します。 このチュートリアルでは、以下のタスクを実行します。 
  
> [!div class="checklist"]
> * ポータルからサーバー名を取得する
> * SSMS を使用してサーバーに接続する
> * サーバー管理者ロールにユーザーまたはグループを追加する 
> * モデル データベース管理者ロールにユーザーまたはグループを追加する
> * 新しいモデル データベース ロールを追加し、ユーザーまたはグループを追加する

Azure Analysis Services のユーザー セキュリティの詳細については、「[認証とユーザーのアクセス許可](../analysis-services-manage-users.md)」を参照してください。 

## <a name="prerequisites"></a>前提条件

- サブスクリプションの Azure Active Directory。
- サブスクリプションで [Azure Analysis Services サーバー](../analysis-services-create-server.md)が作成されている。
- [サーバー管理者アクセス許可](../analysis-services-server-admins.md)を持っている。
- [adventureworks サンプル モデル](../analysis-services-create-sample-model.md)をサーバーに追加する。
- [最新バージョンの SQL Server Management Studio (SSMS) をインストールする](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[ポータル](https://portal.azure.com/)にサインインします。

## <a name="get-server-name"></a>サーバー名を取得する
SSMS からサーバーに接続するには、まずサーバー名が必要です。 ポータルからサーバー名を取得できます。

**Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
   ![Azure でサーバー名を取得する](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>SSMS で接続する

残りのタスクについては、SSMS を使用してサーバーに接続し、サーバーを管理します。

1. SSMS の**オブジェクト エクスプローラー**で、**[接続]** > **[Analysis Services]** の順にクリックします。

    ![接続](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. **[サーバーに接続]** ダイアログ ボックスの **[サーバー名]** に、ポータルからコピーしたサーバー名を貼り付けます。 **[認証]** で **[Active Directory - MFA サポートで汎用]** を選択し、ユーザー アカウントを入力して **[接続]** を押します。
   
    ![SSMS で接続する](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > [Active Directory - MFA サポートで汎用] を選択することをお勧めします。 この種類の認証は、[対話型ではない多要素認証](../../sql-database/sql-database-ssms-mfa-authentication.md)をサポートします。 

3. **オブジェクト エクスプローラー**で、サーバー オブジェクトを展開して表示します。 右クリックして、サーバーのプロパティを表示します。
   
    ![SSMS で接続する](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>サーバー管理者ロールにユーザー アカウントを追加する

このタスクでは、Azure AD のユーザーまたはグループ アカウントをサーバー管理者ロールに追加します。 セキュリティ グループを追加する場合、その `MailEnabled` プロパティが `True` に設定されている必要があります。

1. **オブジェクト エクスプローラー**でサーバー名を右クリックし、**[プロパティ]** をクリックします。 
2. **[分析サーバーのプロパティ]** ウィンドウで、**[セキュリティ]** > **[追加]** の順にクリックします。
3. **[ユーザーまたはグループを選択します]** ウィンドウで、Azure AD にユーザーまたはグループ アカウントを入力し、**[追加]** をクリックします。 
   
     ![サーバー管理者を追加する](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. **[OK]** をクリックし、**[分析サーバーのプロパティ]** を閉じます。

    > [!TIP]
    > また、ポータルで **Analysis Services 管理者**を使用してサーバー管理者を追加することもできます。 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>モデル データベース管理者ロールにユーザーを追加する

このタスクでは、モデルに既に存在する Internet Sales Administrator ロールにユーザーまたはグループ アカウントを追加します。 このロールには、adventureworks サンプル モデル データベースのフル コントロール (管理者) アクセス許可があります。 このタスクでは、作成されたスクリプトで [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) TMSL コマンドを使用します。

1. **オブジェクト エクスプローラー**で、**[データベース]** > **[adventureworks]** > **[ロール]** を展開します。 
2. **Internet Sales Administrator** を右クリックし、**[ロールをスクリプト化]** > **[作成または置換]** > **[新しいクエリ エディター ウィンドウ]** をクリックします。

    ![新しいクエリ エディター ウィンドウ](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. **XMLAQuery** で、**"memberName":** の値を Azure AD のユーザーまたはグループ アカウントに変更します。 既定では、サインインに使っているアカウントが含まれています。ただし、既にサーバー管理者なので、独自のアカウントを追加する必要はありません。

    ![XMLA クエリの TMSL スクリプト](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. **F5** キーを押してスクリプトを実行します。


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>新しいモデル データベース ロールを追加し、ユーザーまたはグループを追加する

このタスクでは、TMSL スクリプトで [Create](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/create-command-tmsl?view=sql-analysis-services-2017) コマンドを使用して新しい Internet Sales Global ロールを作成し、そのロールに対して*読み取り*アクセス許可を指定し、Azure AD のユーザーまたはグループ アカウントを追加します。

1. **オブジェクト エクスプローラー**で **[adventureworks]** を右クリックし、**[新しいクエリ]** > **[XMLA]** の順にクリックします。 
2. 次の TMSL スクリプトをコピーしてクエリ エディターに貼り付けます。

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. `"memberName": "globalsales@adventureworks.com"` オブジェクト値を Azure AD のユーザーまたはグループ アカウントに変更します。
4. **F5** キーを押してスクリプトを実行します。

## <a name="verify-your-changes"></a>変更を確認する

1. **オブジェクト エクスプローラー**でサーバー名をクリックし、**[更新]** をクリックするか、**F5** キーを押します。
2. **[データベース]** > **[adventureworks]** > **[ロール]** を展開します。 前のタスクで追加したユーザー アカウントと新しいロールの変更が表示されていることを確認します。   

    ![オブジェクト エクスプローラーで確認する](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、ユーザーまたはグループのアカウントとロールを削除します。 これを行うには、**[ロールのプロパティ]** > **[メンバーシップ]** を使用してユーザー アカウントを削除するか、ロールを右クリックして **[削除]** をクリックします。


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure AS サーバーに接続し、SSMS の adventureworks サンプル モデル データベースとプロパティを調べる方法について説明しました。 また、SSMS および TMSL スクリプトを使用して既存のロールと新しいロールにユーザーまたはグループを追加する方法について説明しました。 サーバーとサンプル モデル データベース用にユーザー アクセス許可を構成したので、PowerBI などのクライアント アプリを使用して自分と他のユーザーが接続できます。 さらに学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [チュートリアル:Power BI Desktop を使用して接続する](analysis-services-tutorial-pbid.md)

