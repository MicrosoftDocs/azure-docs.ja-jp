---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494016"
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

   > [!NOTE]
   > RedHat OpenShift 4.5 以上では、SCC をサービス アカウントに適用する方法が変更されています。
   > ここで使用するものと同じ名前空間を、次の `azdata arc dc create` コマンドでも使用します。 たとえば `arc` とします。 
   > 
   > RedHat OpenShift 4.5 以上を使用している場合は、以下を実行します。 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
