---
title: Dynamics 365 Customer Engagement のリード管理 - Microsoft 商用マーケットプレース
description: Microsoft AppSource および Azure Marketplace からのリードを管理するように Dynamics 365 Customer Engagement を設定する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: d996a2e0732ad9b4da104265710970e04cdbd368
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98199778"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Dynamics 365 Customer Engagement のリード管理を構成する

この記事では、Dynamics 365 Customer Engagement (旧称は Dynamics CRM Online) を設定する方法について説明します。 コマーシャル マーケットプレース オファーのセールス リードを処理するには、「[Customer Engagement と SharePoint Online でのサーバーベース認証の構成](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online)」の変更の詳細を参照してください。

>[!NOTE]
>これらの手順は、Dynamics 365 Customer Engagement 向け Microsoft ホステッド クラウド環境に固有です。 Dynamics オンプレミス環境への直接接続は、現在サポートされていません。 [HTTPS エンドポイント](./commercial-marketplace-lead-management-instructions-https.md)や [Azure テーブル](./commercial-marketplace-lead-management-instructions-azure-table.md)の構成など、他にもリードを受信するためのオプションがあります。

## <a name="prerequisites"></a>前提条件

この記事の手順を行うには、次のユーザーのアクセス許可が必要です。

* ソリューションをインストールできるようにするための Dynamics 365 Customer Engagement インスタンスに対する管理者権限。
* 商用マーケットプレースのオファーからリードを送信するために使用されるリード サービス用の新しいサービス アカウントを作成するためのテナント管理者権限。
* 管理ポータルへのアクセス。
* Azure portal へのアクセス。

## <a name="install-the-solution"></a>ソリューションをインストールする

1. [Microsoft Marketplace Lead Writer ソリューション](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)をダウンロードし、コンピューターにローカルに保存します。

1. Dynamics インスタンスの URL (`https://tenant.crm.dynamics.com` など) に移動して、Dynamics 365 Customer Engagement を開きます。

