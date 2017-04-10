---
title: "セキュリティ アラートを構成する方法 | Microsoft Docs"
description: "Azure Privileged Identity Management 拡張機能のセキュリティ通知の構成方法を説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4e0c911a-36c6-42a0-8f79-a01c03d2d04f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: dd8987a05c71643b49e0ac9e3c8ae43d47743225


---
# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management でセキュリティ アラートを構成する方法
## <a name="security-alerts"></a>セキュリティのアラート
環境内で疑わしいアクティビティや危険なアクティビティが行われると、Azure Privileged Identity Management (PIM) によりアラートが生成されます。 アラートは、トリガーされると PIM ダッシュボードに表示されます。 アラートを選択して、アラートをトリガーしたユーザーまたはロールが一覧表示されたレポートを表示します。

![PIM ダッシュボードのセキュリティ アラート - スクリーンショット][1]

| アラート: | トリガー | 推奨 |
| --- | --- | --- |
| **ロールが PIM の範囲外に割り当てられています** |管理者が PIM インターフェイスの外部でロールに永続的に割り当てられました。 |新しいロール割り当てを確認してください。 ほかのサービスで割り当てできるのは永続的な管理者のみであるため、こうした割り当てを必要に応じて候補割り当てに変更します。 |
| **ロールをアクティブ化する頻度が高すぎます** |設定で許可された時間に行われた同じロールの再アクティブ化が多すぎます。 |ロールを何度もアクティブ化したユーザーに連絡を取り、理由を確認してください。 時間制限が短すぎてタスクを完了できていない場合や、ロールを自動的にアクティブ化するスクリプトを使用している可能性があります。 |
| **Roles don't require multi-factor authentication for activation (ロールのアクティブ化に多要素認証が要求されていません)** |MFA が有効化されていないロールが設定に存在します。 |最も高い特権を持つロールでは MFA が必須とされていますが、すべてのロールのアクティブ化に対して MFA を有効化することを強くお勧めします。 |
| **管理者が特権ロールを使用してません** |最近ロールをアクティブ化していない、資格のある管理者がいます。 |アクセス レビューを開始して、アクセスが不要なユーザーを確認してください。 |
| **グローバル管理者が多すぎます** |推奨されている数よりも多くのグローバル管理者がいます。 |グローバル管理者の数が多い場合、ユーザーに必要以上の権限が与えられている可能性があります。 低い権限のロールにユーザーを移動するか、一部のユーザーを永続的な割り当てではなくロールの候補にしてください。 |

## <a name="configure-security-alert-settings"></a>セキュリティ アラート設定の構成
環境およびセキュリティ上の目標に合わせて、PIM のセキュリティ アラートの一部をカスタマイズすることができます。 次の手順に従って [設定] ブレードに移動します。

1. [Azure Portal](https://portal.azure.com/) にサインインし、ダッシュボードで **[Azure AD Privileged Identity Management]** タイルを選択します。
2. **[特権ロールの管理]** > **[設定]** > **[通知の設定]** の順に選択します。
   
    ![セキュリティ アラートの設定への移動][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>"ロールをアクティブ化する頻度が高すぎます" アラート
このアラートは、ユーザーが指定期間内に同じ特権ロールを複数回アクティブ化するとトリガーされます。 期間とアクティブ化の回数の両方を構成できます。

* **アクティブ化の更新の時間枠**: 不審な更新の追跡に使用する期間を日、時間、分、および秒で指定します。
* **Number of activation renewals (アクティブ化の更新回数)**: アラートを行う必要があるとみなす、指定した時間枠内でのアクティブ化の回数を 2 ～ 100 で指定します。 スライダーを動かすか、テキスト ボックスに数字を入力して設定を変更できます。

### <a name="there-are-too-many-global-administrators-alert"></a>"グローバル管理者が多すぎます" アラート
このアラートは 2 つの異なる条件が満たされるとトリガーされます。これらの条件は両方とも構成できます。 1 つ目の条件として、グローバル管理者の数が特定のしきい値に達する必要があります。 2 つ目として、総ロール割り当て数のうち一定の割合がグローバル管理者である必要があります。 これらの測定値の一方のみが満たされている場合は、アラートは表示されません。  

* **Minimum number of Global Administrators**(グローバル管理者の最低人数): 安全ではないとみなすグローバル管理者の人数を、2 ～ 100 で指定します。
* **[グローバル管理者の割合]**: 環境内で安全ではないとする、グローバル管理者である管理者の割合を 0% ～ 100 % で指定します。

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>"管理者が特権ロールを使用してません" アラート
このアラートは、ユーザーがロールをアクティブ化しないまま一定の時間が過ぎるとトリガーされます。

* **日数**: ユーザーがロールをアクティブ化しないままでいられる日数を 0 ～ 100 で指定します。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png



<!--HONumber=Dec16_HO5-->


