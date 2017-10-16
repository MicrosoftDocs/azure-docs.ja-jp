---
title: "Azure Stack 上の App Services のデプロイ ソースを構成する | Microsoft Docs"
description: "サービス管理者が Azure Stack 上の App Service のデプロイ ソース (Git、GitHub、BitBucket、DropBox、および OneDrive) を構成する方法"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: be4b032e8f7370f696c47a7b8e82c0a819529f4d
ms.contentlocale: ja-jp
ms.lasthandoff: 09/15/2017

---

# <a name="configure-deployment-sources"></a>デプロイ ソースを構成する

Azure Stack 上の App Service は、複数のソース管理プロバイダーからのオンデマンド デプロイをサポートします。  この機能を使用して、アプリケーション開発者は、ソース管理リポジトリから直接デプロイすることができます。  テナントが App Service をリポジトリに接続するように構成するには、その前に管理者が Azure Stack 上の App Service とソース管理プロバイダーの統合を構成しておく必要があります。  ローカル Git の他に、次のソース管理プロバイダーがサポートされます。

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>App Service Administration でデプロイ ソースを表示する

1. サービス管理者として、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にログインします。
2. **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。![App Service リソース プロバイダー管理][1]
3. **[Source control configuration (ソース管理構成)]** をクリックします。  構成されているすべてのデプロイ ソースの一覧が表示されます。
    ![App Service リソース プロバイダー管理のソース管理構成][2]

## <a name="configure-github"></a>GitHub を構成する

> [!NOTE]
> このタスクを完了するには、GitHub アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

1. GitHub にログインして https://www.github.com/settings/developers に移動し、**[Register a new application]\((新しいアプリケーションの登録)\)** をクリックします。![GitHub - 新しいアプリケーションの登録][3]
2. **[Application name (アプリケーション名)]** を入力します (例: App Service on Azure Stack)。
3. **[Homepage URL (ホームページの URL)]**を入力します。  **ホームページの URL は、Azure Stack ポータルのアドレスにする必要があります** 例: https://portal.local.azurestack.external 。
4. **[Application Description (アプリケーションの説明)]** を入力します。
5. **[Authorization callback URL (認証コールバック エンドポイント URL)]** を入力します。  Azure Stack の既定のデプロイでは、URL は https://portal.local.azurestack.external/tokenauthorize の形式になっています。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。![GitHub - 新しいアプリケーションの入力値での登録][4]
6. **[Register application (アプリケーションを登録する)]**をクリックします。  アプリケーションの **[Client ID (クライアント ID)]** と **[Client Secret (クライアント シークレット)]** を示すページが表示されます。
    ![GitHub - 完了したアプリケーション登録][5]
7.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。 
8.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。 
9. **[Source control configuration (ソース管理構成)]** をクリックします。
10. **[Client ID (クライアント ID)]** と**[Client Secret (クライアント シークレット)]** をコピーして、GitHub 用の対応する入力ボックスに貼り付けます。
11. **[保存]**をクリックします。
12. その他のデプロイ ソースを構成しない場合は、「[管理ロールの修復をスケジュールする](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)」に進みます。


## <a name="configure-bitbucket"></a>BitBucket を構成する

> [!NOTE]
> このタスクを完了するには、BitBucket アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

1. BitBucket にログインし、アカウントの **[Integrations]\(統合\)** に移動します。![BitBucket ダッシュボード - 統合][7]
2. [Access Management]\(アクセス管理\) で **[OAuth]** をクリックし、**[Add consumer]\(コンシューマーの追加\)** をクリックします。![BitBucket OAuth コンシューマーの追加][8]
3. コンシューマーの **[Name (名前)]** を入力します (例: App Service on Azure Stack)。
4. アプリケーションの **[Description (説明)]** を入力します。
5. **[Callback URL (コールバック URL)]** を入力します。  Azure Stack の既定のデプロイでは、コールバック URL は https://portal.local.azurestack.external/TokenAuthorize です。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。  BitBucket の統合を成功させるには、URL の大文字と小文字をここに記載されているとおりにする必要があります。
6. **[URL]** を入力します。この URL は、Azure Stack ポータルの URL にする必要があります 例: https://portal.local.azurestack.external 。
7. 必要な**アクセス許可** (**Repositories**: **Read** **Webhooks**: **Read and write**) を選択します。
8. [ **Save**] をクリックします。  新しいアプリケーションが **[OAuth consumers (OAuth コンシューマー)]**の下に **[Key (キー)]** と **[Secret (シークレット)]** と共に表示されます。
    ![BitBucket アプリケーション リスト][9]
9.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。 
10.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。 
11. **[Source control configuration (ソース管理構成)]** をクリックします。
12. **[Key (キー)]**をコピーして BitBucket 用の **[クライアント ID]** 入力ボックスに、**[Secret (シークレット)]** をコピーして BitBucket 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
13. **[保存]**をクリックします。
14. その他のデプロイ ソースを構成しない場合は、「[管理ロールの修復をスケジュールする](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)」に進みます。

