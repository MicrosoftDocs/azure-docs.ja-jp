---
title: SAP HANA on Azure (L インスタンス) のオンボードの要件 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) のオンボードの要件。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675602"
---
# <a name="onboarding-requirements"></a>オンボードの要件

SAP HANA on Azure (L インスタンス) を実行するための要件を以下に示します。

**Microsoft Azure**

- SAP HANA on Azure (L インスタンス) にリンク可能な Azure サブスクリプション。
- Microsoft Premier サポート契約。 Azure での SAP の実行に関する具体的な情報については、「[SAP Support Note #2015553 - SAP on Microsoft Azure: Support Prerequisites (SAP サポート ノート #2015553 - Microsoft Azure 上のSAP: サポートの前提条件)](https://launchpad.support.sap.com/#/notes/2015553)」をご覧ください。 384 個以上の CPU で HANA L インスタンス ユニットを使用する場合は、Premier サポート契約を拡張して Azure Rapid Response を含める必要があります。
- SAP でのサイズ変更の実行後に必要な HANA L インスタンス SKU の認識。

**ネットワーク接続**

- オンプレミスと Azure 間の ExpressRoute: オンプレミスのデータ センターを Azure に接続するには、1 Gbps 以上の接続を ISP と契約してください。 HANA L インスタンス ユニットと Azure 間の接続には、ExpressRoute 技術も使用されています。 HANA L インスタンス ユニットと Azure 間のこの ExpressRoute 接続は、この特定の ExpressRoute 回線に対するすべてのデータ イングレスおよびエグレス料金を含め、HANA L インスタンス ユニットの価格に含まれています。 そのため、お客様に、オンプレミスと Azure 間の ExpressRoute リンクを超える追加のコストが発生することはありません。

**オペレーティング システム**

- SAP アプリケーション用の SUSE Linux Enterprise Server 12 のライセンス。

   > [!NOTE] 
   > Microsoft が提供するオペレーティング システムは SUSE に登録されません。 また、Subscription Management Tool インスタンスにも接続されません。

- Azure の VM にデプロイされた SUSE Linux Subscription Management Tool。 このツールにより、SAP HANA on Azure (L インスタンス) を SUSE で登録し、個々に更新できるようになります  (HANA L インスタンス データ センター内ではインターネットにアクセスできません)。 
- SAP HANA 用の Red Hat Enterprise Linux 6.7 または 7.x のライセンス。

   > [!NOTE]
   > Microsoft が提供するオペレーティング システムは Red Hat に登録されません。 また、Red Hat Subscription Manager インスタンスにも接続されません。

- Azure の VM にデプロイされた Red Hat Subscription Manager。 Red Hat Subscription Manager により、SAP HANA on Azure (L インスタンス) を Red Hat で登録し、個々に更新できるようになります  (Azure L インスタンス スタンプにデプロイされたテナント内からインターネットに直接アクセスすることはできません)。
- SAP が定める要件上、Linux プロバイダーとのサポート契約も必要です。 HANA L インスタンスのソリューションであること、または Azure で Linux を実行するという事実によって、この要件がなくなるわけではありません。 一部の Linux Azure ギャラリー イメージとは異なり、HANA L インスタンスのソリューション プランにサービス料金は *含まれません*。 Linux ディストリビューターとのサポート契約に関する SAP の要件を満たすのは、お客様の責任になります。 
   - SUSE Linux については、「[SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes (SAP ノート #1984787 - SUSE Linux Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)」および「[SAP Note #1056161 - SUSE priority support for SAP applications (SAP ノート #1056161 - SAP アプリケーションの SUSE 優先サポート)](https://launchpad.support.sap.com/#/notes/1056161)」でサポート契約の要件を確認してください。
   - Red Hat Linux の場合、HANA L インスタンスのオペレーティング システムに対するサポートとサービスの更新を含む適切なサブスクリプション レベルが必要になります。 Red Hat では、SAP ソリューションとして Red Hat Enterprise Linux のサブスクリプションを推奨しています。 [https://access.redhat.com/solutions/3082481](https://access.redhat.com/solutions/3082481) を参照してください。 

各種 Linux バージョンと各種 SAP HANA バージョンのサポート マトリックスについては、[SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581) を確認してください。

オペレーティング システムと HLI ファームウェア/ドライバーのバージョンの互換性対応表については、[HLI の OS のアップグレード](os-upgrade-hana-large-instance.md)に関するページを参照してください。


> [!IMPORTANT] 
> Type II ユニットの場合、現時点では SLES 12 SP2 OS バージョンのみがサポートされています。 


**[データベース]**

- SAP HANA (Platform Edition または Enterprise Edition) 用のライセンスとソフトウェア インストール コンポーネント。

**アプリケーション**

- SAP HANA に接続する SAP アプリケーションのライセンスとソフトウェア インストール コンポーネント、および関連する SAP サポート契約。
- SAP HANA on Azure (L インスタンス) 環境で使用される SAP 以外のアプリケーションのライセンスとソフトウェア インストール コンポーネント、および関連するサポート契約。

**スキル**

- Azure IaaS とそのコンポーネントの使用経験と知識。
- Azure への SAP ワークロードのデプロイの経験と知識。
- SAP HANA インストール認定資格者。
- SAP HANA に関連する高可用性とディザスター リカバリーを設計するための SAP アーキテクトのスキル。

**SAP**

- SAP の顧客であり、SAP とサポート契約を結んでいることが望まれます。
- 特に、Type II クラスの HANA L インスタンス SKU の実装では、SAP HANA のバージョンと、サイズの大きいスケールアップ ハードウェア上での最終的な構成について、SAP に相談してください。

**次の手順**
- [Azure の SAP HANA (L インスタンス) アーキテクチャ](hana-architecture.md)を参照してください