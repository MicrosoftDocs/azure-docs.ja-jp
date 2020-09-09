---
title: Azure でサポートされているメインフレーム ワークロード | Microsoft Docs
description: メインフレーム エミュレーターや Microsoft パートナーの他のサービスを使用して、Microsoft Azure を使用する IBM Z ベースのシステムなどのメインフレーム ワークロードをリホストします。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 05/09/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 07234e5f456a5467adde4d899341a6124d128567
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006116"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Azure でサポートされているメインフレーム ワークロード

ここで紹介するソリューションは、メインフレーム ワークロードを Microsoft Azure に移行する際に役立ちます。 一部のワークロードは、比較的簡単に移行できます。 レガシ システム ソフトウェアに依存する他のワークロードはリホストできます。 

メインフレームのエミュレーションとサービスの詳細については、[Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)を参照してください。

## <a name="migrate-mainframe-closer-to-azure"></a>Azure の近くへのメインフレームの移行

- .NET Framework 向け [ASNA Visual RPG for .NET](https://asna.com/us/products/visual-rpg) コンパイラ (Visual Studio プラグインを使用)。
- [Asysco AMT COBOL](https://www.asysco.com/cobol/) 開発環境 (Unisys、IBM メインフレーム、およびその他の COBOL 言語 (Micro Focus COBOL など))。
- ハイエンド ワークロード用の [Asysco AMT GO](https://www.asysco.com/amt-go/) クラウドベース デプロイ アーキテクチャ。
- データ、コード、スクリプト、セキュリティ、インターフェイス、およびその他のメインフレーム成果物を変換するための [Asysco AMT Transform](https://www.asysco.com/amt-transform/)。
- [Fujitsu NetCOBOL](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/) 開発および統合ツール。
- [Micro Focus Visual COBOL](https://www.microfocus.com/products/visual-cobol/) 開発および統合ツール。
- メインフレームの PL/I 構文、データ型、動作をサポートする、.NET プラットフォーム向け [Micro Focus PL/I](https://www.microfocus.com/campaign/download/pli-modernization/) レガシ コンパイラ。
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) メインフレーム統合プラットフォーム。
- [Modern Systems CTU (COBOL-To-Universal)](https://modernsystems.com/automatic-cobol-to-java-conversion/) 開発および統合ツール。
- [NTT Data Enterprise COBOL](https://us.nttdata.com/en/digital/application-development-and-modernization) 開発および統合ツール。
- メインフレームの PL/I 構文、データ型、動作をサポートする、.NET プラットフォーム向け [NTT Open PL/I](https://us.nttdata.com/en/digital/application-development-and-modernization) レガシ コンパイラ。
- [Raincode COBOL コンパイラ](https://www.raincode.com/products/cobol/)開発および統合ツール。
- メインフレームの PL/I 構文、データ型、動作をサポートする、.NET プラットフォーム向け [Raincode PL/I コンパイラ](https://www.raincode.com/products/pli/)。
- メインフレームのアセンブラー 370 および HLASM 構文に対応する [Raincode ASM370 コンパイラ](https://www.raincode.com/technical-landscape/asm370/)。

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>オンラインおよびバッチ処理用のエミュレーション環境のデプロイ

- CICS、IMS、TIP、HVTIP、およびその他の一般的なメインフレーム環境をサポートする [Asysco AMT GO](https://www.asysco.com/amt-go/) デプロイ アーキテクチャ。
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) メインフレーム統合プラットフォーム。
- [NTT DATA メインフレーム リホスティング開発環境](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) (ネイティブ トランザクション処理環境)。
- JCL トランザクション機能を含む、[NTT DATA バッチ処理環境](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) (BPE)。
- .NET および Azure プラットフォーム向け [Raincode CICS](https://www.raincode.com/technical-landscape/cics/) エミュレーター。
- [Raincode JCL](https://www.raincode.com/products/jcl/) プラグ互換 JCL インタープリター。

## <a name="code-conversion"></a>コード変換

- ソースコード、データ、バッチ、スケジュール、TP モニター、インターフェイス、セキュリティ、管理などを対象とする [Asysco](https://www.asysco.com/azure-cloud/) システム変換テクノロジ。
- インベントリと分析、設計トレーニング、最終リハーサル、移行後のサポートなど、移行プロジェクト用の [Asysco AMT Services](https://www.asysco.com/migration-services/) エンドツーエンド サービス。
- [Blu Age](https://www.bluage.com/) ツール。レガシ ビジネス アプリケーションおよびデータベースをデジタル化します。
- [Heirloom Computing](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) サービス。メインフレームの COBOL、CICS、VSAM を Java に変換します。
- [LzLabs Software Defined Mainframe](https://www.lzlabs.com/) マネージド ソフトウェア コンテナー。メインフレーム アプリケーションを Linux コンピューターまたはプライベート/パブリック/ハイブリッド クラウド環境に移行します。

## <a name="modernization-services"></a>最新化サービス

Microsoft は、大規模な組織によるソリューションの設計、構築、管理を支援するグローバル システム インテグレーター (GSI) と提携しています。 

- [Azure メインフレーム移行センター](https://azure.microsoft.com/migration/mainframe/)
