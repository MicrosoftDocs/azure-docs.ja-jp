---
title: Dynamics CRM | Microsoft Docs
description: Dynamics CRM の潜在顧客管理を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: a1398d172a5c578ec3c0f16627eadd1da3fd1e45
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58437619"
---
# <a name="configure-lead-management-for-dynamics-crm-online"></a>Dynamics CRM Online の潜在顧客管理を構成する

この記事では、潜在顧客を処理するための Dynamics CRM Online の設定方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のユーザーのアクセス許可が必要です。
- ソリューションをインストールするには、Dynamics CRM Online インスタンスの管理者である必要があります。
- 潜在顧客サービスで新しいサービス アカウントを作成するにはテナント管理者である必要があります。

<a name="install-the-solution"></a>ソリューションをインストールする
--------------------

1.  [Microsoft Marketplace Lead Writer ソリューション](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)をダウンロードし、ローカルに保存します。

2.  Dynamics CRM Online を開き、[設定] に移動します。
    >[!NOTE]
    >次の画面キャプチャに示されているオプションが見つからない場合は、必要なアクセス許可を持っていません。
 
       ![Dynamics の [設定] ビュー](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.  **[インポート]** を選択し、手順 1. でダウンロードしたソリューションを選択します。
 
    ![Dynamics の [インポート] オプション](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.  ソリューションのインストールを終えます。

## <a name="configure-user-permissions"></a>ユーザーのアクセス許可を構成する

潜在顧客を Dynamics CRM インスタンスに書き込むには、サービス アカウントを共有し、アカウントのアクセス許可を構成する必要があります。

次の手順を使用して、サービス アカウントを作成し、アクセス許可を割り当てます。 **Azure Active Directory** または **Office 365** を使用できます。

### <a name="azure-active-directory"></a>Azure Active Directory

ユーザー名/パスワードの更新なしで潜在顧客の取得を続行できるというメリットが追加されるため、このオプションをお勧めします。 Azure Active Directory オプションを使用するには、Active Directory アプリケーションのアプリ ID、アプリケーション キー、およびディレクトリ ID を指定します。

次の手順を使用して、Dynamics CRM 向けの Azure Active Directory を構成します。

1.  [Azure portal](https://portal.azure.com/) にサインインし、[Azure Active Directory サービス] を選択します。

2.  **[プロパティ]** を選択し、**[ディレクトリ ID]** をコピーします。Cloud パートナー ポータルで使用する必要があるテナント アカウントはこれによって識別されます。

    ![ディレクトリ ID を取得する](./media/cloud-partner-portal-lead-management-instructions-dynamics/directoryid.png)

3.  **[アプリの登録]** を選択し、**[新しいアプリケーションの登録]** を選択します。
4.  アプリケーションの名前を入力します。
5.  [種類] では、**[Web アプリ/API]** を選択します。
6.  URL を指定します。 このフィールドは潜在顧客では必要ありませんが、アプリケーションを作成するために必要です。
7. **作成**を選択します。
8.  これでアプリケーションが登録されたので、**[プロパティ]** を選択し、**[アプリケーション ID のコピー]** を選択します。この接続情報は、Cloud パートナー ポータルで使用します。
9.  [プロパティ] で、アプリケーションを [マルチテナント] として設定し、**[保存]** を選択します。

10. **[キー]** を選択し、[期間] に *[無期限]* が設定された新しいキーを作成します。 **[保存]** を選択してキーを作成します。 
11. [キー] メニューで、**[キー値のコピー]** を選択します。 Cloud パートナー ポータルで必要になるため、この値のコピーを保存しておきます。
    
    ![Dynamics の登録済みキー](./media/cloud-partner-portal-lead-management-instructions-dynamics/registerkeys.png)
    
12. **[必要なアクセス許可]** を選択し、**[追加]** を選択します。 
13. 新しい API として **[Dynamics CRM Online]** を選択し、"*組織ユーザーとして CRM Online にアクセスする*" ためのアクセス許可をチェックします。

14. Dynamics CRM で、[ユーザー] に移動し、[有効になっているユーザー] ドロップダウンを選択し、**[アプリケーション ユーザー]** に切り替えます。
    
    ![アプリケーション ユーザー](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuserfirst.PNG)

15. **[新規]** を選択して新しいユーザーを作成します。 **[ユーザー: アプリケーション ユーザー]** ドロップダウンを選択します。
    
    ![新しいアプリケーション ユーザーを追加する](./media/cloud-partner-portal-lead-management-instructions-dynamics/applicationuser.PNG)

16. **[新しいユーザー]** で、この接続で使用する名前と電子メールを指定します。 Azure portal で作成したアプリの**アプリケーション ID** を貼り付けます。

     ![新しいユーザーを構成する](./media/cloud-partner-portal-lead-management-instructions-dynamics/leadgencreateuser.PNG)

17. この記事の「セキュリティの設定」に移動して、このユーザー用の接続の構成を完了します。

### <a name="office-365"></a>Office 365

Azure Active Directory を使用したくない場合は、*Microsoft 365 管理センター*で新しいユーザーを登録できます。 潜在顧客を取得し続けるには、90 日ごとにユーザー名/パスワードを更新する必要があります。

次の手順を使用して、Dynamics CRM 向けの Office 365 を構成します。

1. [Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。

2. **[管理者]** タイルを選択します。

    ![Office Online 管理者](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3. **[ユーザーの追加]** を選択します。

    ![ユーザーの追加](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4. 潜在顧客ライター サービス用の新しいユーザーを作成します。 次の設定を構成します。

    -   パスワードを指定し、[Make this user change their password when they first sign in]\(このユーザーが最初にサインインしたときに自分のパスワードを変更するようにする\) オプションをオフにします。
    -   ユーザーのロールとして [ユーザー (管理者のアクセスなし)] を選択します。
    -   次の画面キャプチャに示されているように製品ライセンスを選択します。 選択したライセンスに対して課金されます。 このソリューションは Dynamics CRM Online の Basic ライセンスでも動作します。
    
    ![ユーザーのアクセス許可とライセンスを構成する](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

## <a name="security-settings"></a>セキュリティの設定

最後の手順は、潜在顧客を書き込むために作成したユーザーを有効にすることです。

1.  Dynamics CRM Online にサインインします。
2.  **[設定]** の **[セキュリティ]** を選択します。
    
    ![セキュリティの設定](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

3.  **ユーザーのアクセス許可**で作成したユーザーを選択し、**[ユーザー ロールの管理]** を選択します。 **Microsoft Marketplace Lead Writer** をオンにして、ロールを割り当てます。

    ![ユーザー ロールを割り当てる](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)\

    >[!NOTE]
    >このロールは、インポートしたソリューションによって作成され、潜在顧客を書き込み、互換性確保のためにソリューションのバージョンを追跡するためだけのアクセス許可を持っています。

4.  [セキュリティ]で、**[セキュリティ ロール]** を選択し、Microsoft Marketplace Lead Writer のロールを見つけます。
    
    ![Lead Writer のセキュリティを構成する](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)\

5. **[コア レコード]** タブを選択します。ユーザー エンティティ UI の作成/読み取り/書き込みを有効にします。

    ![ユーザーの作成/読み取り/書き込みを有効にする](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)\

## <a name="wrap-up"></a>まとめ

生成されたアカウント情報を Cloud パートナー ポータルに追加することで、潜在顧客を管理するための Dynamics CRM の構成を終了します。 例: 

-   **Azure Active Directory** - **アプリケーション ID** (例: *23456052-aaaa-bbbb-8662-1234df56788f*)、**ディレクトリ ID** (例: *12345678-8af1-4asf-1234-12234d01db47*)、および**アプリケーション キー** (例: *1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=*)。
-   **Office 365** - **URL** (例: *https://contoso.crm4.dynamics.com*)、**ユーザー名** (例: *contoso\@contoso.onmicrosoft.com*)、および**パスワード** (例: *P\@ssw0rd*)。
