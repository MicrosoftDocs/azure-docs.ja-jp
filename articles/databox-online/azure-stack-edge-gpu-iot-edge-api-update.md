---
title: Azure Stack Edge の 2021 年 1 月更新プログラムの影響 |Microsoft Docs
description: この記事では、2021 年 1 月更新プログラムのインストール後、Azure Stack Edge デバイスに IoT Edge ロール管理が及ぼす影響について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94578741"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Azure Stack Edge デバイスの IoT Edge ロール管理の変更

Azure Stack Edge デバイスの Azure IoT Edge ロール管理には、2021 年 1 月にリリースが予定されている API、SDK、Azure PowerShell の最新バージョンを使用します。 

この記事では、この最新バージョンを使用するときに行う必要がある変更について詳しく説明します。

2021 年 1 月更新プログラムは、Azure Stack Edge Pro - GPU、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスでのみ使用できます。 この記事の情報は、これらのデバイスにのみ適用されます。

> [!NOTE]
> 2021 年 1 月バージョンへのアップグレードは必須ではありません。 現在のバージョンを引き続き使用する場合、IoT Edge ロール管理への影響はありません。 ただし、新機能を利用し、セキュリティ リスクを軽減するために、新しいバージョンをインストールすることをお勧めします。 

## <a name="iot-edge-role-management-changes"></a>IoT Edge ロール管理の変更点

Azure Stack Edge デバイスにオプションの 2021 年 1 月更新プログラムをインストールしたら、IoT Edge ロール管理に最新バージョンの API、SDK、PowerShell コマンドレットを使用する必要があります。

次の変更は、2021 年 1 月更新プログラムを適用した場合にのみ必要です。

- 現在、Role Management API バージョン&nbsp;2019-08-01 を使用している場合は、2021 年 1 月にリリース予定の API バージョンにアップグレードしてください。 
- 現在、SDK バージョン&nbsp;1.0.0 を介してロール管理を使用している場合は、2021 年 1 月にリリース予定のバージョンにアップグレードしてください。
- `Get-AzStackEdgeRole`、`New-AzStackEdgeRole`、`Set-AzStackEdgeRole`、`Remove-AzStackEdgeRole` などの Azure PowerShell コマンドレット (プレビュー) でロール管理を使用している場合は、2021 年 2 月に新しいコマンドレットがリリースされるまでお待ちください。

## <a name="api-usage"></a>API の使用

現在、API を使用して IoT Edge ロール管理を実行している場合は、今後公開される新しい API バージョン 2020-12-01 を使用する必要があります。 現在の Role API を使用している場合は、次期デバイス ソフトウェア バージョンをインストールした後、PUT、GET、または DELETE Kubernetes ロールに移行してから、PUT IoT アドオン API に移行する必要があります。

### <a name="for-the-put-method"></a>PUT メソッドの場合

#### <a name="the-current-http-request"></a>現在の HTTP 要求 

- API 呼び出しは、次の URI で行われます: "https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01"

- 要求本文はこのようになります。

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>今後の HTTP 要求 

- Kubernetes ロールの API 呼び出しは、次の URI で行われます。 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    要求本文は次のようになります。

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- IoT Edge アドオンの API 呼び出しは、次の URI で行われます。 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    要求本文は次のようになります。

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>GET メソッドの場合

#### <a name="the-current-http-response"></a>現在の HTTP 応答

- API 呼び出しは、次の URI で行われます: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- 応答本文はこのようになります。

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>今後の HTTP 応答

- API 呼び出しは、次の URI で行われます:  

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- 応答本文はこのようになります。 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>DELETE メソッドの場合

### <a name="the-current-api-calls"></a>現在の API 呼び出し

API 呼び出しは、次の URI で行われます:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>今後の API 呼び出し

API 呼び出しは、次の URI で行われます:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK の使用

SDK を使用している場合は、2021 年 1 月更新プログラムをインストールした後、次のサンプルに示すように IoT Edge ロールの設定方法を変更する必要があります。 その後、ここで示すように、近日リリースの NuGet パッケージをダウンロードしてインストールし、新しい SDK に移行します。

### <a name="the-current-sdk-sample"></a>現在の SDK のサンプル

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>新しい SDK のサンプル

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>コマンドレットの使用

`Get-AzStackEdgeRole`、`New-AzStackEdgeRole`、`Set-AzStackEdgeRole`、または `Remove-AzStackEdgeRole` コマンドレットを現在使用している場合は、2021 年 2 月のリリースが予定されている新しいバージョンを待つ必要があります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

**Azure Stack Edge Pro - FPGA を使用しています。2021 年 1 月更新プログラムは、FPGA モデルに影響しますか?**

いいえ。 2021 年 1 月更新プログラムは、Azure Stack Edge Pro - FPGA、Azure Stack Edge Pro R、および Azure Stack Edge Mini R デバイスにのみ適用されます。 Azure Stack Edge Pro - FPGA はこの更新プログラムの影響を受けないため、IoT Edge ロール管理を変更する必要はありません。

**2021 年 1 月に Azure Stack Edge Pro - GPU を新しいデバイス ソフトウェアに更新した後、影響を受ける既存のサービスはありますか?**

いいえ。 2021 年 1 月デバイス更新プログラムをインストールしても、構成済みのサービスは影響を受けません。

**IoT Edge 管理 API、SDK、またはコマンドレットの変更点は大まかにどのようなものですか?**

IoT Edge は、Kubernetes ロールに基づくアドオンです。これは、Kubernetes が構成されていることをまず確認してから、IoT Edge 構成を実行する必要があることを意味します。


## <a name="next-steps"></a>次のステップ

- [更新プログラムを適用する方法を確認する](azure-stack-edge-gpu-install-update.md)

