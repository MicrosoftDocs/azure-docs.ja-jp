---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027481"
---
 また、Azure Machine Learning コンピューティング クラスターによって、[マネージド ID](../articles/active-directory/managed-identities-azure-resources/overview.md) がサポートされ、コードに資格情報を含めることなく、Azure リソースへのアクセスが認証されます。 マネージド ID には、次の 2 種類があります。

* **システム割り当てマネージド ID** は、Azure Machine Learning コンピューティング クラスターで直接有効になります。 システム割り当て ID のライフ サイクルは、コンピューティング クラスターに直接関連付けられます。 コンピューティング クラスターが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。
* **ユーザー割り当てマネージド ID** は、Azure マネージド ID サービスを介して提供されるスタンドアロンの Azure リソースです。 ユーザー割り当てマネージド ID を複数のリソースに割り当てることができ、任意の有効期間を設定できます。