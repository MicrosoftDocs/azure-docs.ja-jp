---
author: baanders
description: Azure Digital Twins 設定でのアクセス許可の手順のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "100551872"
---
Azure Digital Twins は、ロールベースのアクセス制御 (RBAC) に [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) を使用します。 つまり、ユーザーが Azure Digital Twins インスタンスへのデータ プレーン呼び出しを行うには、そのための適切なアクセス許可を持つロールをそのユーザーに割り当てておく必要があります。

Azure Digital Twins の場合、このロールは _**"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)**_ です。 ロールとセキュリティの詳細については、[*概念: Azure Digital Twins ソリューションのセキュリティ*](../articles/digital-twins/concepts-security.md)に関するページを参照してください。

このセクションでは、Azure サブスクリプションの Azure AD テナント内のユーザーの電子メールを使用して、Azure Digital Twins インスタンスでのそのユーザーのロールの割り当てを作成する方法について説明します。 組織内のロールによっては、このアクセス許可を自分で設定するか、または Azure Digital Twins インスタンスを管理するだれか他のユーザーの代わりに設定する可能性があります。

### <a name="assign-the-role"></a>ロールを割り当てる

ユーザーに Azure Digital Twins インスタンスを管理するためのアクセス許可を付与するには、そのインスタンス内の _**"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)**_ ロールをそのユーザーに割り当てる必要があります。

> [!NOTE]
> このロールは、Azure Digital Twins インスタンスのスコープでも割り当てることができる Azure AD *[所有者]* ロールとは異なることに注意してください。 これらは 2 つの個別の管理ロールであり、Azure AD *[所有者]* では、 *"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)* で許可されるデータ プレーンへのアクセス機能は許可されません。