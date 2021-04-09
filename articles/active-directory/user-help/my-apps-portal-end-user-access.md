---
title: マイ アプリ ポータルでアプリを検索して使用する - Azure AD
description: マイ アプリ ポータルを見つける方法と、組織のクラウドベース アプリへのアクセス方法について説明します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 6dd9d65ee56bcbf8ca1da72f7d99d4545c5d7fbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100095003"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>マイ アプリ ポータルからアプリにサインインして開始する

職場または学校アカウントを Web ベースの **マイ アプリ** ポータルで使用して、次のことができます。

- 組織のクラウドベースのアプリの多くを表示して開始する
- [**マイ アプリ** ポータル](https://account.activedirectory.windowsazure.com/r#/groups)にアクセスする
- [**マイ アカウント** ポータル](https://myaccount.microsoft.com/)にアクセスする

**マイ アプリ** ポータルにアクセスできない場合は、自分の組織のヘルプ デスクにアクセス許可について問い合わせてください。

> [!IMPORTANT]
> このコンテンツは、**マイ アプリ** のユーザーを対象としています。 管理者の方は、「[アプリケーション管理のドキュメント](../manage-apps/index.yml)」で、クラウドベースのアプリの設定と管理の方法を詳しくご覧いただけます。
>
> 個人用 Microsoft アカウントでのサインインでエラーが発生した場合でも、組織のドメイン名 (contoso.com など) または組織の **テナント ID** (管理者から入手) を使用して、次のいずれかの URL でサインインできます。
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="supported-browsers"></a>サポートされているブラウザー

次の Web ブラウザーのいずれかから、**マイ アプリ** ポータルにアクセスできます。

- Microsoft Edge (モバイル バージョンの Edge がサポートされる唯一のモバイル ブラウザーです)
- Google Chrome
- Mozilla Firefox、バージョン 26.0 以降

お使いのコンピューター上で、または iOS または Android モバイル デバイス上のモバイル バージョンの Edge ブラウザーから、マイ アプリ ポータルにアクセスして使用できます。

![マイ アプリ ポータルの [アプリ] ページ](media/my-apps-portal/my-apps-home.png)

## <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>コンピューター上のマイ アプリ ポータルにアクセスして使用する

組織のクラウドベースのアプリにアクセスして使用するアクセス許可がある場合、**マイ アプリ** ポータルを通じてそれらに到達できます。

1. ご自分のコンピューター上でご自分の職場または学校アカウントにサインインします。

1. サポートされている Web ブラウザーで、 https://myapps.microsoft.com に移動するか、組織から提供されている `https://myapps.microsoft.com/contoso.com` などのカスタマイズされたページへのリンクを使用します。

   **[アプリ]** ページが表示され、組織が所有していて、ユーザーが使用できるクラウドベースのアプリがすべて示されます。

1. **[アプリ]** ページから、使用開始するアプリを選択します。

   アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

### <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>マイ アプリによるセキュリティで保護されたサインイン拡張機能をダウンロードしてインストールする

求められた場合は、**マイ アプリによるセキュリティで保護されたサインイン拡張機能** をダウンロードしてインストールします。 この拡張機能は、デスクトップ ブラウザーを使用して、組織のクラウド アプリでシングル サインオンを使用する場合に役立ちます。 **マイ アプリ** ポータルにマウス カーソルを合わせ、 **[...]** を選択し、 **[アプリケーションの管理]** を選択するだけです。

既に組織によりシングル サインオンが設定されている場合は、拡張機能は自動的にインストールされ、このセクションをスキップできます。

**マイ アプリによるセキュリティで保護されたサインイン拡張機能** が次の場合に役に立ちます。

- サインイン ページから直接アプリにサインインする。
- **クイック検索** 機能を使用して、アプリを開始する。
- **[最近の使用]** セクションで使用した最後のアプリを確認する。
- リモート時に[アプリケーション プロキシ](../manage-apps/application-proxy.md)を利用して社内 URL を使用する。

### <a name="to-download-and-install-the-extension"></a>拡張機能をダウンロードしてインストールするには

使用しているブラウザーに基づいて、拡張機能をダウンロードしてインストールします。

- **Microsoft Edge** - Microsoft Store から、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://microsoftedge.microsoft.com/addons/detail/my-apps-secure-signin-ex/gaaceiggkkiffbfdpmfapegoiohkiipl)機能に移動してから、 **[取得]** を選択して Microsoft Edge レガシ ブラウザー用の拡張機能を取得します。

- **Google Chrome** - Chrome Web ストアから、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)機能に移動して、 **[Chrome に追加]** を選択します。

- **Mozilla Firefox** - **[Firefox アドオン]** ページから、[マイ アプリによるセキュリティで保護されたサインイン拡張](https://addons.mozilla.org/firefox/addon/access-panel-extension/)機能に移動して、 **[Firefox に追加]** を選択します。

アイコンは **アドレス** バーの右側に追加され、サインインして拡張機能をカスタマイズできます。

### <a name="to-change-your-my-apps-portal-using-the-extension"></a>拡張機能を使用してマイ アプリ ポータルを変更するには

**[最近の使用]** セクションに表示するアプリの数を選択し、組織の内部 URL がリダイレクトできるようにするかどうかを決定できます。

1. **アドレス** バーの右側にある新しい **アプリによるセキュリティで保護されたサインイン拡張機能** アイコン![拡張機能アイコン](media/my-apps-portal/my-apps-portal-extension-icon.png)を選択し、続いて **[開始するにはサインインしてください]** を選択します。

1. **[設定]** アイコン![設定アイコン](media/my-apps-portal/my-apps-portal-extension-settings-icon.png) を右クリックし、 **[設定]** を選択します。

1. **[設定]** ボックスで、ポータルに表示する最近使用したアプリの数を選択し、リモートで使用できるように、組織の内部 URL のリダイレクトを許可するかどうかを選択します。

   ![拡張機能の [設定] ページ、使用可能なカスタマイズを表示](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-on-mobile-edge"></a>モバイル Edge 上のマイ アプリ ポータルにアクセスして使用する

お使いのデバイスのモバイル バージョンの Edge から、組織のアプリを表示して使用します。

1. モバイル デバイスで、Apple App Store および Google Play ストアからモバイル Edge ブラウザー アプリをダウンロードしインストールします。

1. モバイル Edge ブラウザーを開き、 https://myapps.microsoft.com に移動するか、組織から提供されている https://myapps.microsoft.com/contoso.com などのカスタマイズされたページへのリンクを使用します。

   **[アプリ]** ページが表示され、組織が所有し、お客様が利用できるクラウドベースのアプリがすべて示されます。

1. **[アプリ]** ページから、使用開始するアプリを選択します。

   アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>マイ アプリ ポータルに新しいアプリを追加する

管理者からアクセス許可が与えられている場合は、新しいアプリを **[アプリ]** ページに追加できます。

1. **[アプリ]** ページから **[セルフサービス アプリの追加]** を選択します。

   ![myapplications.microsoft.com のマイ アプリ ポータルのアプリの追加ページ](media/my-apps-portal/my-apps-portal-add-app-link.png)

1. 指定されたリストから追加するアプリを選択し、 **[追加]** を選択します。

1. アプリは、**アプリ** ページ上のリストに追加されます。

   アプリの中には、追加される前に管理者の承認が必要なものがあります。 その場合、アプリは、管理者が承認するまで、 **[アプリ]** ページに追加されません。

## <a name="start-a-cloud-based-app"></a>クラウドベースのアプリを起動する

**マイ アプリ** ポータルからは、使用可能な任意のクラウドベースのアプリを起動することができます。 表示されるアプリは、使用するアクセス許可があるアプリのみです。

- **[アプリ]** ページから、使用開始するアプリを選択します。

   アプリの新しいページが開き、(必要に応じて) サインインしたり、アプリを使用開始できます。

## <a name="activities-in-the-my-apps-portal"></a>マイ アプリ ポータルでのアクティビティ

**マイ アプリ** ポータルを開いたら、次の操作を実行できます。

- 自分のアプリを、組織が作成して提供しているさまざまなカテゴリに整理できます。 詳細については、[マイ アプリ ポータル内のコレクションへのアクセスと使用](my-applications-portal-workspaces.md)に関する記事を参照してください。
- アプリケーションに付与されたアクセス許可を確認し、更新し、取り消します。 詳細については、「[マイ アプリ ポータルでのアプリケーションのアクセス許可の編集または取り消し](my-applications-portal-permissions-saved-accounts.md)」を参照してください。
- アプリ アカウントを作成、更新、および削除します。 詳細については、[アプリケーションのアクセス許可の編集または取り消し](my-applications-portal-permissions-saved-accounts.md)に関するページを参照してください。
- アカウント管理の **マイ アプリ** ポータルから、[ **[マイ アカウント]**](my-account-portal-overview.md) にアクセスします。
  
  - 異常なサインイン アクティビティの確認
  - パスワードの管理
  - 接続されているデバイス、サブスクリプション、組織、および Office アプリの管理

>[!NOTE]
>使用可能なコレクションまたはカテゴリが表示されない場合は、管理者が設定していないか、共有されているものがないことを意味します。 共有コレクションを表示するための追加のサポートやアクセス許可については、組織のヘルプ デスクに問い合わせてください。

## <a name="next-steps"></a>次のステップ

**アプリ** ページに到達した後、次の操作を実行できます。

- [プロファイルの情報を変更する](./my-account-portal-settings.md)

- [グループ関連の情報を表示し、更新する](my-apps-portal-end-user-groups.md)

- [自分のアクセス レビューを実行する](my-apps-portal-end-user-access-reviews.md)