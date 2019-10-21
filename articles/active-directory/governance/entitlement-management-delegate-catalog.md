---
title: Azure AD エンタイトルメント管理でカタログ作成者にアクセス ガバナンスを委任する (プレビュー) - Azure Active Directory
description: カタログ作成者とプロジェクト マネージャーが自分でアクセスを管理できるよう、IT 管理者からアクセス ガバナンスを委任する方法について説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: da6183599f2dacd1a98d8fe359edda741b903b50
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170881"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理でカタログ作成者にアクセス ガバナンスを委任する (プレビュー)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

自分のカタログを作成できるよう、管理者ではないユーザーに委任する目的で、Azure AD エンタイトルメント管理で定義されたカタログ作成者ロールにそのユーザーを追加できます。 個々のユーザーを追加することも、グループを追加することもできます。グループのメンバーはカタログを作成できるようになります。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 管理者としてカタログ作成者に委任する

カタログ作成者ロールにユーザーを割り当てるには、これらの手順に従います。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューの **[エンタイトルメント管理]** セクションで、 **[設定]** をクリックします。

1. **[編集]** をクリックします。

    ![カタログ作成者を追加するための設定](./media/entitlement-management-delegate/settings-delegate.png)

1. **[Delegate entitlement management]\(エンタイトルメント管理の委任\)** セクションで、 **[Add catalog creators]\(カタログ作成者の追加\)** をクリックし、このエンタイトルメント管理ロールを委任するユーザーまたはグループを選択します。

1. **[選択]** をクリックします。

1. **[Save]** をクリックします。

## <a name="next-steps"></a>次の手順

- [リソースのカタログを作成および管理する](entitlement-management-catalog-create.md)
- [アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)

