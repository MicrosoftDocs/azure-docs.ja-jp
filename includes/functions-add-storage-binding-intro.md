---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329608"
---
Azure Functions を使用すると、独自の統合コードを記述しなくても、Azure サービスやその他のリソースを関数に接続できます。 これらの*バインド*は、入力と出力の両方を表し、関数定義内で宣言されます。 バインドからのデータは、パラメーターとして関数に提供されます。 "*トリガー*" は、特殊な種類の入力バインドです。 関数はトリガーを 1 つしか持てませんが、複数の入力および出力バインドを持つことができます。 詳細については、「[Azure Functions でのトリガーとバインドの概念](../articles/azure-functions/functions-triggers-bindings.md)」を参照してください。