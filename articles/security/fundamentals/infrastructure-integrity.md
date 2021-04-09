---
title: Azure インフラストラクチャの整合性
description: Azure インフラストラクチャの整合性、および、ソフトウェア コンポーネントのビルドでのウイルス スキャンなど、それをセキュリティで保護するために Microsoft で行われている手順について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: b0df14dfcf66f53e01989a27a95afa5e19fcb2fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87543641"
---
# <a name="azure-infrastructure-integrity"></a>Azure インフラストラクチャの整合性

## <a name="software-installation"></a>ソフトウェア インストール
Azure 環境にインストールされているソフトウェア スタック内のすべてのコンポーネントは、Microsoft セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL) プロセスに従ってカスタムビルドされています。 すべてのソフトウェア コンポーネント (オペレーティング システム (OS) イメージや SQL Database を含む) が、変更管理およびリリース管理プロセスの一部としてデプロイされます。 すべてのノード上で実行される OS は、Windows Server 2008 または Windows Server 2012 のカスタマイズされたバージョンです。 正確なバージョンは、OS に期待される役割に従ってファブリック コントローラー (FC) によって選択されます。 さらに、ホスト OS では、承認されていないソフトウェア コンポーネントのインストールは許可されません。

一部の Azure コンポーネントは、ゲスト OS で実行されているゲスト VM に Azure のお客様としてデプロイされます。

## <a name="virus-scans-on-builds"></a>ビルドに対するウイルス スキャン
Azure ソフトウェア コンポーネント (OS を含む) の各ビルドには、Endpoint Protection ウイルス対策ツールを使用したウイルス スキャンを実行する必要があります。 各ウイルス スキャンでは、関連付けられたビルド ディレクトリ内に、スキャンの対象とスキャンの結果が詳細に記載されたログが作成されます。 このウイルス スキャンは、Azure 内のすべてのコンポーネントに対するビルド ソース コードの一部です。 コードは、クリーンな、成功したウイルス スキャンなしで運用に移行されることはありません。 なんらかの問題が示されている場合、そのビルドは凍結された後、"悪意のある" コードがビルドに挿入された場所を識別するために Microsoft セキュリティ内のセキュリティ チームに渡されます。

## <a name="closed-and-locked-environment"></a>閉じてロックされている環境
既定では、Azure インフラストラクチャ ノードとゲスト VM にユーザー アカウントは作成されません。 さらに、既定の Windows 管理者アカウントも無効になっています。 Azure Live Support の管理者は、適切な認証を使用してこれらのマシンにログインし、応急修復のために Azure 運用ネットワークを管理できます。

## <a name="azure-sql-database-authentication"></a>Azure SQL Database の認証
SQL Server の他のすべての実装と同様に、ユーザー アカウントの管理は緊密に制御する必要があります。 Azure SQL Database では SQL Server 認証のみがサポートされています。 顧客のデータ セキュリティ モデルを補完するために、強力なパスワードを持ち、特定の権限で構成されたユーザー アカウントを使用する必要があります。

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>Microsoft 企業ネットワークと Azure クラスター間の ACL とファイアウォール
サービス プラットフォームと Microsoft 企業ネットワークの間のアクセス制御リスト (ACL) とファイアウォールは、承認されていない内部関係者のアクセスから SQL Database インスタンスを保護します。 さらに、Microsoft 企業ネットワークの IP アドレス範囲内のユーザーのみが Windows Fabric プラットフォーム管理エンドポイントにアクセスできます。

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>SQL Database クラスター内のノード間の ACL とファイアウォール
保護を強化するために、多層防御戦略の一部として、SQL Database クラスター内のノード間に ACL とファイアウォールが実装されています。 実行中のすべてのコードだけでなく、Windows Fabric プラットフォーム クラスター内のすべての通信が信頼できます。

## <a name="custom-monitoring-agents"></a>カスタム監視エージェント
SQL Database は、SQL Database クラスターの正常性を監視するために、ウォッチドッグと呼ばれるカスタム監視エージェント (MA) を採用しています。

## <a name="web-protocols"></a>Web プロトコル

### <a name="role-instance-monitoring-and-restart"></a>ロール インスタンスの監視と再開
Azure では、デプロイされて実行中のすべてのロール (インターネットに接続する Web ロールまたはバックエンド処理 worker ロール) が、それぞれがプロビジョニングされているサービスを効果的かつ効率的に提供していることを確認するために、持続した正常性監視の対象になっていることが保証されます。 ロールが (ホストされているアプリケーションの致命的な障害、またはロール インスタンス自体にある基になる構成の問題のどちらかで) 異常な状態になった場合、FC はロール インスタンス内の問題を検出し、是正状態を開始します。

### <a name="compute-connectivity"></a>コンピューティング接続
Azure では、デプロイされているアプリケーションまたはサービスに、標準の Web ベースのプロトコル経由でアクセスできることが保証されます。 インターネットに接続する Web ロールの仮想インスタンスは、外部のインターネット接続が可能であり、Web ユーザーから直接アクセスできます。 パブリックにアクセス可能な Web ロール仮想インスタンスの代わりに worker ロールが実行する操作の機密性と整合性を保護するために、バックエンド処理 worker ロールの仮想インスタンスは、外部のインターネット接続は可能ですが、外部の Web ユーザーから直接アクセスすることはできません。

## <a name="next-steps"></a>次のステップ
Microsoft が提供する Azure アーキテクチャの保護の詳細については、以下を参照してください。

- [Azure の施設、建物、および物理上のセキュリティ](physical-security.md)
- [Azure インフラストラクチャの可用性](infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](infrastructure-network.md)
- [Azure 実稼働環境のネットワーク](production-network.md)
- [Azure SQL Database のセキュリティ機能](infrastructure-sql.md)
- [Azure の実稼働環境の運用と管理](infrastructure-operations.md)
- [Azure インフラストラクチャの監視](infrastructure-monitoring.md)
- [Azure での顧客データの保護](protection-customer-data.md)
