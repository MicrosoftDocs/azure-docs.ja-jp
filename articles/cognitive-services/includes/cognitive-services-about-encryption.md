---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069913"
---
## <a name="about-cognitive-services-encryption"></a>Cognitive Services の暗号化について

データは、[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) に準拠する [256 ビット AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードまたはアプリケーションを変更する必要はありません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

サブスクリプションでは、Microsoft が管理する暗号化キーが既定で使用されます。 カスタマー マネージド キー (CMK) と呼ばれているユーザー独自のキーを使用してサブスクリプションを管理するオプションもあります。 CMK を使用すると、アクセス制御の作成、ローテーション、無効化、取り消しを、いっそう柔軟に行うことができます。 また、データを保護するために使われる暗号化キーを監査することもできます。 CMK がサブスクリプション用に構成されている場合は、Azure Key Vault で暗号化キーを制御しながら、2 つ目の保護レイヤーを提供する二重暗号化が提供されます。