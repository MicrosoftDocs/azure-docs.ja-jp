---
title: Media Services 用の信頼されたストレージ
description: マネージド ID 認証を使用すると、Media Services から、信頼されたストレージ アクセスによって、ファイアウォールまたは VNet 制限で構成されたストレージ アカウントにアクセスできるようになります。
keywords: 信頼されたストレージ、マネージド ID
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: inhenkel
ms.openlocfilehash: e8d21e57f9a844b3cc0538f4805780829a1350f4
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428590"
---
# <a name="trusted-storage-for-media-services"></a>Media Services 用の信頼されたストレージ

Media Services アカウントを作成するときは、それをストレージ アカウントに関連付ける必要があります。 システム認証またはマネージド ID 認証を使用することで、Media Services からそのストレージ アカウントにアクセスできます。 Media Services により、Media Services アカウントとストレージ アカウントが同じサブスクリプションに含まれていることが検証され、また、関連付けを追加するユーザーが Azure Resource Manager RBAC を使用してストレージ アカウントにアクセスしていることが検証されます。

## <a name="trusted-storage-with-a-firewall"></a>ファイアウォールを使用した信頼されたストレージ

ただし、ファイアウォールを使用してストレージ アカウントをセキュリティで保護し、信頼されたストレージを有効にする場合は、[マネージド ID](concept-managed-identities.md) 認証を選択することをお勧めします。 これにより、Media Services は、信頼されたストレージ アクセスによって、ファイアウォールまたは VNet 制限で構成されたストレージ アカウントにアクセスできるようになります。 これにより、Media Services は、信頼されたストレージ アクセスによって、ファイアウォールまたは VNet 制限で構成されたストレージ アカウントにアクセスできるようになります。

> [!NOTE]
> Media Services でストレージ アカウントに対する読み取りと書きを行えるようにするには、AMS マネージド ID ストレージ BLOB データ共同作成者アクセスを許可する必要があります。  汎用的な共同作成者ロールを付与してもうまくいきません。データ プレーンに対する適切なアクセス許可を有効にすることができないからです。

## <a name="further-reading"></a>関連項目

マネージド ID を使用して信頼されたストレージを作成する方法を理解するには、[マネージド ID と Media Services](concept-managed-identities.md) に関するページを参照してください。

信頼された Microsoft サービスの詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../../storage/common/storage-network-security.md#trusted-microsoft-services)」を参照してください。

## <a name="next-steps"></a>次のステップ

マネージド ID がお客様とお客様の Azure アプリケーションにどのように役立つかの詳細については、[Azure AD のマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。
