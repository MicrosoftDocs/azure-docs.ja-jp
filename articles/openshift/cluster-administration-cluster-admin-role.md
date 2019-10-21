---
title: Azure Red Hat OpenShift クラスターの管理者ロール | Microsoft Docs
description: Azure Red Hat OpenShift クラスターの管理者ロールの割り当てと使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936926"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift の顧客管理者ロール

この記事の対象者は、Azure Red Hat OpenShift クラスターのクラスター管理者です。 管理者のアカウントには、すべてのユーザー作成プロジェクトに対する引き上げられたアクセス許可とアクセス権が与えられています。

アカウントに osa-customer-admin 認可ロールがバインドされていると、プロジェクトを自動的に管理できます。

> [!Note] 
> osa-customer-admin クラスター ロールは、cluster-admin クラスター ロールと同じものではありません。


たとえば、一連の動詞 (`create`) に関連付けられているアクションを実行し、一連のリソース名 (`templates`) を操作できます。 これらのロールのその一連の動詞とリソースの詳細を表示するには、次のコマンドを実行します。

`$ oc describe clusterrole/osa-customer-admin`

動詞の名前は、必ずしもすべてが `oc` コマンドに直接マップされるわけではありません。 それらは、実行できる CLI 操作の種類にさらに一般的に相当します。 

たとえば、動詞 `list` は、リソース名のすべてのオブジェクトの一覧を表示できることを意味します (`oc get`)。 動詞 `get` は、名前がわかっている場合に特定のオブジェクトの詳細を表示できることを意味します (`oc describe`)。

## <a name="configure-the-customer-administrator-role"></a>顧客管理者ロールを構成する

顧客管理者ロールは、クラスターの作成中にのみ、フラグ `--customer-admin-group-id` を指定することによって構成できます。 Azure Active Directory と Administrators グループを構成する方法については、「[Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md)」を参照してください。

## <a name="next-steps"></a>次の手順

osa-customer-admin ロールを構成します。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md)
