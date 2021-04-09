---
title: Azure Data Share のセキュリティの概要
description: Azure Data Share のセキュリティの概要
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678426"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share のセキュリティの概要

この記事では、Azure Data Share サービスのセキュリティの概要について説明します。

## <a name="security-overview"></a>セキュリティの概要

Azure Data Share では、保存時と転送中のデータを保護するために Azure によって提供される基盤のセキュリティが利用されます。 データは保存時に暗号化され、基になるデータ ストアでサポートされます。 データは転送中も TLS 1.2 を使用して暗号化されます。 データ共有に関するメタデータも、保存時と転送中に暗号化されます。 Azure Data Share には、共有されている顧客データの内容は保存されません。

Azure Data Share では、データ共有に使用されているデータ ストアにアクセスするために、マネージド ID (以前の MSI) が利用されます。 データ プロバイダーとデータ コンシューマーの間で資格情報が交換されることはありません。 マネージド ID の詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) に関する記事を参照してください。 データを共有するために必要なロールとアクセス許可の詳細については、[ロールとアクセス許可](concepts-roles-permissions.md)に関する記事を参照してください。

## <a name="access-control"></a>アクセス制御

承認されたユーザーによってアクセスされることを保証するために、Azure Data Share へのアクセスの制御を Data Share のリソース レベルで設定することができます。 Data Share リソースの所有者および共同作成者は、データを共有し、共有を受信し、既存の共有に変更を加えることができます。 Data Share リソースの閲覧者は、共有を表示できますが、変更を加えることはできません。 

共有が作成または受信されると、Data Share リソースに対する適切なアクセス許可を持つユーザーは、変更を加えることができます。 共有を作成または受信したユーザーが組織を離れた場合、共有が終了されたり、データのフローが停止されたりすることはありません。 Data Share リソースに対する適切なアクセス許可を持つ他のユーザーは、引き続き共有を管理できます。

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>ファイアウォールが有効になっているデータ ストアとの間でデータを共有する
ファイアウォールがオンになっているストレージ アカウントとの間でデータを共有するには、ストレージ アカウントで **[信頼された Microsoft サービスを許可する]** を有効にする必要があります。 詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)」を参照してください。


## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。
