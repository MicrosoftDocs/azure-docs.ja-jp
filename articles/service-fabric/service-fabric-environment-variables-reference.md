---
title: Azure Service Fabric の環境変数
description: Azure Service Fabric の環境変数について説明します。 変数とその使用法の完全な一覧のリファレンスが含まれています。
ms.topic: reference
ms.date: 12/07/2017
ms.openlocfilehash: b70249daa439b5a631b5a84b10c47f082ce75985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574583"
---
# <a name="service-fabric-environment-variables"></a>Service Fabric の環境変数

Service Fabric には、サービス インスタンスごとに設定される組み込み環境変数があります。 以下ではすべての環境変数を示します。

| 環境変数                         | 説明                                                            | 例                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | アプリケーションのファブリック URI 名です                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | プロセスが属しているコード パッケージの名前です              | コード                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | エンドポイントの IP アドレスまたは FQDN です                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | エンドポイントのポート番号です                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | ログ フォルダーです                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | 一時フォルダーです                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | 作業フォルダーです                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | アプリケーションのホーム フォルダーです                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | プロセスがコンテナーかどうかを指定するブール値です                   | false                                                                |
| Fabric_NodeId                                | プロセスを実行しているノードのノード ID です                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | クラスターのマニフェスト ファイルで指定されている、ノードの IP アドレスまたは FQDN です | localhost または 10.0.0.1                                                |
| Fabric_NodeName                              | プロセスを実行しているノードのノード名です                          | _Node_0                                                              |
| Fabric_ServiceName                           | サービスが ExclusiveProcess モードでホストされている場合の、サービスのファブリック URI 名です。 この変数の値は、ServicePackageActivationMode ExclusiveProcess を使用してサービスを作成する場合にのみ使用できます。  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | ServicePackageActivationId です                                         | GUID                                                               |
| Fabric_ServicePackageName                    | プロセスがその一部であるサービス パッケージの名前です                     | Web1Pkg                                                              |

Service Fabric ランタイムによって使われる内部環境変数:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
