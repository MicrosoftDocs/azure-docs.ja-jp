---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92204466"
---
Azure を使用すると、リソースに "_ロック_" を設定して、削除できないようにしたり、読み取り専用にしたりすることができます。 __リソースをロックすると、予期しない結果になる可能性があります。__ リソースを変更する操作のように見えなくても、実際はロックによってブロックされているアクションを必要とする場合があります。 

たとえば、ワークスペースのリソース グループに削除ロックを適用すると、Azure ML コンピューティング クラスターのスケーリング操作ができなくなります。

リソースのロックの詳細については、「[リソースのロックによる予期せぬ変更の防止](../articles/azure-resource-manager/management/lock-resources.md)」を参照してください。