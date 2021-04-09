---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday モバイル アプリケーションの統合 | Microsoft Docs
description: Azure Active Directory と Workday モバイル アプリケーションの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182021"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workday モバイル アプリケーションの統合

このチュートリアルでは、Azure Active Directory (Azure AD)、条件付きアクセス、および Intune と、Workday モバイル アプリケーションを統合する方法について説明します。 Workday モバイル アプリケーションと Microsoft 製品を統合すると、次のことが可能になります。

* サインインする前に、デバイスがポリシーに準拠していることを確認する。
* Workday モバイル アプリケーションにコントロールを追加して、ユーザーが会社のデータに安全にアクセスできるようにする。 
* Workday にアクセスする Azure AD を制御する。
* ユーザーが自分の Azure AD アカウントを使用して Workday に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

作業を開始するには:

* Workday と Azure AD を統合します。
* [Azure Active Directory シングル サインオン (SSO) と Workday の統合](./workday-tutorial.md)に関する記事を参照します。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、Workday モバイル アプリケーションで Azure AD の条件付きアクセス ポリシーと Intune を構成し、テストします。

シングル サインオン (SSO) を有効にするために、Azure AD で Workday フェデレーション アプリケーションを構成できます。 詳細については、[Azure Active Directory シングル サインオン (SSO) と Workday の統合](./workday-tutorial.md)に関する記事をご覧ください。

> [!NOTE] 
> Workday では、Intune のアプリ保護ポリシーはサポートされていません。 条件付きアクセスを使用するには、モバイル デバイス管理を使用する必要があります。


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>ユーザーが Workday モバイル アプリケーションにアクセスできるようにする

モバイル アプリへのアクセスを許可するように Workday を構成します。 Workday モバイル向けに次のポリシーを構成する必要があります。

1. 機能領域のドメイン セキュリティ ポリシー レポートにアクセスします。
1. 適切なセキュリティ ポリシーを選択します。
    * モバイルの使用 - Android
    * モバイルの使用 - iPad
    * モバイルの使用 - iPhone
1. **[Edit Permissions]\(アクセス許可の編集\)** を選択します。
1. **[View]\(表示\) または [Modify]\(変更\)** チェック ボックスをオンにして、レポートまたはタスクのセキュリティ保護可能な項目へのアクセス権をセキュリティ グループに付与します。
1. **[Get]\(取得\) または [Put]\(配置\)** チェック ボックスをオンにして、統合およびレポートまたはタスクのセキュリティ保護可能なアクションへのアクセス権をセキュリティ グループに付与します。

**[Activate Pending Security Policy Changes]\(保留中のセキュリティ ポリシーの変更をアクティブ化\)** を実行して、保留中のセキュリティ ポリシーの変更をアクティブにします。

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Workday モバイル ブラウザーで Workday サインイン ページを開く

Workday モバイル アプリケーションに条件付きアクセスを適用するには、アプリを外部ブラウザーで開く必要があります。 **[Edit Tenant Setup - Security]\(テナント設定の編集 - セキュリティ\)** で、 **[Enable Mobile Browser SSO for Native Apps]\(ネイティブ アプリに対してモバイル ブラウザー SSO を有効にする\)** をオンにします。 この場合、iOS 用のデバイスと Android 用の仕事用プロファイルに、Intune によって承認されたブラウザーがインストールされている必要があります。