## <a name="configure-onedrive"></a>OneDrive を構成する

> [!NOTE]
> 現時点では、OneDrive for Business アカウントはサポートされていません。  このタスクを完了するには、OneDrive アカウントにリンクされている Microsoft アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

1. https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm に移動し、Microsoft アカウントを使用してログインします。
2. **[マイ アプリケーション]** で **[アプリを追加]** をクリックします。
![OneDrive アプリケーション][10]
3. 新しく登録するアプリケーションの **[名前]** を入力します。「**App Service on Azure Stack**」と入力し、**[アプリケーションの作成]** をクリックします。
4. 次の画面に、新しいアプリケーションのプロパティが表示されます。 **[アプリケーション ID]** を記録します。
![OneDrive アプリケーションのプロパティ][11]
5. **[アプリケーション シークレット]** で **[新しいパスワードを生成]** をクリックし、**[新しいパスワードが生成されました]** の内容を記録します。これがアプリケーション シークレットになります。
> [!NOTE]
> 新しいパスワードを必ず書き留めておいてください。この段階で [OK] をクリックした後、再度表示することはできません。
6. **[プラットフォーム]** で **[プラットフォームの追加]** をクリックし、**[Web]** を選択します。
7. **[リダイレクト URI]** を入力します。  Azure Stack の既定のデプロイでは、リダイレクト URI は https://portal.local.azurestack.external/tokenauthorize の形式になっています。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。![OneDrive アプリケーション - Web プラットフォームの追加][12]
8. **[Microsoft Graph のアクセス許可]** - **[委任されたアクセス許可]** を設定します。
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive アプリケーション - Graph のアクセス許可][13]
10. [ **Save**] をクリックします。
11.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。 
12.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。 
13. **[Source control configuration (ソース管理構成)]** をクリックします。
14. **[アプリケーション ID]** をコピーして OneDrive 用の **[クライアント ID]** 入力ボックスに、**[パスワード]** をコピーして OneDrive 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
15. **[保存]**をクリックします。
16. その他のデプロイ ソースを構成しない場合は、「[管理ロールの修復をスケジュールする](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)」に進みます。

## <a name="configure-dropbox"></a>DropBox を構成する

> [!NOTE]
> このタスクを完了するには、DropBox アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

1. https://www.dropbox.com/developers/apps に移動し、DropBox アカウントを使用してログインします。
2. **[Create app (アプリの作成)]** をクリックします。 
![Dropbox applications][14]
3. **Dropbox API** を選択します。
4. アクセス レベルを **[App Folder (アプリ フォルダー)]** に設定します。
5. アプリケーションの **[名前]** を入力します。
![Dropbox アプリケーションの登録][15]
6. **[Create app (アプリの作成)]** をクリックします。  **[App key (アプリ キー)]** と **[App secret (アプリ シークレット)]** を含むアプリの設定を示すページが表示されます。
7. **[App folder name (アプリ フォルダー名)]** が **[App Service on Azure Stack]** に設定されていることを確認します。
8. **[OAuth 2 Redirect URI (OAuth 2 リダイレクト URI)]** を設定し、**[Add (追加)]** をクリックします。  Azure Stack の既定のデプロイでは、リダイレクト URI は https://portal.local.azurestack.external/tokenauthorize の形式になっています。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。![Dropbox アプリケーションの構成][16]
9.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。 
10.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。 
11. **[Source control configuration (ソース管理構成)]** をクリックします。
12. **[Application Key (アプリケーション キー)]** をコピーして DropBox 用の **[クライアント ID]** 入力ボックスに、**[App secret (アプリ シークレット)]** をコピーして DropBox 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
13. **[保存]**をクリックします。
14. その他のデプロイ ソースを構成しない場合は、「[管理ロールの修復をスケジュールする](azure-stack-app-service-configure-deployment-sources.md#schedule-repair-of-management-roles)」に進みます。

## <a name="schedule-repair-of-management-roles"></a>管理ロールの修復をスケジュールする
さまざまなデプロイ ソースの構成で更新された設定を適用するには、管理ロールを修復する必要があります。  この処理によって構成値が正しく適用され、構成済みのデプロイ ソースをテナントが使用できるようになります。

1. 新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。
2. **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
3. **[Source control configuration (ソース管理構成)]**をクリックします。
4. **[Client ID (クライアント ID)]** と**[Client Secret (クライアント シークレット)]** をコピーして、GitHub 用の対応する入力ボックスに貼り付けます。
5. **[保存]**
6. **[ロール]** をクリックします。
7. **[管理サーバー]** をクリックします。
8. **[Repair All (すべて修復)]** をクリックし、**[はい]** を選択します。  この操作は、統合を完了するすべての管理サーバー上での修復をスケジュールします。  修復操作は、ダウンタイムを最小限に抑えるように管理されます。
    ![App Service リソース プロバイダー管理 - ロール - 管理サーバー すべて][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

