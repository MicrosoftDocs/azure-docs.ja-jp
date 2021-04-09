---
title: PIM で Azure カスタム ロールを使用する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure カスタム ロールを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88782602"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure カスタム ロールを使用する

Azure Active Directory (Azure AD) 組織内の特権ロールを持つ一部のユーザーには厳密な Privileged Identity Management (PIM) 設定を適用する必要があるのに対し、他のユーザーにはより自主性を持たせてよい場合があります。 例として、Azure サブスクリプションで実行されるアプリケーションの開発を支援するために、組織が複数の契約担当者を雇用するサンプル シナリオを考えてみましょう。

リソースの管理者として、承認を必要とせずに従業員がアクセスできるようにする必要があります。 ただし、組織のリソースへのアクセスを要求するときは、すべての契約社員が承認される必要があります。

次のセクションに概要を示した手順に従って、Azure リソース ロール向けに対象 Privileged Identity Management 設定をセットアップします。

## <a name="create-the-custom-role"></a>カスタム ロールを作成する

リソースのカスタム ロールを作成するには、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」に記載された手順に従います。

カスタム ロールを作成するときには、複製する組み込みロールを簡単に覚えておくことができるように、わかりやすい名前を付けます。

> [!NOTE]
> カスタム ロールが複製したい組み込みロールの複製であり、そのスコープが組み込みロールと一致することを確認します。

## <a name="apply-pim-settings"></a>PIM 設定を適用する

お客様の Azure AD 組織でロールを作成したら、Azure portal の **[Privileged Identity Management - Azure リソース]** ページに移動します。 ロールが適用されるリソースを選択します。

![[Privileged Identity Management] の [Azure リソース] ウィンドウ](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

ロールのこのメンバーに適用する必要がある [Privileged Identity Management ロール設定を構成します](pim-resource-roles-configure-role-settings.md)。

最後に、これらの設定の対象にするメンバーの明確なグループに[ロールを割り当てます](pim-resource-roles-assign-roles.md)。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure リソース ロールの設定を構成する](pim-resource-roles-configure-role-settings.md)
- [Azure のカスタム ロール](../../role-based-access-control/custom-roles.md)