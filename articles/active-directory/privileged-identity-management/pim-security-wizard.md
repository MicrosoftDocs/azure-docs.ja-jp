---
title: PIM での Azure AD ロール セキュリティ ウィザード - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を使用して永続的な特権 Azure AD ロールの割り当てを対象的に変換するために使用できるセキュリティ ウィザードについて説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4fd850ac2116dc7f353eea87845501fff020bb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476237"
---
# <a name="azure-ad-roles-security-wizard-in-pim"></a>PIM での Azure AD ロール セキュリティ ウィザード

組織内で Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を初めて実行するユーザーには、ウィザードが表示されます。 このウィザードを使用すると、特権 ID のセキュリティ リスクと、PIM を使用してそのリスクを軽減する方法がわかります。 既存のロール割り当てを後で変更する場合は、ウィザードで実行する必要はありません。

## <a name="wizard-overview"></a>ウィザードの概要

組織で PIM の使用を開始する前の段階では、すべてのロール割り当ては永続的な割り当てになっています。そのため、ユーザーが現在その権限を必要としていない場合でも、常にロールは割り当てられている状態です。 ウィザードの最初の手順には、高い権限を持つロールの一覧と、それらのロールに割り当てられたユーザーの数が表示されます。 ロールの中によく知らないものがある場合は、そのロールの詳細を表示して、ユーザーを詳しく確認できます。

ウィザードの 2 番目の手順では、管理者のロール割り当てを変更できます。  

> [!WARNING]
> ここで重要なのは、全体管理者が少なくとも 1 人存在し、組織アカウント (Microsoft アカウントではない) を持つ特権ロール管理者が複数存在していることです。 特権ロール管理者が 1 人しかいない場合、そのアカウントが削除されると、組織は PIM を管理できなくなります。
> また、ユーザーに Microsoft アカウント (Skype、Outlook.com などの Microsoft サービスへのサインインに使用するアカウント) がある場合は、ロールの割り当てを永続的に維持してください。 そのロールのアクティブ化に MFA を要求すると、ユーザーはロックアウトされます。

## <a name="run-the-wizard"></a>ウィザードを実行する

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** をクリックし、 **[ウィザード]** をクリックします。

    ![[Azure AD ロール] - ウィザードを実行するための 3 つの手順を示すウィザード ページ](./media/pim-security-wizard/wizard-start.png)

1. **[1 特権ロールの検出]** をクリックします。

1. 特権ロールの一覧で、どのユーザーが永続的または対象的であるかを確認します。

    ![[特権ロールの検出] - 永続的メンバーと対象的メンバーを示すロール ウィンドウ](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. **[次へ]** をクリックして、対象的にするメンバーを選択します。

    ![ロールの対象にするメンバーを選択するためのオプションがある [メンバーを対象的に変換] ページ](./media/pim-security-wizard/convert-members-eligible.png)

1. メンバーを選択したら、 **[次へ]** をクリックします。

    ![変換される永続ロールが割り当てられているメンバーを示す [変更の確認] ページ](./media/pim-security-wizard/review-changes.png)

1. **[OK]** をクリックして、永続的な割り当てを対象的に変換します。

    変換が完了したら、通知が表示されます。

    ![変換の状態を示す通知](./media/pim-security-wizard/notification-completion.png)

その他の特権ロールの割り当てを対象的に変換する必要がある場合は、もう一度ウィザードを実行できます。 ウィザードではなく、PIM インターフェイスを使用する場合は、「[Assign Azure AD roles in PIM (PIM で Azure AD ロールを割り当てる)](pim-how-to-add-role-to-user.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [PIM で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [PIM を管理する他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)
