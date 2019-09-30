---
title: Graph API を使用してプロビジョニング構成をエクスポートまたはインポートする | Microsoft Docs
description: Graph API を使用してプロビジョニング構成をエクスポートおよびインポートする方法について説明します。
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
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815863"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Graph API を使用してプロビジョニング構成をエクスポートまたはインポートする

Microsoft Graph API と Graph Explorer を使用すると、User Provisioning の属性マッピングとスキーマを JSON ファイルにエクスポートし、それを Azure AD にインポートすることができます。 ここでキャプチャした手順を使用して、プロビジョニング構成のバックアップを作成することもできます。 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>手順 1:プロビジョニング アプリのサービス プリンシパル ID (オブジェクト ID) を取得します

1. [Azure portal](https://portal.azure.com) を起動し、プロビジョニング アプリケーションの [プロパティ] セクションに移動します。 たとえば、"*Workday to AD User Provisioning アプリケーション*" のマッピングをエクスポートする場合は、そのアプリの [プロパティ] セクションに移動します。 
1. プロビジョニング アプリの [プロパティ] セクションで、"*オブジェクト ID*" フィールドに関連付けられている GUID 値をコピーします。 この値はアプリの **ServicePrincipalId** とも呼ばれ、Graph Explorer の操作で使用されます。

   ![Workday アプリのサービス プリンシパル ID](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>手順 2:Microsoft Graph Explorer にサインインします

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を起動します
1. [Sign-In with Microsoft]\(Microsoft を使用してサインイン\) ボタンをクリックし、Azure AD 全体管理者またはアプリ管理者の資格情報を使用してサインインします。

    ![Graph のサインイン](./media/export-import-provisioning-mappings/wd_export_02.png)

1. サインインに成功すると、左側のウィンドウにユーザー アカウントの詳細が表示されます。

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>手順 3:プロビジョニング アプリのプロビジョニング ジョブ ID を取得します

Microsoft Graph Explorer で、[servicePrincipalId] を「[手順 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)」から抽出した **ServicePrincipalId** に置き換え、次の GET クエリを実行します。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

次の応答を受け取ります。 応答に存在する "id attribute" をコピーします。 この値は **ProvisioningJobId** であり、基になるスキーマ メタデータを取得するために使用されます。

   [![プロビジョニング ジョブ ID](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>手順 4:プロビジョニング スキーマをダウンロードする

Microsoft Graph Explorer で、[servicePrincipalId] と [ProvisioningJobId] を、前の手順で取得した ServicePrincipalId と ProvisioningJobId に置き換えて、次の GET クエリを実行します。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

応答から JSON オブジェクトをコピーしてファイルに保存し、スキーマのバックアップを作成します。

## <a name="step-5-import-the-provisioning-schema"></a>手順 5:プロビジョニング スキーマをインポートする

> [!CAUTION]
> Azure portal を使用して変更できない構成用にスキーマを変更する必要がある場合、または有効で機能しているスキーマを使用して以前にバックアップしたファイルから構成を復元する必要がある場合にのみ、この手順を実行します。

Microsoft Graph Explorer で、[servicePrincipalId] と [ProvisioningJobId] を、前の手順で取得した ServicePrincipalId と ProvisioningJobId に置き換えて、次の PUT クエリを構成します。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

[Request Body]\(要求本文\) タブで、JSON スキーマ ファイルの内容をコピーします。

   [![要求本文](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

[Request Headers]\(要求ヘッダー\) タブで、値が "application/json" の Content-Type ヘッダー属性を追加します。

   [![要求ヘッダー](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

[Run Query]\(クエリの実行\) ボタンをクリックして新しいスキーマをインポートします。
