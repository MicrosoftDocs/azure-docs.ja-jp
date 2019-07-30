---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 05/29/2018
ms.author: azcspmt;jonbeck;cynthn;danlep;amverma
ms.custom: include file
ms.openlocfilehash: 88d9491bae1fcc6acb7ab631514aa4f6244f593d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538011"
---
## <a name="deployment-considerations"></a>デプロイに関する考慮事項
* **Azure サブスクリプション** – 多数のコンピューティング集中型インスタンスをデプロイするには、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

* **価格と可用性** - これらの VM サイズは、Standard 価格レベルでのみ提供されます。 各 Azure リージョンで利用できるかどうかについては、「 [リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/) 」を参照してください。 
* **コア クォータ** – 場合によっては、Azure サブスクリプションのコア クォータを既定値から増やす必要があります。 サブスクリプションによっては、H シリーズを含む特定の VM サイズ ファミリにデプロイできるコア数が制限されることがあります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../articles/azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 (既定の制限は、サブスクリプション カテゴリによって異なる場合があります。)
  
  > [!NOTE]
  > 大規模な容量が必要な場合は、Azure サポートにお問い合わせください。 Azure のクォータは容量保証ではなくクレジット制限です。 クォータに関係なく、使用したコアに対してのみ課金されます。
  > 
  > 
* **仮想ネットワーク** – コンピューティング集中型インスタンスを使用するために、Azure [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) は不要です。 ただし、多くのデプロイでは、少なくともクラウド ベースの Azure Virtual Network またはサイト間接続 (オンプレミス リソースへのアクセスが必要な場合) が必要になります。 必要に応じて、新しい仮想ネットワークを作成して、インスタンスをデプロイします。 アフィニティ グループ内の仮想ネットワークにコンピューティング集中型の VM を追加することはできません。
* **サイズ変更** – 特殊なハードウェアが使用されるため、コンピューティング集中型インスタンスのサイズ変更は同じサイズ ファミリ (H シリーズまたはコンピューティング集中型 A シリーズ) 内でのみ可能です。 たとえば、H シリーズの VM は H シリーズのあるサイズから別のサイズにのみ変更できます。 さらに、コンピューティング集中型以外のサイズからコンピューティング集中型サイズへのサイズ変更はサポートされていません。  

## <a name="rdma-capable-instances"></a>RDMA 対応のインスタンス
コンピューティング集中型インスタンス (A8、A9、H16r、H16mr、HB および HC) のサブセットには、リモート ダイレクト メモリ アクセス (RDMA) 接続のためのネットワーク インターフェイスが備わっています。 NC24rs 構成 (NC24rs_v2 および NC24rs_v3) など、'r' で指定された一部の N シリーズのサイズも RDMA に対応します。 このインターフェイスは、標準の Azure ネットワーク インターフェイスと同様に、他の VM サイズでも利用可能です。 
  
このインターフェイスにより、RDMA 対応インスタンスは InfiniBand (IB) ネットワークを介して通信することができ、HB、HC では EDR のレートで、H16r、H16mr、および RDMA 対応 N シリーズの仮想マシンでは FDR のレートで、A8 と A9 の仮想マシンでは QDR のレートで動作します。 これらの RDMA 機能により、特定の Message Passing Interface (MPI) アプリケーションのスケーラビリティとパフォーマンスが向上します。 速度の詳細については、このページの表の詳細情報を参照してください。

> [!NOTE]
> Azure では、IP over IB は、SR-IOV 対応の VM (InfiniBand の SR-IOV、現在は HB および HC) でのみサポートされています。 RDMA over IB は、RDMA 対応のすべてのインスタンスでサポートされています。
>