![Workday モバイル ブラウザー サインインのスクリーンショット。](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>条件付きアクセス ポリシーを設定する

このポリシーは、iOS または Android デバイスでのサインインにのみ影響します。 これをすべてのプラットフォームに拡張する場合は、 **[任意のデバイス]** を選択します。 このポリシーでは、デバイスがポリシーに準拠していることを要求し、Intune によってこれを検証します。 Android には仕事用プロファイルがあるため、ユーザーは、各自の仕事用プロファイルを使用してサインインし、Intune ポータル サイトを介してアプリをインストールしていない限り、Workday にサインインすることはできません。 iOS で同じ状況が確実に適用されるようにするには、追加の手順が 1 つ必要です。

Workday では、次のアクセス制御がサポートされています。
* 多要素認証を要求する
* デバイスは準拠としてマーク済みである必要がある

Workday アプリでは、以下はサポートされていません。
* 承認済みクライアント アプリを必須にする
* アプリの保護ポリシーが必要 (プレビュー)

Workday をマネージド デバイスとして設定するには、次の手順を実行します。

![[Managed Devices Only]\(マネージド デバイスのみ\) と [クラウド アプリまたは操作] のスクリーンショット。](./media/workday-tutorial/managed-devices-only.png)

1. **[ホーム]**  >  **[Microsoft Intune]**  >  **[条件付きアクセス ポリシー]** の順に選択します。 次に、 **[Managed Devices Only]\(マネージド デバイスのみ\)** を選択します。 

1. **[Managed Devices Only]\(マネージド デバイスのみ\)** の **[名前]** で **[Managed Devices Only]\(マネージド デバイスのみ\)** を選択し、 **[クラウド アプリまたは操作]** を選択します。

1. **[クラウド アプリまたは操作]** で、次の操作を行います。

    a. **[このポリシーが適用される対象を選択する]** を **[クラウド アプリ]** に切り替えます。

    b. **[必要]** で **[アプリを選択]** を選択します。

    c. **[選択]** の一覧から **[Workday]** を選択します。

    d. **[Done]** を選択します。

1. **[ポリシーを有効にする]** を **[オン]** に切り替えます。

1. **[保存]** を選択します。

**[Grant access]\(アクセス権の付与\)** では、次の手順を実行します。

![[Managed Devices Only]\(マネージド デバイスのみ\) と [許可] のスクリーンショット。](./media/workday-tutorial/managed-devices-only-2.png)

1. **[ホーム]**  >  **[Microsoft Intune]**  >  **[条件付きアクセス ポリシー]** の順に選択します。 次に、 **[Managed Devices Only]\(マネージド デバイスのみ\)** を選択します。 

1. **[Managed Devices Only]\(マネージド デバイスのみ\)** の **[名前]** で、 **[Managed Devices Only]\(マネージド デバイスのみ\)** を選択します。 **[アクセス制御]** で **[許可]** を選択します。

1. **[許可]** で、次の操作を行います。

    a. 適用する制御として **[Grant access]\(アクセス権の付与\)** を選択します。

    b. **[デバイスは準拠としてマーク済みである必要があります]** を選択します。

    c. **[選択したコントロールのいずれかが必要]** を選択します。

    d. **[選択]** を選択します。

1. **[ポリシーを有効にする]** を **[オン]** に切り替えます。

1. **[保存]** を選択します。

## <a name="set-up-device-compliance-policy"></a>デバイス コンプライアンス ポリシーを設定する

iOS デバイスがモバイル デバイス管理によって管理されている Workday を介してのみサインインできるようにするには、制限付きアプリの一覧に **com.workday.workdayapp** を追加して、App Store アプリをブロックする必要があります。 これにより、会社のポータルを介して Workday がインストールされているデバイスだけが Workday にアクセスできるようになります。 ブラウザーでは、デバイスは、Intune によって管理され、マネージド ブラウザーを使用している場合にのみ Workday にアクセスできます。

![iOS デバイス コンプライアンス ポリシーのスクリーンショット。](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Intune アプリ構成ポリシーを設定する

| シナリオ | キーと値のペア |
|----------------------------------------------------------------------------------------   |-----------|
| 次の [テナント] および [Web アドレス] フィールドを自動的に設定します。<br>● 仕事用プロファイルで Android を有効にしている場合は、Android 上の Workday。<br>● iPad および iPhone 上の Workday。     | テナントの構成には、これらの値を使用します。 <br>● 構成キー = `UserGroupCode`<br>● 値の型 = String <br>●   構成値 = ご使用のテナントの名前。 例: `gms`<br>Web アドレスの構成には、これらの値を使用します。<br>●  構成キー = `AppServiceHost`<br>●   値の型 = String<br>●    構成値 = ご使用のテナントのベース URL。 例: `https://www.myworkday.com`                                |   |
| iPad および iPhone 上の Workday に対してこれらの操作を無効にします。<br>●    切り取り、コピー、貼り付け<br>●   印刷                       | 機能を無効にするには、次のキーの値 (ブール値) を `False` に設定します。<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Android 上の Workday でスクリーンショットを無効にします。 |機能を無効にするには、`AllowScreenshots` キーの値 (ブール値) を `False` に設定します。|
| ユーザーに推奨される更新プログラムを無効にします。|機能を無効にするには、`AllowSuggestedUpdates` キーの値 (ブール値) を `False` に設定します。|
|アプリ ストアの URL をカスタマイズして、選択したアプリ ストアにモバイル ユーザーを誘導します。|アプリ ストアの URL を変更するには、これらの値を使用します。<br>● 構成キー = `AppUpdateURL`<br>● 値の型 = String<br> ●   構成値 = アプリ ストアの URL|
|       |


## <a name="ios-configuration-policies"></a>iOS 構成ポリシー

1. [Azure portal](https://portal.azure.com/) に移動してサインインします。
1. 「**Intune**」を検索するか、一覧でウィジェットを選択します。
1. **[クライアント アプリ]**  >  **[アプリ]**  >  **[アプリ構成ポリシー]** の順に移動します。 次に、 **[+ 追加]**  >  **[マネージド デバイス]** の順に選択します。
1. 名前を入力します。
1. **[プラットフォーム]** で、 **[iOS/iPadOS]** を選択します。
1. **[関連アプリ]** で、追加した iOS 用 Workday アプリを選択します。
1. **[構成設定]** を選択します。 **[構成設定の形式]** で、 **[XML データを入力する]** を選択します。
1. XML ファイルの例を次に示します。 適用する構成を追加します。 `STRING_VALUE` を、使用する文字列に置き換えます。 `<true /> or <false />` を `<true />` または `<false />` に置き換えます。 構成を追加しない場合、この例は `True` に設定されたものとして機能します。

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. **[追加]** を選択します。
1. ページを最新の情報に更新し、新しく作成したポリシーを選択します。
1. **[割り当て]** を選択し、アプリを適用するユーザーを選択します。
1. **[保存]** を選択します。

## <a name="android-configuration-policies"></a>Android 構成ポリシー

1. [Azure portal](https://portal.azure.com/) に移動してサインインします。
2. 「**Intune**」を検索するか、一覧でウィジェットを選択します。
3. **[クライアント アプリ]**  >  **[アプリ]**  >  **[アプリ構成ポリシー]** の順に移動します。 次に、 **[+ 追加]**  >  **[マネージド デバイス]** の順に選択します。
5. 名前を入力します。 
6. **[プラットフォーム]** で、 **[Android]** を選択します。
7. **[関連アプリ]** で、追加した Android 用 Workday アプリを選択します。
8. **[構成設定]** を選択します。 **[構成設定の形式]** で、 **[JSON データを入力する]** を選択します。