---
title: Azure IoT (モノのインターネット) のセキュリティ保護 | Microsoft Docs
description: " Azure IoT (モノのインターネット) サービスには、さまざまな機能が用意されています。 この記事では、Azure の IoT ソリューションをセキュリティで保護する方法について説明します。 "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: db1c2b9c852479b9af3674f6c5e1f1135ee289f1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114079"
---
# <a name="internet-of-things-security-overview"></a>モノのインターネットのセキュリティの概要
Azure IoT (モノのインターネット) サービスには、さまざまな機能が用意されています。 このエンタープライズ クラスのサービスを使用すると、次の操作を実行できます。

* デバイスからデータを収集する
* インモーション データ ストリームを分析する
* 大規模なデータ セットを格納して照会する
* リアルタイム データと履歴データを表示する
* バックオフィス システムと統合する

これらの機能を提供するため、Azure IoT ソリューション アクセラレータでは、複数の Azure サービスとカスタム拡張機能が構成済みソリューションとしてパッケージ化されています。 構成済みソリューションは、一般的な IoT ソリューション パターンの基本実装です。このソリューションを使用すると、IoT ソリューションの提供に必要な時間を短縮できます。 IoT ソフトウェア開発キットを使用し、独自の要件を満たすためにこのソリューションをカスタマイズして拡張することができます。 新しい IoT ソリューションを開発する場合は、例やテンプレートとしてこのソリューションを使用することもできます。

Azure IoT ソリューション アクセラレータは、IoT ニーズに対する強力なソリューションです。 ただし、IoT ソリューションは、最初からセキュリティを念頭に置いて設計することが最も重要です。 IoT デバイスは非常に多いため、セキュリティ上の問題がが発生すると、すぐに広範囲にわたって重大な影響を及ぼすことがあります。

IoT ソリューションをセキュリティ保護する方法について、以下に示します。

## <a name="security-architecture"></a>セキュリティのアーキテクチャ
システムを設計する際は、そのシステムに対する脅威のリスクを把握し、設計と構築時にそれに応じた適切な防御措置を講じることが重要となります。 重要なことは、最初からセキュリティを考慮に入れて製品を設計することです。攻撃者がどのような方法でシステムに侵入しうるかを把握しておけば、適切な軽減策を初期段階から講じることができるためです。

IoT のセキュリティ アーキテクチャについて詳しくは、「 [モノのインターネット (IoT) のセキュリティ アーキテクチャ](/azure/iot-fundamentals/iot-security-architecture)」を参照してください。

この記事では、次のトピックについて説明します。

* [セキュリティの第一歩は脅威モデル](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [IoT のセキュリティ](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Azure IoT リファレンス アーキテクチャの脅威のモデル化](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>徹底的なセキュリティ
IoT は、世界各地の企業に固有のセキュリティ、プライバシー、およびコンプライアンスの課題をもたらします。 ソフトウェアとその実装方法に関する問題が発生した場合、従来のサイバー テクノロジとは異なり、IoT ではサイバーおよび物理世界が融合すると何が起こるかが懸念されます。 IoT ソリューションを保護するには、デバイスの安全なプロビジョニング、デバイスとクラウド間の安全な接続、処理中および保管中のクラウドでの安全なデータ保護を確実に行う必要があります。 ただし、そのような機能には、リソースが限られたデバイス、デプロイの地理的分散、およびソリューション内の多数のデバイスという問題が伴います。

これらの領域のセキュリティに対処する方法について詳しくは、「 [最初からモノのインターネットをセキュリティで保護する](/azure/iot-fundamentals/iot-security-ground-up)」を参照してください。

この記事では、次のトピックについて説明します。

* [最初からインフラストラクチャをセキュリティで保護する](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure - 企業の IoT インフラストラクチャのセキュリティ保護](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>ベスト プラクティス
IoT インフラストラクチャを保護するには、緻密なセキュリティ戦略が必要です。 クラウド内のデータのセキュリティ保護から、パブリック インターネット経由での転送中におけるデータ整合性の保護、デバイスの安全なプロビジョニング至るまで、各レイヤーは、インフラストラクチャ全体でより高度なセキュリティ確保を実現します。

モノのインターネットのセキュリティのベスト プラクティスについて詳しくは、「 [モノのインターネット (IoT) のセキュリティのベスト プラクティス](/azure/iot-fundamentals/iot-security-best-practices)」を参照してください。

この記事では、次のトピックについて説明します。

* [IoT ハードウェアの製造元/インテグレーター](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [IoT ソリューション開発者](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [IoT ソリューションのデプロイ担当者](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [IoT ソリューションのオペレーター](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
