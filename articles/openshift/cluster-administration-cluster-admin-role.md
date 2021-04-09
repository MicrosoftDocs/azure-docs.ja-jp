---
title: Azure Red Hat OpenShift クラスターの管理者ロール | Microsoft Docs
description: Azure Red Hat OpenShift クラスターの管理者ロールの割り当てと使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636244"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift の顧客管理者ロール

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 は、2022 年 6 月 30 日に廃止されます。 新しい Azure Red Hat OpenShift 3.11 クラスターの作成のサポートは、2020 年 11 月 30 日まで継続されます。 廃止された後、残っている Azure Red Hat OpenShift 3.11 クラスターは、セキュリティの脆弱性を防ぐためにシャットダウンされます。
> 
> このガイドに従って、[Azure Red Hat OpenShift 4 クラスターを作成](tutorial-create-cluster.md)します。
> ご質問がある場合は、[お問い合わせください](mailto:arofeedback@microsoft.com)。

この記事の対象者は、Azure Red Hat OpenShift クラスターのクラスター管理者です。 管理者のアカウントには、すべてのユーザー作成プロジェクトに対する引き上げられたアクセス許可とアクセス権が与えられています。

アカウントに customer-admin-cluster 認可ロールがバインドされていると、プロジェクトを自動的に管理できます。

> [!Note] 
> customer-admin-cluster クラスター ロールは、cluster-admin クラスター ロールと同じものではありません。

たとえば、一連の動詞 (`create`) に関連付けられているアクションを実行し、一連のリソース名 (`templates`) を操作できます。 これらのロールのその一連の動詞とリソースの詳細を表示するには、次のコマンドを実行します。

`$ oc get clusterroles customer-admin-cluster -o yaml`

動詞の名前は、必ずしもすべてが `oc` コマンドに直接マップされるわけではありません。 それらは、実行できる CLI 操作の種類にさらに一般的に相当します。 

たとえば、動詞 `list` は、リソース名のすべてのオブジェクトの一覧を表示できることを意味します (`oc get`)。 動詞 `get` は、名前がわかっている場合に特定のオブジェクトの詳細を表示できることを意味します (`oc describe`)。

## <a name="configure-the-customer-administrator-role"></a>顧客管理者ロールを構成する

customer-admin-cluster クラスター ロールは、クラスターの作成中にのみ、フラグ `--customer-admin-group-id` を指定することによって構成できます。 現在、このフィールドは Azure portal では構成できません。 Azure Active Directory と Administrators グループを構成する方法については、「[Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md)」を参照してください。

## <a name="confirm-membership-in-the-customer-administrator-role"></a>顧客管理者ロールのメンバーシップを確認する

顧客管理者グループにおける自分のメンバーシップを確認するには、OpenShift CLI コマンド `oc get nodes` または `oc projects` を試してください。 `oc get nodes` では、customer-admin-cluster ロールを持っている場合はノードの一覧が表示され、customer-admin-project ロールのみを持っている場合はアクセス許可エラーが表示されます。 `oc projects` では、作業しているプロジェクトだけではなく、クラスター内のすべてのプロジェクトが表示されます。

クラスター内のロールとアクセス許可をさらに調べるには、[`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) コマンドを使用します。

## <a name="next-steps"></a>次の手順

customer-admin-cluster クラスター ロールを構成します。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md)
