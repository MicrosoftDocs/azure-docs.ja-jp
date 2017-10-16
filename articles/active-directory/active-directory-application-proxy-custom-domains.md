---
title: "Azure AD アプリケーション プロキシでのカスタム ドメイン | Microsoft Docs"
description: "Azure AD アプリケーション プロキシでカスタム ドメインを管理して、ユーザーがアクセスする場所に関係なくアプリの URL が同じになるようにします。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1dde300780c8d1f7ea9eee4c92de06bcf70a1f12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシでのカスタム ドメインの使用

Azure Active Directory アプリケーション プロキシ経由でアプリケーションを公開するとき、離れた場所で作業しているユーザーのために外部 URL を作成します。 この URL には、既定のドメインである *yourtenant-msappproxy.net* が与えられます。 たとえば Expenses という名前のアプリを発行し、テナントの名前が Contoso である場合、外部 URL は https://expenses-contoso.msappproxy.net となります。 独自のドメイン名を使用する場合、アプリケーションのカスタム ドメインを構成します。 

可能な限り、アプリケーションのカスタム ドメインを設定することが推奨されます。 カスタム ドメインには次のような長所があります。

- ネットワークの中にいるか、外にいるかに関係なく、同じ URL でアプリケーションにアクセスできます。
- すべてのアプリケーションに同じ内部と外部の URL が与えられている場合、あるアプリケーション内にあり、別のアプリケーションを指すリンクが企業ネットワークの外でも引き続き機能します。 
- ブランドを制御し、必要な URL を作成します。 


## <a name="configure-a-custom-domain"></a>カスタム ドメインを構成する

### <a name="prerequisites"></a>前提条件

カスタム ドメインを構成する前に、次の要件が用意されていることを確認します。 
- [検証済みのドメインが Azure Active Directory に追加されている](active-directory-domains-add-azure-portal.md)。
- ドメインのカスタム証明書 (PFX ファイル形式) 
- [アプリケーション プロキシ経由で公開された](application-proxy-publish-azure-portal.md)オンプレミス アプリ。

### <a name="configure-your-custom-domain"></a>カスタム ドメインの構成

以上の 3 つの要件が用意されているとき、次の手順でカスタム ドメインを設定します。

1. [Azure Portal](https://portal.azure.com)にサインインします。
2. **[Azure Active Directory]** > **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に移動し、管理するアプリを選択します。
3. **[アプリケーション プロキシ]** を選択します。 
4. 外部 URL フィールドで、ドロップダウン リストを利用し、カスタム ドメインを選択します。 一覧にドメインが表示されない場合、そのドメインはまだ検証されていません。 
5. **[保存]** を選びます。
5. 無効になっていた **[証明書]** フィールドが有効になります。 このフィールドを選択します。 

   ![クリックし、証明書をアップロードします。](./media/active-directory-application-proxy-custom-domains/certificate.png)

   このドメインの証明書を既にアップロードしてある場合、その証明書の情報が [証明書] フィールドに表示されます。 

6. PFX 証明書をアップロードし、証明書のパスワードを入力します。 
7. **[保存]** を選択して変更を保存します。 
8. 新しい外部 URL を msappproxy.net ドメインにリダイレクトする [DNS レコード](../dns/dns-operations-recordsets-portal.md)を追加します。 

>[!TIP] 
>カスタム ドメインごとに証明書を 1 つだけアップロードします。 証明書をアップロードしたら、新しいアプリを公開するとき、カスタム ドメインを選択できます。DNS レコードを除き、追加の構成を行う必要はありません。 

## <a name="manage-certificates"></a>証明書の管理

### <a name="certificate-format"></a>証明書形式
証明書の署名方法に制約はありません。 楕円曲線暗号 (ECC)、サブジェクトの別名 (SAN)、その他の一般的な証明書の種類にすべて対応しています。 

ワイルドカード証明書は、そのワイルドカードが目的の外部 URL と一致する場合に限って使用できます。 

自己署名証明書を使用することもできます。 プライベート証明機関を使用している場合は、証明書の CDP (証明書失効ポイントの配布ポイント) をパブリックにする必要があります。

### <a name="changing-the-domain"></a>ドメインを変更する
検証済みのドメインはすべて、アプリケーションの外部 URL ドロップダウン リストに表示されます。 ドメインを変更するには、アプリケーションのそのフィールドだけを更新します。 必要なドメインが一覧にない場合、[検証済みのドメインとしてそれを追加](active-directory-domains-add-azure-portal.md)します。 証明書が関連付けられていないドメインを選択する場合、手順 5. ～ 7. を行い、証明書を追加します。 次に、新しい外部 URL からリダイレクトするように DNS レコードを更新します。 

### <a name="certificate-management"></a>証明書の管理
アプリケーションが外部ホストを共有しない限り、複数のアプリケーションで同じ証明書を使用できます。 

証明書の期限が切れると警告が表示され、ポータルから別の証明書をアップロードするように通知されます。 証明書が取り消された場合、アプリケーションにアクセスしたユーザーにセキュリティ警告が表示されることがあります。 証明書の失効チェックは行われません。  特定のアプリケーションの証明書を更新するには、そのアプリケーションに移動し、新しい証明書をアップロードするように公開済みのアプリケーションでカスタム ドメインを構成するための手順 5-7 を実行します。 古い証明書が他のアプリケーションにより使用されていない場合、自動的に削除されます。 

現在のところ、すべての証明書管理は個々のアプリケーション ページで行われます。アプリケーションとの関連で証明書を管理する必要があります。 

## <a name="next-steps"></a>次のステップ
* Azure AD 認証を使用して発行されたアプリに対する[シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)。
* 発行されたアプリに対する[条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)。
* [Azure AD にカスタム ドメイン名を追加する](active-directory-domains-add-azure-portal.md)


