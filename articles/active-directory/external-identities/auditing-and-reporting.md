---
title: B2B コラボレーション ユーザーの監査およびレポート - Azure AD
description: ゲスト ユーザーは、Azure Active Directory B2B コラボレーションで構成できます
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87907787"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>B2B コラボレーション ユーザーの監査およびレポート
ゲスト ユーザーに対しても、メンバー ユーザーに対する場合と同様に、監査機能を使用できます。 

## <a name="access-reviews"></a>アクセス レビュー
アクセス レビューを使用すると、ゲスト ユーザーがリソースへのアクセスをまだ必要としているかどうかを定期的に確認できます。 **アクセス レビュー** 機能は、**Azure Active Directory** では **[外部 ID]**  >  **[アクセス レビュー]** から利用できます。 Azure portal で **[すべてのサービス]** で「アクセス レビュー」を検索することもできます。 アクセス レビューの使用方法については、「[Azure AD のアクセス レビューによるゲスト アクセスの管理](../governance/manage-guest-access-with-access-reviews.md)」を参照してください。

## <a name="audit-logs"></a>監査ログ

Azure AD 監査ログはシステムとユーザーのアクティビティのレコードを提供しますが、これにはゲスト ユーザーによって開始されたアクティビティも含まれます。 監査ログにアクセスするには、**Azure Active Directory** で、 **[Monitoring]\(監視\)** にある **[監査ログ]** を選択します。 招待された Sam Oogle の招待と使用の履歴の例を示します。

![監査ログの出力例を示すスクリーンショット](./media/auditing-and-reporting/audit-log.png)

各イベントに立ち入って、詳細を取得することができます。 たとえば、承認の詳細を見てみましょう。

![アクティビティの詳細の出力例を示すスクリーンショット](./media/auditing-and-reporting/activity-details.png)

これらのログを Azure AD からエクスポートし、任意のレポート ツールを使用してレポートをカスタマイズすることもできます。

### <a name="next-steps"></a>次のステップ

- [B2B コラボレーション ユーザーのプロパティ](user-properties.md)

