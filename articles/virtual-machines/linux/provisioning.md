---
title: Linux のプロビジョニングの概要
description: Linux VM イメージを取り込む方法、または Azure で使用する新しいイメージを作成する方法の概要です。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 6dafa400f2ce2421db6775084befc0abeab70a04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563220"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM のプロビジョニング
一般化されたイメージ (共有イメージ ギャラリーまたはマネージド イメージ) から VM を作成すると、コントロール プレーンによって VM を作成し、VM にパラメーターと設定を渡すことができます。 これは、VM *プロビジョニング* と呼ばれます。 プロビジョニングでは、VM の起動時に VM で使用できる必須の VM Create パラメーター値 (hostname、username、password、SSH キー、customData) がプラットフォームによって作成されます。 

イメージ内に組み込まれているプロビジョニング エージェントは、プラットフォームとのインターフェイスを作成し、複数の独立したプロビジョニング インターフェイスに接続することで、完了したプラットフォームにプロパティとシグナルを設定します。 

プロビジョニング エージェントには、[Azure Linux エージェント](../extensions/agent-linux.md)または [cloud-init](./using-cloud-init.md) を使用できます。 これらは、一般化されたイメージを作成するための[前提条件](create-upload-generic.md)です。

プロビジョニング エージェントは、すべての動作保証済み [Azure Linux ディストリビューション](./endorsed-distros.md)のサポートを提供します。また、多くの場合、動作保証済みディストリビューションのイメージには、cloud-init と Linux エージェントの両方が付属しています。 これにより、プロビジョニングを処理するための cloud-init のオプションが提供されます。また、Linux エージェントは、[Azure 拡張機能](../extensions/features-windows.md)の処理をサポートします。 拡張機能のサポートを提供すると、VM は、VM のパスワード リセット、Azure 監視、Azure バックアップ、Azure Disk encryption などの追加の Azure サービスをサポートする資格を持つことになります。

プロビジョニングが完了すると、起動のたびに cloud-init が実行されます。 cloud-init は、ネットワークの変更、一時ディスクのマウント、フォーマット、Linux エージェントの起動など、VM への変更を監視します。 Linux エージェントはサーバー上で継続的に実行され、Azure プラットフォームから "目標状態" (新しい構成) を求めています。そのため、拡張機能をインストールするたびに、エージェントがそれらを処理できるようになります。

現在 2 つのプロビジョニング エージェントがありますが、cloud-init は選択したプロビジョニング エージェントである必要があります。また、拡張機能をサポートするには、Linux エージェントをインストールする必要があります。 これにより、プラットフォームの最適化を利用し、Linux エージェントを無効または削除できるようになります。また、エージェントを使用せずにイメージを作成する方法と削除する方法の詳細については、この[ドキュメント](disable-provisioning.md)を参照してください。

いずれかのエージェントの実行をサポートできないが、VM Create のプロパティ (hostname、customData、userName、password、ssh キーなど) を設定する必要がある Linux カーネルがある場合、このドキュメントでは、[エージェントを使用せずに一般化されたイメージを作成](no-agent.md)し、プラットフォームの要件を満たす方法について説明します。


## <a name="provisioning-agent-responsibilities"></a>プロビジョニング エージェントの役割

**イメージのプロビジョニング**
  
- ユーザー アカウントの作成
- SSH 認証の種類の構成
- SSH 公開キーおよびキー ペアのデプロイ
- ホスト名の設定
- プラットフォーム DNS へのホスト名の発行
- プラットフォームへの SSH ホスト キーの指紋のレポート
- リソース ディスクの管理
- リソース ディスクのフォーマットとマウント
- `customData` の使用と処理
 
**ネットワーク**
  
- プラットフォーム DHCP サーバーとの互換性を向上させるためのルートの管理
- ネットワーク インターフェイス名の安定性の保持

**カーネル**
  
- 仮想 NUMA (`2.6.37` 未満のカーネルでは無効) の構成
- `/dev/random` の Hyper-V エントロピの使用
- root デバイス (リモート デバイス) の SCSI タイムアウトの構成

**診断**
  
- コンソールからシリアル ポートへのリダイレクト

## <a name="communication"></a>Communication
プラットフォームからエージェントへの情報の流れは 2 つのチャンネルを経由します。

- 起動時に接続される IaaS デプロイ用 DVD。 DVD には OVF に準拠している構成ファイルが収録されており、このファイルに、実際の SSH キー ペア以外のすべてのプロビジョニング情報が保存されています。
- デプロイとトポロジの構成を取得するために使用する REST API を公開する TCP エンドポイント。


## <a name="azure-provisioning-agent-requirements"></a>Azure プロビジョニング エージェントの要件
Linux エージェントと cloud-init が正しく機能するには、次の該当するシステム パッケージが必要です。
- Python 2.6+
- OpenSSL 1.0+
- OpenSSH 5.3+
- ファイル システム ユーティリティ: `sfdisk`、`fdisk`、`mkfs`、`parted`
- パスワード ツール: chpasswd、sudo
- テキスト処理ツール: sed、grep
- ネットワーク ツール: ip-route
- UDF ファイルシステムのマウントのカーネル サポート。

## <a name="next-steps"></a>次のステップ

必要に応じて、[プロビジョニングを無効にし、Linux エージェントを削除する](disable-provisioning.md)ことができます。
