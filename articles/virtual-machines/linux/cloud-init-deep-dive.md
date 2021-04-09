---
title: cloud-init の理解
description: cloud-init を使用した Azure VM のプロビジョニングについて理解を深めます。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 94dd57310375febb4bc9a55efa704a5fbf4e80e8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559445"
---
# <a name="diving-deeper-into-cloud-init"></a>cloud-init を深く知る
[cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) の詳細を学んだり、深いレベルでトラブルシューティングするには、しくみを理解する必要があります。 このドキュメントでは、重要な部分を示し、Azure 固有の内容について説明します。

一般化されたイメージに cloud-init が含まれ、VM がそのイメージから作成されるとき、cloud-init は構成を処理し、初期ブートの 5 つのステージを実行します。 これらのステージは、cloud-init が構成を適用するポイントを示すため重要です。 


## <a name="understand-cloud-init-configuration"></a>cloud-Init 構成を理解する
プラットフォーム上で実行するように VM を構成することは、cloud-init がイメージ コンシューマーとして複数の構成を適用する必要があることを意味します。操作が必要になる主な構成は、複数の形式をサポートする `User data` (customData) です。これらについては、[こちら](https://cloudinit.readthedocs.io/en/latest/topics/format.html#user-data-formats)を参照してください。 また、追加の構成のためにスクリプト (/var/lib/cloud/scripts) を追加して実行する機能も用意されています。以下で詳しく説明します。

いくつかの構成は、次のように、cloud-init に付属する Azure Marketplace イメージに既に組み込まれています。

1. **クラウド データ ソース** - cloud-init には、クラウド プラットフォームと対話できるコードが含まれます。これらは "データソース" と呼ばれます。 [Azure](https://cloudinit.readthedocs.io/en/latest/topics/datasources/azure.html#azure) で VM が cloud-init イメージから作成されると、cloud-init は Azure データソースを読み込み、それが Azure メタデータ エンドポイントと対話して VM 固有の構成を取得します。
2. **ランタイム構成** (/run/cloud-init)
3. **イメージ構成** (/etc/cloud)、`/etc/cloud/cloud.cfg` や `/etc/cloud/cloud.cfg.d/*.cfg` など。 Azure でこれが使用される場所の例。通常、cloud-init を含む Linux OS イメージには Azure データソース ディレクティブがあります。これにより、どのデータソースを使用するべきかが cloud-init に示され、cloud-init の時間が節約されます。

   ```bash
   /etc/cloud/cloud.cfg.d# cat 90_dpkg.cfg
   # to update this file, run dpkg-reconfigure cloud-init
   datasource_list: [ Azure ]
   ```


## <a name="cloud-init-boot-stages-processing-configuration"></a>cloud-init ブート ステージ (処理構成)

cloud-init を使用してプロビジョニングするとき、構成を処理し、ログに表示される 5 つのブート ステージがあります。

1. [ジェネレーター ステージ](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#generator):cloud-init systemd ジェネレーターが開始され、cloud-init をブート目標に含めるべきかどうかを判別します。含める必要がある場合は cloud-init を有効にします。 

2. [cloud-init ローカル ステージ](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#local):ここで、cloud-init はローカルの "Azure" データソースを探します。これによって、cloud-init と Azure のインターフェイスが有効になり、フォールバックを含むネットワーク構成が適用されます。

3. [cloud-init 初期化ステージ (ネットワーク)](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#network):ネットワークをオンラインにして、NIC とルート テーブル情報が生成される必要があります。 このステージでは、/etc/cloud/cloud.cfg の `cloud_init_modules` に一覧表示されているモジュールが実行されます。 Azure の VM がマウントされ、エフェメラル ディスクがフォーマットされ、ホスト名が設定されます。その他のタスクも行われます。

   `cloud_init_modules` の一部を次に示します。
   
   ```bash
   - migrator
   - seed_random
   - bootcmd
   - write-files
   - growpart
   - resizefs
   - disk_setup
   - mounts
   - set_hostname
   - update_hostname
   - ssh
   ```
   
   このステージの後で、cloud-init は、VM が正常にプロビジョニングされたことを Azure プラットフォームに通知します。 一部のモジュールで障害が発生している可能性があります。すべてのモジュール エラーによってプロビジョニングが失敗するわけではありません。

4. [cloud-init 構成ステージ](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#config):このステージでは、/etc/cloud/cloud.cfg に定義され一覧表示されている `cloud_config_modules` のモジュールが実行されます。


5. [cloud-init 最終ステージ](https://cloudinit.readthedocs.io/en/latest/topics/boot.html#final):この最終ステージでは、/etc/cloud/cloud.cfg に一覧表示されている、`cloud_final_modules` のモジュールが実行されます。 ここでは、ブート プロセスの後半で実行される必要があるモジュールが実行されます。パッケージのインストールやスクリプトの実行などです。 

   -   このステージでは、スクリプトを `/var/lib/cloud/scripts` の下のディレクトリに配置して実行できます。
   - `per-boot` - このディレクトリ内のスクリプトは、再起動のたびに実行されます。
   - `per-instance` - このディレクトリ内のスクリプトは、新しいインスタンスが最初に起動されるときに実行されます。
   - `per-once` - このディレクトリ内のスクリプトは、1 回だけ実行されます。

## <a name="next-steps"></a>次のステップ

[cloud-init のトラブルシューティング](cloud-init-troubleshooting.md)
