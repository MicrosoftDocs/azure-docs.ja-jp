---
title: マイ アプリ ポータルでアプリにアクセスし使用する - Azure Active Directory| Microsoft Docs
description: マイ アプリ ポータルにアクセスする方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaed7357221f5b766bfb8b9e1a9031c08854ac9a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60477288"
---
# <a name="access-and-use-apps-on-the-my-apps-portal"></a>マイ アプリ ポータルでアプリにアクセスして使用する
Web ベースの**マイ アプリ** ポータルでは、職場または学校アカウントを使用して、社内にあるクラウドベースのアプリを表示して起動したり、一部のプロフィール情報とアカウント情報を更新したり、**グループ**情報を確認したり、アプリとグループの**アクセス レビュー**を実行したりすることができます。 **マイ アプリ** ポータルにアクセスできない場合は、ヘルプデスクに連絡してアクセス許可を得る必要があります。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>このコンテンツはユーザーを対象としています。 管理者の方は、「[アプリケーション管理のドキュメント](https://docs.microsoft.com/azure/active-directory/manage-apps)」で、クラウドベースのアプリの設定と管理の方法を詳しくご覧いただけます。

## <a name="supported-browsers"></a>サポートされているブラウザー
次の Web ブラウザーのいずれかから、**マイ アプリ** ポータルにアクセスできます。

- Google Chrome

- Mozilla Firefox、バージョン 26.0 以降

- Microsoft Edge

- Internet Explorer、バージョン 11 (制限付きサポート)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能をダウンロードしてインストールする
求められた場合は、マイ アプリによるセキュリティで保護されたサインイン拡張機能をダウンロードしてインストールします。 この拡張機能は、シングル サインオン プロセスを使用する必要がある組織の任意のクラウド アプリを開始する場合に役立ちます。 既に組織によりシングル サインオンが設定されている場合は、拡張機能は自動的にインストールされ、このセクションをスキップできます。

この拡張機能は次の場合に役立ちます。 

- サインイン ページから直接アプリにサインインする。

- **クイック検索**機能を使用して、アプリを開始する。

- **[最近の使用]** セクションで使用した最後のアプリを確認する。

- リモート時に[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)を利用して社内 URL を使用する。

### <a name="to-download-and-install-the-extension"></a>拡張機能をダウンロードしてインストールするには
使用しているブラウザーに基づいて、拡張機能をダウンロードしてインストールします。

- **Google Chrome。** Chrome Web ストアから、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)機能に移動して、 **[Add to Chrome]** (Chrome に追加) を選択します。

- **Mozilla Firefox**。 **[Firefox Add-ons]** (Firefox アドオン) ページから、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://addons.mozilla.org/firefox/addon/access-panel-extension/)機能に移動して、 **[Add to Firefox]** (Firefox に追加) を選択します。

- **Microsoft Edge**。Microsoft Store から、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab)機能に移動して、 **[取得]** を選択します。

アイコンは**アドレス** バーの右側に追加され、サインインして拡張機能をカスタマイズできます。

#### <a name="to-change-your-my-apps-portal-using-the-extension"></a>拡張機能を使用してマイ アプリ ポータルを変更するには
**[最近の使用]** セクションに表示するアプリの数を選択し、組織の内部 URL がリダイレクトできるようにするかどうかを決定できます。

1. **アドレス** バーの右側にある新しい**アプリによるセキュリティで保護されたサインイン拡張機能**アイコン![拡張機能アイコン](media/my-apps-portal/my-apps-portal-extension-icon.png)を選択し、続いて **[開始するにはサインインしてください]** を選択します。

2. **[設定]** アイコン![設定アイコン](media/my-apps-portal/my-apps-portal-extension-settings-icon.png) を右クリックし、 **[設定]** を選択します。

