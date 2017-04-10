---
title: "Azure Active Directory のアクセスに関する問題のトラブルシューティング | Microsoft Docs"
description: "組織のオンライン リソースへのアクセスに関する問題を解決するための手順について説明します。"
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
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fbabf6f2e1e588ba509c4da84ab1700b1b5d4f87
ms.openlocfilehash: ad9f9a8c5b370ffa916b9089ef3ce523fe0266c7


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Azure Active Directory のアクセスに関する問題のトラブルシューティング
社内の SharePoint Online イントラネットにアクセスしようとして、"アクセスが拒否されました" というエラー メッセージが表示されたら、 皆さんはどうしますか。


この記事では、組織のオンライン リソースへのアクセスに関する問題を解決するための手順について説明します。

Azure Active Directory (Azure AD) のアクセスに関する問題を解決するためには、ご利用のデバイス プラットフォームを対象とした記事のセクションを参照してください。

* Windows デバイス
* iOS デバイス (iPhone と iPad については準備中)
* Android デバイス (Android スマートフォンとタブレットについては準備中)

## <a name="access-from-a-windows-device"></a>Windows デバイスからのアクセス
ご利用のデバイスで次のいずれかのプラットフォームが実行されている場合は、アプリケーションまたはサービスにアクセスしようとしたときに表示されるエラー メッセージを以降のセクションから探してください。

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>デバイスが登録されていない
デバイスが Azure AD には登録されていないものの、アプリケーションがデバイス ベースのポリシーで保護されている場合、次のいずれかのエラー メッセージが表示される可能性があります。

![登録されていないデバイスに対する "ここからはアクセスできません" メッセージ](./media/active-directory-conditional-access-device-remediation/01.png "シナリオ")

デバイスが社内の Active Directory ドメインに参加している場合は、次の手順を試してください。

1. 職場アカウント (Active Directory アカウント) を使用して Windows にサインインしていることを確認します。
2. 仮想プライベート ネットワーク (VPN) または DirectAccess を使用して企業ネットワークに接続します。
3. 接続後、Windows ロゴ キーを押しながら L キーを押して Windows セッションをロックします。
4. 職場アカウントの資格情報を入力して、Windows セッションのロックを解除します。
5. しばらく待ってから、アプリケーションまたはサービスにもう一度アクセスしてみます。
6. 同じページが表示された場合は、**[詳細]** リンクをクリックして詳細情報を管理者にお伝えください。

デバイスがドメインに参加しておらず、Windows 10 を実行している場合、次の 2 つの選択肢があります。

* Azure AD Join を実行する。
* 職場または学校アカウントを Windows に追加する。

この 2 つの選択肢の違いについては、「[職場での Windows 10 デバイスの使用](active-directory-azureadjoin-windows10-devices.md)」を参照してください。

Azure AD Join を実行するには、次の手順に従います。ご利用のデバイスで稼働しているプラットフォームに応じた手順を実行してください。 (Windows Phone では Azure AD Join が利用できません。)

**Windows 10 Anniversary Update**

1. **[設定]** アプリを開きます。
2. **[アカウント]** > **[職場または学校にアクセスする]** をクリックします。
3. **[接続]**をクリックします。
4. **[Join this device to Azure AD (このデバイスを Azure AD に参加させる)]** をクリックします。
5. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
6. サインアウトしてから、職場アカウントを使用してサインインします。
7. アプリケーションにもう一度アクセスしてみます。

**Windows 10 November 2015 Update**

1. **[設定]** アプリを開きます。
2. **[システム]** > **[バージョン情報]** をクリックします。
3. **[Azure AD に参加]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. サインアウトしてから、職場アカウント (Azure AD アカウント) を使用してサインインします。
6. アプリケーションにもう一度アクセスしてみます。

職場または学校アカウントを追加するには、次の手順を実行します。

**Windows 10 Anniversary Update**

1. **[設定]** アプリを開きます。
2. **[アカウント]** > **[職場または学校にアクセスする]** をクリックします。
3. **[接続]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. アプリケーションにもう一度アクセスしてみます。

**Windows 10 November 2015 Update**

1. **[設定]** アプリを開きます。
2. **[アカウント]** > **[Your accounts (自分のアカウント)]** をクリックします。
3. **[Add work or school account (職場または学校アカウントを追加)]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. アプリケーションにもう一度アクセスしてみます。

デバイスがドメインに参加しておらず、Windows 8.1 を実行している場合は、Workplace Join を実行し、次の手順に従って、Microsoft Intune に登録できます。

1. **[PC 設定]**を開きます。
2. **[ネットワーク]** > **[社内]** をクリックします。
3. **[結合]**をクリックします。
4. 自分の組織に対する本人確認を行い、Multi-Factor Authentication の認証情報を提示して、表示される手順に従います。
5. **[オン]**をクリックします。
6. アプリケーションにもう一度アクセスしてみます。

### <a name="browser-is-not-supported"></a>ブラウザーがサポート対象外
次のいずれかのブラウザーでアプリケーションまたはサービスにアクセスしようとすると、アクセスが拒否される場合があります。

* Windows 10 または Windows Server 2016 の場合、Chrome や Firefox など、Microsoft Edge および Microsoft Internet Explorer 以外のブラウザー。
* Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012、または Windows Server 2008 R2 の場合、Firefox。

次のようなエラー ページが表示されます。

![サポートされていないブラウザーに対する "ここからはアクセスできません" メッセージ](./media/active-directory-conditional-access-device-remediation/02.png "シナリオ")

唯一の修復方法は、デバイスのプラットフォーム向けにアプリケーションでサポートされているブラウザーを使用することです。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)




<!--HONumber=Jan17_HO4-->


