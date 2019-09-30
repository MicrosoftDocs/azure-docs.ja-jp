---
title: Dynamics 365 for Customer Engagement リード管理 | Azure Marketplace
description: Dynamics 365 for Customer Engagement のリード管理を構成します。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 03d67249ef1d74844a7e9019e90f6331ecfdf0c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102970"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Dynamics 365 for Customer Engagement のリード管理の構成

この記事では、Dynamics 365 for Customer Engagement (以前の Dynamics CRM Online) を設定する方法について説明します。マーケットプレース オファーからのリードを処理するには、[こちら](https://docs.microsoft.com/dynamics365/customer-engagement/admin/switch-dynamics-crm-online-dynamics-365)で変更の詳細をご覧ください。 

>[!Note]
>これらの手順は、Microsoft ホステッド クラウドの Dynamics 365 for Customer Engagement 環境に固有です。 Dynamics オンプレミス環境への直接接続は現在サポートされていません。リードを受け取るように [https エンドポイント](./commercial-marketplace-lead-management-instructions-https.md)や [Azure テーブル](./commercial-marketplace-lead-management-instructions-azure-table.md)を構成するなど、リードを受け取るためのオプションが他にもあります。

## <a name="prerequisites"></a>前提条件

この記事の手順を実行するには、次のユーザーのアクセス許可が必要です。

* ソリューションをインストールしてこれらの手順に従うには、Dynamics 365 for Customer Engagement インスタンスの管理者である必要があります。
* マーケットプレース オファーからリードを送信するために使用するリード サービス用の新しいサービス アカウントを作成するには、テナント管理者である必要があります。
* Office 365 管理ポータルにアクセスできる必要があります。
* Azure portal にアクセスできる必要があります。

## <a name="install-the-solution"></a>ソリューションをインストールする

1.  [Microsoft Marketplace Lead Writer ソリューション](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)をダウンロードし、コンピューターにローカルに保存します。

2.  Dynamics インスタンスの URL (`https://tenant.crm.dynamics.com` など) に移動して、Dynamics 365 for Customer Engagement を開きます。

3.  上部のナビゲーション バーで歯車アイコンと **[詳細設定]** を選択して、[設定] にアクセスします。
 
    ![Dynamics - [詳細設定]](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  [設定] ページが表示されたら、上部のナビゲーション バーから [設定] メニューにアクセスし、 **[ソリューション]** を選択します。

    >[!Note]
    >次の画面キャプチャに示されているオプションが見つからない場合は、先に進むために必要なアクセス許可を持っていません。 Dynamics 365 for Customer Engagement インスタンス上で管理者に連絡してください。

    ![Dynamics 365 - [ソリューション]](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. [ソリューション] ページで、 **[インポート]** を選択し、手順 1. でダウンロードした "*Microsoft Marketplace Lead Writer*" ソリューションを保存した場所に移動します。

    ![Dynamics 365 for Customer Engagement - [インポート]](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. ソリューションのインポート ウィザードに従って、ソリューションのインポートを完了します。

## <a name="configure-user-permissions"></a>ユーザーのアクセス許可を構成する

リードを Dynamics 365 for Customer Engagement インスタンスに書き込むには、サービス アカウントをマイクロソフトと共有し、アカウントのアクセス許可を構成する必要があります。

次の手順を使用して、サービス アカウントを作成し、アクセス許可を割り当てます。 **Azure Active Directory** または **Office 365** を使用できます。

>[!Note]
>選択した認証オプションに応じて、選択した内容に基づいて対応する指示に進むことができます。 「[Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory)」または「[Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365)」をご覧ください。

### <a name="azure-active-directory"></a>Azure Active Directory

ユーザー名/パスワードの更新なしでリードの取得を続行できるというメリットが追加されるため、このオプションをお勧めします。 Azure Active Directory オプションを使用するには、Active Directory アプリケーションのアプリ ID、アプリケーション キー、およびディレクトリ ID を指定します。

次の手順を使用して、Dynamics 365 for Customer Engagement 向けの Azure Active Directory を構成します。

1. [Azure portal](https://portal.azure.com/) にサインインし、左側のナビゲーションから [Azure Active Directory サービス] を選択します。

2. Azure Active Directory の左側のナビゲーションで **[プロパティ]** を選択し、そのページ上の **[ディレクトリ ID]** の値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*ディレクトリ ID*" 値です。

    ![Azure Active Directory - [プロパティ]](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. Azure Active Directory の左側のナビゲーションで、 **[アプリの登録]** を選択し、そのページで **[新規登録]** を選択します。
4. アプリケーションの名前を入力します。 わかりやすいアプリケーション名を指定します。
5. [サポートされているアカウントの種類] で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
6. [リダイレクト URI] で、 **[Web]** を選択し、URI (`https://contosoapp1/auth` など) を指定します。 
7. **[登録]** を選択します。

    ![アプリケーションを登録する](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. アプリケーションが登録されたので、アプリケーションの [概要] ページにアクセスし、そのページにある **[アプリケーション (クライアント) ID]** の値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータルと Dynamics 内で提供する必要がある "*アプリケーション (クライアント) ID*" 値です。

    ![アプリケーション (クライアント) ID](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. アプリの左側のナビゲーションで **[証明書とシークレット]** を選択し、そのページで **[新しいクライアント シークレット]** を選択します。 クライアント シークレットのわかりやすい説明を入力し、[有効期限] の下で **[なし]** オプションを選択します。 **[追加]** を選択して、クライアント シークレットを作成します。

    ![アプリケーション - 証明書とシークレット](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. クライアント シークレットが正常に作成されたらすぐに、**クライアント シークレットの値をコピー**します。 ページから移動した後は値を取得できなくなります。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*クライアント シークレット*" 値です。 
11. アプリの左側のナビゲーションで **[API のアクセス許可]** を選択し、 **[アクセス許可の追加]** を選択します。
12. [Microsoft API] を選択してから、API として **[Dynamics CRM]** を選択します。
13. " *[アプリケーションに必要なアクセス許可の種類]* " で、 **[委任されたアクセス許可]** が選択されていることを確認します。 [**user_impersonation** "*Access Common Data Service as organization users*"]\(user_impersonation 組織ユーザーとして Common Data Service にアクセス\) のアクセス許可を確認します。 **[アクセス許可の追加]** を選択します.

    ![Add permissions](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Azure portal 上で手順 1-13 を完了したら、URL (`https://tenant.crm.dynamics.com` など) に移動して、Dynamics 365 for Customer Engagement インスタンスに移動します。
15. 上部のナビゲーション バーで歯車アイコンと **[詳細設定]** を選択して、[設定] にアクセスします。
16. [設定] ページが表示されたら、上部のナビゲーション バーから [設定] メニューにアクセスし、 **[セキュリティ]** を選択します。
17. [セキュリティ] ページで、 **[ユーザー]** を選択します。  [ユーザー] ページで、[有効になっているユーザー] ドロップダウンを選択し、 **[アプリケーション ユーザー]** に切り替えます。
18. **[新規]** を選択して新しいユーザーを作成します。 

    ![新しいユーザーを作成する](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. **[新しいユーザー]** で、[ユーザー:アプリケーション ユーザー] が選択されていることを確認します。 この接続で使用するユーザーのユーザー名、フル ネーム、メール アドレスを指定します。 また、手順 8. で Azure portal 内で作成したアプリの**アプリケーション ID** を貼り付けます。 **[保存して閉じる]** を選択して、ユーザーの追加を完了します。

    ![新しいユーザー](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. この記事の「セキュリティの設定」に移動して、このユーザー用の接続の構成を完了します。

### <a name="office-365"></a>Office 365

Azure Active Directory を使用したくない場合は、*Microsoft 365 管理センター*で新しいユーザーを登録できます。 潜在顧客を取得し続けるには、90 日ごとにユーザー名/パスワードを更新する必要があります。

次の手順を使用して、Dynamics 365 for Customer Engagement 向けの Office 365 を構成します。

1. [Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。

2. **[ユーザーの追加]** を選択します。

    ![Microsoft 365 管理センター - ユーザーの追加](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. 潜在顧客ライター サービス用の新しいユーザーを作成します。 次の設定を構成します。

    * ユーザー名を指定します
    * パスワードを指定し、[Make this user change their password when they first sign in]\(このユーザーが最初にサインインしたときに自分のパスワードを変更するようにする\) オプションをオフにします。
    * ユーザーのロールとして [ユーザー (管理者のアクセスなし)] を選択します。
    * 次の画面キャプチャに表示される製品ライセンスとして、"Dynamics 365 Customer Engagement plan" を選択します。 選択したライセンスに対して課金されます。 

これらの値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある "*ユーザー名とパスワード*" です。

![[Microsoft 365 管理センター] - [新しいユーザー]](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>セキュリティの設定

最後の手順は、潜在顧客を書き込むために作成したユーザーを有効にすることです。

1. Dynamics インスタンスの URL (`https://tenant.crm.dynamics.com` など) に移動して、Dynamics 365 for Customer Engagement を開きます。
2. 上部のナビゲーション バーで歯車アイコンと **[詳細設定]** を選択して、[設定] にアクセスします。
3. [設定] ページが表示されたら、上部のナビゲーション バーから [設定] メニューにアクセスし、 **[セキュリティ]** を選択します。
4. [セキュリティ] ページで、 **[ユーザー]** を選択し、このドキュメントの「ユーザーのアクセス許可を構成する」セクションで作成したユーザーを選択してから、 **[ロールの管理]** を選択します。 

    ![ロールの管理](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. ロール名 "Microsoft Marketplace Lead Writer" を検索し、それを選択してユーザーにロールを割り当てます。

    ![ユーザー ロールの管理](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >このロールは、インポートしたソリューションによって作成され、潜在顧客を書き込み、互換性確保のためにソリューションのバージョンを追跡するためだけのアクセス許可を持っています。

6. [セキュリティ] ページに戻り、 **[セキュリティ ロール]** を選択します。 ロール "Microsoft Marketplace Lead Writer" を検索し、それを選択します。

    ![セキュリティ ロール](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. セキュリティ ロールに入った後で、 **[コア レコード]** タブを選択します。"ユーザー エンティティの UI 設定" エンティティを検索し、対応する各円を 1 回をクリックして、そのエンティティのユーザー (黄色い 1/4 円) に対して作成、読み取り、書き込みアクセス許可を有効にします。

    ![Microsoft Marketplace Lead Writer - [コア レコード]](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. 次に、 **[カスタマイズ]** タブに移動します。"システム ジョブ" エンティティを検索し、対応する各円を 4 回クリックして、そのエンティティの組織 (緑の塗りつぶし) に対して読み取り、書き込み、追加先アクセス許可を有効にします。

    ![Microsoft Marketplace Lead Writer - [カスタマイズ]](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **保存して閉じます**。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Dynamics 365 For Customer Engagement にリードを送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
2. [リード管理] セクションで **[接続]** を選択します。

    ![リード管理への接続](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. [接続の詳細] ポップアップ ウィンドウで、[リードのターゲット] として **[Dynamics 365 for Customer Engagement]** を選択します

    ![[接続の詳細] - [リードのターゲット]](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. `https://contoso.crm4.dynamics.com` などの **[Dynamics 365 インスタンス URL]** を指定します。
5. **[認証]** の方法として、[Azure Active Directory] または [Office 365] を選択します。 
6. [Azure Active Directory] を選択した場合は、 **[アプリケーション (クライアント) ID]** (例: `23456052-aaaa-bbbb-8662-1234df56788f`)、 **[ディレクトリ ID]** (例: `12345678-8af1-4asf-1234-12234d01db47`)、 **[クライアント シークレット]** (例: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`) を指定します。

    ![接続の詳細 - Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Office 365 を選択した場合は、 **[ユーザー名]** (例: `contoso@contoso.onmicrosoft.com`) と [パスワード] (例:`P@ssw0rd`) を指定します。

    ![[接続の詳細] - [ユーザー名]](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。
