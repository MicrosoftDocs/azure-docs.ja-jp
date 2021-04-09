---
title: Azure AD Connect - AD FS ファームの TLS/SSL 証明書の更新 | Microsoft Docs
description: このドキュメントでは、Azure AD Connect を使って AD FS ファームの TLS/SSL 証明書を更新する手順について詳しく説明します。
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279909"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory フェデレーション サービス (AD FS) ファームの TLS/SSL 証明書の更新

## <a name="overview"></a>概要
この記事では、Azure AD Connect を使って Active Directory フェデレーション サービス (AD FS) ファームの TLS/SSL 証明書を更新する方法について説明します。 選択されたユーザー サインイン方法が AD FS でない場合でも、Azure AD Connect ツールを使って AD FS ファーム用の TLS/SSL 証明書を簡単に更新できます。

3 つのシンプルな手順で、すべてのフェデレーション サーバーと Web アプリケーション プロキシ (WAP) サーバーにわたって、AD FS ファームの TLS/SSL 証明書の更新操作全体を実行できます。

![3 つの手順](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>AD FS によって使われる証明書の詳細については、「[AD FS で使用される証明書とは](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11))」をご覧ください。

## <a name="prerequisites"></a>前提条件

* **AD FS ファーム**: AD FS ファームが Windows Server 2012 R2 以降ベースであることを確認します。
* **Azure AD Connect**: Azure AD Connect のバージョンが 1.1.553.0 以降であることを確認します。 **AD FS SSL 証明書の更新** タスクを使用します。

![TLS タスクの更新](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>手順 1: AD FS ファームの情報を提供する

Azure AD Connect は、次の方法で自動的に AD FS ファームに関する情報の取得を試みます。
1. AD FS (Windows Server 2016 以降) でファームの情報を照会します。
2. Azure AD Connect でローカルに保存される、以前の実行の情報を参照します。

表示されるサーバーの一覧にサーバーを追加したり、一覧からサーバーを削除したりすることで一覧を変更して、AD FS ファームの現在の構成を反映できます。 サーバーの情報が提供されるとすぐに、Azure AD Connect は接続の状態と現在の TLS/SSL 証明書の状態を表示します。

![AD FS サーバーの情報](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

AD FS ファームの一部ではなくなったサーバーが一覧に含まれる場合は、**[削除]** をクリックして、AD FS ファームのサーバーの一覧からそのサーバーを削除します。

![一覧でのオフライン サーバー](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Azure AD Connect での AD FS ファームのサーバー一覧からのサーバーの削除はローカルな操作であり、Azure AD Connect がローカルに保持する AD FS ファームの情報が更新されます。 Azure AD Connect が、変更を反映するために AD FS の構成を変更することはありません。    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>手順 2: 新しい TLS/SSL 証明書を提供する

AD FS ファームのサーバーに関する情報の確認が済むと、Azure AD Connect は新しい TLS/SSL 証明書を要求します。 パスワードで保護された PFX 証明書を指定して、インストールを続けます。

![TLS/SSL 証明書](./media/how-to-connect-fed-ssl-update/certificate.png)

証明書を指定すると、Azure AD Connect が一連の前提条件を確認します。 証明書を確認し、証明書が AD FS ファーム用として正しいことを確認します。

-   証明書のサブジェクト名/代替サブジェクト名がフェデレーション サービス名と同じであるか、またはワイルドカード証明書であること。
-   証明書の有効期間が 30 日より長いこと。
-   証明書の信頼チェーンが有効であること。
-   証明書がパスワードで保護されていること。

## <a name="step-3-select-servers-for-the-update"></a>手順 3: 更新するサーバーを選ぶ

次の手順では、TLS/SSL 証明書を更新する必要があるサーバーを選びます。 オフラインになっているサーバーを更新対象に選ぶことはできません。

![更新するサーバーを選ぶ](./media/how-to-connect-fed-ssl-update/selectservers.png)

構成が完了すると、更新の状態を示すメッセージが表示され、AD FS サインインを確認するためのオプションが用意されます。

![構成の完了](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>FAQ

* **新しい AD FS TLS/SSL 証明書のサブジェクト名はどのようなものにする必要がありますか。**

    Azure AD Connect は、証明書のサブジェクト名/代替サブジェクト名にフェデレーション サービスの名前が含まれるかどうかを確認します。 たとえば、フェデレーション サービス名が fs.contoso.com である場合、サブジェクト名/代替サブジェクト名も fs.contoso.com である必要があります。  ワイルドカード証明書も受け付けられます。

* **WAP サーバー ページで資格情報を再度要求されるのはなぜですか。**

    AD FS サーバーへの接続用に提供した資格情報に、WAP サーバーを管理するための権限がない場合、Azure AD Connect は WAP サーバーの管理権限のある資格情報を要求します。

* **サーバーがオフラインとして表示されます。どうすればよいですか。**

    サーバーがオフラインの場合、Azure AD Connect はどのような操作も実行できません。 サーバーが AD FS ファームの一部である場合は、サーバーへの接続を確認します。 問題を解決した後は、ウィザードで更新アイコンをクリックして状態を更新します。 以前はファームの一部であったサーバーが存在しなくなっている場合は、**[削除]** をクリックして、Azure AD Connect が保持するサーバーの一覧から削除します。 Azure AD Connect の一覧からサーバーを削除しても、AD FS の構成自体は変更されません。 Windows Server 2016 以降の AD FS を使用している場合、サーバーは構成設定に残るため、タスクを実行するともう一度表示されます。

* **新しい TLS/SSL 証明書でファーム サーバーのサブセットを更新できますか。**

    はい。 いつでも、**SSL 証明書の更新** タスクを再び実行して、残りのサーバーを更新できます。 **[SSL 証明書の更新を実行するサーバーを選択します]** ページでは、**[SSL の有効期限]** でサーバーの一覧を並べ替えて、まだ更新されていないサーバーに簡単にアクセスできます。

* **以前の実行でサーバーを削除しましたが、[AD FS サーバー] ページの一覧にオフラインとしてまだ表示されます。削除した後にオフライン サーバーがまだ表示されるのはなぜですか。**

    Azure AD Connect の一覧からサーバーを削除しても、AD FS の構成では削除されません。 Azure AD Connect は、AD FS (Windows Server 2016 以降) を参照して、ファームに関するあらゆる情報を取得します。 サーバーが AD FS の構成に存在する場合は、再び一覧に表示されます。  

## <a name="next-steps"></a>次のステップ

- [Azure AD Connect とフェデレーション](how-to-connect-fed-whatis.md)
- [Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ](how-to-connect-fed-management.md)