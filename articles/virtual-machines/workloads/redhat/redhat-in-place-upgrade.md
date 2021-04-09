---
title: Azure 上の Red Hat Enterprise Linux イメージのインプレース アップグレード
description: Red Hat Enterprise 7.x イメージから最新の 8.x バージョンへのインプレース アップグレードを実行する方法について説明します。
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676069"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>インプレース アップグレードの Red Hat Enterprise Linux

この記事では、Red Hat Enterprise Linux (RHEL) 7 から Red Hat Enterprise Linux 8 へのインプレース アップグレードを実行する手順を説明します。 この手順では Azure の `leapp` ツールを使用します。 インプレース アップグレード中に、既存の RHEL 7 オペレーティング システムが RHEL 8 バージョンに置き換えられます。

>[!Note] 
> Red Hat Enterprise Linux の SQL Server のオファリングでは、Azure でのインプレース アップグレードはサポートされていません。

## <a name="what-to-expect-during-the-upgrade"></a>アップグレード中の予期される動作
アップグレード中にシステムが数回再起動されます。 最後の再起動により、VM が RHEL 8 の最新のマイナー リリースにアップグレードされます。 

アップグレード処理は、20 分から 2 時間かかる場合があります。 合計時間は、VM のサイズやシステムにインストールされているパッケージの数など、いくつかの要因によって異なります。

## <a name="preparations"></a>準備
Red Hat と Azure では、インプレース アップグレードを使用してシステムを次のメジャー バージョンに移行することをお勧めします。 

アップグレードを開始する前に、次の考慮事項に注意してください。 

>[!Important] 
> アップグレードを開始する前に、イメージのスナップショットを作成してください。

* 最新の RHEL 7 バージョンを使用していることを確認してください。 現在の最新バージョンは RHEL 7.9 です。 ロックされたバージョンを使用していて、RHEL 7.9 にアップグレードできない場合は、[こちらの手順に従って、非 EUS (Extended Update Support) リポジトリに切り替えます](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock)。

* 次のコマンドを実行して、アップグレードをチェックし、正常に完了するかどうかを確認します。 このコマンドでは、 */var/log/leapp/leapp-report.txt* ファイルが生成されます。 このファイルには、処理と実行内容について、およびアップグレードが可能かどうかが記載されています。

    >[!NOTE]
    > この記事のコマンドを実行するには、root アカウントを使用してください。 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* シリアル コンソールが機能していることを確認します。 このコンソールは、アップグレード処理中に監視を行うために使用します。

* */etc/ssh/sshd_config* で SSH の root アクセスを有効にします。
    1. */etc/ssh/sshd_config* ファイルを開きます。
    1. `#PermitRootLogin yes` を検索します。
    1. 番号記号 (`#`) を削除して、文字列をコメント解除します。

## <a name="upgrade-steps"></a>アップグレードの手順

これらの手順に慎重に従ってください。 実稼働インスタンスで試す前に、テスト マシンでアップグレードを試すことをお勧めします。

1. `yum` update を実行して、最新のクライアント パッケージを取得します。
    ```bash
    yum update -y
    ```

1. `leapp-client-package`をインストールします。
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. [Red Hat ポータル](https://access.redhat.com/articles/3664871)で、*repomap.csv* と *pes-events.json* が含まれている *leapp-data.tar.gz* ファイルを入手します。 *leapp-data.tar.gz* ファイルを展開します。
    1. *leapp-data.tar.gz* ファイルをダウンロードします。
    1. コンテンツを展開し、ファイルを削除します。 次のコマンドを使用します。
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. `leapp` 用の `answers` ファイルを追加します。
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. アップグレードを開始します。
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  `leapp upgrade` コマンドが正常に完了したら、システムを手動で再起動して処理を完了させます。 システムは数回再起動されるため、使用できません。 シリアル コンソールを使用して処理を監視します。

1.  アップグレードが正常に完了したことを確認します。
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. アップグレードが完了したら、root の SSH アクセスを削除します。
    1. */etc/ssh/sshd_config* ファイルを開きます。
    1. `#PermitRootLogin yes` を検索します。
    1. 番号記号 (`#`) を追加して、文字列をコメントにします。

1. SSHD サービスを再起動して、変更を適用します。
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>一般的な問題

次のエラーは、通常、`leapp preupgrade` 処理が失敗した場合、または `leapp upgrade` 処理が失敗した場合に発生します。

* **Error**: 次の無効なプラグイン パターンに一致するものが見つかりませんでした。

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **解決策**:サブスクリプション マネージャー プラグインを無効にします。 */etc/yum/pluginconf.d/subscription-manager.conf* ファイルを編集し、`enabled` を `enabled=0`に変更して無効にします。

    このエラーは、有効になっているサブスクリプション マネージャー `yum` プラグインが `PAYG` VM で使用されていない場合に発生します。

* **Error**: root を使用したリモート ログインで問題が発生した可能性があります。

    このエラーは、`leapp preupgrade` が失敗したときに表示されることがあります。

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **解決策**: */etc/sshd_config* で root アクセスを有効にします。

    このエラーは、 */etc/sshd_config* で root の SSH アクセスが有効になっていない場合に発生します。 詳細については、この記事の「[準備](#preparations)」セクションを参照してください。 


## <a name="next-steps"></a>次のステップ
* [Azure の Red Hat イメージ](./redhat-images.md)の詳細を確認します。
* [Red Hat Update Infrastructure](./redhat-rhui.md) の詳細を確認します。
* [RHEL BYOS プラン](./byos.md)の詳細を確認します。
* Red Hat のインプレース アップグレード処理の詳細については、Red Hat ドキュメントの「[Upgrading from RHEL 7 TO RHEL 8 (RHEL 7 から RHEL 8 へのアップグレード)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)」を参照してください。
* すべてのバージョンの RHEL の Red Hat サポート ポリシーの詳細については、Red Hat ドキュメントの「[Red Hat Enterprise Linux life cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」を参照してください。