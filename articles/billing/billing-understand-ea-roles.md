---
title: Azure の Enterprise 管理者ロールを理解する | Microsoft Docs
description: Azure の Enterprise 管理者ロールについて説明します。
services: billing
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2019
ms.author: banders
ms.openlocfilehash: 62a16a6a96fbfdfbb4ed2d70b74470e29efaa844
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849855"
---
# <a name="understand-azure-enterprise-agreement-administrative-roles-in-azure"></a>Azure の Azure Enterprise Agreement 管理者ロールを理解する

組織による使用量/支出管理を支援するために、Enterprise Agreement (EA) をお持ちの Azure カスタマーは 5 つの異なる管理者ロールを割り当てることができます。

- エンタープライズ管理者
- エンタープライズ管理者 (読み取り専用)
- 部門管理者
- 部門管理者 (読み取り専用)
- アカウント所有者
 
以上のロールは Azure Enterprise Agreement 管理に固有のものであり、リソース アクセスを制御する目的で Azure に与えられる組み込みロールに追加されるものです。 詳細については、「[Azure リソースの組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。

次のセクションでは、各ロールの制限事項と機能について説明します。

## <a name="user-limit-for-admin-roles"></a>管理ロールのユーザー数制限

|Role| ユーザー数制限|
|---|---|
|エンタープライズ管理者|無制限|
|エンタープライズ管理者 (読み取り専用)|無制限|
|部門管理者|無制限|
|部門管理者 (読み取り専用)|無制限|
|アカウント所有者|アカウントにつき 1 つ<sup>1</sup>|

<sup>1</sup> アカウントごとに、重複のない Microsoft アカウント、または職場のアカウントまたは学校のアカウントを 1 つ必要とします。

## <a name="organization-structure-and-permissions-by-role"></a>組織の構造とロール別のアクセス許可

|タスク| エンタープライズ管理者|エンタープライズ管理者 (読み取り専用)|部門管理者|部門管理者 (読み取り専用)|アカウント所有者|
|---|---|---|---|---|---|
|エンタープライズ管理者を表示する|✔|✔|✘|✘|✘|
|エンタープライズ管理者を追加または削除する|✔|✘|✘|✘|✘|
|通知の連絡窓口を表示する<sup>2</sup> |✔|✔|✘|✘|✘|
|通知の連絡窓口を追加または削除する<sup>2</sup> |✔|✘|✘|✘|✘|
|部門を作成し、管理する |✔|✘|✘|✘|✘|
|部門管理者を表示する|✔|✔|✔|✔|✘|
|部門管理者を追加または削除する|✔|✘|✔|✘|✘|
|登録済みのアカウントを表示する |✔|✔|✔<sup>3</sup>|✔<sup>3</sup>|✘|
|登録にアカウントを追加する/アカウント所有者を変更する|✔|✘|✔<sup>3</sup>|✘|✘|
|サブスクリプションとサブスクリプション アクセス許可を作成し、管理する|✘|✘|✘|✘|✔|

- <sup>2</sup> 通知の連絡窓口には Azure Enterprise Agreement に関する電子メール通信が送信されます。
- <sup>3</sup> タスクは部門内のアカウントに制限されます。


## <a name="usage-and-costs-access-by-role"></a>ロール別の使用量/コスト アクセス

|タスク| エンタープライズ管理者|エンタープライズ管理者 (読み取り専用)|部門管理者|部門管理者 (読み取り専用) |アカウント所有者|
|---|---|---|---|---|---|
|年額コミットメントを含む、与信残高を表示する|✔|✔|✘|✘|✘|
|部門の支出クォータを表示する|✔|✔|✘|✘|✘|
|部門の支出クォータを設定する|✔|✘|✘|✘|✘|
|組織の EA 価格シートを表示する|✔|✔|✘|✘|✘|
|使用量とコストの詳細を表示する|✔|✔|✔<sup>4</sup>|✔<sup>4</sup>|✔<sup>5</sup>|
|Azure portal でリソースを管理する|✘|✘|✘|✘|✔|

- <sup>4</sup> エンタープライズ管理者が Enterprise portal で **DA ビューの請求額**ポリシーを有効にする必要があります。 有効にすると、部門管理者は部門のコスト詳細を表示できます。
- <sup>5</sup> エンタープライズ管理者が Enterprise portal で **AO ビューの請求額**ポリシーを有効にする必要があります。 有効にすると、アカウント所有者はアカウントのコスト詳細を表示できます。


## <a name="pricing-in-azure-portal"></a>Azure portal の価格

Azure portal に表示される価格は管理者ロールによって異なる場合があります。また、エンタープライズ管理者による請求金額の表示ポリシーの設定方法にも左右されます。 Azure portal に表示される価格を左右する Enterprise portal の 2 つのポリシー:

- DA ビューの請求額
- AO ビューの請求額

これらのポリシーを設定する方法については、「[Manage access to billing information for Azure](billing-manage-access.md)」(Azure の課金情報へのアクセスの管理) を参照してください。

次の表は、Enterprise Agreement 管理者ロール、請求金額の表示ポリシー、Azure portal のロールベースのアクセス制御 (RBAC) ロール、Azure portal に表示される価格の関係をまとめたものです エンタープライズ管理者には、組織の EA 価格に基づいて使用料詳細が常に表示されます。 ただし、部門管理者とアカウント所有者では、請求金額の表示ポリシーとその RBAC ロールに基づいて、表示される価格ビューが異なります。 次の表に記載されている部門管理者ロールは、部門管理者ロールと部門管理者 (読み取り専用) ロールの両方を指します。

|Enterprise Agreement 管理者ロール|ロールの請求金額の表示ポリシー|RBAC ロール|価格ビュー|
|---|---|---|---|
|アカウント所有者または部門管理者|✔ 有効|Owner|組織の EA 価格|
|アカウント所有者または部門管理者|✘ 無効|Owner|小売価格|
|アカウント所有者または部門管理者|✔ 有効 |なし|価格設定なし|
|アカウント所有者または部門管理者|✘ 無効 |なし|価格設定なし|
|なし|適用不可 |Owner|小売価格|

Enterprise portal でエンタープライズ管理者ロールと請求金額の表示ポリシーを設定します。 RBAC ロールは Azure portal で更新できます。 詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure の課金情報へのアクセスの管理](billing-manage-access.md)
- [RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)
- [Azure リソースの組み込みロール](../role-based-access-control/built-in-roles.md)
