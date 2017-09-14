---
title: "Windows デバイスから Azure Portal で目的の場所にアクセスできない | Microsoft Docs"
description: "このダイアログが表示されないようにするために、目的の場所にアクセスできない原因と確認事項について説明します。"
services: active-directory
keywords: "デバイス ベースの条件付きアクセス, デバイス登録, デバイス登録の有効化, デバイス登録と MDM"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/05/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: d86e47507f7a578e8a0affb690b6d96673b6b69a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="you-cant-get-there-from-here-on-a-windows-device"></a>Windows デバイスで目的の場所にアクセスできない

たとえば、組織の SharePoint Online イントラネットにアクセスしようとしたとき、"*ここからアクセスすることはできません*" というページが表示される可能性があります。 このページは、特定の条件下において組織のリソースへのアクセスを防ぐ条件付きアクセス ポリシーを管理者が構成しているために表示されています。 ヘルプ デスクまたは管理者に連絡しないと問題を解決できない場合もありますが、まずはこちらのいくつかの手順をお試しください。

**Windows** デバイスを使用している場合、次のことを確認する必要があります。

- サポートされているブラウザーを使用していますか。

- お使いのデバイスで、サポートされているバージョンの Windows を実行していますか。

- お使いのデバイスは準拠デバイスですか。






## <a name="supported-browser"></a>サポートされているブラウザー

管理者によって条件付きアクセス ポリシーが構成されている場合、サポートされているブラウザーを使用しないと組織のリソースにアクセスできません。 Windows デバイスでは、サポートされているのは **Internet Explorer** と **Edge** のみです。

リソースにアクセスできない原因がサポートされていないブラウザーにあるかどうかは、エラー ページの詳細セクションで簡単に識別できます。

![サポートされていないブラウザーに対する "ここからはアクセスできません" メッセージ](./media/active-directory-conditional-access-device-remediation/02.png "シナリオ")

唯一の修復方法は、デバイスのプラットフォーム向けにアプリケーションでサポートされているブラウザーを使用することです。 サポートされているブラウザーの完全な一覧については、「[サポートされているブラウザー](active-directory-conditional-access-supported-apps.md)」を参照してください。  


## <a name="supported-versions-of-windows"></a>サポートされている Windows のバージョン

デバイスで使用する Windows オペレーティング システムは、次の条件を満たしている必要があります。 

- デバイスで Windows デスクトップ オペレーティング システムを実行している場合、Windows 7 以降である必要があります。
- デバイスで Windows Server オペレーティング システムを実行している場合、Windows Server 2008 R2 以降である必要があります。 


## <a name="compliant-device"></a>準拠デバイス

準拠デバイスからでないと組織のリソースにアクセスできないという条件付きアクセス ポリシーを、管理者が構成している可能性があります。 準拠させるには、デバイスをオンプレミスの Active Directory に参加させるか、Azure Active Directory に参加させる必要があります。

リソースにアクセスできない原因が準拠していないデバイスにあるかどうかは、エラー ページの詳細セクションで簡単に識別できます。
 
![登録されていないデバイスに対する "ここからはアクセスできません" メッセージ](./media/active-directory-conditional-access-device-remediation/01.png "シナリオ")


### <a name="is-your-device-joined-to-an-on-premises-active-directory"></a>デバイスがオンプレミスの Active Directory に参加している場合

**デバイスが組織内のオンプレミスの Active Directory に参加している場合は、次の手順を実施します。**

1. 職場アカウント (Active Directory アカウント) を使用して Windows にサインインしていることを確認します。
2. 仮想プライベート ネットワーク (VPN) または DirectAccess を使用して企業ネットワークに接続します。
3. 接続後、Windows ロゴ キーを押しながら L キーを押して Windows セッションをロックします。
4. 職場アカウントの資格情報を入力して、Windows セッションのロックを解除します。
5. しばらく待ってから、アプリケーションまたはサービスにもう一度アクセスしてみます。
6. 同じページが表示された場合は、**[詳細]** リンクをクリックして詳細情報を管理者にお伝えください。


### <a name="is-your-device-not-joined-to-an-on-premises-active-directory"></a>デバイスがオンプレミスの Active Directory に参加していない場合

デバイスがオンプレミスの Active Directory に参加しておらず、Windows 10 を実行している場合、次の 2 つの選択肢があります。

* Azure AD Join を実行する。
* 職場または学校アカウントを Windows に追加する。

この 2 つの選択肢の違いについては、「[職場での Windows 10 デバイスの使用](active-directory-azureadjoin-windows10-devices.md)」を参照してください。  
デバイスについては、次のとおりです。

- 組織のデバイスである場合は、Azure AD Join を実行する必要があります。
- 個人のデバイスまたは Windows スマートフォンである場合は、Windows に職場または学校アカウントを追加する必要があります。 



#### <a name="azure-ad-join-on-windows-10"></a>Windows 10 での Azure AD Join

デバイスを Azure AD に参加させる手順は、そのデバイス上で実行している Windows 10 のバージョンに関連付けられています。 お使いの Windows 10 オペレーティング システムのバージョンを確認するには、**winver** コマンドを実行します。 

![Windows のバージョン](./media/active-directory-conditional-access-device-remediation/03.png )


**Windows 10 Anniversary Update (Version 1607):**

1. **[設定]** アプリを開きます。
2. **[アカウント]** > **[職場または学校にアクセスする]** をクリックします。
3. **[接続]**をクリックします。
4. **[Join this device to Azure AD (このデバイスを Azure AD に参加させる)]** をクリックします。
5. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
6. サインアウトしてから、職場アカウントを使用してサインインします。
7. アプリケーションにもう一度アクセスしてみます。

**Windows 10 November 2015 Update (Version 1511):**

1. **[設定]** アプリを開きます。
2. **[システム]** > **[バージョン情報]** をクリックします。
3. **[Azure AD に参加]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. サインアウトしてから、職場アカウント (Azure AD アカウント) を使用してサインインします。
6. アプリケーションにもう一度アクセスしてみます。


#### <a name="workplace-join-on-windows-81"></a>Windows 8.1 での Workplace Join

デバイスがドメインに参加しておらず、Windows 8.1 を実行している場合は、Workplace Join を実行し、次の手順に従って、Microsoft Intune に登録できます。

1. **[PC 設定]**を開きます。
2. **[ネットワーク]** > **[社内]** をクリックします。
3. **[結合]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. **[オン]**をクリックします。
6. アプリケーションにもう一度アクセスしてみます。



#### <a name="add-your-work-or-school-account-to-windows"></a>職場または学校アカウントを Windows に追加する。 


**Windows 10 Anniversary Update (Version 1607):**

1. **[設定]** アプリを開きます。
2. **[アカウント]** > **[職場または学校にアクセスする]** をクリックします。
3. **[接続]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. アプリケーションにもう一度アクセスしてみます。


**Windows 10 November 2015 Update (Version 1511):**

1. **[設定]** アプリを開きます。
2. **[アカウント]** > **[Your accounts (自分のアカウント)]** をクリックします。
3. **[Add work or school account (職場または学校アカウントを追加)]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. アプリケーションにもう一度アクセスしてみます。





## <a name="next-steps"></a>次のステップ
[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)


