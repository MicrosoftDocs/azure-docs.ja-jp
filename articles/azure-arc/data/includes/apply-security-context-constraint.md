---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 03/02/2021
ms.author: mikeray
ms.openlocfilehash: 0fca43f76b24a08ca96be749f7f2a822b0be2418
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687594"
---
このセクションでは、セキュリティ コンテキスト制約 (SCC) を適用する方法について説明します。 プレビュー リリースでは、これらによってセキュリティ制約が緩和されます。 

1. カスタム セキュリティ コンテキスト制約 (SCC) をダウンロードします。 次のいずれかを使用します: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [Raw](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
       次のコマンドを実行すると、arc-data-scc.yaml がダウンロードされます。

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. SCC を作成します。

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. SCC をサービス アカウントに適用します。

   > [!NOTE]
   > ここで使用するものと同じ名前空間を、次の `azdata arc dc create` コマンドでも使用します。 たとえば `arc` とします。

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

   ```console
   oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   ```
