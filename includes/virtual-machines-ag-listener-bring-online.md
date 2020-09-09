---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "67181126"
---
1. フェールオーバー クラスター マネージャーで、 **[ロール]** を展開し、可用性グループを強調表示します。  

2. **[リソース]** タブで、リスナー名を右クリックし、 **[プロパティ]** をクリックします。

3. **[依存関係]** タブをクリックします。複数のリソースが一覧表示される場合は、IP アドレスに (AND ではなく) OR 依存関係があることを確認します。  

4. **[OK]** をクリックします。

5. リスナー名を右クリックし、 **[オンラインにする]** をクリックします。

6. リスナーがオンラインになったら、 **[リソース]** タブで可用性グループを右クリックし、 **[プロパティ]** をクリックします。
   
    ![可用性グループ リソースを構成する](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. (IP アドレス リソース名ではなく) リスナー名のリソースへの依存関係を作成し、 **[OK]** をクリックします。
   
    ![リスナー名の依存関係を追加する](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. SQL Server Management Studio を起動し、プライマリ レプリカに接続します。

9. **[AlwaysOn 高可用性]**  >  **[可用性グループ]**  >  **\<AvailabilityGroupName\>**  >  **[可用性グループ リスナー]** の順に移動します。  
    フェールオーバー クラスター マネージャーで作成したリスナー名が表示されます。

10. リスナー名を右クリックし、 **[プロパティ]** をクリックします。

11. **[ポート]** ボックスで、以前に使用した $EndpointPort (このチュートリアルでは、既定値は 1433) を使用し、可用性グループ リスナーのポート番号を指定して、 **[OK]** をクリックします。

