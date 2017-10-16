---
title: Azure Active Directory Terms of Use| Microsoft Docs
description: "Azure AD Terms of Use を使うと、Azure AD サービスのユーザーに使用条件を提供できます。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: b6318b419a0ea87fd1fb56656b1161909876f338
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Azure Active Directory Terms of Use 機能 (プレビュー)
Azure AD Terms of Use は、エンド ユーザーに情報を提示するために使うことができる簡単な方法を提供します。  これにより、ユーザーは法律またはコンプライアンスの要件に関する免責事項を確認できます。

Azure AD Terms of Use は、pdf 形式で内容を提示します。   この pdf はどのような内容でもよく (既存の契約ドキュメントなど)、エンド ユーザーのサインイン時にユーザーの同意を得ることができます。  使用条件は、アプリケーション、ユーザーのグループ、または複数の使用条件がある場合はさまざまな目的に対して、使うことができます。

以降では、Azure AD Terms of Use の使い方について説明します。  

## <a name="why-use-azure-ad-terms-of-use"></a>Azure AD Terms of Use を使う理由
従業員やゲストからアクセス前に使用条件への同意を得ることが難しくはありませんか。 会社の使用条件に同意したユーザーと同意していないユーザーを確認する手助けが必要ではありませんか。  Azure AD Terms of Use は、エンド ユーザーに情報を提示するために使うことができる簡単な方法を提供します。  これにより、ユーザーは法律またはコンプライアンスの要件に関する免責事項を確認できます。

Azure AD Terms of Use は、次のシナリオで使用できます。
-   組織内のすべてのユーザーに対する一般的な使用条件。
-   ユーザーの属性に基づく特定の使用条件 (例:  [動的グループ](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)によって行われる、医師と看護師や、国内従業員と国際従業員などの区別)。
-   ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスに基づく特定の使用条件。


## <a name="prerequisites"></a>前提条件
Azure AD Terms of Use を構成する手順は次のとおりです。

