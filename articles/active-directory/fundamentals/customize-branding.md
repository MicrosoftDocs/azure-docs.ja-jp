---
title: 組織のサインイン ページにブランドを追加する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のサインイン ページに組織のブランドを追加する方法に関する説明。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: lizross
ms.reviewer: kexia
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bef3960e5c117a131b59a65196214c51e01bd3c8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204577"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>組織の Azure Active Directory のサインイン ページにブランドを追加する
組織のロゴとカスタム カラー スキーマを使用して、Azure Active Directory (Azure AD) のサインイン ページの外観に一貫性を持たせます。 サインイン ページは、Azure AD を ID プロバイダーとして使用している組織の Web ベースのアプリ (Office 365 など) にユーザーが サインインするときに表示されます。

>[!Note]
>カスタム ブランドを追加するには、Azure Active Directory Premium 1 エディション、Premium 2 エディション、または Basic エディションを使用しているか、Office 365 ライセンスを持っている必要があります。 ライセンスとエディションの詳細については、「[Azure AD Premium へのサインアップ](active-directory-get-started-premium.md)」を参照してください。<br><br>Azure Active Directory のワールドワイド インスタンスを使用している中国のお客様は、Azure AD Premium および Basic エディションを使用できます。 中国の 21Vianet が運営する Azure サービスでは、Azure AD Premium エディションと Basic エディションは現在サポートされていません。 詳細については、[Azure Active Directory フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)を利用してお問い合わせください。

