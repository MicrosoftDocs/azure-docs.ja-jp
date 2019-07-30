---
title: Azure Migrate の最新情報 | Microsoft Docs
description: Azure Migrate サービスの概要を示します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 06/10/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 2c3bc596076f3ec4f9d41f0da819ddd386fee63c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811046"
---
# <a name="whats-new-in-azure-migrate"></a>Azure Migrate の新着情報

[Azure Migrate](migrate-services-overview.md) を使用すると、サーバー、アプリ、データを検出して評価し、Microsoft Azure クラウドに移行するのに役立ちます。 この記事では、Azure Migrate の新機能について概要を説明します。



## <a name="azure-migrate-new-version"></a>Azure Migrate の新しいバージョン

Azure Migrate の新しいバージョンが、2019 年 7 月にリリースされました。 

- **現在の (新しい) バージョン**: Azure Migrate プロジェクトの作成、オンプレミス マシンの検出、評価と移行の調整を行う場合はこちらのバージョンを使用します。 
- **前のバージョン**: 前バージョンの Azure Migrate (評価のサポート対象がオンプレミスの VMware VM のみ) を使用しているお客様の場合、今後は最新バージョンを使用してください。 前のバージョンでは、新しい Azure Migrate プロジェクトを作成したり、新しい検出を実行したりできなくなりました。 既存のプロジェクトには引き続きアクセスできます。 それを行うには、Azure portal の [すべてのサービス] で、Azure Migrate を検索します。 Azure Migrate の通知には、古い Azure Migrate プロジェクトにアクセスするためのリンクがあります。


## <a name="azure-migrate-features"></a>Azure Migrate の機能

新しいバージョンの Azure Migrate には、いくつかの新機能があります。


- **統合された移行プラットフォーム**: Azure Migrate では、Azure への移行過程を一元化、管理、追跡するための、デプロイのフローとポータル エクスペリエンスが向上した、単一のポータルが提供されるようになりました。
- **評価と移行のツール**: Azure Migrate では、ネイティブ ツールが提供され、他の Azure サービスおよび独立系ソフトウェア ベンダー (ISV) のツールが統合されます。 ISV の統合について、[詳しくはこちらをご覧ください](migrate-services-overview.md#isv-integration)。
- **Azure Migrate の評価**: Azure Migrate Server Assessment ツールを使用すると、Azure への移行に関して VMware VM と Hyper-V VM を評価できます。 また、他の Azure サービスおよび ISV ツールを使用する移行についても評価できます。
- **Azure Migrate の移行**: Azure Migrate Server Migration ツールを使用すると、オンプレミスの VMware VM と Hyper-V VM を、Azure だけでなく、物理サーバー、他の仮想化サーバー、プライベート/パブリッククラウド VM にも移行できます。 さらに、ISV ツールを使用して Azure に移行することもできます。
- **Azure Migrate アプライアンス**: Azure Migrate では、オンプレミスの VMware VM と Hyper-V VM の検出と評価のために、軽量のアプライアンスがデプロイされます。
    - このアプライアンスは、Azure Migrate Server Assessment と、エージェントレスの移行のための Azure Migrate Server Migration によって使用されます。
    - アプライアンスでは、評価と移行のために、サーバーのメタデータとパフォーマンス データが継続的に検出されます。  
- **VMware VM の移行**: Azure Migrate Server Migration では、オンプレミスの VMware VM を Azure に移行するために、2 つの方法が提供されています。  Azure Migrate アプライアンスを使用するエージェントレスの移行と、レプリケーション アプライアンスを使用し、移行する各 VM にエージェントが展開されるエージェントベースの移行です。 [詳細情報](server-migrate-overview.md)
 - **データベースの評価と移行**: Azure Migrate では、Azure Database Migration Assistant を使用して、Azure への移行についてオンプレミスのデータベースを評価できます。 Azure Database Migration Service を使用してデータベースを移行できます。
- **Web アプリの移行**: Azure App Service のパブリック エンドポイント URL を使用して、Web アプリを評価できます。 内部 .NET アプリの移行の場合は、App Service Migration Assistant をダウンロードして実行できます。 
- **Data Box**: Azure Migrate で Azure Data Box を使用して、大量のオフライン データを Azure にインポートします。


## <a name="next-steps"></a>次の手順

- Azure Migrate の価格について、[詳しくはこちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。
- Azure Migrate について[よく寄せられる質問を確認](resources-faq.md)します。
- [VMware VM](tutorial-assess-vmware.md) と [Hyper-V VM](tutorial-assess-hyper-v.md) を評価するチュートリアルをお試しください。
