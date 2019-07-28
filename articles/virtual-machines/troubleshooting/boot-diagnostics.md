---
title: Azure での VM のブート診断 | Microsoft Docs
description: Azure 内の仮想マシンに対する 2 つのデバッグ機能の概要
services: virtual-machines
author: deland-han
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: delhan
ms.openlocfilehash: 7796d24b88ccc531b8042bfadf9d87f0072a2994
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709862"
---
# <a name="how-to-use-boot-diagnostics-to-troubleshoot-virtual-machines-in-azure"></a>ブート診断を使用して、Azure 内の仮想マシンをトラブルシューティングする方法

仮想マシンは、さまざまな理由で起動できない状態になる可能性があります。 Resource Manager デプロイ モデルを使用して作成した仮想マシンの問題に対処するために、Azure 仮想マシンに対してサポートされているコンソール出力とスクリーン ショットというデバッグ機能を使用できます。 

Linux 仮想マシンについては、コンソール ログの出力をポータルから表示できます。 Azure では、Windows 仮想マシンと Linux 仮想マシンの両方に関して、ハイパーバイザーからの VM のスクリーンショットを表示できます。 どちらの機能も、すべてのリージョンの Azure Virtual Machines でサポートされています。 スクリーンショットと出力結果がストレージ アカウントに表示されるまでに最大 10 分かかる場合があります。

**[ブート診断]** オプションを選択すると、ログとスクリーンショットを表示できます。

![リソース マネージャー](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)

## <a name="common-boot-errors"></a>一般的な起動エラー

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [An operating system wasn't found (オペレーティング システムが見つかりませんでした)](https://support.microsoft.com/help/4010142)
- [Boot failure or INACCESSIBLE_BOOT_DEVICE (起動エラーまたは INACCESSIBLE_BOOT_DEVICE)](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-virtual-machine-created-using-the-azure-portal"></a>Azure Portal を使用して作成された仮想マシンの診断を有効にする

次の手順は、Resource Manager デプロイ モデルを使用して作成された Azure 仮想マシン向けです。

**[管理]** タブの **[監視]** セクションで、 **[ブート診断]** がオンになっていることを確認します。 **[診断ストレージ アカウント]** ドロップダウン リストから、診断ファイルの配置先となるストレージ アカウントを選択します。
 
![VM を作成する](./media/virtual-machines-common-boot-diagnostics/enable-boot-diagnostics-vm.png)

> [!NOTE]
> ブート診断機能では、Premium ストレージ アカウントはサポートされていません。 ブート診断に Premium ストレージ アカウントを使用すると、VM の起動時に StorageAccountTypeNotSupported エラーが表示されることがあります。
>

### <a name="deploying-from-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートからのデプロイ

Azure Resource Manager テンプレートからデプロイする場合は、ご利用の仮想マシン リソースに移動し、診断プロファイル セクションを追加します。 API バージョン ヘッダーを "2015-06-15" またはそれ以降に設定します。 最新バージョンは "2018-10-01" です。

```json
{
  "apiVersion": "2018-10-01",
  "type": "Microsoft.Compute/virtualMachines",
  … 
```

これらのログの格納先となるストレージ アカウントは、診断プロファイルによって選択することができます。

```json
    "diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[concat('https://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
    }
    }
    }
}
```

テンプレートを使用したリソースのデプロイの詳細については、「[クイック スタート:Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)」を参照してください。

## <a name="enable-boot-diagnostics-on-existing-virtual-machine"></a>既存の仮想マシンでブート診断を有効にする 

既存の仮想マシンでブート診断を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、仮想マシンを選択します。
2. **[サポート + トラブルシューティング]** セクションで **[ブート診断]** を選択し、 **[設定]** タブを選択します。
3. **[ブート診断]** 設定で、状態を **[オン]** に変更し、 **[ストレージ アカウント]** ドロップダウン リストからストレージ アカウントを選択します。 
4. 変更を保存します。

    ![既存の VM の更新](./media/virtual-machines-common-boot-diagnostics/enable-for-existing-vm.png)

変更を有効にするには、仮想マシンを再起動する必要があります。

### <a name="enable-boot-diagnostics-using-the-azure-cli"></a>Azure CLI を使用してブート診断を有効にする

Azure CLI を使用して、既存の Azure 仮想マシンでブート診断を有効にすることができます。 詳細については、[az vm boot-diagnostics](
https://docs.microsoft.com/cli/azure/vm/boot-diagnostics?view=azure-cli-latest) を参照してください。
