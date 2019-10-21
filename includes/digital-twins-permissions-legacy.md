---
title: インクルード ファイル
description: インクルード ファイル
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978604"
---
## <a name="azure-active-directory-legacy-registration"></a>Azure Active Directory のレガシ登録

1. [Azure portal](https://portal.azure.com) で、左側のウィンドウの **[Azure Active Directory]** を開き、 **[プロパティ]** ウィンドウを開きます。 **ディレクトリ ID** を一時ファイルにコピーします。 この値は、次のセクションでサンプル アプリケーションを構成する際に使用します。

    [![Azure Active Directory のディレクトリ ID](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. [Azure portal](https://portal.azure.com) で、左側のウィンドウの **[Azure Active Directory]** を開き、 **[App registrations (Legacy)]\(アプリの登録 (レガシ)\)** ウィンドウを開きます。 **[新しいアプリケーションの登録]** ボタンを選択します。

1. **[名前]** ボックスに、このアプリ登録の表示名を入力します。 **[アプリケーションの種類]** で **[ネイティブ]** を選択し、 **[リダイレクト URI]** で `https://microsoft.com` を選択します。 **作成** を選択します。

    [![登録されたアプリ](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  登録されたアプリを開き、 **[アプリケーション ID]** フィールドの値を一時ファイルにコピーします。 この値により、Azure Active Directory アプリが識別されます。 アプリケーション ID は、次のセクションでサンプル アプリケーションを構成する際に使用します。

    [![Azure Active Directory のアプリケーション ID](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. アプリ登録ウィンドウを開きます。 **[設定]**  >  **[必要なアクセス許可]** の順に選択します。その後、次の操作を行います。

   a. 左上にある **[追加]** を選択して、 **[API アクセスの追加]** ウィンドウを開きます。

   b. **[API を選択します]** を選択し、**Azure Digital Twins** を検索します。 検索で API が見つからない場合は、代わりに **Azure Smart Spaces** を検索します。

   c. **[Azure Digital Twins (Azure Smart Spaces Service)]** オプションを選択し、 **[選択]** を選択します。

   d. **[アクセス許可の選択]** を選択します。 **[読み取り/書き込みアクセス]** の委任されたアクセス許可チェック ボックスをオンにし、 **[選択]** を選択します。

   e. **[API アクセスの追加]** ウィンドウで、 **[完了]** を選択します。

   f. **[必要なアクセス許可]** ウィンドウで、 **[アクセス許可の付与]** ボタンを選択し、表示される確認メッセージを受け入れます。 この API のアクセス許可が与えられていない場合は、管理者に問い合わせてください。

      [![[必要なアクセス許可]](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox) ウィンドウ

 
