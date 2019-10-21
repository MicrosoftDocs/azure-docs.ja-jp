---
title: 複数のテナントを Azure Sentinel for MSSP サービス プロバイダーに対して使用する | Microsoft Docs
description: 複数のテナントを Azure Sentinel for MSSP サービス プロバイダーに対して使用する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 34997fb3cd91e4540c76ecdd781d21e2ed1bdb07
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71241247"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Azure Sentinel で複数のテナントを使用する 

マネージド セキュリティ サービス プロバイダー (MSSP) であり、[Azure Lighthouse](../lighthouse/overview.md) を使用して顧客のセキュリティ運用センター (SOC) を管理している場合、自社の Azure テナントから顧客のテナントに直接接続することなく、顧客の Azure Sentinel リソースを管理できます。 

## <a name="prerequisites"></a>前提条件
- [Azure Lighthouse をオンボードする](../lighthouse/how-to/onboard-customer.md)
- この作業が成功するには、少なくとも 1 つのサブスクリプションでテナントを Azure Sentinel リソース プロバイダーに登録する必要があります。 テナントに Azure Sentinel が登録されている場合は、作業を開始する準備ができています。 そうでない場合は、Azure portal で **[サブスクリプション]** 、 **[リソース プロバイダー]** の順に選択し、`Microsoft.Security.Insights` を検索して **[登録]** を選択します。
   ![リソース プロバイダーを確認する](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>他のテナントから Azure Sentinel にアクセスする方法
1. **[ディレクトリ + サブスクリプション]** で、委任されたディレクトリと、顧客の Azure Sentinel ワークスペースが配置されているサブスクリプションを選択します。

   ![セキュリティ インシデントを生成する](media/multiple-tenants-service-providers/directory-subscription.png)

1. Azure Sentinel を開きます。 選択したサブスクリプションのすべてのワークスペースが表示され、それらを自社のテナント内のワークスペースと同様にシームレスに操作できるようになります。

> [!NOTE]
> Azure Sentinel でマネージド ワークスペース内からコネクタをデプロイすることはできません。 コネクタをデプロイするには、コネクタをデプロイするテナントに直接サインインし、必要なアクセス許可を使用してそこで認証を受ける必要があります。





## <a name="next-steps"></a>次の手順
このドキュメントでは、複数の Azure Sentinel テナントをシームレスに管理する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

