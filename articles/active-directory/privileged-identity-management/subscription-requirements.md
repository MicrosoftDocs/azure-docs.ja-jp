---
title: PIM を使用するためのライセンスの要件 - Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) を使用するためのライセンスの要件について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: markwahl-msft
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/16/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83c31c2731a8e872dfd2750fced8b91d283d0892
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804058"
---
# <a name="license-requirements-to-use-pim"></a>PIM を使用するためのライセンスの要件

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用するには、有効なライセンスがディレクトリになければいけません。 さらに、管理者と関連ユーザーにライセンスが割り当てられている必要があります。 この記事では、PIM を使用するためのライセンスの要件について説明します。

## <a name="prerequisites"></a>前提条件

PIM を使用するには、お客様のディレクトリに次のいずれかの有料ライセンスまたは試用版ライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 M5

詳細については、「[Azure Active Directory とは](../fundamentals/active-directory-whatis.md)」を参照してください。

## <a name="which-users-must-have-licenses"></a>ライセンスが必要なユーザー

PIM を操作する管理者または PIM の恩恵を受けるユーザーには、それぞれライセンスが必要です。 たとえば、次のようになります。

- PIM を使用して管理された Azure AD ロールを持つ管理者
- PIM を使用して管理された Azure リソース ロールを持つ管理者
- 特権ロール管理者のロールに割り当てられた管理者
- PIM を使用して管理された Azure AD ロールに対象として割り当てられたユーザー
- PIM で要求を承認または却下できるユーザー
- Just-In-Time 割り当てまたは直接割り当て (時間ベース) を使用して Azure リソース ロールに割り当てられたユーザー  
- アクセス レビューに割り当てられたユーザー
- アクセス レビューを実行するユーザー

お客様のユーザーにライセンスを割り当てる方法については、「[Azure Active Directory ポータルを使用したライセンスの割り当てと削除](../fundamentals/license-users-groups.md)」を参照してください。

## <a name="what-happens-when-a-license-expires"></a>ライセンスの有効期限が切れた場合に発生すること

Azure AD Premium P2、EMS E5、または試用版ライセンスの有効期限が切れた場合、PIM の機能はお客様のディレクトリで利用できなくなります。

- Azure AD ロールへの永続的なロールの割り当てには影響しません。
- 特権ロールのアクティブ化、特権アクセスの管理、または特権ロールのアクセス レビューの実行のために、Azure portal の PIM サービスと、PIM の Graph API コマンドレットおよび PowerShell インターフェイスを利用することはできなくなります。
- ユーザーが今後特権ロールをアクティブ化できなくなるため、Azure AD ロールの候補ロールの割り当ては削除されます。
- Azure AD ロールの継続的なアクセス レビューが終了し、PIM 構成設定が削除されます。
- ロールの割り当てを変更しても、PIM から電子メールが送信されなくなります。

## <a name="next-steps"></a>次の手順

- [PIM をデプロイする](pim-deployment-plan.md)
- [PIM の使用を開始する](pim-getting-started.md)
- [PIM で管理できないロール](pim-roles.md)
