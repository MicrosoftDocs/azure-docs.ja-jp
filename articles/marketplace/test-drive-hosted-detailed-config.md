---
title: Azure Marketplace でのホストされた体験版の詳細な構成
description: コマーシャル マーケットプレースでのホストされた体験版の構成手順について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/06/2020
ms.openlocfilehash: 88779f67a2fa9b18f0177a1459b32c672343bb57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462950"
---
# <a name="detailed-configuration-for-hosted-test-drives"></a>ホストされた体験版の詳細な構成

この記事では、Dynamics 365 for Customer Engagement または Dynamics 365 for Operations 用のホストされた体験版を構成する方法について説明します。

## <a name="configure-for-dynamics-365-customer-engagement"></a>Dynamics 365 Customer Engagement 用に構成する

1. [パートナー センター](https://partner.microsoft.com/)にサインインします。
2. 上記のリンクにアクセスできない場合は、[こちら](https://appsource.microsoft.com/partners/list-an-app)でアプリケーションを公開する要求を送信する必要があります。 要求が確認されると、公開プロセスを始めるためのアクセスが許可されます。
3. 既存の **Dynamics 365 for Customer Engagement** オファーを検索するか、新しい **Dynamics 365 for Customer Engagement** オファーを作成します。
4. **[体験版を有効にする]** チェック ボックスをオンにし、 **[体験版の種類]** を選択して (下の説明を参照)、 **[保存]** を選択します。

    [![[体験版を有効にする] チェック ボックスの選択。](media/test-drive/enable-test-drive-check-box.png)](media/test-drive/enable-test-drive-check-box.png#lightbox)

    - **[体験版の種類]** – **[Microsoft Hosted (Dynamics 365 for Customer Engagement & PowerApps)]\(Microsoft ホスト (Dynamics 365 for Customer Engagement & PowerApps)\)** を選択します。 これは、体験版ユーザーのプロビジョニングとプロビジョニング解除を行うサービスが Microsoft によってホストされて管理されることを示します。

5. [こちらの説明](./test-drive-azure-subscription-setup.md)に従って、テナントで体験版ユーザーをプロビジョニングおよびプロビジョニング解除するためのアクセス許可を Microsoft AppSource に付与します。 この手順では、以下で説明する **Azure AD アプリ ID** と **Azure AD アプリ キー** の値を生成します。
6. **[Test drive technical configuration]\(体験版の技術的構成\)** ページで以下のフィールドを設定します。

    [![体験版の技術的構成ページ。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **[同時実行体験版の最大数]** – アクティブな体験版を同時に実行できるユーザーの数。 体験版がアクティブになっている間、各ユーザーは Dynamics のライセンスを消費するので、少なくともここで指定する数の Dynamics ライセンスを体験版ユーザーに使用できるようにします。 3 から 5 をお勧めします。
    - **[体験版の期間]** – ユーザーの体験版がアクティブになっている最大時間数。 この時間が経過すると、ユーザーはテナントからプロビジョニング解除されます。 アプリの複雑さに応じて、2 から 24 時間をお勧めします。 この時間が経過した後でユーザーが再び体験版にアクセスしたい場合、ユーザーはいつでも別の体験版を要求できます。
    - **[インスタンス URL]** – 体験版ユーザーが体験版を始めるときの転送先の URL。 通常これは、アプリとサンプル データがインストールされている Dynamics 365 インスタンスの URL です。 値の例: `https://testdrive.crm.dynamics.com`。
    - **[インスタンスの Web API URL]** – Dynamics 365 インスタンスの Web API URL。 この値を取得するには、Microsoft Dynamics 365 インスタンスにサインインし、 **[設定]**  >  **[カスタマイズ]**  >  **[開発者リソース]**  >  **[インスタンスの Web API]** に移動して、アドレス (URL) をコピーします。 値の例:

        :::image type="content" source="./media/test-drive/sample-web-api-url.png" alt-text="インスタンス Web API の例。":::

    - **[ロール名]** : 体験版用に作成した Dynamics 365 のカスタム セキュリティ ロールの名前、または既存のロールを使用できます。 新しいロールには、Customer Engagement インスタンスにサインインするために必要な最小権限が追加されている必要があります。 [Microsoft Dynamics 365 にサインインするために必要な最小権限](https://community.dynamics.com/crm/b/crminogic/archive/2016/11/24/minimum-privileges-required-to-login-microsoft-dynamics-365)に関するページを参照してください。 これは、体験版の使用中にユーザーに割り当てられるロールです。 値の例: `testdriverole`。
    
        > [!IMPORTANT]
        > セキュリティ グループのチェックが追加されていないことを確認します。 これにより、ユーザーを Customer Engagement インスタンスと同期させることができます。

    - **[Azure Active Directory テナント ID]** – Dynamics 365 インスタンスのための Azure テナントの ID。 この値を取得するには、Azure portal にサインインし、 **[Azure Active Directory]**  >  **[プロパティ]** に移動して、ディレクトリ ID をコピーします。 値の例:172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **[Azure Active Directory テナント名]** – Dynamics 365 インスタンスのための Azure テナントの名前。 「`<tenantname>.onmicrosoft.com`」の形式を使用します。 値の例: `testdrive.onmicrosoft.com`。
    - **[Azure Active Directory アプリケーション ID]** – 手順 5 で作成した Azure Active Directory (AD) アプリの ID。 値の例: `53852862-a2ae-4e43-9461-faa49650a096`。
    - **[Azure Active Directory アプリケーションのクライアント シークレット]** – 手順 5 で作成した Azure AD アプリのシークレット。 値の例: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`。

7. マーケットプレースに掲載する詳細を指定します。 **[言語]** を選択すると、さらに必要なフィールドが表示されます。

    [![Marketplace に掲載する詳細ページ。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **[説明]** – 体験版の概要。 このテキストは、体験版のプロビジョニング中にユーザーに表示されます。 書式設定された内容を提供したい場合、このフィールドでは HTML を使用できます。
    - **[ユーザー マニュアル]** – 体験版のユーザーがアプリの使用方法を理解するのに役立つ PDF のユーザー マニュアル。
    - **[体験版のデモ ビデオ]** – アプリを紹介するビデオ (省略可能)。

## <a name="configure-for-dynamics-365-operations"></a>Dynamics 365 Operations 用に構成する

2. 上記のリンクにアクセスできない場合は、[こちら](https://appsource.microsoft.com/partners/list-an-app)でアプリケーションを公開する要求を送信する必要があります。 要求が確認されると、公開プロセスを始めるためのアクセスが許可されます。
3. 既存の **Dynamics 365 for Operations** オファーを検索するか、新しい **Dynamics 365 for Operations** オファーを作成します。
4. **[体験版を有効にする]** チェック ボックスをオンにし、 **[体験版の種類]** を選択して (下の説明を参照)、 **[保存]** を選択します。

    [![[体験版を有効にする] チェック ボックスをオンにする。](media/test-drive/enable-test-drive-check-box-operations.png)](media/test-drive/enable-test-drive-check-box-operations.png#lightbox)

    - **[体験版の種類]** – **[Dynamics 365 for Operations]** オプションを選択します。 これは、体験版ユーザーのプロビジョニングとプロビジョニング解除を行うサービスを Microsoft がホストして管理することを意味します。

5. [こちらの説明](https://github.com/Microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md)に従って、テナントで体験版ユーザーをプロビジョニングおよびプロビジョニング解除するためのアクセス許可を Microsoft AppSource に付与します。 この手順では、以下で説明する **Azure AD アプリ ID** と **Azure AD アプリ キー** の値を生成します。
6. **[Test drive technical configuration]\(体験版の技術的構成\)** ページで以下のフィールドを設定します。

    [![Marketplace の技術的構成のページ。](media/test-drive/technical-config-details.png)](media/test-drive/technical-config-details.png#lightbox)

    - **[同時実行体験版の最大数]** – アクティブな体験版を同時に実行できるユーザーの数。 体験版がアクティブになっている間、各ユーザーは Dynamics のライセンスを消費するので、少なくともここで指定する数の Dynamics ライセンスを体験版ユーザーに使用できるようにします。 3 から 5 をお勧めします。
    - **[体験版の期間]** – ユーザーの体験版がアクティブになっている最大時間数。 この時間が経過すると、ユーザーはテナントからプロビジョニング解除されます。 アプリの複雑さに応じて、2 から 24 時間をお勧めします。 この時間が経過した後でユーザーが再び体験版にアクセスしたい場合、ユーザーはいつでも別の体験版を要求できます。
    - **[インスタンス URL]** – 体験版ユーザーが体験版を始めるときの転送先の URL。 通常これは、アプリとサンプル データがインストールされている Dynamics 365 インスタンスの URL です。 値の例: `https://testdrive.crm.dynamics.com`。
    - **[Azure Active Directory テナント ID]** – Dynamics 365 インスタンスのための Azure テナントの ID。 この値を取得するには、Azure portal にサインインし、 **[Azure Active Directory]**  >  **[プロパティ]** に移動して、ディレクトリ ID をコピーします。 値の例:172f988bf-86f1-41af-91ab-2d7cd01112341。
    - **[Azure Active Directory テナント名]** – Dynamics 365 インスタンスのための Azure テナントの名前。 「`<tenantname>.onmicrosoft.com`」の形式を使用します。 値の例: `testdrive.onmicrosoft.com`。
    - **[Azure Active Directory アプリケーション ID]** – 手順 5 で作成した Azure Active Directory (AD) アプリの ID。 値の例: `53852862-a2ae-4e43-9461-faa49650a096`。
    - **[Azure Active Directory アプリケーションのクライアント シークレット]** – 手順 5 で作成した Azure AD アプリのシークレット。 値の例: `IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=`。
    - **[試用版を利用する法人]** – 試用版ユーザーを割り当てる法人を指定します。 「[法人を作成または変更する](/dynamicsax-2012/appuser-itpro/create-or-modify-a-legal-entity)」で新しいものを作成できます。
    - **[ロール名]** – 体験版用に作成した Dynamics 365 のカスタム セキュリティ ロールの AOT (アプリケーション オブジェクト ツリー) の名前。 これは、体験版の使用中にユーザーに割り当てられるロールです。

        :::image type="content" source="./media/test-drive/security-config.png" alt-text="セキュリティ構成のページ。":::

7. マーケットプレースに掲載する詳細を指定します。 **[言語]** を選択すると、さらに必要なフィールドが表示されます。

    [![Marketplace に掲載する詳細ページ。](media/test-drive/marketplace-listing-details.png)](media/test-drive/marketplace-listing-details.png#lightbox)

    - **[説明]** – 体験版の概要。 このテキストは、体験版のプロビジョニング中にユーザーに表示されます。 書式設定された内容を提供したい場合、このフィールドでは HTML を使用できます。
    - **[ユーザー マニュアル]** – 体験版のユーザーがアプリの使用方法を理解するのに役立つ PDF のユーザー マニュアル。
    - **[体験版のデモ ビデオ]** – アプリを紹介するビデオ (省略可能)。

<!--
## Next steps

- [Set up your Azure subscription](test-drive-azure-subscription-setup.md) -->