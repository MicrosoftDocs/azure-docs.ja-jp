---
title: ディザスター リカバリーのためにプロビジョニング構成をエクスポートし、既知の良好な状態にロールバックする
description: ディザスター リカバリーのためにプロビジョニング構成をエクスポートし、既知の良好な状態にロールバックする方法について説明します。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256375"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>方法: プロビジョニング構成をエクスポートし、既知の良好な状態にロールバックする

この記事では、次の方法について学習します。

- Azure portal からプロビジョニング構成をエクスポートしてインポートする
- Microsoft Graph API を使用して、プロビジョニング構成をエクスポートし、インポートする

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Azure portal からプロビジョニング構成をエクスポートしてインポートする

### <a name="export-your-provisioning-configuration"></a>プロビジョニング構成をエクスポートする

構成をエクスポートするには:

1. [Azure portal](https://portal.azure.com/) の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
1. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択し、ご使用のアプリケーションを選択します。
1. 左側のナビゲーション ウィンドウで **[プロビジョニング]** を選択します。 プロビジョニング構成ページで、 **[属性マッピング]** をクリックし、次に **[詳細オプションの表示]** をクリックし、最後に **[スキーマを確認する]** をクリックします。 これにより、スキーマ エディターが表示されます。
1. ページの上部にあるコマンド バーで [ダウンロード] をクリックしてスキーマをダウンロードします。

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>ディザスター リカバリー - 既知の良好な状態にロールバックする

構成をエクスポートして保存すると、以前のバージョンの構成にロールバックできます。 属性マッピングまたはスコープ フィルターを変更するときにいつでも使用できるように、プロビジョニング構成をエクスポートして保存することをお勧めします。 必要な作業は、スキーマ エディターで、前の手順でダウンロードした JSON ファイルを開き、JSON ファイルの内容全体をコピーし、JSON ペイロードの内容全体を置き換え、保存するだけです。 アクティブなプロビジョニング サイクルがある場合、そのサイクルは完了し、次のサイクルで更新されたスキーマが使用されます。 また、次のサイクルは、新しい構成に基づいてすべてのユーザーとグループを再評価する初期サイクルになります。 前の構成にロールバックする場合、次の点を考慮してください。

- ユーザーをスコープに含める必要があるかどうかを判断するために、ユーザーはもう一度評価されます。 スコープ フィルターが変更されている場合、ユーザーはそれ以上スコープに含まれなくなり、それらのフィルターは無効になります。 ほとんどの場合、これは望ましい動作ですが、これを回避することが望ましい場合には[スコープ外の削除のスキップ](./skip-out-of-scope-deletions.md)機能を使用できます。 
- プロビジョニング構成を変更すると、サービスが再起動され、[初回サイクル](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental)がトリガーされます。

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Microsoft Graph API を使用して、プロビジョニング構成をエクスポートし、インポートする

Microsoft Graph API と Microsoft Graph Explorer を使用すると、ご自分のユーザー プロビジョニングの属性マッピングとスキーマを JSON ファイルにエクスポートし、それを Azure AD にインポートし直すことができます。 ここでキャプチャした手順を使用して、プロビジョニング構成のバックアップを作成することもできます。

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>手順 1:プロビジョニング アプリのサービス プリンシパル ID (オブジェクト ID) を取得します

1. [Azure portal](https://portal.azure.com) を起動し、プロビジョニング アプリケーションの [プロパティ] セクションに移動します。 たとえば、"*Workday to AD User Provisioning アプリケーション*" のマッピングをエクスポートする場合は、そのアプリの [プロパティ] セクションに移動します。
1. プロビジョニング アプリの [プロパティ] セクションで、"*オブジェクト ID*" フィールドに関連付けられている GUID 値をコピーします。 この値はお使いのアプリの **ServicePrincipalId** とも呼ばれ、Microsoft Graph Explorer の操作で使用されます。

   ![Workday アプリのサービス プリンシパル ID](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>手順 2:Microsoft Graph Explorer にサインインします

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を起動します
1. [Sign-In with Microsoft]\(Microsoft を使用してサインイン\) ボタンをクリックし、Azure AD 全体管理者またはアプリ管理者の資格情報を使用してサインインします。

    ![Microsoft Graph のサインイン](./media/export-import-provisioning-configuration/wd_export_02.png)

1. サインインに成功すると、左側のウィンドウにユーザー アカウントの詳細が表示されます。

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>手順 3:プロビジョニング アプリのプロビジョニング ジョブ ID を取得します

Microsoft Graph Explorer で、[servicePrincipalId] を「[手順 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)」から抽出した **ServicePrincipalId** に置き換え、次の GET クエリを実行します。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

次の応答を受け取ります。 応答に存在する "id attribute" をコピーします。 この値は **ProvisioningJobId** であり、基になるスキーマ メタデータを取得するために使用されます。

   [![プロビジョニング ジョブ ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>手順 4:プロビジョニング スキーマをダウンロードする

Microsoft Graph Explorer で、[servicePrincipalId] と [ProvisioningJobId] を、前の手順で取得した ServicePrincipalId と ProvisioningJobId に置き換えて、次の GET クエリを実行します。

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

応答から JSON オブジェクトをコピーしてファイルに保存し、スキーマのバックアップを作成します。

### <a name="step-5-import-the-provisioning-schema"></a>手順 5:プロビジョニング スキーマをインポートする

> [!CAUTION]
> Azure portal を使用して変更できない構成用にスキーマを変更する必要がある場合、または有効で機能しているスキーマを使用して以前にバックアップしたファイルから構成を復元する必要がある場合にのみ、この手順を実行します。

Microsoft Graph Explorer で、[servicePrincipalId] と [ProvisioningJobId] を、前の手順で取得した ServicePrincipalId と ProvisioningJobId に置き換えて、次の PUT クエリを構成します。

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

[Request Body]\(要求本文\) タブで、JSON スキーマ ファイルの内容をコピーします。

   [![要求本文](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

[Request Headers]\(要求ヘッダー\) タブで、値が "application/json" の Content-Type ヘッダー属性を追加します。

   [![要求ヘッダー](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

**[クエリの実行]** を選択して新しいスキーマをインポートします。