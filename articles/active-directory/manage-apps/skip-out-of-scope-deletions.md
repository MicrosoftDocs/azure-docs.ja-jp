---
title: スコープ外のユーザーの削除をスキップする | Microsoft Docs
description: スコープ外のユーザーのプロビジョニングを解除する既定の動作をオーバーライドする方法について説明します。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a40b699c01f50ceb1bedbc36e7f1467772336f
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997073"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>スコープ外に出るユーザー アカウントの削除をスキップする

既定では、Azure AD プロビジョニング エンジンは、スコープ外に出るユーザーを論理的に削除または無効化します。 ただし、Workday to AD User Inbound Provisioning などの特定のシナリオでは、この動作が予期されていない場合があり、この既定の動作をオーバーライドしたいことがあります。  

このガイドでは、Microsoft Graph API と Microsoft Graph API エクスプローラーを使用して、スコープ外に出るアカウントの処理を制御するフラグ ***SkipOutOfScopeDeletions*** を設定する方法について説明します。 
* ***SkipOutOfScopeDeletions*** が 0 (false) に設定されている場合、スコープ外に出たアカウントはターゲットで無効になります
* ***SkipOutOfScopeDeletions*** が 1 (true) に設定されている場合、スコープ外に出るアカウントはターゲットで無効になりません。このフラグは "*プロビジョニング アプリ*" レベルで設定され、Graph API を使用して構成できます。 

この構成は *Workday to Active Directory User Provisioning* アプリで広く使用されているため、次の手順には Workday アプリケーションのスクリーンショットが含まれています。 ただし、これは **他のすべてのアプリ**（ServiceNow、Salesforce、Dropboxなど）でも使用できます。

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>手順 1:プロビジョニング アプリのサービス プリンシパル ID (オブジェクト ID) を取得します

1. [Azure portal](https://portal.azure.com) を起動し、プロビジョニング アプリケーションの [プロパティ] セクションに移動します。 たとえば、"*Workday to AD User Provisioning アプリケーション*" のマッピングをエクスポートする場合は、そのアプリの [プロパティ] セクションに移動します。 
1. プロビジョニング アプリの [プロパティ] セクションで、"*オブジェクト ID*" フィールドに関連付けられている GUID 値をコピーします。 この値はアプリの **ServicePrincipalId** とも呼ばれ、Graph Explorer の操作で使用されます。

   ![Workday アプリのサービス プリンシパル ID](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>手順 2:Microsoft Graph Explorer にサインインします

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を起動します
1. [Sign-In with Microsoft]\(Microsoft を使用してサインイン\) ボタンをクリックし、Azure AD 全体管理者またはアプリ管理者の資格情報を使用してサインインします。

    ![Graph のサインイン](./media/export-import-provisioning-mappings/wd_export_02.png)

1. サインインに成功すると、左側のウィンドウにユーザー アカウントの詳細が表示されます。

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>手順 3:既存のアプリの資格情報と接続の詳細を取得します

Microsoft Graph Explorer で、[servicePrincipalId] を「[手順 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)」から抽出した **ServicePrincipalId** に置き換え、次の GET クエリを実行します。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![GET ジョブのクエリ](./media/skip-out-of-scope-deletions/skip-03.png)

応答をテキスト ファイルにコピーします。 それは、以下に示す JSON テキストのようになります。デプロイに固有の値が黄色で強調表示されています。 緑色で強調表示されている行を末尾に追加し、青色で強調表示されている Workday 接続パスワードを更新します。 

   ![GET ジョブの応答](./media/skip-out-of-scope-deletions/skip-04.png)

マッピングに追加する JSON ブロックを次に示します。 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>手順 4:SkipOutOfScopeDeletions フラグを使用して、シークレット エンドポイントを更新します

Graph エクスプローラーで次のコマンドを実行して、***SkipOutOfScopeDeletions*** フラグを使用してシークレット エンドポイントを更新します。 

以下の URL で、[servicePrincipalId] を[手順 1.](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) で抽出した **ServicePrincipalId** に置き換えます。 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
手順 3. で更新したテキストを "要求本文" にコピーし、"要求ヘッダー" でヘッダー "Content-Type" を "application/json" に設定します。 

   ![PUT 要求](./media/skip-out-of-scope-deletions/skip-05.png)

[クエリの実行] をクリックします。 

出力は "Success – Status Code 204 (成功 - 状態コード 204)" になるはずです。 

   ![PUT 応答](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>手順 5:スコープ外のユーザーが無効になっていないことを確認します

特定のユーザーをスキップするようにスコープ規則を更新することで、このフラグが予期される動作になることをテストできます。 次の例では、新しいスコープ規則を追加することによって、ID 21173 の従業員 (以前はスコープ内) を除外しています。 

   ![スコープ設定の例](./media/skip-out-of-scope-deletions/skip-07.png)

次のプロビジョニング サイクルでは、Azure AD プロビジョニング サービスによって、ユーザー 21173 がスコープ外に出たことが識別され、SkipOutOfScopeDeletions プロパティが有効になっている場合は、そのユーザーの同期規則によって、次のようなメッセージが表示されます。 

   ![スコープ設定の例](./media/skip-out-of-scope-deletions/skip-08.png)


