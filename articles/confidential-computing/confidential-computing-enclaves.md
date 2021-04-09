---
title: Azure 上のコンフィデンシャル コンピューティング仮想マシン
description: コンフィデンシャル コンピューティング ワークロードを可能にする Intel SGX ハードウェアについて説明します。
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 554260b2a2760380d3bb2d91ee25b4a03bf2f1ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551370"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Azure Confidential Computing 仮想マシン (VM) の概要


Azure は、仮想環境でコンフィデンシャル コンピューティングを提供する初めてのクラウド プロバイダーです。 Microsoft は、ハードウェアとアプリケーションの間の抽象化レイヤーとしての機能を果たす仮想マシンを開発してきました。 必要に応じて冗長性と可用性を確保しつつ、ワークロードを大規模に実行することができます。  

## <a name="intel-sgx-enabled-virtual-machines"></a>Intel SGX 対応の仮想マシン

Azure Confidential Computing の仮想マシンでは、CPU ハードウェアの一部が、アプリケーション内の一部のコードとデータ用に予約されています。 この制限された領域がエンクレーブです。 

![VM モデル](media/overview/hardware-backed-enclave.png)

Azure Confidential Computing インフラストラクチャは現在、特殊な SKU の仮想マシン (VM) で構成されています。 これらの VM は、Software Guard Extension (Intel SGX) を搭載した Intel プロセッサ上で動作します。 [Intel SGX](https://intel.com/sgx) は、コンフィデンシャル コンピューティングの核となる保護機能の強化を実現するコンポーネントです。 

現在 Azure では、ハードウェアベースのエンクレーブ作成用に、Intel SGX テクノロジを基盤とする [DCsv2 シリーズ](../virtual-machines/dcv2-series.md)を提供しています。 DCsv2 シリーズの VM で動作するセキュア エンクレーブベースのアプリケーションを構築すれば、アプリケーションのデータとコードをその使用中に保護することができます。 

ハードウェアベースの信頼できるエンクレーブを使用した Azure Confidential Computing 仮想マシンのデプロイについて、[詳しい情報](virtual-machine-solutions.md)をご覧いただけます。

## <a name="enclaves"></a>エンクレーブ

エンクレーブは、ハードウェアのプロセッサやメモリの、セキュリティで保護された部分です。 デバッガーを使用しても、エンクレーブ内のデータやコードを表示することはできません。 信頼されていないコードがエンクレーブ メモリの内容を変更しようとすると、環境が無効になって操作が拒否されます。

基本的に、エンクレーブはセキュリティで保護されたボックスと考えてください。 暗号化されたコードとデータは、箱の中に置くことになります。 箱の外からは何も見えません。 データを取り出す際は、まずデータの暗号化を解除するためのキーをエンクレーブに与えます。その後データは処理され、再び暗号化したうえで、エンクレーブから取り出されます。

各エンクレーブには、各エンクレーブが保持できるメモリの量を決定する、暗号化されたページ キャッシュ (EPC) のサイズが設定されています。 DCsv2 仮想マシンが大きくなるほど、EPC メモリの量が増えます。 VM サイズあたりの最大 EPC については、[DCsv2 の仕様](../virtual-machines/dcv2-series.md)に関するページを参照してください。



### <a name="developing-applications-to-run-inside-enclaves"></a>エンクレーブ内で実行するアプリケーションの開発
アプリケーションの開発時には、[ソフトウェア ツール](application-development.md)を使用して、エンクレーブ内に一部のコードとデータを隠蔽することができます。 信頼された環境の外からはだれもコードとデータを表示したり変更したりできないよう、これらのツールによって保護されます。 

## <a name="next-steps"></a>次の手順
- Azure Confidential Computing 仮想マシンにソリューションをデプロイするための[ベスト プラクティス](virtual-machine-solutions.md)を参照する
- [DCsv2 シリーズ仮想マシンをデプロイする](quick-create-portal.md)
- OE SDK を使用して、[エンクレーブ対応アプリケーションを開発する](application-development.md)