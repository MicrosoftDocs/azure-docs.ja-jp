---
title: "Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス | Microsoft Docs"
description: "条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。 条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: f96189735512090f993f61c0d64a249f650ea2a2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)によって、許可されたユーザーがリソースにアクセスする方法を微調整できます。  
このトピックでは、条件付きアクセス ポリシーの次のアイテムのサポート情報を提供します。 

- クラウド アプリの割り当て

- クライアント アプリの条件



## <a name="cloud-apps-assignments"></a>クラウド アプリの割り当て

条件付きアクセス ポリシーを構成する場合、[ポリシーの適用対象のクラウド アプリを選択](active-directory-conditional-access-azure-portal.md#who)する必要があります。 

![コントロール](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Microsoft クラウド アプリ

Microsoft から、次のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。

- Azure Remote App

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (OneDrive for Business を含む)

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft Teams


### <a name="other-apps"></a>その他のアプリ 

Microsoft クラウド アプリに加えて、次の種類のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。

- Azure Active Directory (Azure AD) に接続されているアプリケーション

- フェデレーションが事前に統合されているサービスとしてのソフトウェア (SaaS) アプリケーション

- パスワード シングル サインオン (SSO) を使うアプリケーション

- 基幹業務アプリケーション

- Azure AD アプリケーション プロキシを使うアプリケーション。 


## <a name="client-apps-conditions"></a>クライアント アプリの条件 

条件付きアクセス ポリシーを構成する場合、[クライアント アプリの条件](active-directory-conditional-access-azure-portal.md#client-apps)を設定できます。 クライアント アプリの条件では、次の種類のクライアント アプリからアクセス試行が行われたときに、アクセスを許可またはブロックすることができます。

- [ブラウザー] ボタンを
- モバイル アプリとデスクトップ アプリ

![コントロール](./media/active-directory-conditional-access-technical-reference/03.png)


### <a name="supported-browsers"></a>サポートされているブラウザー 

条件付きアクセス ポリシーで *[ブラウザー]* を選択してリソースへのアクセスを許可する場合、アクセスは、サポートされているブラウザーを使ってアクセス試行が行われた場合にのみ許可されます。 サポートされていないブラウザーを使ってアクセス試行が行われた場合、試行はブロックされます。

![サポートされているブラウザー](./media/active-directory-conditional-access-technical-reference/05.png)

条件付きアクセス ポリシーでは、次のブラウザーがサポートされています。 


| OS                     | ブラウザー                 | サポート     |
| :--                    | :--                      | :-:         |
| Windows 10                 | IE、Edge                 | ![○][1] |
| Windows 10                 | Chrome                   | Preview     |
| Windows 8/8.1            | IE、Chrome               | ![○][1] |
| Windows 7                  | IE、Chrome               | ![○][1] |
| iOS                    | Safari                   | ![○][1] |
| Android                | Chrome                   | ![○][1] |
| Windows Phone          | IE、Edge                 | ![○][1] |
| Windows Server 2016    | IE、Edge                 | ![○][1] |
| Windows Server 2016    | Chrome                   | 近日対応予定 |
| Windows Server 2012 R2 | IE、Chrome               | ![○][1] |
| Windows Server 2008 R2 | IE、Chrome               | ![○][1] |
| Mac OS                 | Safari                   | ![○][1] |
| Mac OS                 | Chrome                   | 近日対応予定 |

> [!NOTE]
> Chrome サポートの場合、Windows 10 Creators Update を使用している必要があり、[ここ](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)に示されている拡張機能をインストールする必要があります。


### <a name="supported-mobile-apps-and-desktop-clients"></a>サポートされているモバイル アプリとデスクトップ クライアント

条件付きアクセス ポリシーで **[モバイル アプリとデスクトップ クライアント]** を選択してリソースへのアクセスを許可する場合、アクセスは、サポートされているモバイル アプリまたはデスクトップ クライアントを使ってアクセス試行が行われた場合にのみ許可されます。 サポートされていないモバイル アプリまたはデスクトップ クライアントを使ってアクセス試行が行われると、試行はブロックされます。

![コントロール](./media/active-directory-conditional-access-technical-reference/06.png)

以下のモバイル アプリとデスクトップ クライアントは、Office 365 をはじめとする Azure AD 接続サービス アプリケーションに対する条件付きアクセスをサポートします。


| クライアント アプリ| 対象サービス| プラットフォーム |
| :-- | --- | --- |
| アプリ用の MFA と場所のポリシー。 デバイス ベースのポリシーはサポートされていません。| 任意のマイ アプリ アプリ サービス| Android および iOS|
| Azure リモート アプリ| Azure Remote App サービス| Windows 10、Windows 8.1、Windows 7、iOS、Android、Mac OS X|
| Dynamics CRM アプリ| Dynamics CRM| Windows 10、Windows 8.1、Windows 7、iOS、Android|
| Microsoft Teams Services - Microsoft Teams とそのすべてのクライアント アプリ (Windows デスクトップ、MAC OS X、iOS、Android、WP、および Web クライアント) をサポートするすべてのサービスを制御します| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS、Android、および MAC OS X|
| メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用)| Office 365 Exchange Online| Windows 10|
| Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用)| Office 365 Exchange Online| Windows 8.1、Windows 7|
| Outlook Mobile アプリ| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office for macOS)| Office 365 Exchange Online| Mac OS X|
| Office 2016 アプリ、ユニバーサル Office アプリ、Office 2013 (最新の認証を使用)、OneDrive 同期クライアント ([メモ](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)参照)。Office Groups および SharePoint アプリについては将来サポート予定| Office 365 SharePoint Online| Windows 10|
| Office 2016 アプリ、Office 2013 (最新の認証を使用)、OneDrive 同期クライアント ([メモ](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)参照)| Office 365 SharePoint Online| Windows 8.1、Windows 7|
| Office モバイル アプリ| Office 365 SharePoint Online| iOS、Android|
| Office 2016 for macOS (Word、Excel、PowerPoint、OneNote のみ)。 OneDrive for Business は将来サポート予定| Office 365 SharePoint Online| Mac OS X|
| Office Yammer アプリ| Office 365 Yammer| Windows 10、iOS、Android|
| PowerBI アプリ。 Android 用の Power BI では現在、デバイス ベースの条件付きアクセスはサポートされていません。| PowerBI サービス| Windows 10、Windows 8.1、Windows 7、iOS|
| Visual Studio Team Services アプリ| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS、Android|














## <a name="next-steps"></a>次のステップ

- 条件付きアクセスの概要については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」をご覧ください
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」をご覧ください



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



