---
title: Azure Stack Edge Pro デバイスの障害から回復する
description: Azure Stack Edge Pro で障害が発生したデバイスから回復する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: b1bfbda007619bf5bd94d47297845881758037bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636642"
---
# <a name="recover-from-a-failed-azure-stack-edge-pro-gpu-device"></a>障害が発生した Azure Stack Edge Pro GPU デバイスから回復する 

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスでの許容できない障害から回復する方法について説明します。 Azure Stack Edge Pro GPU デバイスで許容できない障害が発生した場合は、デバイスの交換が必要です。

## <a name="before-you-begin"></a>開始する前に

次のソリューションがあることを確認します。

- デバイスの障害に関して Microsoft サポートに連絡し、デバイスの交換を推奨されていること。 
- [デバイスの障害に対する準備](azure-stack-edge-gpu-prepare-device-failure.md)に関する記事の説明に従って、デバイスの構成をバックアップしてあること。


## <a name="configure-replacement-device"></a>交換用デバイスを構成する

デバイスで許容できない障害が発生した場合は、交換用デバイスを注文する必要があります。 交換用デバイスの構成手順は変わりません。 

障害が発生したデバイスから、バックアップしてあるデバイス構成情報を取得します。 この情報を使用して、交換用デバイスを構成します。  

以下の手順のようにして、交換用デバイスを構成します。

