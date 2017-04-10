---
title: "Azure AD Connect: Active Directory フェデレーション サービス (AD FS) ファームの SSL 証明書の更新 | Microsoft Docs"
description: "このドキュメントでは、Azure AD Connect を使って AD FS ファームの SSL 証明書を更新する手順について詳しく説明します。"
services: active-directory
keywords: "Azure AD Connect, AD FS SSL の更新, AD FS 証明書の更新, 変更、AD FS 証明書, 新しい AD FS 証明書, AD FS 証明書, 更新、AD FS SSL 証明書, 更新、SSL 証明書、AD FS, 構成、AD FS SSL 証明書, AD FS, SSL, 証明書, AD FS サービス通信証明書, 更新、フェデレーション, 構成、フェデレーション, AAD Connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9bb29624324305f5295e0a50ef79d8f4c6d063c0
ms.lasthandoff: 03/21/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory フェデレーション サービス (AD FS) ファームの SSL 証明書の更新

## <a name="overview"></a>概要
この記事では、Azure AD Connect を使って Active Directory フェデレーション サービス (AD FS) ファームの SSL 証明書を更新する方法について説明します。 Azure AD Connect のサインイン方法が AD FS に設定されている場合、Azure AD Connect ツールを使って AD FS ファーム用の SSL 証明書を簡単に更新できます。 この更新は、次の簡単な 3 つの手順で、すべてのフェデレーション サーバーとWeb アプリケーション プロキシ (WAP) サーバーに対して実行できます。

![3 つの手順](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>AD FS によって使われる証明書の詳細については、「[AD FS で使用される証明書とは](https://technet.microsoft.com/library/cc730660.aspx)」をご覧ください。

## <a name="prerequisites"></a>前提条件

* **AD FS ファーム**: AD FS ファームが Windows Server 2012 R2 以降ベースであることを確認します。
* **Azure AD Connect**: Azure AD Connect のバージョンが 1.1.443.0 以降であることを確認します。 **AD FS SSL 証明書の更新**タスクを使用します。

![SSL の更新タスク](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>手順 1: AD FS ファームの情報を提供する

Azure AD Connect は、次の方法で自動的に AD FS ファームに関する情報の取得を試みます。
1. AD FS (Windows Server 2016 以降) でファームの情報を照会します。
2. Azure AD Connect でローカルに保存される、以前の実行の情報を参照します。

表示されるサーバーの一覧にサーバーを追加したり、一覧からサーバーを削除したりすることで一覧を変更して、AD FS ファームの現在の構成を反映できます。 サーバーの情報が提供されるとすぐに、Azure AD Connect は接続の状態と現在の SSL 証明書の状態を表示します。

![AD FS サーバーの情報](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

AD FS ファームの一部ではなくなったサーバーが一覧に含まれる場合は、**[削除]** をクリックして、AD FS ファームのサーバーの一覧からそのサーバーを削除します。

![一覧でのオフライン サーバー](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Azure AD Connect での AD FS ファームのサーバー一覧からのサーバーの削除はローカルな操作であり、Azure AD Connect がローカルに保持する AD FS ファームの情報が更新されます。 Azure AD Connect が、変更を反映するために AD FS の構成を変更することはありません。    

## <a name="step-2-provide-a-new-ssl-certificate"></a>手順 2: 新しい SSL 証明書を提供する

AD FS ファームのサーバーに関する情報の確認が済むと、Azure AD Connect は新しい SSL 証明書を要求します。 パスワードで保護された PFX 証明書を指定して、インストールを続けます。

![SSL 証明書](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

証明書を指定すると、Azure AD Connect が一連の前提条件を確認します。 証明書を確認し、証明書が AD FS ファーム用として正しいことを確認します。

-    証明書のサブジェクト名/代替サブジェクト名がフェデレーション サービス名と同じであるか、またはワイルドカード証明書であること。
-    証明書の有効期間が 30 日より長いこと。
-    証明書の信頼チェーンが有効であること。
-    証明書がパスワードで保護されていること。

## <a name="step-3-select-servers-for-the-update"></a>手順 3: 更新するサーバーを選ぶ

次の手順では、SSL 証明書を更新する必要があるサーバーを選びます。 オフラインになっているサーバーを更新対象に選ぶことはできません。

![更新するサーバーを選ぶ](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

構成が完了すると、更新の状態を示すメッセージが表示され、AD FS サインインを確認するためのオプションが用意されます。

![構成の完了](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQ

* **新しい AD FS SSL 証明書のサブジェクト名はどのようなものにする必要がありますか。**

    Azure AD Connect は、証明書のサブジェクト名/代替サブジェクト名にフェデレーション サービスの名前が含まれるかどうかを確認します。 たとえば、フェデレーション サービス名が fs.contoso.com である場合、サブジェクト名/代替サブジェクト名も fs.contoso.com である必要があります。  ワイルドカード証明書も受け付けられます。

* **WAP サーバー ページで資格情報を再度要求されるのはなぜですか。**

    AD FS サーバーへの接続用に提供した資格情報に、WAP サーバーを管理するための権限がない場合、Azure AD Connect は WAP サーバーの管理権限のある資格情報を要求します。

* **サーバーがオフラインとして表示されます。どうすればよいですか。**

    サーバーがオフラインの場合、Azure AD Connect はどのような操作も実行できません。 サーバーが AD FS ファームの一部である場合は、サーバーへの接続を確認します。 問題を解決した後は、ウィザードで更新アイコンをクリックして状態を更新します。 以前はファームの一部であったサーバーが存在しなくなっている場合は、**[削除]** をクリックして、Azure AD Connect が保持するサーバーの一覧から削除します。 Azure AD Connect の一覧からサーバーを削除しても、AD FS の構成自体は変更されません。 Windows Server 2016 以降の AD FS を使用している場合、サーバーは構成設定に残るため、タスクを実行するともう一度表示されます。

* **新しい SSL 証明書でファーム サーバーのサブセットを更新できますか。**

    はい。 いつでも、**SSL 証明書の更新**タスクを再び実行して、残りのサーバーを更新できます。 **[SSL 証明書の更新を実行するサーバーを選択します]** ページでは、**[SSL の有効期限]** でサーバーの一覧を並べ替えて、まだ更新されていないサーバーに簡単にアクセスできます。

* **以前の実行でサーバーを削除しましたが、[AD FS サーバー] ページの一覧にオフラインとしてまだ表示されます。削除した後にオフライン サーバーがまだ表示されるのはなぜですか。**

    Azure AD Connect の一覧からサーバーを削除しても、AD FS の構成では削除されません。 Azure AD Connect は、AD FS (Windows Server 2016 以降) を参照して、ファームに関するあらゆる情報を取得します。 サーバーが AD FS の構成に存在する場合は、再び一覧に表示されます。  

## <a name="next-steps"></a>次のステップ

- [Azure AD Connect とフェデレーション](active-directory-aadconnectfed-whatis.md)
- [Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ](active-directory-aadconnect-federation-management.md)

