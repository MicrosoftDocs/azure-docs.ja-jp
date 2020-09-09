---
title: Azure Data Share のセキュリティの概要
description: Azure Data Share のセキュリティの概要
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 10f31b74b461941b15f13e45f90b5fbc408c90fe
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108415"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share のセキュリティの概要

この記事では、Azure Data Share サービスのセキュリティの概要について説明します。

## <a name="security-overview"></a>セキュリティの概要

Azure Data Share では、保存時と転送中のデータを保護するために Azure によって提供される基盤のセキュリティが利用されます。 データは保存時に暗号化され、基になるデータ ストアでサポートされます。 データは転送中も暗号化されます。 データ共有に関するメタデータも、保存時と転送中に暗号化されます。 

Azure Data Share のリソース レベルでアクセス制御を設定し、承認されたユーザーだけによってアクセスされることを保証できます。 

Azure Data Share では、データ共有に使用されているデータ ストアにアクセスするために、マネージド ID (以前の MSI) が利用されます。 データ プロバイダーとデータ コンシューマーの間で資格情報が交換されることはありません。 マネージド ID の詳細については、[Azure リソースのマネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) に関する記事を参照してください。 データを共有するために必要なロールとアクセス許可の詳細については、[ロールとアクセス許可](concepts-roles-permissions.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。




