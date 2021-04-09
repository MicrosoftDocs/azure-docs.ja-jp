---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 0c7e011cf8445164e0931f71e390813c9134dd89
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200958"
---
1. [Azure portal](https://portal.azure.com/) で Azure Stack Edge リソースを選択し、 **[概要]** に移動します。 デバイスがオンラインになっている必要があります。 **[Cloud storage gateway]\(クラウド ストレージ ゲートウェイ\) > [ストレージ アカウント]** に移動します。

2. デバイスのコマンド バーで **[+ ストレージ アカウントの追加]** を選択します。 

   ![ストレージ アカウントの追加](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. **[Edge ストレージ アカウントの追加]** ペインで、次の設定を指定します。

    1. お使いのデバイスでの Edge ストレージ アカウントの一意の名前を指定します。 ストレージ アカウント名に使用できるのは、小文字と数字のみです。 特殊文字は使用できません。 ストレージ アカウント名は、(すべてのデバイスについてではなく) そのデバイス内で一意である必要があります。

    2. ストレージ アカウントで保持されているデータに関する情報の説明を入力します (省略可能)。  
    
    3. 既定では、Edge ストレージ アカウントはクラウド内の Azure ストレージ アカウントにマップされ、ストレージ アカウントのデータは自動的にクラウドにプッシュされます。 Edge ストレージ アカウントのマップ先となる Azure ストレージ アカウントを指定します。

    4. 新しいコンテナーを作成するか、Azure ストレージ アカウント内の既存のコンテナーから選択します。 Edge ストレージ アカウントに書き込まれたデバイスのデータは、マップされた Azure ストレージ アカウントで選択されているストレージ コンテナーに自動的にアップロードされます。

    5. ストレージ アカウントのオプションをすべて指定した後、 **[追加]** を選択して、Edge ストレージ アカウントを作成します。 Edge ストレージ アカウントが正常に作成されると、通知を受け取ります。 その後、新しい Edge ストレージ アカウントが、Azure portal のストレージ アカウントの一覧に表示されます。

    <!--[Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->
    
4. この新しいストレージ アカウントを選択して **[アクセス キー]** に移動すると、Blob service エンドポイントと、対応するストレージ アカウント名を確認できます。 これらの値とアクセス キーを一緒に使用すると Edge ストレージ アカウントに接続できるので、この情報をコピーしておきます。

    ![ストレージ アカウントの追加 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    アクセス キーを取得するには、[Azure Resource Manager を使用してデバイスのローカル API に接続](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)します。 