## <a name="customize-your-azure-ad-sign-in-page"></a>Azure AD のサインイン ページをカスタマイズする
Azure AD のサインイン ページをカスタマイズできます。このページは、ユーザーが組織のテナント固有のアプリ ([ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com) など) にサインインするとき、またはドメイン変数 ([ *https://passwordreset.microsoftonline.com/?whr=contoso.com* ](https://passwordreset.microsoftonline.com/?whr=contoso.com)) を渡すときに表示されます。

カスタム ブランドは、ユーザーが www\.office.com などのサイトに移動したときにすぐに表示されるわけではありません。 カスタマイズされたブランドは、ユーザーがサインインした後で表示されます。

> [!NOTE]
> すべてのブランド要素は省略可能です。 たとえば、背景画像なしでバナー ロゴを指定した場合、サインイン ページには目的のサイト (たとえば Office 365) の既定の背景画像とロゴが表示されます。<br><br>さらに、サインイン ページのブランド情報は、個人用 Microsoft アカウントに継承されることはありません。 ユーザーまたはゲスト ユーザーが個人用 Microsoft アカウントを使用してサインインした場合、そのサインイン ページには組織のブランドは反映されません。

### <a name="to-customize-your-branding"></a>ブランドをカスタマイズするには
1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

2. **Azure Active Directory** を選択し、 **[会社のブランド]** を選択し、 **[構成]** を選択します。

    ![[Contoso - 会社のブランド] ページ。[構成] オプションを強調表示](media/customize-branding/company-branding-configure-button.png)

3. **[会社のブランドの構成]** ページで、次の情報の一部または全部を指定します。

    >[!Important]
    >このページに追加するすべてのカスタム イメージには、イメージのサイズ (ピクセル単位) と可能なファイル サイズ (KB) の制限があります。 これらの制限により、おそらくフォト エディターを使用して適切なサイズのイメージを作成する必要があります。

    - **全般設定**

        ![[会社のブランドの構成] ページ。全般設定完了済み](media/customize-branding/configure-company-branding-general-settings.png)

        - **言語**。 言語は、既定値として自動的に設定され、変更することはできません。
        
        - **サインイン ページの背景画像**。 サインイン ページの背景として表示する .png または .jpg 画像ファイルを選択します。 
        
            この画像のサイズは 1920 x 1080 ピクセルを超えることはできず、ファイル サイズは 300 KB 未満である必要があります。

        - **バナー ロゴ**。 ユーザーがユーザー名を入力した後のサインイン ページと **[マイ アプリ]** ポータル ページに表示するロゴの .png または .jpg のバージョンを選択します。
            
            この画像は、高さ 60 ピクセル、幅 280 ピクセルを超えることはできません。 背景とロゴの背景が調和しない可能性があるため、透過画像を使用することをお勧めします。 さらに、画像の周囲にパディングを追加しないことをお勧めします。これを行うとロゴが小さくなる可能性があります。

        - **ユーザー名のヒント**。 ユーザーがユーザー名を忘れた場合に表示されるヒント テキストを入力します。 このテキストは、リンクもコードも含まない Unicode にする必要があり、64 文字を超えることはできません。 ゲストがアプリにサインインする場合は、このヒントを追加しないことをお勧めします。

        - **サインイン ページ テキスト**。 サインイン ページの下部に表示されるテキストを入力します。 このテキストを使用して、ヘルプ デスクの電話番号や法的声明などの追加情報を伝達できます。 このテキストは Unicode であり、256 文字以下にする必要があります。 さらに、リンクまたは HTML タグを含めないことをお勧めします。

    - **詳細設定**
            
        ![[会社のブランドの構成] ページ。詳細設定完了済み](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **サインイン ページの背景色**。 帯域幅が小さい接続状況で背景画像の代わりに表示される 16 進数の色を指定します (たとえば、白は #FFFFFF)。 バナー ロゴまたは組織のプライマリ カラーを使用することをお勧めします。

        - **正方形のロゴ イメージ**。 新しい Windows 10 Enterprise デバイスのセットアップ プロセス中にユーザーに表示される組織のロゴの .png (推奨) または .jpg 画像を選択します。 この画像は Windows 認証でのみ使用され、デプロイで [Windows Autopilot]( https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) を使用しているテナント、または他の Windows 10 エクスペリエンスのパスワード入力ページにのみ表示されます。 場合によっては、同意ダイアログに表示されることもあります。
        
            この画像のサイズは 240 x 240 ピクセルを超えることはできず、ファイル サイズは 10 KB 未満である必要があります。 背景とロゴの背景が調和しない可能性があるため、透過画像を使用することをお勧めします。 さらに、画像の周囲にパディングを追加しないことをお勧めします。これを行うとロゴが小さくなる可能性があります。
    
        - **正方形のロゴ、濃色のテーマ**。 上記の正方形のロゴ イメージと同じです。 このロゴ イメージは、OOBE (out-of-box experience) 中に Windows 10 の Azure AD 参加画面などの暗い背景で正方形のロゴ イメージが使用されるときに使用されます。  ロゴが白、濃い青、および黒の背景で見やすく表示される場合は、このイメージを追加する必要はありません。 
        
        - **サインインしたままにする表示オプション**。 ユーザーが明示的にサインアウトするまで Azure AD にサインインしたままにすることを選択できます。 **[いいえ]** を選択した場合、このオプションは非表示になり、ユーザーはブラウザーを閉じて再度開くたびにサインインする必要があります。
        
            >[!Note]
            >SharePoint Online と Office 2010 の一部の機能は、ユーザーがサインインしたままにすることを選択できるかどうかに依存します。 このオプションを **[いいえ]** に設定すると、ユーザーにサインインを求める追加のメッセージが予期せず表示されることがあります。
   

3. ブランドの追加が完了したら、 **[保存]** を選択します。

    このプロセスによって最初のカスタム ブランド構成が作成され、それがテナントの既定値になります。 追加構成がある場合に、既定の構成を選択できます。
    
    >[!Important]
    >企業のブランド構成をテナントにさらに追加するには、 **[Contoso - 会社のブランド]** ページで **[新しい言語]** を選択する必要があります。 これにより **[会社のブランドの構成]** ページが開き、上記と同じ手順に従うことができます。

## <a name="update-your-custom-branding"></a>カスタム ブランドを更新する
カスタム ブランドを作成した後、同じページに戻って変更を行うことができます。

### <a name="to-edit-your-custom-branding"></a>カスタム ブランドを編集するには
1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

2. **Azure Active Directory** を選択し、 **[会社のブランド]** を選択し、 **[構成]** を選択します。

    ![[Contoso - 会社のブランド] ページ。既定の構成を表示](media/customize-branding/company-branding-default-config.png)

3. この記事の「[Azure AD のサインイン ページをカスタマイズする](#customize-your-azure-ad-sign-in-page)」セクションの説明に基づいて、 **[会社のブランドの構成]** ページで任意の情報を追加、削除、または変更します。

4. **[保存]** を選択します。

   サインイン ページのブランドに加えた変更が表示されるまでに、最大 1 時間かかる場合があります。

## <a name="add-language-specific-company-branding-to-your-directory"></a>言語固有の会社のブランドをディレクトリに追加するには
元の構成の言語を既定の言語から変更することはできません。 ただし、別の言語の構成が必要な場合は、新しい構成を作成できます。

### <a name="to-add-a-language-specific-branding-configuration"></a>言語固有のブランド構成を追加するには

1. ディレクトリのグローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

2. **Azure Active Directory** を選択し、 **[会社のブランド]** を選択し、 **[新しい言語]** を選択します。

    ![[Contoso - 会社のブランド] ページ。[新しい言語] オプションを強調表示](media/customize-branding/company-branding-new-language.png)

3. この記事の「[Azure AD のサインイン ページをカスタマイズする](#customize-your-azure-ad-sign-in-page)」セクションの説明に基づいて、 **[会社のブランドの構成]** ページで言語 (例: フランス語) を選択し、翻訳した情報を追加します。

4. **[保存]** を選択します。

    **[Contoso – 会社のブランド]** ページが更新され、新しいフランス語の構成が表示されます。

    ![[Contoso - 会社のブランド] ページ。既定の構成を表示](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>ページにカスタム ブランドを追加するには
ページにカスタム ブランドを追加するには、URL の末尾を `?whr=yourdomainname` テキストを使用して変更します。 この変更は、多要素認証 (MFA) 設定ページ、セルフサービス パスワード リセット (SSPR) 設定 ページ、サインイン ページを含むさまざまなページに反映されます。

**例:**

**元の URL:** https://aka.ms/MFASetup<br>
**カスタム URL:** https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com

**元の URL:** https://aka.ms/SSPR<br>
**カスタム URL:** https://passwordreset.microsoftonline.com/?whr=contoso.com

 
