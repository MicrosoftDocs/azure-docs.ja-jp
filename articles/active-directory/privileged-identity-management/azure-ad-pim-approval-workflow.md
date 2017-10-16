---
title: "Azure Privileged Identity Management の承認ワークフロー | Microsoft Docs"
description: "Privileged Identity Management (PIM) での承認ワークフローについて学習する"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: cf6a9213fa0a1cba8725aabb42abe51b805ece7a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="approvals-preview"></a>承認 (プレビュー)

## <a name="overview"></a>概要

Privileged Identity Management の承認を使用して、ロールがアクティブ化の承認を必要とするように構成できます。また、代理承認者として 1 名以上のユーザーあるいはグループを選択できます。 ロールの構成と承認者の選択を行う方法については後ほど説明します。

>[!NOTE]
この機能はまだ開発段階であることに注意してください。バグが含まれている可能性があります。 テキストや名前付け規則を含めて、機能は変更される場合があります。最終的な状態ではありません。


## <a name="key-terminology"></a>主要な用語

*対象ロール ユーザー* – 対象ロール ユーザーとは、組織内で対象の Azure AD ロール (アクティブ化を必要とするロール) に割り当てられているユーザーです。

*代理承認者* – 代理承認者は、ロールのアクティブ化要求を承認する、Azure AD 内の 1 名以上の個人またはグループです。

## <a name="scenarios"></a>シナリオ

プライベート プレビューでは、次のシナリオをサポートしています。

**特権ロール管理者 (PRA) として:**

