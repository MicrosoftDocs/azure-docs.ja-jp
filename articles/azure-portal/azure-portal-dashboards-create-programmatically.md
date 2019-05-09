---
title: プログラムによる Azure ダッシュボードの作成 | Microsoft Docs
description: この記事では、プログラムで Azure ダッシュボードを作成する方法について説明します。
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: b24a0397a1365479907fedc6348caa54508dbbb0
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444873"
---
# <a name="programmatically-create-azure-dashboards"></a>プログラムによる Azure ダッシュボードの作成

このドキュメントでは、Azure ダッシュボードをプログラムで作成し、公開する手順について説明します。 ドキュメント全体で、次のダッシュボードを参照します。

![サンプルのダッシュボード](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>概要

Azure 内の共有ダッシュボードは、仮想マシンやストレージ アカウントと同様に[リソース](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)です。  このため、共有ダッシュボードは、[Azure Resource Manager REST API](/rest/api/)、または [Azure CLI](https://docs.microsoft.com/cli/azure)、[Azure PowerShell コマンド](https://docs.microsoft.com/powershell/azure/get-started-azureps)、およびこれらの API を使用して構築されたその他の多数の [Azure ポータル](https://portal.azure.com)機能を使用してプログラム的に管理でき、リソース管理を簡易化できます。  

これらの API とツールを使用することにより、リソースの作成、一覧表示、取得、変更、および削除を実行できます。  ダッシュボードはリソースであるため、使用する API やツールを任意に選択できます。

どのツールを使用するかにかかわらず、リソースの作成 API を呼び出す前に、ダッシュボード オブジェクトの JSON 表現を構築する必要があります。 このオブジェクトには、ダッシュボード上のパーツ (別名 "タイル") に関する情報が 含まれています。 これには、サイズ、位置、バインドされているリソースとユーザーによるカスタマイズ設定が含まれます。

この JSON ドキュメントを構築するための最も実用的な方法は、[ポータル](https://portal.azure.com/)での対話によりタイルを追加して、配置することです。 次に JSON をエクスポートします。 最後に、スクリプト、プログラム、および展開ツールで後で使用できるテンプレートを結果から作成します。

## <a name="create-a-dashboard"></a>ダッシュボードを作成する

新しいダッシュボードを作成するには、ポータルのメイン画面で [新しいダッシュボード] コマンドを使用します。

![[新しいダッシュボード] コマンド](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

次に、タイルのギャラリーを使用してタイルを検索し追加できます。 タイルは、ドラッグ アンド ドロップにより追加できます。 タイルの中には、ドラッグ ハンドルを使用してサイズ変更できるものもあり、コンテキスト メニューから固定サイズが設定できるものもあります。

### <a name="drag-handle"></a>ドラッグ ハンドル
![ドラッグ ハンドル](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

### <a name="fixed-sizes-via-context-menu"></a>コンテキスト メニューから設定した固定サイズ
![サイズ コンテキスト メニュー](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>ダッシュボードの共有

ダッシュボードを希望の設定に設定したら、ダッシュボードを公開 ([共有] コマンドを使用して) し、リソース エクスプローラーを使用して JSON をフェッチします。

![[共有] コマンド](./media/azure-portal-dashboards-create-programmatically/share-command.png)

[共有] コマンドをクリックすると、発行するサブスクリプションとリソース グループを選択するダイアログが表示されます。 選択したサブスクリプションとリソース グループに[書き込みアクセス権が必要](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)であることに注意してください。

![共有とアクセス](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>ダッシュボードの JSON 表現をフェッチします。

公開はわずか数秒で完了します。  完了したら、[リソース エクスプローラー](https://portal.azure.com/#blade/HubsExtension/ArmExplorerBlade)から JSON をフェッチします。

![リソース エクスプローラーからの参照](./media/azure-portal-dashboards-create-programmatically/browse-resource-explorer.png)

リソース エクスプローラーから、選択したサブスクリプションとリソース グループに移動します。 次に、新たに公開されたダッシュボード リソースをクリックして JSON を表示します。

![リソース エクスプローラーの JSON](./media/azure-portal-dashboards-create-programmatically/resource-explorer-json.png)

## <a name="create-a-template-from-the-json"></a>JSON からテンプレートを作成する

次に、適切なリソース管理 API、コマンド ライン ツール、またはポータル内からプログラムを使用して再利用できるように、この JSON からテンプレートを作成します。

テンプレートを作成するために、ダッシュボードの JSON 構造の詳細を理解する必要はありません。 ほぼすべての場合、各タイルの構造と構成を維持し、それらが参照している Azure のリソースのセットをパラメーター化することをお勧めします。 エクスポートされた JSON ダッシュボードを参照し、使用されているすべての Azure リソース ID を確認します。 この例のダッシュボードでは、複数のタイルが単一の Azure 仮想マシンを参照しています。 これは、このダッシュボードで単一のリソースのみを参照しているためです。 サンプル JSON (巻末に添付) で "/subscriptions" を検索すると、この ID が数箇所見つかります。

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

このダッシュボードを任意の仮想マシン用に後で発行する際には、JSON 内のこの文字列すべてをパラメーター化する必要があります。 

Azure でリソースを作成する API には、次の 2 種類があります。 一度に 1 つのリソースを作成する[命令型 API](https://docs.microsoft.com/rest/api/resources/resources) と、単一の API 呼び出しで依存する複数のリソースの作成を調整する[テンプレート ベースの展開](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) API があります。 後者は、パラメーター化とテンプレートをネイティブでサポートするため、この例で使用します。

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>テンプレート デプロイを使用して、テンプレートからプログラムによってダッシュボードを作成します。

Azure では、複数のリソースの配置を調整するための機能が提供されています。 展開するリソースを表し、その間の関係を表す展開テンプレートを作成します。  各リソースの JSON 形式は、1 つずつ作成した場合と同じです。 違いは、[テンプレート言語](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)では、変数、パラメーター、基本的な機能などのいくつかの概念が追加されている点です。 この拡張構文は、テンプレートのデプロイの文脈でのみサポートされ、前述の命令型 API では機能しません。

この方法を使用する場合は、テンプレートのパラメーター構文を使用してパラメーター化する必要があります。  次のように、上記で特定したリソース ID のすべてのインスタンスを置換します。

### <a name="example-json-property-with-hard-coded-resource-id"></a>ハード コーディングされたリソース ID を使用した JSON プロパティの例
`id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"`

### <a name="example-json-property-converted-to-a-parameterized-version-based-on-template-parameters"></a>テンプレート パラメーターに基づいてパラメーター化されたバージョンに変換されたサンプルの JSON プロパティ

`id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"`

また、必要なテンプレート メタデータとパラメーターを、次のように、JSON テンプレートの上部で宣言する必要があります。

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```

__このドキュメントの末尾に、実際に機能する、詳細なテンプレートがあります。__

テンプレートを作成したら、[REST API](https://docs.microsoft.com/rest/api/resources/deployments)、[PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)、[Azure CLI](https://docs.microsoft.com/cli/azure/group/deployment#az-group-deployment-create)、または[ポータルの [テンプレートのデプロイ] ページ](https://portal.azure.com/#create/Microsoft.Template)を使用してデプロイできます。

ここでは、2 つのバージョンのサンプル ダッシュボード JSON があります。 1 つ目は、既にリソースにバインドされている、ポータルからエクスポートされたバージョンです。 2 つ目は、すべての VM にプログラムを使用してバインドすることができ、Azure Resource Manager を使用して展開できるテンプレート バージョンです。

## <a name="json-representation-of-our-example-dashboard-before-templating"></a>サンプルのダッシュボードの JSON による表現 (テンプレート作成前)

前述の手順に従って、既にデプロイされているダッシュボードの JSON 表現をフェッチすると、このように表示されます。 このダッシュボードが特定の Azure 仮想マシンを参照していることを示すハードコーディングされたリソース識別子に注目してください。

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>サンプル ダッシュボードのテンプレートによる表現

テンプレート バージョンのダッシュボードには、__virtualMachineName__、__virtualMachineResourceGroup__、および __dashboardName__ の 3 つのパラメーターが定義されています。  このパラメーターを使用すると、デプロイするたびに、ダッシュボードが異なる Azure 仮想マシンを参照するよう設定できます。 パラメーター化された ID は、このダッシュボードがプログラムを使用して設定でき、任意の Azure 仮想マシンを参照するようデプロイできることを示すために強調表示されています。 この機能をテストする最も簡単な方法は、次のテンプレートをコピーして、[Azure ポータルの [テンプレートのデプロイ] ページ](https://portal.azure.com/#create/Microsoft.Template)に貼り付けることです。 

この例では、ダッシュボードが単独でデプロイされていますが、テンプレート言語を使用すれば、複数のリソースをデプロイし、1 つまたは複数のダッシュボードをバンドルできます。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}


```
