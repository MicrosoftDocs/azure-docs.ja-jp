---
title: Azure のエンクレーブの構成証明
description: 構成証明を利用し、コンフィデンシャル コンピューティングの信頼されている環境が安全であることを確認する方法について説明します
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: a7b0ca65329016b0a73f612115d8caba43dfbe2a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551353"
---
# <a name="attesting-sgx-enclaves"></a>SGX エンクレーブの構成証明

Azure のコンフィデンシャル コンピューティングからは、コードまたはデータの一部を分離できる ntel SGX ベースの仮想マシンが与えられます。 このような[エンクレーブ](confidential-computing-enclaves.md)を使用するときは、信頼された環境が本当に安全であるかを検証しなければなりません。 この検証が構成証明のプロセスです。 

## <a name="overview"></a>概要 

証明書利用者は、構成証明を通じて、ソフトウェアが (1) エンクレーブで実行されていること、また (2) エンクレーブが最新かつ安全であることについて、より強い確信を得ることができます。 たとえばエンクレーブは、基になるハードウェアに対し、エンクレーブが確かにそのプラットフォーム上に存在することの証明を含んだ資格情報を生成するよう要求します。 そのレポートを別のエンクレーブに渡し、そこで、レポートが同じプラットフォーム上で生成されたことを確認できます。

![エンクレーブのコードを構成証明する](media/attestation/attestation.png)



構成証明は、システムのソフトウェアや半導体に適合した安全な構成証明サービスを使用して実装する必要があります。 使用できるサービスの例

- [Microsoft Azure Attestation (プレビュー)](../attestation/overview.md) または
- [Intel の構成証明およびプロビジョニング サービス](https://software.intel.com/sgx/attestation-services)


いずれも Azure コンフィデンシャル コンピューティング Intel SGX インフラストラクチャと互換性があります。 

## <a name="next-steps"></a>次のステップ
[エンクレーブ対応アプリ向け Microsoft Azure Attestation サンプル](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)をお試しください。