3. **[設定]** ボックスで、ポータルに表示する最近使用したアプリの数を選択し、リモートで使用できるように、組織の内部 URL のリダイレクトを許可するかどうかを選択します。

    ![拡張機能の [設定] ページ、使用可能なカスタマイズを表示](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>デバイスごとにマイ アプリ ポータルにアクセスして使用する
Intune で管理されるブラウザーから、または iOS または Android モバイル デバイスからコンピューター上のマイ アプリ ポータルにアクセスして使用できます。

![マイ アプリ ポータルの [アプリ] ページ](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>コンピューター上のマイ アプリ ポータルにアクセスして使用する
組織のクラウドベースのアプリにアクセスして使用するアクセス許可がある場合、**マイ アプリ** ポータルを通じてそれらに到達できます。

1.  職場または学校のアカウントにサインインします。

2.  Web ブラウザーを開いて https://myapps.microsoft.com に移動するか、組織から与えられたリンクを使用します。 たとえば、 https://myapps.microsoft.com/contoso.com など、組織のカスタマイズされたページに転送されることがあります。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。
    
    アプリの新しいページが開きます。このページでサインインしたり (必要な場合)、アプリを使用し始められます。

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Intune Managed Browser でマイ アプリ ポータルにアクセスして使用する
iOS および Android デバイスで Intune Managed Browser から組織のアプリを表示し使用します。

1. モバイル デバイス上では、[Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) および [Google Play ストア](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser)から Intune Managed Browser アプリをダウンロードしインストールします。

2. Intune Managed Browser アプリを開いて、 https://myapps.microsoft.com に移動するか、組織から提供されたリンクを使用します。 たとえば、 https://myapps.microsoft.com/contoso.com など、組織のカスタマイズされたページに転送されることがあります。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。
    
    アプリの新しいページが開きます。このページでサインインしたり (必要な場合)、アプリを使用し始められます。

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>iOS デバイス上のマイ アプリ ポータルにアクセスして使用する
iOS バージョン 7 以降を実行している iPhone または iPad デバイスから **マイ アプリ** ポータルを表示および使用します。 [マイ アプリ モバイル アプリ](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)をインストールして、iOS デバイス上の組織のアプリにアクセスすることもできます。 

1. モバイル デバイスでは、Safari などの Web ブラウザー アプリを起動します。

2. [https://myapps.microsoft.com](https://myapps.microsoft.com ) に移動するか、組織から与えられたリンクを使用します。 たとえば、 https://myapps.microsoft.com/contoso.com など、組織のカスタマイズされたページに転送されることがあります。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。
    
    アプリの新しいページが開きます。このページでサインインしたり (必要な場合)、アプリを使用し始められます。

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Android デバイス上のマイ アプリ ポータルにアクセスして使用する
Android デバイス上の**マイ アプリ** ポータルを表示して使用します。

1. モバイル デバイスでは、Google Chrome などの Web ブラウザー アプリを起動します。

2. [https://myapps.microsoft.com](https://myapps.microsoft.com ) に移動するか、組織から与えられたリンクを使用します。 たとえば、 https://myapps.microsoft.com/contoso.com など、組織のカスタマイズされたページに転送されることがあります。

    **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

3. **[アプリ]** ページから、使用開始するアプリを選択します。
    
    アプリの新しいページが開きます。このページでサインインしたり (必要な場合)、アプリを使用し始められます。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>マイ アプリ ポータルに新しいアプリを追加する
管理者からアクセス許可が与えられている場合は、新しいアプリを **[アプリ]** ページに追加できます。

1. **[アプリ]** ページから **[アプリの追加]** を選択します。

    **[アプリの追加]** ページが表示されます。

    ![[アプリの追加] ページ、マイ アプリ ポータル内](media/my-apps-portal/my-apps-portal-add-apps-page.png)

2. 指定されたリストから追加するアプリを選択し、 **[追加]** を選択します。

3. アプリは、**アプリ** ページ上のリストに追加されます。

    アプリの中には、追加される前に管理者の承認が必要なものがあります。 このような状況では、アプリは、管理者が承認するまで、**アプリ** ページに追加されません。

## <a name="next-steps"></a>次の手順
**アプリ** ページに到達した後、次のアクションを実行できます。

- [プロファイル情報を変更する](my-apps-portal-end-user-update-profile.md)。

- [グループ関連の情報を表示し、更新する](my-apps-portal-end-user-groups.md)。

- [自分のアクセス レビューを実行する](my-apps-portal-end-user-access-reviews.md)。