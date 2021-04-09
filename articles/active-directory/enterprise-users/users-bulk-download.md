---
title: Azure Active Directory ポータルでユーザーの一覧をダウンロードする | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザー レコードを一括ダウンロードします。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861595"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Azure Active Directory ポータルでユーザーの一覧をダウンロードする

Azure Active Directory (Azure AD) では、ユーザーの一括インポート (作成) 操作がサポートされています。

## <a name="required-permissions"></a>必要なアクセス許可

Azure AD 管理センターからユーザー一覧をダウンロードするには、Azure AD の組織レベルの管理者ロールの 1 つが割り当てられたユーザーとしてサインインしている必要があります (最低でもユーザー管理者ロールが必要です)。 ゲスト招待元とアプリケーション開発者は、管理者ロールとは見なされません。

## <a name="to-download-a-list-of-users"></a>ユーザーの一覧をダウンロードするには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
2. [Azure Active Directory]、[ユーザー] の順に移動します。 次に、各ユーザーの横にある左側の列のチェック ボックスをオンにすることで、ダウンロードに含めるユーザーを選択します。 注:現時点で、すべてのユーザーをエクスポートするよう選択する方法はありません。 それぞれ個々に選択する必要があります。
3. Azure AD で、 **[ユーザー]**  >  **[ユーザーをダウンロード]** を選択します。
4. **[ユーザーをダウンロード]** ページで **[開始]** を選択し、ユーザー プロファイルのプロパティが一覧表示された CSV ファイルを受け取ります。 エラーが発生した場合は、[一括操作の結果] ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

   ![ダウンロードするユーザー一覧を表示する場所を選択します](./media/users-bulk-download/bulk-download.png)

   ダウンロードしたファイルには、フィルター処理されたユーザーの一覧が含まれます。

   次のユーザー属性が含まれます。

   - userPrincipalName
   - displayName
   - surname
   - mail
   - givenName
   - objectId
   - userType
   - jobTitle
   - department
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - postalCode
   - telephoneNumber
   - mobile
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>状態の確認

**[一括操作の結果]** ページでは、保留中の一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認します。](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>一括ダウンロード サービスの制限

ユーザー一覧の作成の一括操作は、それぞれ最大 1 時間かかる場合があります。 これにより、最小で 50 万ユーザーを一括作成およびダウンロードできます。

## <a name="next-steps"></a>次のステップ

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一括復元](users-bulk-restore.md)
