---
title: Windows AWS EC2 インスタンスを Azure に移動する
description: アマゾン ウェブ サービス (AWS) EC2 Windows インスタンスを Azure 仮想マシンに移行します。
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 86d3e67b121bf7d66651e1e823e5f137e918a171
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550792"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Windows VM をアマゾン ウェブ サービス (AWS) から Azure 仮想マシンに移行する

ワークロードのホストについて Azure 仮想マシンを評価する場合、既存のアマゾン ウェブ サービス (AWS) EC2 Windows VM インスタンスをエクスポートしてから、仮想ハード ディスク (VHD) を Azure にアップロードできます。 VHD のアップロード後、その VHD から Azure に新しい VM を作成できます。 

この記事では、AWS から Azure に 1 つの VM を移行する方法について説明します。 規模を拡大して AWS から Azure に複数の VM を移行したい場合は、「[Azure Site Recovery を使用してアマゾン ウェブ サービス (AWS) から Azure に仮想マシンを移行する](../../site-recovery/migrate-tutorial-aws-azure.md)」を参照してください。

## <a name="prepare-the-vm"></a>VM を準備する 
 
一般化された VHD と特殊化された VHD のいずれも Azure にアップロードできます。 どちらのタイプも、AWS からエクスポートする前に VM を準備する必要があります。 

- **一般化された VHD** - 一般化した VHD では、Sysprep を使用して個人アカウント情報がすべて削除されています。 新しい VM を作成するためのイメージとして VHD を使用する場合、以下を行う必要があります。 
 
    * [Windows VM の準備](prepare-for-upload-vhd-image.md)。  
    * Sysprep を使用した仮想マシンの一般化。  

 
- **特殊化された VHD** - 特殊化された VHD では、ユーザーアカウント、アプリケーション、その他のステート データが元の VM から保持されます。 新しい VM を作成するために VHD を使用する場合、以下のステップが完了していることを確認します。  
    * [Windows VHD の Azure へのアップロードの準備](prepare-for-upload-vhd-image.md)。 Sysprep を使用して VM を一般化 **しないでください**。 
    * VM にインストールされたゲストの仮想化ツールやエージェント (VMware tools) の削除。 
    * IP アドレスと DNS 設定を DHCP で取得するよう VM が構成されていることを確認。 これにより、サーバーが起動時に VNet 内の IP アドレスを取得します。  


## <a name="export-and-download-the-vhd"></a>VHD をエクスポートおよびダウンロードする 

Amazon S3 バケット内の VHD に EC2 インスタンスをエクスポートします。 Amazon のドキュメント記事「[Exporting an Instance as a VM Using VM Import/Export (VM Import/Export を使用して VM としてインスタンスをエクスポート)](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html)」の手順に従って [create-instance-export-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) コマンドを実行し、EC2 インスタンスを VHD ファイルにエクスポートします。 

エクスポートされた VHD ファイルは、指定した Amazon S3 バケットに保存されます。 VHD をエクスポートするための基本的な構文を次に示します。\<brackets> 内のプレースホルダー テキストを実際の情報に置き換えるだけです。

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

VHD のエクスポートが完了したら、「[How Do I Download an Object from an S3 Bucket? (S3 バケットからオブジェクトをダウンロードする方法)](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html)」の手順に従って、S3 バケットから VHD ファイルをダウンロードします。 

> [!IMPORTANT]
> VHD をダウンロードする場合、AWS からデータ転送料金が請求されます。 詳細については、「[Amazon S3 Pricing (Amazon S3 の料金)](https://aws.amazon.com/s3/pricing/)」を参照してください。


## <a name="next-steps"></a>次のステップ

これで、VHD を Azure にアップロードして新しい VM を作成できるようになりました。 

- エクスポート前に Sysprep を実行してソースを **一般化** した場合、[一般化した VHD のアップロードとそれを使用した Azure の新しい VM の作成](upload-generalized-managed.md)に関するページを参照してください。
- エクスポート前に Sysprep を実行しなかった場合、VHD は **特殊化された** と見なされます。その場合、[特殊化した VHD を Azure にアップロードして新しい VM を作成する方法](create-vm-specialized.md)に関するページを参照してください。

 
