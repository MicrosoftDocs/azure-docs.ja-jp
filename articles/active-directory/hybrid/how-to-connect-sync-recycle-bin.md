---
title: 'Azure AD Connect 同期: AD のごみ箱の有効化 | Microsoft Docs'
description: このトピックでは、Azure AD Connect を使った AD のごみ箱機能の使用を推奨します。
services: active-directory
keywords: AD のごみ箱、誤削除、ソース アンカー
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12073a75cd248c9226c7ce5ecc21b64617823b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279637"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect 同期: AD のごみ箱の有効化
Azure AD に同期される、オンプレミスの Active Directory 向けの AD ごみ箱機能を有効にすることをお勧めします。 

オンプレミスの AD ユーザー オブジェクトを誤って削除し、この機能を使って復元した場合、Azure AD により対応する Azure AD ユーザー オブジェクトが復元されます。  AD のごみ箱機能について詳しくは、「[Scenario Overview for Restoring Deleted Active Directory Objects (削除された Active Directory オブジェクトの復元シナリオの概要)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379542(v=ws.10))」をご覧ください。

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>AD のごみ箱を有効にする利点
この機能を使用すると、Azure AD ユーザー オブジェクトを復元できます。これを行うには、次の操作を実行します。

* オンプレミスの AD ユーザー オブジェクトを誤って削除した場合、対応する Azure AD ユーザー オブジェクトは次回の同期サイクルで削除されます。 既定では、Azure AD は論理削除済み状態で 30 日間、削除された Azure AD ユーザー オブジェクトを保持します。

* オンプレミスの AD ごみ箱機能を有効にしていれば、そのソース アンカーの値を変更することなく、削除されたオンプレミスの AD ユーザー オブジェクトを復元できます。 回復したオンプレミスの AD ユーザー オブジェクトが Azure AD と同期されたときに、Azure AD により対応する論理削除された Azure AD ユーザー オブジェクトが復元されます。 ソース アンカー属性について詳しくは、「[Azure AD Connect: 設計概念](./plan-connect-design-concepts.md#sourceanchor)」をご覧ください。

* オンプレミスの AD ごみ箱機能を有効にしていない場合、AD ユーザー オブジェクトを作成して、削除されたオブジェクトを置き換える必要があります。 ソース アンカー属性について、システムで生成される AD 属性 (ObjectGuid など) を使用するように Azure AD Connect 同期サービスを構成した場合、新しく作成される AD ユーザー オブジェクトのソース アンカー値は、削除された AD ユーザー オブジェクトの値とは同じになりません。 新たに作成した AD ユーザー オブジェクトが Azure AD と同期されると、削除された Azure AD ユーザー オブジェクトが復元されるのではなく、新しい Azure AD ユーザー オブジェクトが自動的に作成されます。

> [!NOTE]
> 既定では、削除された Azure AD ユーザー オブジェクトは、論理削除済み状態で 30 日間保持された後、完全に削除されます。 ただし、管理者は、このようなオブジェクトの削除までの時間を短縮できます。 完全に削除されたオブジェクトは、オンプレミスの AD ごみ箱機能を有効にしていても、回復することはできません。

## <a name="next-steps"></a>次のステップ
**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)