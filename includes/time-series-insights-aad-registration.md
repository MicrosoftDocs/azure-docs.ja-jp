---
title: インクルード ファイル
description: インクルード ファイル
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 09/24/2019
ms.openlocfilehash: ccfbd16f4db770558f1bc0284860a5f8d9fb8b68
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266944"
---
> [!IMPORTANT]
> * 従来の **[Azure Active Director]**  >  **[アプリの登録 (レガシー)]** ブレードは、2019 年 5 月に新しい **[Azure Active Directory]**  >  **[アプリの登録]** ブレードに差し替えられています。
> * レガシー ブレードで作成された、またはそのブレードに表示されていたアプリの登録は、自動的に新しいブレードに表示されます。
> * 新しい Azure アプリの登録エクスペリエンスの総合的な情報については、[Azure アプリ登録トレーニング ガイド](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide)と [Azure Active Directory クイックスタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)をご覧ください。

1. [Azure portal](https://ms.portal.azure.com/) で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** を選択します。

   [![Azure Active Directory での新しいアプリケーションの登録](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > 新しい Azure Active Directory アプリ登録パネルでは、 **[所有しているアプリケーション]** を選択することで、表示されるアプリをフィルター処理できます。

    アプリを登録すると、ここにそのアプリが表示されます。

1. アプリケーションの名前を指定し、 **[この組織のディレクトリ内のアカウントのみ]** を選択して、この API にアクセスできる **[サポートされているアカウントの種類]** を指定します。 認証されたユーザーのリダイレクト先となる有効な URI を選択し、 **[登録]** を選択します。

   [![Azure Active Directory にアプリケーションを作成する](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. 表示されているアプリの **[概要]** ブレードに、Azure Active Directory アプリの重要な情報が表示されます。 **[所有しているアプリ]** からアプリを選択し、 **[概要]** を選択します。

   [![アプリケーション ID をコピーする](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   使用する **[アプリケーション (クライアント) ID]** を、クライアント アプリケーション内にコピーします。

1. **[認証]** ブレードで、重要な認証構成設定を指定します。 

    1. **[リダイレクト URI]** は、認証要求で指定されたアドレスと一致する必要があります。

        * ローカル開発環境でホストされているアプリでは、 **[パブリック クライアント (モバイルとデスクトップ)]** を選択します。 **[既定のクライアントの種類]** が [はい] に設定されていることを確認します。
        * Azure App Service でホストされているシングル ページ アプリでは、 **[Web]** を選択します。

    1. **[ID トークン]** をオンにすることで、暗黙的な許可フローを有効にします。

   [![新しいクライアント シークレットを生成する](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   **[Save]** をクリックします。

1. **[証明書とシークレット]** を選択し、 **[新しいクライアント シークレット]** を選択して、クライアントがその ID を証明する際に使用できるアプリケーション パスワードを作成します。

   [![新しいクライアント シークレットを生成する](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   クライアント シークレット パスワードが表示されます。 普段使用しているテキスト エディターにキーをコピーします。

   > [!NOTE]
   > 代わりに証明書をインポートすることもできます。 セキュリティを強化するには、証明書をお勧めします。 証明書を使用する場合は、 **[証明書のアップロード]** を選択してください。

1. お使いの Azure Active Directory アプリを Azure Time Series Insights に関連付けます。 **[API のアクセス許可]**  >  **[アクセス許可の追加]**  >  **[所属する組織で使用している API]** を選択します。 

    [![API をお使いの Azure Active Directory アプリに関連付ける](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   検索バーに「`Azure Time Series Insights`」と入力した後、`Azure Time Series Insights` を選択します。

1. 次に、お使いのアプリで必要な API のアクセス許可の種類を指定します。 既定では、 **[委任されたアクセス許可]** が強調表示されます。 アクセス許可の種類を選択した後、 **[アクセス許可の追加]** を選択します。

    [![お使いのアプリで必要な API のアクセス許可の種類を指定する](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)