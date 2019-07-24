---
title: クイック スタート - Azure リソースに対するユーザーのアクセス権を表示する | Microsoft Docs
description: ロールベースのアクセス制御 (RBAC) と Azure portal を使用して、ユーザーまたは他のセキュリティ プリンシパルが Azure リソースに対して持っているアクセス権を表示する方法について説明します。
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/30/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f388215b2829066906ee7faf41abb17307bf3fff
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337947"
---
# <a name="quickstart-view-the-access-a-user-has-to-azure-resources"></a>クイック スタート:Azure リソースに対するユーザーのアクセス権を表示する

[ロールベースのアクセス制御 (RBAC)](overview.md) の **[アクセス制御 (IAM)]** ブレードを使用して、ユーザーまたは別のセキュリティ プリンシパルが Azure リソースに対して持っているアクセス権を表示できます。 ただし、1 人のユーザーまたは別のセキュリティ プリンシパルのアクセス権をすばやく表示することだけが必要な場合があります。 これを行う最も簡単な方法は、Azure portal の **[アクセスの確認]** 機能を使うことです。

## <a name="view-role-assignments"></a>ロールの割り当てを表示する

 ユーザーのアクセス権を表示する方法は、ユーザーのロールの割り当てを一覧表示することです。 サブスクリプションのスコープで単独のユーザー、グループ、サービス プリンシパル、またはマネージド ID のロールの割り当てを表示するには、次の手順のようにします。

1. Azure portal で、**[すべてのサービス]**、**[サブスクリプション]** の順にクリックします。

1. お使いのサブスクリプションをクリックします。

1. **[アクセス制御 (IAM)]** をクリックします。

1. **[アクセスの確認]** タブをクリックします。

    ![アクセス制御 - [アクセスの確認] タブ](./media/check-access/access-control-check-access.png)

1. **[検索]** 一覧で、アクセス権を確認するセキュリティ プリンシパルの種類を選択します。

1. 検索ボックスに、表示名、メール アドレス、またはオブジェクト識別子のディレクトリを検索するための文字列を入力します。

    ![[アクセスの確認] の選択リスト](./media/check-access/check-access-select.png)

1. セキュリティ プリンシパルをクリックして **[割り当て]** ウィンドウを開きます。

    ![[割り当て] ウィンドウ](./media/check-access/check-access-assignments.png)

    このウィンドウでは、選択したセキュリティ プリンシパルに割り当てられているロールとスコープを確認できます。 このスコープに拒否割り当てがある場合、またはこのスコープに継承されている場合は、それが表示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チュートリアル:RBAC と Azure portal を使用して Azure リソースへのアクセス権をユーザーに付与する](quickstart-assign-role-user-portal.md)
