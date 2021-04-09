---
title: Azure のブート診断
description: Azure のブート診断とマネージ ブート診断の概要
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 030f19d61e19349de7e2d3416a3aee1385fd03c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607524"
---
# <a name="azure-boot-diagnostics"></a>Azure のブート診断

ブート診断は、VM ブート エラーの診断を可能にする、Azure の仮想マシン (VM) のデバッグ機能です。 ブート診断を使用すると、ユーザーは、シリアル ログ情報とスクリーンショットを収集して、起動中の VM の状態を確認できます。

## <a name="boot-diagnostics-storage-account"></a>ブート診断ストレージ アカウント
Azure portal で VM を作成するとき、ブート診断は既定で有効になっています。 推奨されるブート診断エクスペリエンスでは、マネージド ストレージ アカウントを使用します。これにより、Azure VM の作成にかかる時間の面で、パフォーマンスが大幅に向上します。 これは、Azure が管理するストレージ アカウントが使用されることで、ブート診断データの保存用に新しいユーザー ストレージ アカウントを作成するのにかかる時間が不要になるためです。

> [!IMPORTANT]
> ブート診断データの BLOB (ログとスナップショット イメージで構成されます) は、マネージド ストレージ アカウントに格納されます。 お客様は、ディスクのプロビジョニング サイズではなく、BLOB で使用された Gib に対してのみ課金されます。 スナップショット メーターは、マネージド ストレージ アカウントの課金に使用されます。 管理対象のアカウントは Standard LRS と Standard ZRS のどちらかで作成されるため、お客様の診断データ BLOB のサイズに対して 1 か月あたり $ 0.05/GB のみが請求されます。 価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。 お客様には、VM リソース URI に関連付けられているこの料金が表示されます。 

別のブート診断エクスペリエンスでは、ユーザー管理のストレージ アカウントを使用します。 ユーザーは、新しいストレージ アカウントを作成することも、既存のものを使用することもできます。
> [!NOTE]
> ブート診断に関連付けられたユーザー管理のストレージ アカウントでは、ストレージ アカウントおよび関連付けられている仮想マシンが同じサブスクリプションに存在する必要があります。 



## <a name="boot-diagnostics-view"></a>ブート診断のビュー
仮想マシンのブレードにある [ブート診断] オプションは、Azure portal の *[サポートとトラブルシューティング]* セクションにあります。 [ブート診断] を選択すると、スクリーンショットとシリアル ログ情報が表示されます。 シリアル ログには、カーネル メッセージングが含まれています。スクリーンショットは、VM の現在の状態のスナップショットです。 VM で Windows と Linux のどちらが実行されているかによって、どのような外観のスクリーンショットが予想されるかが決まります。 Windows の場合、ユーザーにはデスクトップの背景が表示され、Linux の場合はログイン プロンプトが表示されます。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux のブート診断のスクリーンショット":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows のブート診断のスクリーンショット":::

## <a name="enable-managed-boot-diagnostics"></a>マネージド ブート診断を有効にする 
マネージド ブート診断は、Azure portal、CLI、および ARM テンプレートを使用して有効にすることができます。 PowerShell を使用した有効化は、まだサポートされていません。 

### <a name="enable-managed-boot-diagnostics-using-the-azure-portal"></a>Azure portal を使用してマネージド ブート診断を有効にする
Azure portal で VM を作成する場合、既定の設定では、マネージド ストレージ アカウントを使用してブート診断を有効にします。 これを表示するには、VM の作成中に *[管理]* タブに移動します。 

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-enable-portal.png" alt-text="VM の作成中にマネージド ブート診断を有効にしているスクリーンショット。":::

### <a name="enable-managed-boot-diagnostics-using-cli"></a>CLI を使用してマネージド ブート診断を有効にする
マネージド ストレージ アカウントでのブート診断は、Azure CLI 2.12.0 以降でサポートされています。 ストレージ アカウントで名前または URI を入力しない場合は、マネージド アカウントが使用されます。 詳細とコード サンプルについては、[ブート診断に関する CLI ドキュメント](/cli/azure/vm/boot-diagnostics)に関する記事を参照してください。

### <a name="enable-managed-boot-diagnostics-using-azure-resource-manager-arm-templates"></a>Azure Resource Manager (ARM) テンプレートを使用してマネージド ブート診断を有効にする
API バージョン 2020-06-01 以降はすべて、マネージド ブート診断をサポートしています。 詳細については、[ブート診断インスタンス ビュー](/rest/api/compute/virtualmachines/createorupdate#bootdiagnostics)に関する記事を参照してください。

```ARM Template
            "name": "[parameters('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2020-06-01",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[parameters('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[parameters('virtualMachineComputerName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "linuxConfiguration": {
                        "disablePasswordAuthentication": true
                    }
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true
                    }
                }
            }
        }
    ],

```

## <a name="limitations"></a>制限事項
- ブート診断は、Azure Resource Manager VM でのみ使用できます。
- マネージド ブート診断は、アンマネージド OS ディスクを使用する VM をサポートしていません。
- ブート診断は、Premium Storage アカウントをサポートしていません。ブート診断に Premium Storage アカウントを使用すると、ユーザーは VM の起動時に `StorageAccountTypeNotSupported` エラーを受け取ります。 
- マネージド ストレージ アカウントは、Resource Manager API バージョン "2020-06-01" 以降でサポートされています。
- Azure シリアル コンソールは現在、ブート診断用のマネージド ストレージ アカウントとの互換性はありません。 [Azure シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-overview)についてさらに詳しく学習します。
- ポータルは、単一インスタンス VM のマネージド ストレージ アカウントでのブート診断の使用のみをサポートしています。

## <a name="next-steps"></a>次の手順

[Azure シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-overview)の詳細、およびブート診断を使用して [Azure の仮想マシンでトラブルシューティング](/troubleshoot/azure/virtual-machines/boot-diagnostics)を行う方法について確認します。