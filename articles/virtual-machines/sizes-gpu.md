---
title: Azure VM のサイズ - GPU | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまな GPU 最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a63a468301a5b0d54abd474fd9bb62212b890f76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554417"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 最適化済み仮想マシンのサイズ

GPU 最適化済み VM サイズは、単一の GPU、複数の GPU、またはフラクショナル GPU で使用できる特化された仮想マシンです。 これらのサイズは、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードを意図して設計されています。 この記事では、GPU、vCPU、データ ディスク、NIC の数と種類についての情報を提供します。 このグループ内の各サイズのストレージのスループットおよびネットワーク帯域幅も含まれています。

- [NCv3 シリーズ](ncv3-series.md) と [NC T4_v3 シリーズ](nct4-v3-series.md)のサイズは、コンピューティング集中型 GPU 高速化アプリケーション用に最適化されています。 例としては、CUDA および OpenCL ベースのアプリケーションやシミュレーション、AI、ディープ ラーニングなどが挙げられます。 NC T4 v3 シリーズは、NVIDIA の Tesla T4 GPU と AMD EPYC2 Rome プロセッサを搭載した推論のワークロードにフォーカスしています。 NCv3 シリーズは、NVIDIA の Tesla V100 GPU を搭載した高性能計算と AI のワークロードにフォーカスしています。

- [NDv2 シリーズ](ndv2-series.md)のサイズは、スケールアップとスケールアウトのディープ ラーニング トレーニング アプリケーションにフォーカスしています。 NDv2 シリーズは、Nvidia Volta V100 と Intel Xeon Platinum 8168 (Skylake) プロセッサを使用しています。

- [NV シリーズ](nv-series.md)と [NVv3 シリーズ](nvv3-series.md)のサイズは、リモートの視覚化、ストリーミング、ゲーム、エンコーディング、および OpenGL や DirectX などのフレームワークを使用する VDI シナリオ用に最適化および設計されています。 これらの VM は、NVIDIA Tesla M60 GPU によってバックアップされています。

- [NVv4 シリーズ](nvv4-series.md)の VM サイズは、VDI およびリモート視覚化用に最適化され、設計されています。 パーティション分割された GPU では、NVv4 は、より小さな GPU リソースを必要とするワークロードに適したサイズを提供します。 これらの VM は、AMD Radeon Instinct MI25 GPU によってバックアップされています。 NVv4 VM では現在、Windows ゲスト オペレーティング システムのみがサポートされています。

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA または AMD GPU ドライバーをインストールする必要があります。

- NVIDIA GPU によってバックアップされる VM では、[NVIDIA GPU ドライバー拡張機能](./extensions/hpccompute-gpu-windows.md)によって、適切な NVIDIA CUDA または GRID ドライバーがインストールされます。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](./extensions/hpccompute-gpu-windows.md)を参照してください。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](./extensions/overview.md)」をご覧ください。   

   または、NVIDIA GPU ドライバーを手動でインストールできます。 サポートされているオペレーティング システム、ドライバー、インストール手順、および検証手順については、「[Windows を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする](./windows/n-series-driver-setup.md)」または「[Linux を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする](./linux/n-series-driver-setup.md)」を参照してください。

- AMD GPU によってバックアップされる VM のサポートされているオペレーティング システム、ドライバー、インストール手順、および確認手順については、「[Windows を実行している N シリーズ VM に AMD GPU ドライバーをインストールする](./windows/n-series-amd-driver-setup.md)」を参照してください。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

- N シリーズ VM を利用できるリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。

- N シリーズ VM をデプロイするには、Resource Manager デプロイ モデルを使用する必要があります。

- N シリーズ VM は、それぞれのディスクに対してサポートされる Azure Storage の種類が異なります。 NC および NV の VM では、Standard Disk Storage (HDD) で提供される VM ディスクのみがサポートされます。 他のすべての GPU VM では、Standard Disk Storage と Premium Disk Storage (SSD) で提供される VM ディスクがサポートされます。

- 多数の N シリーズ VM をデプロイする場合は、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

- Azure サブスクリプションの (リージョンごとの) コア クォータと NC、NCv2、NCv3、ND、NDv2、NV、または NVv2 コアの個別のクォータを増やす必要が生じる場合があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../azure-portal/supportability/how-to-create-azure-support-request.md) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
