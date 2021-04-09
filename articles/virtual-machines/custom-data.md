---
title: カスタム データと Azure 仮想マシン
description: Azure 仮想マシンでのカスタム データと cloud-init の使用の詳細
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 2924caaac5fb8c512100d9e897f7f153af9a3b3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87284916"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Azure 仮想マシンでのカスタム データと cloud-init

プロビジョニング時にスクリプトまたはその他のメタデータを Microsoft Azure 仮想マシンに挿入する必要がある場合があります。  他のクラウドでは、この概念は多くの場合、ユーザー データと呼ばれます。  Microsoft Azure には、カスタム データと呼ばれる同様の機能があります。 

カスタム データは、最初の起動時または初期セットアップ時にのみ VM で使用可能になります。これを "プロビジョニング" と呼びます。 プロビジョニングとは、VM 作成のパラメーター (ホスト名、ユーザー名、パスワード、証明書、カスタムデータ、キーなど) が VM で使用可能になり、[Linux エージェント](./extensions/agent-linux.md)や [cloud-init](./linux/using-cloud-init.md#troubleshooting-cloud-init) などのプロビジョニング エージェントがそれらを処理するプロセスです。 


## <a name="passing-custom-data-to-the-vm"></a>カスタム データを VM に渡す
カスタム データを使用するには、変換を実行する CLI ツール (AZ CLI など) を使用していない限り、コンテンツを API に渡す前に、それを base64 でエンコードする必要があります。 そのサイズは 64 KB を超えることはできません。

CLI では、カスタム データをファイルとして渡すことができ、それは base64 に変換されます。
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

Azure Resource Manager (ARM) には、[base64 関数](../azure-resource-manager/templates/template-functions-string.md#base64)があります。

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>カスタム データを処理する
VM にインストールされているプロビジョニング エージェントは、プラットフォームとのインターフェイスを処理し、それをファイル システムに配置します。 

### <a name="windows"></a>Windows
カスタム データは、バイナリ ファイルとして *%SYSTEMDRIVE%\AzureData\CustomData.bin* に配置されますが、処理されません。 このファイルを処理する場合は、カスタム イメージをビルドし、CustomData.bin を処理するコードを記述する必要があります。

### <a name="linux"></a>Linux  
Linux OS の場合、カスタム データは ovf-env.xml ファイルを介して VM に渡され、プロビジョニング中に */var/lib/waagent* ディレクトリにコピーされます。  また、新しいバージョンの Microsoft Azure Linux エージェントも便宜上、base64 でエンコードされたデータを */var/lib/waagent/CustomData* にコピーします。

Azure では、現在、次の 2 つのプロビジョニング エージェントがサポートされています。
* Linux エージェント - 既定では、このエージェントはカスタム データを処理しないため、それが有効になったカスタム イメージをビルドする必要があります。 [こちらのドキュメント](https://github.com/Azure/WALinuxAgent#configuration)に従うと、関連する設定は次のようになります。
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

カスタム データを有効にして、スクリプトを実行すると、そのスクリプトが完了するまで、準備ができたことやプロビジョニングが成功したことを通知する VM の報告が遅延されます。 スクリプトが VM プロビジョニングの合計許容時間である 40 分を超えると、VM の作成は失敗します。 スクリプトの実行が失敗した場合、または実行中にエラーが発生した場合、致命的なプロビジョニング エラーとは見なされないため、スクリプトの完了状態について警告する通知パスを作成する必要があります。

カスタム データの実行をトラブルシューティングするには、 */var/log/waagent.log* を確認してください。

* cloud-init - 既定では、cloud-init はカスタム データを処理し、cloud-init 構成、スクリプトなど、[複数の形式](https://cloudinit.readthedocs.io/en/latest/topics/format.html)のカスタム データを受け入れます。cloud-init は、Linux エージェントと同様にカスタム データを処理します。 構成処理またはスクリプトの実行中にエラーが発生した場合、致命的なプロビジョニング エラーとは見なされないため、スクリプトの完了状態について警告する通知パスを作成する必要があります。 ただし、cloud init は、Linux エージェントとは異なり、ユーザーのカスタム データの構成が完了するのを待たずに、VM の準備ができたことをプラットフォームに報告します。 Azure での cloud-init の詳細については、[こちらのドキュメント](./linux/using-cloud-init.md)を参照してください。


カスタム データの実行のトラブルシューティングについては、トラブルシューティングに関する[こちらのドキュメント](./linux/using-cloud-init.md#troubleshooting-cloud-init)を参照してください。


## <a name="faq"></a>よく寄せられる質問
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>VM の作成後にカスタム データを更新できますか?
単一の VM の場合、VM モデルのカスタム データは更新できませんが、VMSS の場合、[REST API](/rest/api/compute/virtualmachinescalesets/update) を使用して VMSS カスタム データを更新できます (PS または AZ CLI クライアントには適用されません)。 VMSS モデルのカスタム データを更新すると、次のようになります。
* VMSS の既存のインスタンスは、再イメージ化されるまで、更新されたカスタム データを取得しません。
* VMSS のアップグレードされた既存のインスタンスは、更新されたカスタム データを取得しません。
* 新しいインスタンスは、新しいカスタム データを受け取ります。

### <a name="can-i-place-sensitive-values-in-custom-data"></a>カスタム データに機密性の高い値を配置できますか?
機密性の高いデータはカスタム データに格納 **しない** ことをお勧めします。 詳細については、[Azure のセキュリティと暗号化のベスト プラクティス](../security/fundamentals/data-encryption-best-practices.md)に関する記事を参照してください。


### <a name="is-custom-data-made-available-in-imds"></a>カスタム データは IMDS で使用できますか?
いいえ、この機能は現在使用できません。
