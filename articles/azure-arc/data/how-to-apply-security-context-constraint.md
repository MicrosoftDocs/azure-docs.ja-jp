---
title: セキュリティ コンテキスト制約を適用する方法
description: Azure Red Hat OpenShift または Red Hat OpenShift Container Platform のセキュリティ コンテキスト制約を適用する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 01/15/2021
ms.topic: how-to
ms.openlocfilehash: a6751a9ff96b82f9d4fdaacd449c8a766afaa381
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697747"
---
# <a name="apply-a-security-context-constraint-for-azure-arc-enabled-data-services-on-openshift"></a>OpenShift で Azure Arc 対応データ サービスのセキュリティ コンテキスト制約を適用する

この記事では、Azure Arc 対応データ サービスのセキュリティ コンテキスト制約を適用する方法について説明します。 

## <a name="applicability"></a>適用範囲

これは、Azure Red Hat OpenShift または Red Hat OpenShift Container Platform へのデプロイに適用されます。 

## <a name="apply-security-context-constraint"></a>セキュリティ コンテキスト制約を適用する

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Arc データ コントローラーを作成する](create-data-controller.md)
- [Azure Data Studio でデータ コントローラーを作成する](create-data-controller-azure-data-studio.md)
- [[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] を使用した Azure Arc データ コントローラーの作成[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)

