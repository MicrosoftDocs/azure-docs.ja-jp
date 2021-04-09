---
title: Azure AD エンタイトルメント管理でアクセス パッケージへのアクセスを要求するリンクを共有する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でアクセス パッケージを要求するリンクの共有方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32973d063c36a45eee79db0b4da4e7419cb70013
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798548"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理でアクセス パッケージへのアクセスを要求するリンクを共有する

ディレクトリ内のほとんどのユーザーは、マイ アクセス ポータルにサインインでき、要求できるアクセス パッケージの一覧を自動的に表示できます。 ただし、まだディレクトリに含まれていない外部のビジネス パートナーには、アクセス パッケージの要求に使用できるリンクを送信する必要があります。 

アクセス パッケージのカタログが[外部ユーザーに対して有効](entitlement-management-catalog-create.md)になっており、[外部ユーザーのディレクトリ用のポリシー](entitlement-management-access-package-request-policy.md)があれば、外部ユーザーはマイ アクセス ポータルのリンクを使用してアクセス パッケージを要求することができます。

## <a name="share-link-to-request-an-access-package"></a>リンクを共有してアクセス パッケージを要求する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. [概要] ページで、**[My Access portal link]\(マイ アクセス ポータルのリンク)** をコピーします。

    ![アクセス パッケージの概要 - マイ アクセス ポータルのリンク](./media/entitlement-management-shared/my-access-portal-link.png)

    マイ アクセス ポータルのリンクを内部のビジネス パートナーに送信するときは、リンク全体をコピーすることが大切です。 そうすることで、パートナーが確実にディレクトリのポータルにアクセスして要求を行うことができます。 リンクは `myaccess` で始まり、ディレクトリ ヒントを含み、アクセス パッケージ ID で終了します。  (米国政府の場合、マイ アクセス ポータルのリンクのドメインは `myaccess.microsoft.us` になります)。

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. リンクを外部のビジネス パートナーにメール送信または送信します。 外部のビジネス パートナーは、そのユーザーとリンクを共有して、アクセス パッケージを要求することができます。

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージへのアクセスを要求する](entitlement-management-request-access.md)
- [アクセス要求を承認または拒否する](entitlement-management-request-approve.md)