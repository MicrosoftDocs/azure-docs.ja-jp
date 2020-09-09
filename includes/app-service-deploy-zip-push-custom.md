---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 0019e50615f3e66778709ad8cb28f92967c66e2e
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050481"
---
## <a name="deployment-customization"></a>展開のカスタマイズ

展開プロセスでは、プッシュする .zip ファイルにはすぐに実行できる状態のアプリが含まれるものと想定されています。 既定では、カスタマイズは実行されません。 継続的インテグレーションで取得するものと同じビルド プロセスを有効にするには、アプリケーションの設定に以下を追加します。

`SCM_DO_BUILD_DURING_DEPLOYMENT=true`

.zip プッシュ展開を使うときのこの設定の既定値は **false** です。 継続的インテグレーション展開の場合の既定値は **true** です。 **true** に設定すると、展開の間にユーザーの展開関連の設定が使われます。 これらの設定は、アプリ設定として、または .zip ファイルのルートに存在する .deployment 構成ファイルで、構成できます。 詳しくは、展開リファレンスの「[Repository and deployment-related settings](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings)」(リポジトリと展開関連の設定) をご覧ください。