1. グローバル管理者、セキュリティ管理者、または Azure AD Terms of Use を構成するディレクトリの条件付きアクセス管理者を使って、Azure AD にサインインします。
2. ディレクトリに、Azure AD Premium P1、P2、EMS E3、EMS E5 のいずれかのサブスクリプションがあることを確認します。  ない場合は、[Azure AD Premium](active-directory-get-started-premium.md) を入手するか、[試用版の利用を開始](https://azure.microsoft.com/trial/get-started-active-directory/)します。
3. [https://aka.ms/catou](https://aka.ms/catou) で Azure AD Terms of User ダッシュボードを表示します。



## <a name="add-company-terms-of-use"></a>会社の使用条件を追加する
使用条件が完成した後は、次の手順を使って追加します。

### <a name="to-add-terms-of-use"></a>使用条件を追加するには
1. ダッシュボード ([https://aka.ms/catou](https://aka.ms/catou)) に移動します
2. [追加] をクリックします。</br>
![TOU の追加](media/active-directory-tou/tou2.png)
3. 使用条件の **[名前]** を入力します。
4. **[表示名]** を入力します。  このヘッダーは、ユーザーがサインインするときに表示されます。
5. 完成した使用条件の pdf を**参照**して選びます。  推奨フォント サイズは 24 です。
6. テンプレートまたはカスタム条件付きアクセス ポリシーを使って、アップロードした使用条件を**適用**できます。  カスタム条件付きアクセス ポリシーを使うと、特定のクラウド アプリケーションやユーザー グループまで、きめ細かい使用条件を有効にできます。  詳しくは、[条件付きアクセス ポリシーの構成](active-directory-conditional-access-best-practices.md)に関するページをご覧ください。
7. **Create** をクリックしてください。
8. カスタム条件付きアクセス テンプレートを選んだ場合、表示される新しい画面で CA のポリシーをカスタマイズできます。
7. 新しい使用条件が表示されるようになります。</br>

![TOU の追加](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>使用条件を削除する
次の手順を使って、古い使用条件を削除できます。

### <a name="to-delete-terms-of-use"></a>使用条件を削除するには
1. ダッシュボード ([https://aka.ms/catou](https://aka.ms/catou)) に移動します
2. 削除する使用条件を選びます。
3. [ **削除**] をクリックします。
4. 使用条件が表示されなくなります。


## <a name="audit-terms-of-use"></a>使用条件を監査する
Azure AD Terms of Use には使いやすい監査機能があり、誰がいつ使用条件を受け入れたかを確認できます。  監査を使い始める手順は次のとおりです。

### <a name="to-audit-terms-of-use"></a>使用条件を監査するには
1. ダッシュボード ([https://aka.ms/catou](https://aka.ms/catou)) に移動します
2. [監査イベント] をクリックします。</br>
![監査イベント](media/active-directory-tou/tou8.png)
3.  Azure AD の [監査ログ] 画面では、ドロップダウンを使って情報をフィルターし、特定の監査ログ情報だけを対象にすることができます。
![監査イベント](media/active-directory-tou/tou9.png)
4.  .csv ファイルで情報をダウンロードしてローカルに使うこともできます。

## <a name="what-users-see"></a>ユーザーに対する表示
使用条件を作成して適用すると、対象ユーザーには次のように表示されます。  これらの画面はサインインの間に表示されます。
-   PDF のフォント サイズは 24 にすることをお勧めします。
![監査イベント](media/active-directory-tou/tou10.png)
-   モバイル デバイスでは次のように表示されます</br></br>
![監査イベント](media/active-directory-tou/tou11.png)

## <a name="additional-information"></a>追加情報
使用条件を使うときに知っておくべき情報と役に立つ情報を次に示します。


次の場合、新しいポリシーを満たすには、対象ユーザーはいったんサインアウトしてからサインインし直す必要があります。
 - 使用条件で条件付きアクセス ポリシーが有効になった場合
 - または、第 2 の使用条件が作成された場合

これは、条件付きアクセス ポリシーは直ちに有効になるためです。 このような場合、管理者には "不適切なクラウド" または "Azure AD トークンの問題" が表示されるようになります。 新しいポリシーを満たすには、管理者はサインアウトしてサインインし直す必要があります。





## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q: ユーザーが使用条件を受け入れたかどうか、および受け入れた日時を確認するにはどうすればよいですか?**</br>
A: 使用条件を受け入れたユーザーは監査ログに書き込まれます。 Azure AD の監査ログを検索して結果を確認できます。  

**Q: 使用条件を変更した場合、ユーザーはもう一度同意する必要がありますか?**</br>
A: はい、管理者は使用条件を変更でき、ユーザーは新しい条件に同意し直す必要があります。

**Q: 使用条件は複数の言語をサポートしていますか?**</br>
A: いいえ、現在は、1 つの使用条件で複数の言語を使うことはできません。  ただし、グループを対象にすることができます (たとえば、フランスの使用条件は英国の使用条件と異なります)。 

**Q: 使用条件はいつトリガーされますか?**</br>
A: 使用条件は、サインイン エクスペリエンスの間にトリガーされます。

**Q: どのようなアプリケーションを使用条件の対象にできますか?**</br>
A: 最新の認証を使ったエンタープライズ アプリケーションに条件付きアクセス ポリシーを作成できます。  詳細については、[エンタープライズ アプリケーション](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal)に関するページをご覧ください。

**Q: 特定のユーザーまたはアプリケーションに複数の使用条件を追加できますか?**</br>
A: はい、そのグループやアプリを対象とする複数の条件付きアクセス ポリシーを作成することで可能です。 複数の使用条件の対象になっているユーザーは、一度に 1 つに使用条件に同意します。
 
**Q: ユーザーが使用条件を拒否した場合はどうなりますか?**</br>
A: ユーザーは、アプリケーションへのアクセスをブロックされます。 ユーザーは、アクセスするには、もう一度サインインして使用条件に同意する必要があります。