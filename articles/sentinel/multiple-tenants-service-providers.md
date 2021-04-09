---
title: Azure Sentinel で複数のテナントをマネージド セキュリティ サービス プロバイダーとして管理する | Microsoft Docs
description: Azure Lighthouse を使用して、Azure Sentinel で複数のテナントをマネージド セキュリティ サービス プロバイダー (MSSP) としてオンボードおよび管理する方法。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578150"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Azure Sentinel で複数のテナントを MSSP として管理する

マネージド セキュリティ サービス プロバイダー (MSSP) が [Azure Lighthouse](../lighthouse/overview.md) を使用してセキュリティ運用センター (SOC) サービスを顧客に提供している場合は、顧客のテナントに接続する必要なしに、顧客の Azure Sentinel リソースを自社の Azure テナントから直接管理することができます。 

## <a name="prerequisites"></a>前提条件

- [Azure Lighthouse をオンボードする](../lighthouse/how-to/onboard-customer.md)

- この作業を適切に行うために、自社のテナント (MSSP テナント) には、1 つ以上のサブスクリプションで Azure Sentinel リソース プロバイダーが登録されている必要があります。 さらに、各顧客のテナントには、リソース プロバイダーが登録されている必要があります。 自社のテナントに Azure Sentinel が登録されており、顧客のテナントにも同様に登録されている場合は、作業を開始する準備ができています。 登録を確認するには、次の手順を行います。

    1. Azure portal で **[サブスクリプション]** を選択したら、メニューから関連するサブスクリプションを選択します。

    1. サブスクリプション画面で、ナビゲーション メニューの **[設定]** から **[リソース プロバイダー]** を選択します。

    1. **["*サブスクリプション名*" | リソース プロバイダー]** 画面で、*Microsoft.OperationalInsights* と *Microsoft.SecurityInsights* を検索して選択し、 **[状態]** 列を確認します。 プロバイダーの状態が *[未登録]* である場合は、 **[登録]** を選択します。
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="リソース プロバイダーを確認する":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>マネージド テナントから Azure Sentinel にアクセスする方法

1. **[ディレクトリ + サブスクリプション]** で、委任されたディレクトリ (ディレクトリとテナントは同じです) と、顧客の Azure Sentinel ワークスペースが配置されているサブスクリプションを選択します。

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="テナントとサブスクリプションを選択する":::

1. Azure Sentinel を開きます。 選択したサブスクリプションのすべてのワークスペースが表示され、それらを自社のテナント内のワークスペースと同様にシームレスに操作できるようになります。

> [!NOTE]
> Azure Sentinel でマネージド ワークスペース内からコネクタをデプロイすることはできません。 コネクタをデプロイするには、コネクタをデプロイするテナントに直接サインインし、必要なアクセス許可を使用してそこで認証を受ける必要があります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、複数の Azure Sentinel テナントをシームレスに管理する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。

