---
title: プログラムによる Azure ダッシュボードの作成
description: Azure portal のダッシュボードをテンプレートとして使用して、プログラムで Azure ダッシュボードを作成することができます。 JSON 参照を含めます。
ms.topic: how-to
ms.date: 12/4/2020
ms.openlocfilehash: bd56dc1c729c5aa7a77e79aa3af3366166fdcfea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095175"
---
# <a name="programmatically-create-azure-dashboards"></a>プログラムによる Azure ダッシュボードの作成

この記事では、Azure ダッシュボードをプログラムで作成し、公開する手順について説明します。 ドキュメント全体で、次のダッシュボードを参照します。

![サンプルのダッシュボード](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>概要

[Azure portal](https://portal.azure.com)内の共有ダッシュボードは、仮想マシンやストレージ アカウントと同様に[リソース](../azure-resource-manager/management/overview.md)です。 [Azure Resource Manager REST API](/rest/api/)、[Azure CLI](/cli/azure)、[Azure PowerShell コマンド](/powershell/azure/get-started-azureps)を使用して、プログラムによってリソースを管理できます。

多くの機能がこれらの API でビルドされ、リソース管理を容易にします。 これらの API とツールを使用することにより、リソースの作成、一覧表示、取得、変更、および削除を実行できます。 ダッシュボードはリソースであるため、使用する API やツールを任意に選択できます。

どちらのツールを使用する場合でも、プログラムでダッシュボードを作成するには、ダッシュボード オブジェクトの JSON 表現を構築します。 このオブジェクトには、ダッシュボード上のタイルに関する情報が含まれています。 これには、サイズ、位置、バインドされているリソースとユーザーによるカスタマイズ設定が含まれます。

この JSON ドキュメントを構築する最も実用的な方法は、Azure portal を使用することです。 タイルを対話的に追加して配置することができます。 次に、JSON をエクスポートして、スクリプト、プログラム、およびデプロイツールで後で使用できるテンプレートを結果から作成します。

## <a name="create-a-dashboard"></a>ダッシュボードを作成する

ダッシュボードを作成するには、[Azure portal](https://portal.azure.com) メニューから **ダッシュボード** を選択してから、**新しいダッシュボード** を選択します。

![[新しいダッシュボード] コマンド](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

タイルのギャラリーを使用してタイルを検索し追加します。 タイルは、ドラッグ アンド ドロップにより追加できます。 一部のタイルでは、ドラッグハンドルを使用したサイズ変更がサポートされています。

![ハンドルをドラッグしてサイズを変更](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

コンテキスト メニューでは、他のユーザーが選択できるサイズが固定されています。

![サイズを変更するためのコンテキストメニューのサイズを変更する](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>ダッシュボードの共有

ダッシュボードを構成した後の次のステップは、 **[共有]** コマンドを使用してダッシュボードを発行することです。

![ダッシュボードの共有](./media/azure-portal-dashboards-create-programmatically/share-command.png)

**共有** を選択すると、発行先のサブスクリプションとリソースグループを選択するように求められます。 選択するサブスクリプションとリソース グループに書き込みアクセス権がなければなりません。 詳細については、[Azure portal を使用して Azure ロールを割り当てる方法](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

![共有とアクセスに変更を加える](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>ダッシュボードの JSON 表現をフェッチします。

公開はわずか数秒で完了します。 完了したら、次に、 **[ダウンロード]** コマンドを使用して JSON をフェッチします。

![JSON 表現をダウンロードする](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>JSON からテンプレートを作成する

次の手順では、この JSON からテンプレートを作成します。 適切なリソース管理 API、コマンド ライン ツール、またはポータル内からプログラムを使用してそのテンプレートを使用します。

テンプレートを作成するために、ダッシュボードの JSON 構造の詳細を解釈する必要はありません。 ほとんどの場合、各タイルの構造と構成を保持する必要があります。 次に、タイルが指す Azure リソースのセットをパラメーター化します。 エクスポートされた JSON ダッシュボードを参照し、使用されているすべての Azure リソース ID を確認します。 この例のダッシュボードでは、複数のタイルが単一の Azure 仮想マシンを参照しています。 これは、このダッシュボードで単一のリソースのみを参照しているためです。 サンプル JSON (巻末に添付) で "/subscriptions" を検索すると、この ID が数箇所見つかります。

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

このダッシュボードを任意の仮想マシン用に後で発行する際には、JSON 内のこの文字列すべてをパラメーター化します。

Azure でリソースを作成する API には、次の 2 種類があります。

* 命令型 API は、一度に 1 つのリソースを作成します。 詳しくは、[リソース](/rest/api/resources/resources)に関する記事をご覧ください。
* 1 つの API 呼び出しで複数の依存リソースを作成する、テンプレートベースのデプロイシステムです。 詳細については、「[Deploy resources with Resource Manager templates and Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)」 (Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ) を参照してください。

テンプレートベースのデプロイでは、パラメーター化とテンプレート化がサポートされています。 この記事では、この方法を使用します。

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>テンプレート デプロイを使用して、テンプレートからプログラムによってダッシュボードを作成します。

Azure では、複数のリソースの配置を調整するための機能が提供されています。 デプロイするリソースを表し、その間の関係を表すデプロイメント テンプレートを作成します。  各リソースの JSON 形式は、1 つずつ作成した場合と同じです。 違いは、テンプレート言語では、変数、パラメーター、基本的な機能などのいくつかの概念が追加されている点です。 この拡張構文は、テンプレート デプロイメントのコンテキストでのみサポートされます。 前述の命令型 API で使用すると機能しません。 詳細については、[ Azure Resource Manager テンプレートの構造と構文を理解する](../azure-resource-manager/templates/template-syntax.md)を参照してください。

パラメーター化は、テンプレートのパラメーター構文を使用して実行する必要があります。  ここに示すように、以前に見つけたリソース ID のすべてのインスタンスを置き換えます。

ハードコードされたリソース ID を持つ JSON プロパティの例：

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

テンプレート パラメーターに基づいてパラメーター化されたバージョンに変換されたサンプルの JSON プロパティ

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

JSON テンプレートの上部で、次のように必要なテンプレートメタデータとパラメーターを宣言します。

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
テンプレートを構成したら、次のいずれかの方法を使用してデプロイします。

* [REST API](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Azure portal でのテンプレートのデプロイページ](https://portal.azure.com/#create/Microsoft.Template)

次に、2 つのバージョンのサンプル ダッシュボード JSON があります。 1 つ目は、既にリソースにバインドされている、ポータルからエクスポートされたバージョンです。 2 番目は、プログラムで任意の仮想マシンにバインドし、Azure Resource Manager を使用してデプロイできるテンプレートバージョンです。

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>テンプレートを作成する前のサンプルダッシュボードの JSON 表現

この例は、この記事を読んだ後に何を期待できるかを示しています。 指示は、すでにデプロイされているダッシュボードの JSON 表現をエクスポートしました。 このダッシュボードが特定の Azure 仮想マシンを指していることを示すハードコードされたリソース識別子です。

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

テンプレートバージョンのダッシュボードでは、`virtualMachineName`、`virtualMachineResourceGroup`、および`dashboardName`という 3 つのパラメーターが定義されています。  このパラメーターを使用すると、デプロイするたびに、ダッシュボードが異なる Azure 仮想マシンを参照するよう設定できます。 このダッシュボードは、任意の Azure 仮想マシンを指すようにプログラムで構成および展開できます。 この機能をテストするには、次のテンプレートをコピーして、[Azure portal テンプレートのデプロイメント ページ](https://portal.azure.com/#create/Microsoft.Template)に貼り付けます。

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

パラメーター化されたテンプレートを使用してダッシュボードをデプロイする例を確認したので、[Azure Resource Manager REST API](/rest/api/)、[Azure CLI](/cli/azure)、または [Azure PowerShell コマンド](/powershell/azure/get-started-azureps)を使用して、テンプレートをデプロイしてみることができます。

## <a name="programmatically-create-a-dashboard-by-using-azure-cli"></a>Azure CLI を使用してダッシュボードをプログラムで作成する

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- これらの例ではダッシュボード [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json) を使用します。 山かっこ内の内容を実際の値に置き換えます。

[az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) コマンドを実行して、ダッシュボードを作成します。

```azurecli
az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

ダッシュボードを更新するには、[az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) コマンドを使用します。

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
--input-path portal-dashboard-template-testvm.json --location centralus
```

[az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) コマンドを実行して、ダッシュボードの詳細を確認します。

```azurecli
az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
```

現在のサブスクリプションのすべてのダッシュボードを表示するには、[az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list) を使用します。

```azurecli
az portal dashboard list
```

リソース グループのすべてのダッシュボードを表示することもできます。

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

## <a name="next-steps"></a>次のステップ

デスクトップの詳細については、「[Azure portal の設定を管理する](set-preferences.md)」を参照してください。

ダッシュボードの Azure CLI サポートの詳細については、「[az portal dashboard](/cli/azure/ext/portal/portal/dashboard)」を参照してください。
