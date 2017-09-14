---
title: "Azure Active Directory でサインイン ページをカスタマイズする | Microsoft Docs"
description: "Azure サインイン ページに会社のブランドを追加する方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 6bfce3d4ec243779229cc4f39e1c22149229a66a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/06/2017

---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>クイック スタート: Azure AD のサインイン ページに会社のブランドを追加する
多くの企業は、管理下にある Web サイトとサービスに関して利用者が戸惑わないよう、そのすべての外観に統一感を持たせたいと考えています。 Azure Active Directory (Azure AD) では、会社のロゴや独自の配色でサインイン ページの外観をカスタマイズできるようにすることでこれを実現します。 サインイン ページは、Office 365 など、Azure AD を ID プロバイダーとして使用する Web ベースのアプリケーションにサインインするときに表示されるページです。 このページを使用して資格情報を入力します。

> [!NOTE]
> * 会社のブランドは、Azure AD の Premium または Basic エディションにアップグレードした場合か、Office 365 のライセンスを所有している場合にのみ使用できます。 ご利用のライセンス タイプで機能がサポートされているかどうかについては、[Azure Active Directory の価格情報](https://azure.microsoft.com/pricing/details/active-directory/)に関するページを参照してください。
> 
> * Azure Active Directory のワールドワイド インスタンスを使用している中国のお客様は、Azure Active Directory Premium および Basic エディションを使用できます。 中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure Active Directory Premium および Basic エディションは現在サポートされていません。 詳細については、 [Azure Active Directory フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からお問い合わせください。

## <a name="customizing-the-sign-in-page"></a>サインイン ページのカスタマイズ

<!--You can customize the following elements on the sign-in page: <attach image>-->

カスタマイズした会社のブランドが Azure AD のサインイン ページに表示されるのは、ユーザーがテナント固有の URL ([*https://outlook.com/contoso.com*](https://outlook.com/contoso.com) など) にアクセスしたときです。

テナント固有ではない URL (www.office.com など) にアクセスしたときには、そのユーザーが誰であるかをシステムが把握していないため、カスタマイズした会社のブランドはサインイン ページに表示されません。 ユーザーが各自のユーザー ID を入力するか、ユーザー タイルを選択すると、会社のブランドが表示されます。

> [!NOTE]
> * ドメイン名は、ブランドを構成した Azure Portal の **[ドメイン]** 部分に "アクティブ" として表示される必要があります。 詳細については、[カスタム ドメイン名の追加](add-custom-domain.md)に関する記事をご覧ください。
> * サインイン ページのブランド情報が、個人用 Microsoft アカウントのサインイン ページに継承されることはありません。 つまり従業員やゲスト ユーザーが個人用 Microsoft アカウントでサインインした場合、そのサインイン ページには組織のブランドが表示されません。


### <a name="banner-logo"></a>バナー ロゴ 

説明 | 制約 | 推奨事項
------- | ------- | ----------
バナー ロゴはサインイン ページとアクセス パネル ページに表示されます。<br>サインイン ページでは、ユーザーの組織が判明した時点 (通常、ユーザー名が入力された後) でロゴが表示されます。  | 透過 JPG または透過 PNG<br>高さの最大値: 36 ピクセル<br>幅の最大値: 245 ピクセル | 組織のロゴはここで使用します。<br>透過画像を使用してください。 背景が白になることを前提にしないでください。<br>画像のロゴの周囲にパディングを追加しないでください。追加すると、ロゴが過度に小さくなります。

### <a name="username-hint"></a>ユーザー名のヒント   
説明 | 制約 | 推奨事項
------- | ------- | ----------
これはユーザー名フィールドのヒント テキストをカスタマイズします。 | 最大 64 文字の Unicode テキスト<br>プレーン テキストのみ | 所属する組織の外部からゲスト ユーザーがアプリにサインインすることが予想される場合は、これを設定しないことをお勧めします。
            
### <a name="sign-in-page-text"></a>サインイン ページ テキスト   
説明 | 制約 | 推奨事項
------- | ------- | ----------
これはサインイン フォームの下部に表示され、ヘルプ デスクの電話番号や法的声明などの追加情報を知らせるために使用できます。 | 最大 256 文字の Unicode テキスト<br>プレーン テキストのみ (リンクや HTML タグを含まない)   

### <a name="sign-in-page-image"></a>サインイン ページの画像  
説明 | 制約 | 推奨事項
------- | ------- | ----------
これはサインイン ページの背景に表示され、表示可能な領域の中央に固定されて、ブラウザー ウィンドウを埋めるように拡大したりトリミングしたりします。    <br>携帯電話のように小さい画面では、この画像は表示されません。<br>ページが読み込まれるときに、コードによってこの画像に不透明度 0.55 の黒いマスクが適用されます。 | JPG または PNG<br>画像サイズ: 1920 x 1080 ピクセル<br>ファイル サイズ: &lt; 300 KB | <br>強調して表示したいものが集中していない場所でこの画像を使用します。 非透過のサインイン フォームがこの画像の中央に覆いかぶさるように表示され、ブラウザー ウィンドウのサイズに応じて画像の一部を覆うことができます。<br>ファイル サイズをできるだけ小さくして読み込み時間を短縮します。 

### <a name="background-color"></a>背景色
説明 | 制約 | 推奨事項
------- | ------- | ----------
回線速度が遅い場合に、この色が背景画像の代わりに表示されます。 | 16 進数の RGB 色 (例: #FFFFFF) | バナー ロゴまたは所属する組織のプライマリ カラーを使用することをお勧めします。

### <a name="show-option-to-remain-signed-in"></a>サインインしたままにする表示オプション
説明 | 制約 | 推奨事項
------- | ------- | ----------
Azure AD サインインでは、ユーザーはブラウザーを閉じたり開き直したりしたときにサインインしたままの状態にするオプションを選択できます。 これはそのオプションを非表示にするときに使用します。<br>これを "No" に設定するとユーザーにはこのオプションが表示されなくなります。 | &nbsp; | これはセッションの有効期間には影響しません。<br>SharePoint Online と Office 2010 の一部の機能は、ユーザーがサインインしたままにすることを選択できるかどうかに依存します。 これを非表示に設定する場合、ユーザーにサインインを求める追加のメッセージが予期せず表示されることがあります。

> [!NOTE]
> すべての要素は省略できます。 たとえば、背景画像なしでバナー ロゴを指定した場合、サインイン ページにはロゴとともに目的のサイトの背景画像が表示されます (例: Office 365)。

## <a name="add-company-branding-to-your-directory"></a>ディレクトリに会社のブランドを追加するには

1. ディレクトリの全体管理者であるアカウントで [Azure Portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、テキスト ボックスに「**ユーザーとグループ**」と入力して、**Enter** キーを押します。

   ![ユーザー管理を開く](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[会社のブランド]** を選択します。
4. **[Users and groups - Company branding (ユーザーとグループ - 会社のブランド)]** ブレードで、**[編集]** をクリックします。

    ![カスタム ブランドを編集する](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. カスタマイズする要素に変更を加えます。 すべての要素は省略できます。
6. [ **Save**] をクリックします。

サインイン ページのブランドに加えた変更が表示されるまでに、最大 1 時間かかる場合があります。

## <a name="add-language-specific-company-branding-to-your-directory"></a>言語固有の会社のブランドをディレクトリに追加するには

1. ディレクトリのグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
2. テキスト ボックスで **[ユーザーとグループ]** を選択し、次に **[入力]** を選択します。

   ![ユーザー管理を開く](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. **[ユーザーとグループ]** ブレードで、**[会社のブランド]** を選択します。
4. **[ユーザーとグループ - 会社のブランド]** ブレードで、**[言語の追加]** をクリックします。

    ![言語固有のブランド要素を追加する](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. カスタマイズする要素に変更を加えます。 すべての要素は省略できます。
6. [ **Save**] をクリックします。

サインイン ページのブランドに加えた変更が表示されるまでに、最大 1 時間かかる場合があります。

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、Azure AD ディレクトリに会社のブランドを追加する方法について学習しました。 

次のリンクを使用して、Azure ポータルから、会社のブランドを Azure AD に構成することができます。

> [!div class="nextstepaction"]
> [会社のブランドの構成](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 
