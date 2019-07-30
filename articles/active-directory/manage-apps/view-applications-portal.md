---
title: テナント アプリケーションの表示 - Azure Active Directory | Microsoft Docs
description: Azure portal を使用して、Azure Active Directory (Azure AD) テナントのアプリケーションを表示します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 764c7c048dd0398b495ee31421a8e2bc9f8169ee
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477195"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Azure Active Directory テナントのアプリケーションの表示

このクイック スタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントのアプリケーションを表示します。

## <a name="before-you-begin"></a>開始する前に

結果を表示するには、Azure AD テナントに少なくとも 1 つのアプリケーションが必要です。 アプリケーションを追加するには、[アプリケーションの追加](add-application-portal.md)に関するクイック スタートを参照してください。

Azure AD テナントの全体管理者、クラウド アプリケーション管理者、またはアプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。

## <a name="find-the-list-of-tenant-applications"></a>テナント アプリケーションの一覧を表示する

Azure AD テナント アプリケーションは、Azure portal の **[エンタープライズ アプリ]** セクションで確認できます。

テナント アプリケーションを表示するには:

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
1. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。
1. **[アプリケーションの種類]** ドロップダウン メニューの **[すべてのアプリケーション]** を選択し、 **[適用]** を選択します。 テナント アプリケーションのランダム サンプルが表示されます。
1. 他のアプリケーションを表示するには、一覧の一番下にある **[さらに読み込む]** を選択します。 テナント内のアプリケーションの数によっては、一覧をスクロールするよりも[特定のアプリケーションを検索する](#search-for-a-tenant-application)方が簡単な場合があります。

## <a name="select-viewing-options"></a>表示オプションを選択する

探しているものに応じてオプションを選択します。

1. **[アプリケーションの種類]** 、 **[アプリケーションの状態]** 、 **[アプリケーションの可視性]** によってアプリケーションを表示できます。
1. **[アプリケーションの種類]** で、次のいずれかのオプションを選択します。

    - **[エンタープライズ アプリケーション]** には、Microsoft 以外のアプリケーションが表示されます。
    - **[Microsoft アプリケーション]** には、Microsoft アプリケーションが表示されます。
    - **[すべてのアプリケーション]** には、Microsoft 以外のアプリケーションと Microsoft アプリケーションの両方が表示されます。

1. **[アプリケーションの状態]** で **[任意]** 、 **[無効]** 、または **[有効]** を選択します。 **[任意]** オプションでは、無効なアプリケーションと有効なアプリケーションの両方が含まれます。
1. **[アプリケーションの可視性]** で、 **[任意]** または **[非表示]** を選択します。 **[非表示]** オプションは、テナントに存在するにもかかわらずユーザーには表示されないアプリケーションを示します。
1. 必要なオプションを選択したら、 **[適用]** を選択します。

## <a name="search-for-a-tenant-application"></a>テナント アプリケーションを検索する

特定のアプリケーションを検索するには:

1. **[アプリケーションの種類]** メニューの **[すべてのアプリケーション]** を選択し、 **[適用]** を選択します。
1. 検索するアプリケーションの名前を入力します。 アプリケーションが Azure AD テナントに追加されている場合は、それが検索結果に表示されます。 この例は、GitHub がテナント アプリケーションに追加されていないことを示しています。

    ![テナントにアプリが追加されていないことを表示する例](media/view-applications-portal/search-for-tenant-application.png)

1. アプリケーション名の最初の数文字を入力してください。 この例は、**Sales** で始まるすべてのアプリケーションを示しています。

    ![Sales で始まるすべてのアプリを表示する例](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure AD テナント内でアプリケーションを表示する方法について学習しました。 アプリケーションの種類、状態、可視性によって、アプリケーションの一覧をフィルター処理する方法を学習しました。 また、特定のアプリケーションを検索する方法についても学習しました。

探しているアプリケーションが見つかったら、[テナントへの他のアプリケーションの追加](add-application-portal.md)に進むことができます。 または、プロパティと構成オプションを表示または編集するアプリケーションを選択できます。 たとえば、シングル サインオンを構成できます。

> [!div class="nextstepaction"]
> [シングル サインオンの構成](configure-single-sign-on-non-gallery-applications.md)
