---
title: Azure Active Directory レポート API の前提条件 | Microsoft Docs
description: Azure AD Reporting API にアクセスするための前提条件の詳細
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348740"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory レポート API にアクセスするための前提条件

[Azure Active Directory (Azure AD) レポート API](./concept-reporting-api.md) は、一連の REST ベースの API を使用してデータへのプログラムによるアクセスを提供します。 これらの API は、プログラミング言語やツールから呼び出すことができます。

Reporting API は、 [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) を使用して Web API へのアクセスを承認します。

レポート API へのアクセスを準備するには、次が必要です。

1. [ロールの割り当て](#assign-roles)
2. [ライセンスの要件](#license-requirements)
3. [アプリケーションを登録する](#register-an-application)
4. [アクセス許可を付与する](#grant-permissions)
5. [構成設定を収集する](#gather-configuration-settings)

## <a name="assign-roles"></a>ロールを割り当てる

API を介してレポート データにアクセスするには、次のいずれかのロールが割り当てられている必要があります。

- セキュリティ閲覧者

- セキュリティ管理者

- グローバル管理者

## <a name="license-requirements"></a>ライセンスの要件

テナントのサインイン レポートにアクセスするには、Azure AD テナントに Azure AD Premium ライセンスが関連付けられている必要があります。 任意の Azure AD テナントのサインイン レポートにアクセスするには Azure AD Premium P1 (以上) のライセンスが必要です。 また、ディレクトリ タイプが Azure AD B2C の場合は、追加のライセンス要件なしで、API を使用してサインイン レポートにアクセスできます。 


## <a name="register-an-application"></a>アプリケーションを登録する

スクリプトを使用してレポート API にアクセスする場合でも、登録する必要があります。 登録することで、承認の呼び出しと、コードによるトークンの受信に必要な **アプリケーション ID** が得られます。

Azure AD Reporting API にアクセスするようにディレクトリを構成するには、Azure AD テナントの **グローバル管理者** ディレクトリ ロールのメンバーでもある Azure 管理者アカウントで [Azure portal](https://portal.azure.com) にサインインする必要があります。

> [!IMPORTANT]
> このような管理者特権を備えた資格情報で実行されるアプリケーションは、きわめて強力です。アプリケーションの IDとシークレットの資格情報は必ず安全な場所に保管してください。
> 

**Azure AD アプリケーションを登録するには:**

1. [Azure portal](https://portal.azure.com) で、左側のナビゲーション ウィンドウから **[Azure Active Directory]** を選択します。
   
    ![スクリーンショットには、Azure portal メニューで選択されている [Azure Active Directory] が示されています。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **[Azure Active Directory]** ページで、 **[アプリの登録]** を選択します。

    ![スクリーンショットには、[管理] メニューで選択されている [アプリの登録] が示されています。](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. **[アプリの登録]** ページから、 **[New registration]\(新しい登録\)** を選択します。

    ![スクリーンショットには、選択されている [New registration]\(新しい登録\) が示されています。](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. **[アプリケーションの登録]** ページ:

    ![スクリーンショットには、このステップの値を入力できる [アプリケーションの登録] ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. **[名前]** ボックスに、「`Reporting API application`」と入力します。

    b. **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。

    c. **[リダイレクト URL]** で **[Web]** を選択し、テキスト ボックスに「`https://localhost`」と入力します。

    d. **[登録]** を選択します。 


## <a name="grant-permissions"></a>[アクセス許可の付与] 

アクセスする API によっては、アプリに次のアクセス許可を付与する必要があります。  

| API | 権限 |
| --- | --- |
| Windows Azure Active Directory | ディレクトリ データの読み取り |
| Microsoft Graph | すべての監査ログ データの読み取り |

![スクリーンショットには、[A P I のアクセス許可] ウィンドウで [アクセス許可の追加] を選択できる場所が示されています。](./media/howto-configure-prerequisites-for-reporting-api/36.png)

次のセクションでは、両方の API の手順を示します。 どちらかの API にアクセスしない場合は、関連する手順を省略できます。

**API を使用するためのアクセス許可をアプリケーションに付与するには:**


1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** の順に選択します。 

    ![スクリーンショットには、[アクセス許可の追加] を選択できる [A P I アクセス許可] ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. **[API アクセス許可の要求] ページ** で、 **[サポートされているレガシ API]** の **[Azure Active Directory Graph]** を見つけます。 

    ![スクリーンショットには、Azure Active Directory グラフを選択できる [A P I アクセス許可の要求] ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. **[必要なアクセス許可]** ページで、 **[アプリケーションのアクセス許可]** を選択し、 **[ディレクトリ]** を展開して、 **[Directory.ReadAll]** チェック ボックスをオンにします。  **[アクセス許可の追加]** を選択します.

    ![スクリーンショットには、アプリケーションのアクセス許可を選択できる [A P I アクセス許可の要求] ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. **[Reporting API Application - API Permissions]\(レポート API アプリケーション - API のアクセス許可\)** ページで、 **[Grant admin consent]\(管理者の同意の付与\)** を選択します。 

    ![スクリーンショットには、[Grant admin consent]\(管理者の同意の付与\) を選択できる [Reporting A P I Application]\(レポート API アプリケーション\) ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. 注:**Microsoft Graph** は、API の登録時に既定で追加されます。

    ![スクリーンショットには、[アクセス許可の追加] を選択できる [A P I アクセス許可] ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>構成設定を収集する 

このセクションでは、ディレクトリから次の設定を取得する方法を示しています。

- ドメイン名
- クライアント ID
- クライアント シークレット

Reporting API への呼び出しを構成するときに、これらの値が必要です。 

### <a name="get-your-domain-name"></a>ドメイン名を取得する

**ドメイン名を取得するには:**

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** を選択します。
   
    ![スクリーンショットには、Azure portal メニューで選択されている [Azure Active Directory] が示されています。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **[Azure Active Directory]** ページで、 **[カスタム ドメイン名]** を選択します。

    ![スクリーンショットには、Azure Active Directory で選択されたカスタム ドメイン名が示されています。](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. ドメインの一覧からドメイン名をコピーします。


### <a name="get-your-applications-client-id"></a>アプリケーションのクライアント ID を取得する

**アプリケーションのクライアント ID を取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** をクリックします。
   
    ![スクリーンショットには、Azure portal メニューで選択されている [Azure Active Directory] が示されています。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. **[アプリの登録]** ページから、アプリケーションを選択します。

3. アプリケーション ページから、 **[アプリケーション ID]** へ移動して、 **[Click to copy]\(クリックしてコピー\)** を選択します。

    ![スクリーンショットには、アプリケーション I D をコピーできる [Reporting A P I Application]\(レポート API アプリケーション\) ページのスクリーンショットが示されています。](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>アプリケーションのクライアント シークレットを取得する
 API を使用して監査ログへのアクセスまたはサインインを試みるときにエラーが発生しないようにします。

**アプリケーションのクライアント シークレットを取得するには:**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** をクリックします。
   
    ![スクリーンショットには、Azure portal メニューで選択されている [Azure Active Directory] が示されています。](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  **[アプリの登録]** ページから、アプリケーションを選択します。

3.  **[API Application]\(API アプリケーション\)** ページで **[Certificates and Secrets]\(証明書とシークレット\)** を選択し、 **[Client Secrets]\(クライアント シークレット\)** セクションで **[+ New Client Secret]\(+ 新しいクライアント シークレット\)** をクリックします。 

    ![スクリーンショットには、クライアント シークレットを追加できる [Certificates and Secrets]\(証明書とシークレット\) ページが示されています。](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. **[クライアント シークレットの追加]** ページで、以下を追加します。

    a. **[説明]** ボックスに、「`Reporting API`」と入力します。

    b. **[有効期限]** として、 **[2 年]** を選択します。

    c. **[保存]** をクリックします。

    d. キー値をコピーします。

## <a name="troubleshoot-errors-in-the-reporting-api"></a>レポート API でのエラーのトラブルシューティング

このセクションでは、Microsoft Graph API を使用してアクティビティ レポートにアクセスする際に生じる可能性のある一般的なエラー メッセージと、その解決手順を示します。

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>エラー:Microsoft Graph からユーザー ロールを取得できませんでした

 Graph エクスプローラーを使用してサインインするときにエラーが発生しないようにするには、Graph エクスプローラーの UI の両方のサインイン ボタンを使用してアカウントにサインインします。 

![Graph エクスプローラー](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>エラー:Microsoft Graph から Premium ライセンスを確認できませんでした 

Graph エクスプローラーを使用してサインインにアクセスしようとしたときにこのようなエラー メッセージが表示された場合は、左側のナビゲーションのアカウントの下にある **[アクセス許可の変更]** を選択し、 **[Tasks.ReadWrite]** と **[Directory.Read.All]** を選びます。 

![アクセス許可の変更 UI](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>エラー:テナントが B2C ではないか、またはテナントに Premium ライセンスがありません

サインイン レポートへのアクセスには、Azure Active Directory Premium 1 (P1) ライセンスが必要です。 サインインへのアクセス中にこのようなエラー メッセージが表示された場合は、テナントに Azure AD P1 ライセンスがあることを確認してください。

### <a name="error-the-allowed-roles-does-not-include-user"></a>エラー:許可されたロールにユーザーが含まれません。 

 API を使用して監査ログへのアクセスまたはサインインを試みるときにエラーが発生しないようにします。 アカウントが Azure Active Directory テナントの **セキュリティ閲覧者** ロールまたは **レポート閲覧者** ロールの一部であることを確認します。

### <a name="error-application-missing-aad-read-directory-data-permission"></a>エラー:アプリケーションに AAD の 'ディレクトリ データの読み取り' アクセス許可がありません 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>エラー:アプリケーションに Microsoft Graph API の 'すべての監査ログ データの読み取り' アクセス許可がありません

「[Azure Active Directory レポート API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)」の手順に従って、アプリケーションが適切なアクセス許可のセットを使用して実行されていることを確認してください。 

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory レポート API と証明書を使用してデータを取得します](tutorial-access-api-with-certificates.md)
* [監査 API リファレンス](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [サインイン アクティビティ レポート API リファレンス](/graph/api/resources/signin?view=graph-rest-beta)