1. 「[展開のチェックリスト](azure-stack-edge-gpu-deploy-checklist.md)」で必要な情報を収集します。 以前のデバイス構成で保存した情報を使用することができます。 
1. 障害が発生したものと同じ構成の新しいデバイスを注文します。  注文するには、Azure portal で[新しい Azure Stack Edge リソースを作成](azure-stack-edge-gpu-deploy-prep.md#)します。
1. [開梱](azure-stack-edge-gpu-deploy-install.md#unpack-the-device)し、[ラックに取り付け](azure-stack-edge-gpu-deploy-install.md#rack-the-device)、[デバイスのケーブル接続](azure-stack-edge-gpu-deploy-install.md#cable-the-device)を行います。 
1. [デバイスのローカル UI に接続](azure-stack-edge-gpu-deploy-connect.md)します。
1. 古いデバイスで使用していたものと同じ IP アドレスを使用して、ネットワークを構成します。 同じ IP アドレスを使用することで、環境内で使用されているクライアント コンピューターへの影響を最小限に抑えることができます。 [ネットワーク設定を構成する](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)方法を参照してください。
1. 古いデバイスと同じデバイス名と DNS ドメインを割り当てます。 そうすることで、クライアントは同じデバイス名を使用して新しいデバイスと通信できるようになります。 方法については、[デバイス設定の構成](azure-stack-edge-gpu-deploy-set-up-device-update-time.md)に関する記事を参照してください。
1. 古いデバイスの場合と同じ方法で、新しいデバイスで証明書を構成します。 新しいデバイスには新しいノードのシリアル番号が付いていることに注意してください。 古いデバイスで独自の証明書を使用していた場合は、新しいノード証明書を取得する必要があります。 方法については、[証明書の構成](azure-stack-edge-gpu-deploy-configure-certificates.md)に関する記事を参照してください。
1. Azure portal からアクティブ化キーを取得し、新しいデバイスをアクティブ化します。 方法については、[デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事を参照してください。

これで、古いデバイスで実行していたワークロードを展開する準備が整いました。

## <a name="restore-edge-cloud-shares"></a>Edge クラウド共有を復元する

デバイスの Edge クラウド共有のデータを復元するには、次の手順のようにします。

1. 障害が発生したデバイスで以前に作成したものと同じ共有名を使用して、[共有を追加](azure-stack-edge-gpu-manage-shares.md#add-a-share)します。 共有を作成するときに、 **[BLOB コンテナーを選択]** が **[既存のものを使用]** オプションに設定されていることを確認した後、前のデバイスで使用されていたコンテナーを選択します。
1. 以前のデバイスへのアクセス権を持っていた[ユーザーを追加](azure-stack-edge-gpu-manage-users.md#add-a-user)します。
1. 以前にデバイス上の共有に関連付けられていた[ストレージ アカウントを追加](azure-stack-edge-gpu-manage-storage-accounts.md#add-an-edge-storage-account)します。 Edge ストレージ アカウントを作成するときに、既存のコンテナーから選択し、前のデバイスでマップされていた Azure ストレージ アカウントにマップされていたコンテナーをポイントします。 以前のデバイスで Edge ストレージ アカウントに書き込まれたデバイスのデータは、マップされた Azure ストレージ アカウントで選択されているストレージ コンテナーに自動的にアップロードされています。
1. Azure から[共有のデータを更新します](azure-stack-edge-gpu-manage-shares.md#refresh-shares)。 これにより、既存のコンテナーから共有にすべてのクラウド データが取り込まれます。

## <a name="restore-edge-local-shares"></a>Edge ローカル共有を復元する

デバイスで障害が発生した場合に備えて、次のいずれかのバックアップ ソリューションを展開して、Kubernetes または IoT のワークロードからのローカル共有データを保護している場合があります。

| サードパーティ製ソフトウェア           | ソリューションへの参照                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> 詳細については、Cohesity にお問い合わせください。          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 詳細については、Commvault にお問い合わせください。 |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> 詳細については、Veritas にお問い合わせください。   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 詳細については、Veeam にお問い合わせください。 |

交換用デバイスを完全に構成したら、ローカル ストレージ用にデバイスを有効にします。 

次の手順のようにして、ローカル共有からデータを回復します。

1. [デバイスでコンピューティングを構成します](azure-stack-edge-gpu-deploy-configure-compute.md)。
1. [ローカル共有を追加](azure-stack-edge-gpu-manage-shares.md#add-a-local-share)して戻します。
1. 選択したデータ保護ソリューションで提供されている回復手順を実行します。 前の表の情報を参照してください。

## <a name="restore-vm-files-and-folders"></a>VM ファイルとフォルダーを復元する

デバイスで障害が発生した場合に備えて、次のいずれかのバックアップ ソリューションを展開し、VM 上のデータを保護している場合があります。



| バックアップ ソリューション        | サポート対象 OS   | 関連項目                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Azure Backup 用の Microsoft Azure Recovery Services (MARS) エージェント | Windows        | [MARS エージェントについて](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows、Linux | [Microsoft Azure の統合、バックアップ、回復のソリューションの概要](https://www.cohesity.com/solution/cloud/azure) <br>詳細については、Cohesity にお問い合わせください。                          |
| Commvault               | Windows、Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> 詳細については、Commvault にお問い合わせください。
| Veritas                 | Windows、Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-edge-with-NetBackup/ba-p/883370) <br> 詳細については、Veritas にお問い合わせください。                    |
| Veeam                   | Windows、Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> 詳細については、Veeam にお問い合わせください。 |

交換用デバイスの構成が完全に済んだら、前に使用していた VM イメージを使用して VM を再展開できます。 

次の手順のようにして、VM のデータを回復します。
 
1. デバイスに [VM イメージから VM を展開します](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)。 
1. 選択したデータ保護ソリューションを VM にインストールします。
1. 選択したデータ保護ソリューションで提供されている回復手順を実行します。 前の表の情報を参照してください。

## <a name="restore-a-kubernetes-deployment"></a>Kubernetes のデプロイを復元する

Azure Arc で Kubernetes のデプロイを実行した場合は、許容できないデバイス障害が発生した後にデプロイを復元することができます。 アプリケーションの定義が格納されている `git` リポジトリから顧客のアプリケーションとコンテナーを再デプロイする必要があります。 [Azure Arc を使用した Kubernetes のデプロイに関する情報](./azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)<!--Original text: Kubernetes deployments can be restored from a non-tolerated failure with the device when deployed with Azure Arc. Customer application/containers deployed onto a Kubernetes on Azure Stack Edge via Azure Arc can be redeployed from the git repository where the application definition is. Here is a link to the article to deploy Kubernetes with Arc -->
 
## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro デバイスを返却する](azure-stack-edge-return-device.md)方法を確認します。
