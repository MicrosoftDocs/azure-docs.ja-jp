---
title: "Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス | Microsoft Docs"
description: "Azure Active Directory の条件付きアクセス制御の利用方法について学習します。 ユーザーを認証し、アプリケーションへのアクセスを制御するための条件を指定します。 指定した条件が満たされると、ユーザーは認証され、アプリケーションへのアクセス許可が与えられます。"
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
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 27ace4e9bc4a1626059fba657dce0c629d52f32d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)を利用し、許可されたユーザーがリソースにアクセスする方法を微調整できます。  

このトピックでは、条件付きアクセス ポリシーの次の構成オプションのサポート情報を提供します。 

- クラウド アプリケーションの割り当て

- デバイス プラットフォームの条件 

- クライアント アプリケーションの条件

- 承認されたクライアント アプリケーションの要件



## <a name="cloud-apps-assignments"></a>クラウド アプリの割り当て

条件付きアクセス ポリシーを構成する場合、[ポリシーを使用するクラウド アプリを選択](active-directory-conditional-access-azure-portal.md#who)する必要があります。 

![ポリシーに対してクラウド アプリを選択する](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft クラウド アプリケーション

Microsoft から、次のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (OneDrive for Business を含む)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>他のアプリケーション 

Microsoft クラウド アプリに加えて、次の種類のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。

- Azure AD に接続しているアプリケーション

- フェデレーションが事前に統合されているサービスとしてのソフトウェア (SaaS) アプリケーション

- パスワード シングル サインオン (SSO) を使うアプリケーション

- 基幹業務アプリケーション

- Azure AD アプリケーション プロキシを使うアプリケーション


## <a name="device-platform-condition"></a>デバイス プラットフォームの条件

条件付きアクセス ポリシーでは、デバイス プラットフォームの条件を構成し、ポリシーをクライアント上のオペレーティング システムに関連付けることができます。

![クライアント OS にアクセス ポリシーを関連付ける](./media/active-directory-conditional-access-technical-reference/41.png)

Azure AD 条件付きアクセスは、次のデバイス プラットフォームをサポートします。

- Android

- iOS

- Windows Phone

- Windows

- macOS (プレビュー)



## <a name="client-apps-condition"></a>クライアント アプリの条件 

条件付きアクセス ポリシーを構成する場合、クライアント アプリの条件に[クライアント アプリを選択](active-directory-conditional-access-azure-portal.md#client-apps)できます。 クライアント アプリの条件を設定し、次の種類のクライアント アプリからアクセス試行が行われたときに、アクセスを許可またはブロックします。

- [ブラウザー] ボタンを
- モバイル アプリとデスクトップ アプリ

![クライアント アプリのアクセスの制御](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>サポートされているブラウザー 

条件付きアクセス ポリシーの **[ブラウザー]** オプションを利用してブラウザーのアクセスを制御します。 サポートされているブラウザーがアクセスを試行したときにのみ、アクセスが与えられます。 サポートされていないブラウザーでアクセスが試された場合、アクセスはブロックされます。

![サポートされているブラウザーのアクセスの制御](./media/active-directory-conditional-access-technical-reference/05.png)

条件付きアクセス ポリシーでは、次のブラウザーがサポートされています。 


| OS                     | ブラウザー                    | サポート     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer、Edge     | ![○][1] |
| Windows 10             | Chrome                      | ![○][1] |
| Windows 8 / 8.1        | Internet Explorer、Chrome   | ![○][1] |
| Windows 7              | Internet Explorer、Chrome   | ![○][1] |
| iOS                    | Safari、Intune Managed Browser                      | ![○][1] |
| Android                | Chrome、Intune Managed Browser                      | ![○][1] |
| Windows Phone          | Internet Explorer、Edge     | ![○][1] |
| Windows Server 2016    | Internet Explorer、Edge     | ![○][1] |
| Windows Server 2016    | Chrome                      | 近日対応予定 |
| Windows Server 2012 R2 | Internet Explorer、Chrome   | ![○][1] |
| Windows Server 2008 R2 | Internet Explorer、Chrome   | ![○][1] |
| macOS                  | Safari                      | ![○][1] |
| macOS                  | Chrome                      | 近日対応予定 |

> [!NOTE]
> Chrome サポートの場合、Windows 10 Creators Update (バージョン 1703) 以降を使用する必要があります。<br>
> [この拡張機能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)をインストールできます。

### <a name="supported-mobile-applications-and-desktop-clients"></a>サポートされているモバイル アプリケーションとデスクトップ クライアント

条件付きアクセス ポリシーの **[モバイル アプリとデスクトップ クライアント]** オプションを利用してアプリとクライアントのアクセスを制御します。 サポートされているモバイル アプリまたはデスクトップ クライアントがアクセスを試行したときにのみ、アクセスが許可されます。 サポートされていないアプリまたはクライアントでアクセスが試された場合、アクセスはブロックされます。

![サポートされているモバイル アプリまたはデスクトップ クライアントのアクセスの制御](./media/active-directory-conditional-access-technical-reference/06.png)

以下のモバイル アプリとデスクトップ クライアントは、Office 365 をはじめとする Azure AD 接続サービス アプリケーションに対する条件付きアクセスをサポートします。


| クライアント アプリケーション| 対象サービス| プラットフォーム |
| --- | --- | --- |
| Azure Multi-Factor Authentication とアプリの場所ポリシー (デバイスベースのポリシーはサポートされていません)| 任意のマイ アプリ アプリ サービス| Android、iOS|
| Azure RemoteApp| Azure RemoteApp サービス| Windows 10、Windows 8.1、Windows 7、iOS、Android、macOS|
| Dynamics 365 アプリ| Dynamics 365| Windows 10、Windows 8.1、Windows 7、iOS、Android|
| Microsoft Office 365 Teams (Microsoft Teams とそのすべてのクライアント アプリ (Windows Desktop、iOS、Android, Windows Phone、Web クライアント) をサポートするすべてのサービスを制御します)| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS、Android|
| メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用)| Office 365 Exchange Online| Windows 10|
| Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用)| Office 365 Exchange Online| Windows 8.1、Windows 7|
| Outlook Mobile アプリ| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office for macOS)| Office 365 Exchange Online| macOS|
| Office 2016 アプリ、ユニバーサル Office アプリ、Office 2013 (最新の認証を使用)、[OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 同期クライアント、Office Groups と SharePoint アプリについては今後サポート予定| Office 365 SharePoint Online| Windows 10|
| Office 2016 アプリ、Office 2013 (最新の認証を使用)、[OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 同期クライアント| Office 365 SharePoint Online| Windows 8.1、Windows 7|
| Office モバイル アプリ| Office 365 SharePoint Online| iOS、Android|
| Office 2016 for macOS (Word、Excel、PowerPoint、OneNote のみサポート)、OneDrive for Business については今後サポート予定| Office 365 SharePoint Online| macOS|
| Office Yammer アプリ| Office 365 Yammer| Windows 10、iOS、Android|
| PowerBI アプリ (現在のところ、Android ではサポートされていません)| PowerBI サービス| Windows 10、Windows 8.1、Windows 7、iOS|
| Visual Studio Team Services アプリ| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS、Android|



## <a name="approved-client-app-requirement"></a>承認されたクライアント アプリケーションの要件 

条件付きアクセス ポリシーの **[承認されたクライアント アプリが必要です]** オプションを利用してクライアント接続を制御します。 承認されたクライアント アプリが接続を試行したときにのみ、アクセスが許可されます。

![承認されたクライアント アプリのアクセスの制御](./media/active-directory-conditional-access-technical-reference/21.png)

次のクライアント アプリは、承認されたクライアント アプリケーションの要件に基づいて使用できます。

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype for Business

- Microsoft Teams

- Microsoft Visio

- Microsoft Word


**解説**

- 承認されたクライアント アプリは、Intune モバイル アプリケーション管理機能をサポートしています。

- **[承認されたクライアント アプリが必要です]** 要件:

    - [デバイス プラットフォームの条件](#device-platforms-condition)については iOS と Android のみをサポートしています。

    - [クライアント アプリの条件](#supported-browsers)の **[ブラウザー]** オプションはサポートしていません。
    
    - [クライアント アプリの条件](#supported-mobile-apps-and-desktop-clients)の **[モバイル アプリとデスクトップ クライアント]** オプションが選択されている場合、それに取って代わります。


## <a name="next-steps"></a>次のステップ

- 条件付きアクセスの概要については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」をご覧ください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、[Azure Active Directory の条件付きアクセスの推奨プラクティス](active-directory-conditional-access-best-practices.md)に関するページをご覧ください。



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