-   [特定のロールの承認を有効化します。](#enable-approval-for-specific-roles)

-   [要求を承認するユーザーまたはグループ (あるいは両方) を指定します。](#specify-approver-users-and/or-groups-to-approve-requests)

-   [すべての特権ロールの要求と承認の履歴を表示します。](#view-request-and-approval-history-for-all-privileged-roles)

**指定された承認者として:**

-   [保留中の承認 (要求) を表示します。](#view-pending-approvals-requests)

-   [ロールの昇格の要求を承認または拒否します (単独および一括)。](#approve-or-reject-requests-for-role-elevation-single-and/or-bulk)

-   [自分の承認/却下の理由を説明します。](#provide-justification-for-my-approval/rejection) 

**対象ロール ユーザーとして:**

-   [承認が必要なロールのアクティブ化を要求します。](#request-activation-of-a-role-that-requires-approval)

-   [アクティブ化要求の状態を表示します。](#view-the-status-of-your-request-to-activate)

-   [アクティブ化が承認された場合に Azure AD でタスクを完了します。](#complete-your-task-in-azure-ad-if-activation-was-approved)

### <a name="navigation"></a>「ナビゲーション」

承認に対応するためにナビゲーションが更新されました。

![](media/azure-ad-pim-approval-workflow/image001.png)

既定のランディング ページから、PIM に関する情報や新しい承認ドキュメントに簡単にアクセスできます。

![](media/azure-ad-pim-approval-workflow/image002.png)

PIM のすべてのユーザーのために [My Audit History (My 監査履歴)] という新しいセクションも追加されています。 ここでは自分の ID に関するすべての情報を確認できます。 ここでは一箇所に、保留中の要求、完了した要求、解決する要求に関して行ったすべての決定事項、過去のロール アクティブ化すべてが含まれます。

![](media/azure-ad-pim-approval-workflow/image003.png)

### <a name="enable-approval-for-specific-roles"></a>特定のロールの承認を有効化する

特定のロールの承認を有効化するには、まず左のナビゲーションから [Directory Roles (ディレクトリ ロール)] を選択します。

![](media/azure-ad-pim-approval-workflow/image004.png)

[Directory Roles (ディレクトリ ロール)] の左側のナビゲーションで [設定] を選択します。

![](media/azure-ad-pim-approval-workflow/image006.png)

次のように [特権ロール] を選択します。

![](media/azure-ad-pim-approval-workflow/image009.png)

[承認を要求する] セクションで [有効化] を選択します。

![](media/azure-ad-pim-approval-workflow/image011.png)

有効にすると、次の詳細を表示するブレードが展開されます。

![](media/azure-ad-pim-approval-workflow/image013.png)

>[!NOTE]
承認者を指定しない場合は、PRA が既定の承認者になります。 PRA が、このロールのすべてのアクティブ化要求を承認する必要があります。

### <a name="specify-approver-users-andor-groups-to-approve-requests"></a>要求を承認するユーザーまたはグループ (あるいは両方) を指定する

承認を委任するには、[承認者の選択] のオプションをクリックします。

![](media/azure-ad-pim-approval-workflow/image015.png)

[承認者の選択] ブレードが読み込まれたら、特定のユーザーまたはグループを上部の検索バーを使用して検索するか、値が設定済みのリストから選択し、[選択] をクリックします。

![](media/azure-ad-pim-approval-workflow/image017.png)

注: 一度に複数のユーザーまたはグループを選択できます。

次に示すように、選択した承認者の一覧に選択内容が表示されます。

![](media/azure-ad-pim-approval-workflow/image019.png)

承認者を削除するには、その名前の横にある [削除] ボタンをクリックします。

承認者を追加するには、プロセスを繰り返します。

## <a name="view-request-and-approval-history-for-all-privileged-roles"></a>すべての特権ロールの要求と承認の履歴を表示する

すべての特権ロールの要求と承認の履歴を表示するには、ダッシュボードから [監査履歴] を選択します。

![](media/azure-ad-pim-approval-workflow/image021.png)

>[!NOTE]
[アクション] でデータを並べ替え、[アクティブ化 - 承認済み] を探します。

### <a name="view-pending-approvals-requests"></a>保留中の承認 (要求) を表示する

代理承認者として、承認の要求が保留されると電子メール通知を受け取ります。 PIM ポータルでこれらの要求を表示するには、ダッシュボード (新ナビゲーション) の左側のナビゲーション バーで [保留中の承認要求] タブを選択します。

![](media/azure-ad-pim-approval-workflow/image023.png)

ここで、承認が保留されている要求の一覧を確認できます。

![](media/azure-ad-pim-approval-workflow/image024.png)

### <a name="approve-or-reject-requests-for-role-elevation-single-andor-bulk"></a>ロールの昇格の要求を承認または拒否する (単独および一括)

承認または拒否する要求を選択し、処理に対応するアクション バーのボタンをクリックします。

![](media/azure-ad-pim-approval-workflow/image025.png)

### <a name="provide-justification-for-my-approvalrejection"></a>自分の承認/却下の理由を説明する

新しいブレードが開き、ここで一度に複数の要求を承認または拒否できます。 決定の理由を入力し、ブレードの下部で [承認] \(または [拒否]) をクリックします。

![](media/azure-ad-pim-approval-workflow/image029.png)

要求のプロセスが完了すると、行った決定が状態のシンボルに反映されます (この例の決定は承です)。

![](media/azure-ad-pim-approval-workflow/image031.png)

### <a name="request-activation-of-a-role-that-requires-approval"></a>承認が必要なロールのアクティブ化を要求する

承認を必要とするロールのアクティブ化の要求は、以前の PIM ナビゲーションからも新しいナビゲーションからも行うことができます。ロールのアクティブ化のプロセスは変わっていません。 アクティブ化するロールを一覧から選択するだけです。

![](media/azure-ad-pim-approval-workflow/image033.png)

特権ロールが Multi-Factor Authentication を必要とする場合は、最初にそのタスクを完了するように求められます。

![](media/azure-ad-pim-approval-workflow/image035.png)

完了したら、[アクティブ化] をクリックし、必要であれば理由を入力します。

![](media/azure-ad-pim-approval-workflow/image037.png)

依頼者には、要求の承認が保留されているという通知が表示されます。

![](media/azure-ad-pim-approval-workflow/image039.png)

### <a name="view-the-status-of-your-request-to-activate"></a>アクティブ化要求の状態を表示する

保留中のアクティブ化要求の状態を表示するには、新しいナビゲーションからアクセスする必要があります。 左側のナビゲーション バーで [My Requests (My 要求)] タブを選択します。

![](media/azure-ad-pim-approval-workflow/image041.png)

要求の状態は既定では [保留中] ですが、すべての要求または拒否された要求の表示に切り替えることができます。

### <a name="complete-your-task-in-azure-ad-if-activation-was-approved"></a>アクティブ化が承認された場合に Azure AD でタスクを完了する

要求が承認されると、ロールがアクティブになり、そのロールを必要とするすべての作業を進めることができます。

![](media/azure-ad-pim-approval-workflow/image043.png)

## <a name="next-steps"></a>次のステップ

お客様のフィードバックを必要としています。 ご意見やフィードバックをぜひお寄せください。
