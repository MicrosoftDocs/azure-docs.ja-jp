---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671193"
---
1. dapl、rdmacm、ibverbs、mlx4 をインストールします

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. /etc/waagent.conf で、次の構成行のコメントを解除して RDMA を有効にします。 このファイルを編集するにはルート アクセスが必要です。
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. /etc/security/limits.conf ファイルで次のメモリ設定 (KB単位) を追加または変更します。 このファイルを編集するにはルート アクセスが必要です。 テスト目的で、memlock を無制限に設定することができます。 (例: `<User or group name>   hard    memlock   unlimited`)。

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Intel MPI ライブラリをインストールします。 Intel からライブラリを[購入してダウンロード](https://software.intel.com/intel-mpi-library/)するか、[無料評価版](https://registrationcenter.intel.com/en/forms/?productid=1740)をダウンロードします。

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Intel MPI 5.x ランタイムのみがサポートされています。
 
   インストール手順については、[Intel MPI ライブラリのインストール ガイド](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)をご覧ください。

5. (Intel MPI の最新バージョンに必要な) non-root non-debugger プロセスに対して ptrace を有効にします。
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
