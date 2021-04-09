---
title: Azure のコンフィデンシャル コンピューティング開発ツール
description: ツールとライブラリを使用し、コンフィデンシャル コンピューティング用のアプリケーションを開発する
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551387"
---
# <a name="application-development-on-intel-sgx"></a>Intel SGX でのアプリケーション開発 


コンフィデンシャル コンピューティング インフラストラクチャには、特定のツールとソフトウェアが必要です。 このページでは特に、Intel SGX で実行している Azure コンフィデンシャル コンピューティング仮想マシンのアプリケーション開発に関連する概念について説明します。 このページを読む前に、[Intel SGX とエンクレーブの紹介](confidential-computing-enclaves.md)ページをお読みください。 

エンクレーブおよび隔離された環境の利点を活用するには、コンフィデンシャル コンピューティングをサポートするツールを使用する必要があります。 エンクレーブ アプリケーション開発をサポートするさまざまなツールが存在します。 たとえば、次のオープンソース フレームワークを使用できます。 

- [Open Enclave Software Development Kit (OE SDK)](#oe-sdk)
- [Confidential Consortium Framework (CCF)](#ccf)

## <a name="overview"></a>概要

エンクレーブを使用して構築されたアプリケーションは、次の 2 つの観点からパーティション分割されます。

1. "信頼されていない" コンポーネント (ホスト)
1. "信頼された" コンポーネント (エンクレーブ)


![アプリケーション開発](media/application-development/oe-sdk.png)


**ホスト** は、エンクレーブ アプリケーションが実行されている、信頼されていない環境です。 ホストで展開されているエンクレーブ コードには、ホストからアクセスすることはできません。 

**エンクレーブ** は、アプリケーション コード、およびキャッシュされたそのデータおよびメモリが実行される場所です。 シークレットと機密データを常に保護された状態にするために、安全な計算はエンクレーブ内で実行する必要があります。 


アプリケーションの設計時に、アプリケーションのどの部分をエンクレーブで実行する必要があるかを特定して、決定することが重要です。 信頼されたコンポーネントに配置したコードは、アプリケーションの他の部分から隔離されます。 エンクレーブが初期化されてコードがメモリに読み込まれた後は、信頼されていない環境から、そのコードを読み取ったり、変更したりすることはできなくなります。 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

エンクレーブ内で実行されるコードを記述するには、ご利用のプロバイダーによってサポートされているライブラリまたはフレームワークを使用します。 [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) は、コンフィデンシャル コンピューティングに対応したさまざまなハードウェアに対する抽象化を可能にするオープンソースの SDK です。 

OE SDK は、あらゆる CSP のハードウェアに対する単一の抽象化レイヤーとして構築されています。 Azure Confidential Computing 仮想マシン上で OE SDK を使用して、エンクレーブ上にアプリケーションを作成、実行することができます。

## <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF) はノードの分散ネットワークです。各ノードでそれぞれのエンクレーブが実行されます。 信頼されたノードネット ワークでは、分散型元帳を実行できます。 この台帳により、プロトコルで使用される、セキュリティで保護された信頼性の高いコンポーネントが提供されます。 

![CCF ノード](media/application-development/ccf.png)

このオープンソース フレームワークを使用すると、ブロックチェーンで高スループット、細かく調整された機密性、コンソーシアム ガバナンスが可能になります。 各ノードで TEE を使用することで確実に、合意とトランザクションが安全に処理されます。


## <a name="next-steps"></a>次のステップ 
- [コンフィデンシャル コンピューティング DCsv2-Series 仮想マシンをデプロイする](quick-create-portal.md)
- [OE SDK をダウンロードしてインストールし、アプリケーション開発を開始する](https://github.com/openenclave/openenclave)