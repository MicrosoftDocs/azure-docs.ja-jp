---
title: Azure Data Share プレビューのロールおよび要件
description: Azure Data Share プレビューのロールおよび要件
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 76889904dd89da0b44dad6b47250351c73f75653
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788526"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>Azure Data Share プレビューのロールおよび要件

この記事では、Azure Data Share プレビューを使用してデータを共有する、または Azure Data Share プレビューを使用して承諾およびデータの受信を行う際に必要となるロールについて説明します。 

## <a name="roles-and-requirements"></a>ロールと要件

Azure Data Share を使用してデータを共有または受信するには、Azure へのサインインに使用するユーザー アカウントが、データの共有元またはデータの受信先となるストレージ アカウントに Data Share アクセス許可を付与できる必要があります。 通常、このアクセス許可を保持しているのは、**所有者**ロールまたは Microsoft.Authorization/ロール割り当て/書き込みのアクセス許可が割り当てられているカスタム ロールです。 

Azure ストレージ アカウントとのデータ共有またはデータ受信を行うには、データを受信するストレージ アカウントの所有者である必要があります。 自分で作成したストレージ アカウントの場合でも、自分がそのストレージ アカウントの所有者権限が自動的に割り当てられることはありません。 自分の Azure Storage アカウントに所有者ロールを追加するには、次の手順に従います。

1. Azure portal でストレージ アカウントに移動します
1. **[アクセス制御 (IAM)]** を選択します
1. **[追加]** をクリックします。
1. ストレージ BLOB データ所有者として自分自身を追加します

サブスクリプションで自分が持っているアクセス許可を表示するには、Azure portal で右上隅にあるユーザー名を選択し、 **[アクセス許可]** を選択します。 複数のサブスクリプションにアクセスできる場合は、適切なサブスクリプションを選択します。 

## <a name="resource-provider-registration"></a>リソース プロバイダーの再登録 

Azure Data Share の招待を承諾するときは、サブスクリプションに Microsoft.DataShare リソース プロバイダーを手動で登録する必要があります。 Microsoft.DataShare リソース プロバイダーを Azure サブスクリプションに登録するには、次の手順に従います。 

1. Azure portal で、 **[サブスクリプション]** に移動します。
1. Azure Data Share に使っているサブスクリプションを選択します
1. **[リソース プロバイダー]** をクリックします
1. Microsoft.DataShare を検索します
1. **[登録]** をクリックします。

## <a name="next-steps"></a>次の手順

- Azure でのロールの詳細 - [ロール定義について](../role-based-access-control/role-definitions.md)

