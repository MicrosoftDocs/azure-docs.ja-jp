---
title: 動的グループ ルールの更新と管理、およびメンバーシップのトラブルシューティング - Azure Active Directory | Microsoft Docs
description: Azure portal でグループ メンバーシップ ルールの作成と状態チェックを行う方法。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656926"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Azure Active Directory で動的グループを更新してメンバーシップを管理する

Azure Active Directory (Azure AD) では、ユーザーまたはデバイスのプロパティに基づいてグループ メンバーシップを決定するルールを使用できます。 この記事では、Azure portal で動的グループのルールを設定する方法について説明します。
セキュリティ グループまたは Office 365 グループに対して、動的メンバーシップがサポートされています。 グループ メンバーシップのルールが適用されるときに、ユーザーとデバイスの属性がメンバーシップのルールと一致するかどうかが評価されます。 ユーザーまたはデバイスの属性が変更されると、組織内のすべての動的グループ ルールが、メンバーシップの変更のために処理されます。 ユーザーとデバイスは、グループの条件を満たす場合に、追加または削除されます。

構文の例、メンバーシップ ルールでサポートされているプロパティ、演算子、および値については、「[Azure Active Directory の動的グループ メンバーシップ ルール](groups-dynamic-membership.md)」を参照してください。

## <a name="to-update-a-group-membership-rule"></a>グループ メンバーシップ ルールを更新するには

1. 全体管理者、Intune 管理者、またはテナントのユーザー管理者ロールであるアカウントを使用して、[Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。
1. **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. グループを選択して、そのプロファイルを開きます。
1. グループのプロファイル ページで、 **[動的メンバーシップ ルール]** を選択します。 ルール ビルダーでは、最大で 5 つの式がサポートされます。 6 番目以降の式を追加するには、テキスト ボックスを使用する必要があります。

   ![動的グループのメンバーシップのルールを追加する](./media/groups-update-rule/update-dynamic-group-rule.png)

1. メンバーシップのルールで使用できるカスタム拡張機能プロパティを表示するには:
   1. **[カスタム拡張機能のプロパティを取得します]** を選択します。
   2. アプリケーション ID を入力し、 **[プロパティの更新]** を選択します。
1. ルールを更新した後、 **[保存]** を選択します。

入力したルールが有効でない場合は、ルールを処理できなかった理由の説明がポータルの Azure 通知に表示されます。 ルールを修正する方法を理解するには、こちらを注意深くお読みください。

## <a name="check-processing-status-for-a-rule"></a>ルールの処理状態をチェックする

グループの **[概要]** ページで、メンバーシップの処理状態と最終更新日を確認できます。
  
  ![動的グループの状態の表示](./media/groups-create-rule/group-status.png)

**[メンバーシップの処理]** の状態には、次の状態メッセージが表示される場合があります。

* **評価中**:グループの変更が受信され、更新プログラムが評価されています。
* **処理中**:更新プログラムが処理されています。
* **更新の完了**:処理が完了し、該当するすべての更新が行われました。
* **処理エラー**:メンバーシップのルールを評価するエラーが原因で、処理を完了できませんでした。
* **更新の一時停止**:動的メンバーシップ規則の更新プログラムが管理者によって一時停止されました。 MembershipRuleProcessingState は、"一時停止" に設定されます。

**[メンバーシップの最終更新日時]** の状態には、次の状態メッセージが表示される場合があります。

* **日付と時刻**: メンバーシップが最後に更新された日時。
* **進行中**: 更新は現在進行中です。
* **不明**:最終更新時刻を取得することができません。 新しいグループである可能性があります。

特定のグループのメンバーシップ規則の処理中にエラーが発生すると、そのグループの **[概要]** ページの上部にアラートが表示されます。 24 時間以上、テナント内のすべてのグループに対して保留中の動的メンバーシップの更新が処理できない場合は、 **[すべてグループ]** の上部にアラートが表示されます。

![処理エラー メッセージ アラート](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>次の手順

次の記事は、Azure AD の動的グループの操作に関する追加情報を提供します。

* 動的ルールの構造の完全なリファレンスについては、[動的メンバーシップ ルールの構文](groups-dynamic-membership.md)に関するページを参照してください。
* [静的メンバーシップ グループを作成し、メンバーを追加します](../fundamentals/active-directory-groups-create-azure-portal.md)。
