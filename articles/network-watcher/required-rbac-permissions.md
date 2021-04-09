---
title: 機能を使用するために必要な Azure RBAC のアクセス許可
titleSuffix: Azure Network Watcher
description: Network Watcher 機能を使用するために必要な、Azure ロールベースのアクセス制御のアクセス許可について説明します。
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 47418b9c5235255ff7dbf4a1a151e51e4c9aba58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019671"
---
# <a name="azure-role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>Network Watcher 機能を使用するために必要な、Azure ロールベースのアクセス制御のアクセス許可

Azure ロールベースのアクセス制御 (Azure RBAC) を使用すると、割り当てられた職務を遂行するために必要な特定のアクションのみを組織内のメンバーに割り当てることができます。 Network Watcher 機能を使用するには、Azure にログインするアカウントを、[所有者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner)、[共同作成者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor)、または[ネットワーク共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor)の組み込みのロールに割り当てるか、Network Watcher 機能の各セクションの下に一覧表示されているアクションが割り当てられている[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に割り当てる必要があります。 Network Watcher の機能の詳細については、「[Network Watcher とは](network-watcher-monitoring-overview.md)」を参照してください。

## <a name="network-watcher"></a>Network Watcher

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Network Watcher を取得する                                          |
| Microsoft.Network/networkWatchers/write                             | Network Watcher を作成する                             |
| Microsoft.Network/networkWatchers/delete                            | Network Watcher を削除する                                       |

## <a name="nsg-flow-logs"></a>NSG フロー ログ

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | フロー ログを構成する                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | フロー ログのクエリ状態                                    |

## <a name="connection-troubleshoot"></a>接続のトラブルシューティング

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | 接続のトラブルシューティング テストの開始
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | 接続のトラブルシューティング テストのクエリ結果                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | 接続のトラブルシューティング テストの実行                             |

## <a name="connection-monitor"></a>接続モニター

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | 接続モニターを起動する                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | 接続モニターを停止する                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | 接続モニターのクエリを実行する                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | 接続モニターを取得する                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | 接続モニターを作成する                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | 接続モニターを削除する                                    |

## <a name="packet-capture"></a>パケット キャプチャ

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | パケット キャプチャの状態のクエリを実行する                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | パケット キャプチャを停止する                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | パケット キャプチャを取得する                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | パケット キャプチャを作成する                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | パケット キャプチャを削除する                                        |

## <a name="ip-flow-verify"></a>IP フロー検証

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | IP フローを確認する                                              |

## <a name="next-hop"></a>次のホップ

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | VM から次ホップを取得する                                     |

## <a name="network-security-group-view"></a>ネットワーク セキュリティ グループ ビュー

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | セキュリティ グループを表示する                                           |

## <a name="topology"></a>トポロジ

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | トポロジを取得する                                                   |
| Microsoft.Network/networkWatchers/topology/read                     | 同上                                                  |

## <a name="reachability-report"></a>到達可能性レポート

| アクション                                                              | 説明                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Azure 到達可能性レポートを取得する                               |


## <a name="additional-actions"></a>追加のアクション

Network Watcher 機能には、次のアクションも必要です。

| アクション                                                           | 説明                                                    |
| ---------                                                           | -------------                                                  |
| Microsoft.Authorization/\*/Read                                     | Azure ロールの割り当てとポリシー定義のフェッチに使用されます          |
| Microsoft.Resources/subscriptions/resourceGroups/Read               | サブスクリプションのすべてのリソース グループの列挙に使用されます    |
| Microsoft.Storage/storageAccounts/Read                              | 指定したストレージ アカウントのプロパティの取得に使用されます   |
| Microsoft.Storage/storageAccounts/listServiceSas/Action、 </br> Microsoft.Storage/storageAccounts/listAccountSas/Action、 <br> Microsoft.Storage/storageAccounts/listKeys/Action| [ストレージ アカウントへのセキュリティで保護されたアクセス](../storage/common/storage-sas-overview.md)およびストレージ アカウントへの書き込みを有効にした、Shared Access Signature (SAS) のフェッチに使用されます |
| Microsoft.Compute/virtualMachines/Read、 </br> Microsoft.Compute/virtualMachines/Write| VM へのログイン、パケットの取得、そのストレージ アカウントへのアップロードに使用されます|
| Microsoft.Compute/virtualMachines/extensions/Read </br> Microsoft.Compute/virtualMachines/extensions/Write| Network Watcher 拡張機能が存在するかどうかの確認と必要に応じたインストールに使用されます |
| Microsoft.Compute/virtualMachineScaleSets/Read、 </br> Microsoft.Compute/virtualMachineScaleSets/Write| 仮想マシンのスケール セットへのアクセス、パケットの取得、およびそのストレージ アカウントへのアップロードに使用されます|
| Microsoft.Compute/virtualMachineScaleSets/extensions/Read、 </br> Microsoft.Compute/virtualMachineScaleSets/extensions/Write| Network Watcher 拡張機能が存在するかどうかの確認と必要に応じたインストールに使用されます |
| Microsoft.Insights/alertRules/*                                     | メトリック アラートの設定に使用されます                                     |
| Microsoft.Support/*                                                 | Network Watcher からのサポート チケットの作成および更新に使用されます |