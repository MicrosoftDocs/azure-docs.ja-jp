---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889289"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用して通常の更新プログラムをインストールするには
1. デバイスのシリアル コンソールを開き、オプション 1 の **[フル アクセスによるログイン]** を選択します。 パスワードを入力します。 既定のパスワードは *Password1*です。 
2. コマンド プロンプトに、次のコマンドを入力します。
   
     `Get-HcsUpdateAvailability`
   
    更新プログラムが利用可能かどうかと、更新プログラムが中断を伴うものであるかどうかが通知されます。
3. コマンド プロンプトに、次のコマンドを入力します。
   
     `Start-HcsUpdate`
   
    更新プロセスが開始します。

> [!IMPORTANT]
> * このコマンドは通常の更新プログラムのみに適用されます。 このコマンドは 1 つのコントローラーでのみ実行しますが、両方のコントローラーが更新されます。 
> * 更新プロセス中にコントローラーのフェールオーバーが行われても、システムの可用性または処理には影響しません。
> 
> 

