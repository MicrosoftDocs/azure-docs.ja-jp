---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 3312d1ec7c2535e103cf8959599c0d4c3014f520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86218065"
---
1. [PowerShell インターフェイスに接続する](#connect-to-the-powershell-interface)。
2. `Get-HcsApplianceInfo` を使用してデバイスの情報を取得します。

    このコマンドレットの使用例を次に示します。

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    こちらの重要なデバイス情報のいくつかをまとめた表をご覧ください。

    | パラメーター | 説明 |
    |-----------|-------------|
    | FriendlyName                   | ローカル Web UI でデバイスのデプロイ中に構成されるデバイスのフレンドリ名。 既定のフレンドリ名は、デバイスのシリアル番号です。  |
    | SerialNumber                   | デバイスのシリアル番号は、出荷時に割り当てられる一意の数です。                                                                             |
    | モデル                          | Azure Stack Edge または Data Box Gateway デバイスのモデルです。 モデルは、Azure Stack Edge の場合は物理で、Data Box Gateway の場合は仮想です。                   |
    | FriendlySoftwareVersion        | デバイス ソフトウェアのバージョンに対応するフレンドリ文字列。 プレビューを実行しているシステムの場合、ソフトウェアのフレンドリ バージョンは Data Box Edge 1902 です。 |
    | HcsVersion                     | デバイスで実行されている HCS ソフトウェアのバージョン。 たとえば、Data Box Edge 1902 に対応する HCS ソフトウェアのバージョンは 1.4.771.324 です。            |
    | LocalCapacityInMb              | デバイスの合計容量 (メガビット単位)。                                                                                                        |
    | IsRegistered                   | この値は、デバイスがサービスでアクティブ化されるかどうかを示します。                                                                                         |