1. 上部のバーにある歯車アイコンを選択し、 **[詳細設定]** を選択します。

    ![Dynamics 365 の [詳細設定] メニュー項目](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. **[設定]** ページで、上部のバーにある **[設定]** メニューを開き、 **[ソリューション]** を選択します。

    >[!NOTE]
    >次の画面に示されているオプションが表示されない場合は、先に進むために必要なアクセス許可を持っていません。 Dynamics 365 Customer Engagement インスタンスの管理者にお問い合わせください。

    ![Dynamics 365 の [ソリューション] オプション](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. **[ソリューション]** ページで、 **[インポート]** を選択し、手順 1 でダウンロードした **Microsoft Marketplace Lead Writer** ソリューションを保存した場所に移動します。

    ![[インポート] ボタン](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. ソリューションのインポート ウィザードに従って、ソリューションのインポートを完了します。

## <a name="configure-user-permissions"></a>ユーザーのアクセス許可を構成する

リードを Dynamics 365 Customer Engagement インスタンスに書き込むには、サービス アカウントを Microsoft と共有し、アカウントのアクセス許可を構成する必要があります。

次の手順を使用して、サービス アカウントを作成し、アクセス許可を割り当てます。 Azure Active Directory または Office 365 を使用できます。

>[!NOTE]
>選択した認証オプションに応じて、対応する手順に進みます。 「[Azure Active Directory](#azure-active-directory)」または「[Office 365](#office-365)」をご覧ください。

### <a name="azure-active-directory"></a>Azure Active Directory

リードの獲得を継続するためにユーザー名またはパスワードを更新する必要がないため、このオプションをお勧めします。 Azure Active Directory オプションを使用するには、Active Directory アプリケーションのアプリ ID、アプリケーション キー、およびディレクトリ ID を指定します。

Dynamics 365 Customer Engagement 向けに Azure Active Directory を構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) にサインインします。 左ウィンドウで、 **[Azure Active Directory]** を選択します。

1. **[プロパティ]** を選択し、 **[ディレクトリのプロパティ]** ページの **[ディレクトリ ID]** の値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要があります。

    ![Azure Active Directory の [プロパティ] メニュー項目](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Azure Active Directory の左側のペインで、 **[アプリの登録]** を選択し、そのページで **[新規登録]** を選択します。
1. アプリケーション名としてわかりやすい名前を入力します。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント]** を選択します。
1. **[リダイレクト URI (省略可能)]** で、 **[Web]** を選択し、URL (例: `https://contosoapp1/auth`) を入力します。
1. **[登録]** を選択します。

    ![[アプリケーションの登録] ページ](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. アプリケーションが登録されたので、アプリケーションの [概要] ページにアクセスします。 そのページの **[アプリケーション (クライアント) ID]** の値をコピーします。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータルと Dynamics 365 内で提供する必要があります。

    ![[アプリケーション (クライアント) ID] ボックス](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. アプリの左側のペインで **[証明書とシークレット]** を選択し、 **[新しいクライアント シークレット]** ボタンを選択します。 クライアント シークレットのわかりやすい説明を入力し、 **[有効期限]** の下で **[なし]** オプションを選択します。 **[追加]** を選択して、クライアント シークレットを作成します。

    ![[証明書とシークレット] メニュー項目](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. クライアント シークレットが正常に作成されたらすぐに、 **[クライアント シークレット]** の値をコピーします。 ページから移動した後は値を取得できなくなります。 この値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要があります。 
1. アプリの左側のペインで **[API のアクセス許可]** を選択し、 **[+ アクセス許可の追加]** を選択します。
1. **[Microsoft API]** を選択し、API として **[Dynamics CRM]** を選択します。
1. **[アプリケーションに必要なアクセス許可の種類]** で、 **[委任されたアクセス許可]** が選択されていることを確認します。
1. **[アクセス許可]** で、 **[Access Common Data Service as organization users]\(組織ユーザーとして Common Data Service にアクセスする\)** の **[user_impersonation]** チェックボックスをオンにします。 **[アクセス許可の追加]** を選択します。

    ![[アクセス許可の追加] ボタン](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Azure portal で手順 1 から 14 までを完了したら、URL (たとえば、`https://tenant.crm.dynamics.com`) にアクセスして、Dynamics 365 Customer Engagement インスタンに移動します。
1. 上部のバーにある歯車アイコンを選択し、 **[詳細設定]** を選択します。
1. **[設定]** ページで、上部のバーにある **[設定]** メニューを開き、 **[セキュリティ]** を選択します。
1. **[セキュリティ]** ページで、 **[ユーザー]** を選択します。 **[ユーザー]** ページで、 **[有効なユーザー]** ドロップダウンを選択し、 **[アプリケーション ユーザー]** を選択します。
1. **[新規]** を選択して新しいユーザーを作成します。

    ![新しいユーザーを作成する](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. **[新規ユーザー]** ペインで、 **[ユーザー: アプリケーション ユーザー]** が選択されていることを確認します。 この接続で使用するユーザーのユーザー名、フル ネーム、メール アドレスを指定します。 また、手順 8. で Azure portal 内で作成したアプリの **アプリケーション ID** を貼り付けます。 **[保存して閉じる]** を選択して、ユーザーの追加を完了します。

    ![[新規ユーザー] ペイン](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. この記事の「セキュリティの設定」に移動して、このユーザー用の接続の構成を完了します。

### <a name="office-365"></a>Office 365

Azure Active Directory を使用しない場合は、Microsoft 365 管理センターで新しいユーザーを登録できます。 リードを取得し続けるには、90 日ごとにユーザー名とパスワードを更新する必要があります。

Dynamics 365 Customer Engagement 向けに Office 365 を構成するには、次の手順を行います。

1. [Microsoft 365 管理センター](https://admin.microsoft.com)にサインインします。

1. **[ユーザーの追加]** を選択します。

    ![Microsoft 365 管理センターの [ユーザーの追加] オプション](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. 潜在顧客ライター サービス用の新しいユーザーを作成します。 次の設定を構成します。

    * ユーザー名を入力します。
    * パスワードを入力し、 **[ユーザーが初回サインイン時にパスワードを変更する]** オプションをオフにします。
    * ユーザーのロールとして **[ユーザー (管理者のアクセスなし)]** を選択します。
    * 次の画面に示すように、製品ライセンスとして **[Dynamics 365 Customer Engagement プラン]** を選択します。 選択したライセンスに対して課金されます。 

これらの値を保存します。これは、マーケットプレース オファーのリードを受け取るために発行ポータル内で提供する必要がある **[ユーザー名]** と **[パスワード]** の値です。

![Microsoft 365 管理センターの[新しいユーザー] ペイン](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>セキュリティの設定

最後の手順は、リードを書き込むために作成したユーザーを有効にすることです。

1. Dynamics インスタンスの URL (`https://tenant.crm.dynamics.com` など) に移動して、Dynamics 365 Customer Engagement を開きます。
1. 上部のバーにある歯車アイコンを選択し、 **[詳細設定]** を選択します。
1. **[設定]** ページで、上部のバーにある **[設定]** メニューを開き、 **[セキュリティ]** を選択します。
1. **[セキュリティ]** ページで、 **[ユーザー]** を選択し、このドキュメントの「ユーザーのアクセス許可を構成する」セクションで作成したユーザーを選択します。 次に、 **[ロールの管理]** を選択します。

    ![[ロールの管理] タブ](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. ロール名 **Microsoft Marketplace Lead Writer** を検索し、それを選択してユーザーにロールを割り当てます。

    ![[ユーザー ロールの管理] ペイン](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >このロールは、インポートしたソリューションによって作成され、潜在顧客を書き込み、互換性確保のためにソリューションのバージョンを追跡するためだけのアクセス許可を持っています。

1. **[セキュリティ]** ページに移動し、 **[セキュリティ ロール]** を選択します。 ロール **Microsoft Marketplace Lead Writer** を検索し、それを選択します。

    ![[セキュリティ ロール] ペイン](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. [セキュリティ ロール] で、 **[コア レコード]** タブを選択します。 **[ユーザー エンティティの UI 設定]** 項目を検索します。 対応するオプション ボタンを選択することで、そのエンティティのユーザー (黄色い 1/4 の円) に対して作成、読み取り、書き込みのアクセス許可を有効にします。

    ![Microsoft Marketplace Lead Writer の [コア レコード] タブ](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. **[カスタマイズ]** タブで、 **[システム ジョブ]** 項目を検索します。 対応するオプション ボタンを選択することで、そのエンティティの組織 (緑の塗りつぶしのオプション ボタン) に対して読み取り、書き込み、AppendTo のアクセス許可を有効にします。

    ![Microsoft Marketplace Lead Writer の [カスタマイズ] タブ](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. **[保存して閉じる]** を選択します。

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Dynamics 365 Customer Engagement にリードを送信するようにオファーを構成する

発行ポータルでオファーのリード管理情報を構成するには、次の手順を行います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
1. **[潜在顧客]** セクションで、 **[接続]** を選択します。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="[潜在顧客]":::

1. [接続の詳細] ポップアップ ウィンドウで、[リードのターゲット] として **[Dynamics 365 Customer Engagement]** を選択します。

    ![[リードのターゲット] ボックス](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Dynamics 365 インスタンスの **URL**(例: `https://contoso.crm4.dynamics.com`) を入力します。

1. **[認証]** の方法として、[Azure Active Directory] または [Office 365] を選択します。
1. **[Azure Active Directory]** を選択した場合は、 **[アプリケーション (クライアント) ID]** (例: `23456052-aaaa-bbbb-8662-1234df56788f`)、 **[ディレクトリ ID]** (例: `12345678-8af1-4asf-1234-12234d01db47`)、 **[クライアント シークレット]** (例: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`) を入力します。

    ![Azure Active Directory による認証を選択](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. **[Office 365]** を選択した場合は、 **[ユーザー名]** (例: `contoso@contoso.onmicrosoft.com`) と **[パスワード]** (例: `P@ssw0rd`) を入力します。

    ![Office 365 の [ユーザー名] ボックス](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. **[連絡先の電子メール]** で、新しいリードを受信したときに、メール通知を受け取る必要がある社内のユーザーのメール アドレスを入力します。 セミコロンで区切ると、複数のメール アドレスを指定できます。
1. **[OK]** を選択します。

リードのターゲットに正常に接続されたことを確認するには、 **[検証]** ボタンを選択します。 成功した場合は、リードのターゲットにテスト リードが表示されます。

![[連絡先の電子メール] ボックス](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。
