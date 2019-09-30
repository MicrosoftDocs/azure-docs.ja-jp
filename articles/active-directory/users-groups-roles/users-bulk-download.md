---
title: Azure Active Directory ポータルでユーザーの一覧をダウンロードする (プレビュー) | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザー レコードを一括ダウンロードします。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38cc8fd4e063896bbd8843a54f0a01058462c618
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901299"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Azure Active Directory ポータルでユーザーの一覧をダウンロードする (プレビュー)

Azure Active Directory (Azure AD) では、ユーザーの一括インポート (作成) 操作がサポートされています。

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

ユーザー一覧の作成の一括操作は、それぞれ最大 1 時間かかる場合があります。 これにより、最小で 50 万ユーザーを一括作成およびダウンロードできます。

## <a name="required-permissions"></a>必要なアクセス許可

Azure AD 管理センターからユーザー一覧をダウンロードするには、Azure AD の組織レベルの管理者ロールの 1 つが割り当てられたユーザーとしてサインインしている必要があります。 ゲスト招待元とアプリケーション開発者は、管理者ロールとは見なされません。

## <a name="to-download-a-list-of-users"></a>ユーザーの一覧をダウンロードするには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[ユーザーをダウンロード]** を選択します。
1. **[ユーザーをダウンロード]** ページで **[開始]** を選択し、ユーザー プロファイルのプロパティが一覧表示された CSV ファイルを受け取ります。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

   ![ダウンロードするユーザー一覧を表示する場所を選択します](./media/users-bulk-download/bulk-download.png)

## <a name="check-status"></a>状態の確認

**[一括操作の結果 (プレビュー)]** ページでは、保留中のご自分の一括要求の状態を確認できます。

   ![[一括操作の結果] ページでアップロードの状態を確認する](./media/users-bulk-download/bulk-center.png)

## <a name="next-steps"></a>次の手順

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一括復元](users-bulk-restore.md)
