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
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147256"
---
 また、Azure Machine Learning コンピューティング クラスターによって、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) がサポートされ、コードに資格情報を含めることなく、Azure リソースへのアクセスが認証されます。 マネージド ID には、次の 2 種類があります。

* **システム割り当てマネージド ID** は、Azure Machine Learning コンピューティング クラスターで直接有効になります。 システム割り当て ID のライフ サイクルは、コンピューティング クラスターに直接関連付けられます。 コンピューティング クラスターが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。
* **ユーザー割り当てマネージド ID** は、Azure マネージド ID サービスを介して提供されるスタンドアロンの Azure リソースです。 ユーザー割り当てマネージド ID を複数のリソースに割り当てることができ、任意の有効期間を設定できます。