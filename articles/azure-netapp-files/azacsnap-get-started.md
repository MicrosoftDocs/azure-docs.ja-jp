---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールの使用を開始する | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合スナップショット ツールをインストールするためのガイドを提供します。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736364"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールの使用を開始する (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合スナップショット ツールをインストールするためのガイドを提供します。

## <a name="getting-the-snapshot-tools"></a>スナップショット ツールの入手

お客様には、最新バージョンの [AzAcSnap インストーラー](https://aka.ms/azacsnapdownload)を Microsoft から入手することをお勧めします。

自己インストール ファイルには、[AzAcSnap インストーラー署名ファイル](https://aka.ms/azacsnapdownloadsignature)が関連付けられており、ダウンロードされたインストーラーを GPG で検証できるように、Microsoft の公開キーで署名されています。

これらのダウンロードが完了したら、このガイドの手順に従ってインストールします。

### <a name="verifying-the-download"></a>ダウンロードの検証

上記のようにしてダウンロードできるインストーラーには、ファイル名拡張子が `.asc` の PGP 署名ファイルが関連付けられています。 このファイルを使用して、ダウンロードしたインストーラーが検証済みの Microsoft 提供ファイルであることを保証できます。 Linux パッケージの署名に使用される Microsoft の PGP 公開キーはこちら (<https://packages.microsoft.com/keys/microsoft.asc>) で入手でき、署名ファイルに署名するために使用されています。

Microsoft の PGP 公開キーは、次のようにしてユーザーのローカル環境にインポートできます。

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Microsoft の PGP 公開キーは、次のコマンドで信頼されています。

1. ストア内のキーを一覧表示する。
2. Microsoft のキーを編集する。
3. `fpr` でフィンガープリントを確認する
4. それを信頼するようにキーに署名する。

```bash
gpg --list-keys
```

一覧表示されたキー:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Microsoft の公開キーに署名する対話型 `gpg` セッションからの出力:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

インストーラーの PGP 署名ファイルは、次のようにして確認できます。

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

GPG の使用方法の詳細については、「[The GNU Privacy Handbook](https://www.gnupg.org/gph/en/manual/book1.html)」(GNU プライバシー ハンドブック) を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

スナップショット ツールは、次のシナリオで使用できます。

- 単一の SID
- 複数の SID
- HSR
- スケールアウト
- MDC (シングル テナントのみサポート)
- 単一コンテナー
- SUSE オペレーティング システム
- RHEL オペレーティング システム
- SKU タイプ I
- SKU タイプ II

「[HANA L インスタンスのサポートされるシナリオ](../virtual-machines/workloads/sap/hana-supported-scenario.md)」を参照してください

## <a name="snapshot-support-matrix-from-sap"></a>SAP からのスナップショット サポート マトリックス

次のマトリックスは、ストレージ スナップショット バックアップ用に SAP でサポートされている SAP HANA のバージョンを示すガイドラインとして提供されています。

| データベースのバージョン       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|単一コンテナー データベース| √       | √      | -      | -      | -      | -      |
|MDC シングル テナント        | -       | -      | √      | √      | √      | √      |
|MDC マルチ テナント     | -       | -      | -      | -      | -      | √      |
> √ = <small>ストレージ スナップショット用に SAP によってサポートされています</small>

## <a name="important-things-to-remember"></a>重要な注意点

- スナップショット ツールを設定した後は、使用可能なストレージ領域を継続的に監視し、必要に応じて古いスナップショットを定期的に削除して、ストレージがいっぱいにならないようにします。
- 常に最新のスナップショット ツールを使用します。
- すべての状況で同じバージョンのスナップショット ツールを使用します。
- 運用システムで使用する前に、スナップショット ツールをテストし、コマンドの必要なパラメーターと出力に問題がないことを確認します。
- バックアップ用に HANA ユーザーを設定するときは (詳細はこのドキュメントで後述)、HANA インスタンスごとにユーザーを設定する必要があります。 MDC 用の (SID データベースではなく) SYSTEMDB の HANA インスタンスにアクセスするために、SAP HANA ユーザー アカウントを作成します。 単一コンテナー環境では、テナント データベースの下に設定できます。
- お客様は、ストレージ アクセス用に SSH 公開キーを提供する必要があります。 この操作は、コマンドの実行に使用される各ユーザーに対し、ノードごとに 1 回実行する必要があります。
- ボリュームごとのスナップショットの数は 250 個に制限されています。
- 構成ファイルを手動で編集する場合は、"vi" などの Linux のテキスト エディターを常に使用し、メモ帳などの Windows エディターは使用しないでください。 Windows のエディターを使用すると、ファイルの形式が壊れるおそれがあります。
  - SAP HANA ユーザーが SAP HANA と通信できるように `hdbuserstore` を設定します。
- DR の場合:DR を設定する前に、スナップショット ツールを DR ノードでテストする必要があります。
- ディスク領域を定期的に監視します。SAP HANA 2 以降のリリースでは、`azacsnap -c backup` の `--trim` オプションで、自動的なログの削除が管理されます。
- **スナップショットが作成されないリスク** - スナップショット ツールは、構成ファイルで指定されている SAP HANA システムのノードのみと対話します。  このノードが使用できなくなった場合、別のノードとの通信を自動的に開始するメカニズムはありません。  
  - **スタンバイを使用した SAP HANA スケールアウト** のシナリオの場合は、マスター ノードにスナップショット ツールをインストールして構成するのが一般的です。 ただし、マスター ノードが使用できなくなった場合は、スタンバイ ノードによってマスター ノードの役割が引き継がれます。 この場合、実装チームは、スナップショットが作成されないことがないよう、スナップショット ツールを両方のノード (マスターとスタンバイ) に構成する必要があります。 通常の状態では、crontab によって開始された HANA のスナップショットはマスター ノードで取得されますが、マスター ノードのフェールオーバー後は、新しいマスター ノード (以前のスタンバイ) などの別のノードから、それらのスナップショットを実行する必要があります。 このような結果を実現するには、スタンバイ ノードにスナップショット ツールをインストールし、ストレージの通信を有効にし、hdbuserstore を構成し、`azacsnap.json` を構成し、フェールオーバーの前に crontab コマンドをステージングする必要があります。
  - **SAP HANA HSR HA** のシナリオでは、両方 (プライマリとセカンダリ) のノードでスナップショット ツールをインストール、構成、スケジュールすることをお勧めします。 その後、プライマリ ノードが使用できなくなった場合は、セカンダリで取得されているスナップショットがセカンダリ ノードによって引き継がれます。 通常の状態では、crontab によって開始された HANA のスナップショットはプライマリ ノードによって取得され、セカンダリ ノードでスナップショットを取得しようとしても、プライマリが正常に機能しているため失敗します。  ただし、プライマリ ノードがフェールオーバーした後は、それらのスナップショットはセカンダリ ノードから実行されます。 このような結果を実現するには、セカンダリ ノードにスナップショット ツールをインストールし、ストレージの通信を有効にし、`hdbuserstore` を構成し、azacsnap.json を構成し、フェールオーバーの前に crontab を有効にする必要があります。

## <a name="guidance-provided-in-this-document"></a>このドキュメントで提供されるガイダンス

スナップショット ツールの使用方法を示すため、次のガイダンスが用意されています。

### <a name="taking-snapshot-backups"></a>スナップショット バックアップの取得

- [ストレージ スナップショットの前提条件](azacsnap-installation.md#prerequisites-for-installation)
  - [ストレージとの通信を有効にする](azacsnap-installation.md#enable-communication-with-storage)
  - [SAP HANA との通信を有効にする](azacsnap-installation.md#enable-communication-with-sap-hana)
- [スナップショットを手動で取得する方法](azacsnap-tips.md#take-snapshots-manually)
- [スナップショットの自動バックアップを設定する方法](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [スナップショットを監視する方法](azacsnap-tips.md#monitor-the-snapshots)
- [スナップショットを削除する方法](azacsnap-tips.md#delete-a-snapshot)
- [スナップショットを復元する方法](azacsnap-tips.md#restore-a-snapshot)
- [`boot` スナップショットを復元する方法](azacsnap-tips.md#restore-a-boot-snapshot)
- [スナップショットについて知っておくべき重要事項](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> スナップショットは、単一 SID と複数 SID の両方でテストされます。

### <a name="performing-disaster-recovery"></a>ディザスター リカバリーの実行

- [DR セットアップの前提条件](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [ディザスター リカバリーを設定する方法](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [プライマリから DR サイトへのデータ レプリケーションを監視する方法](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [DR サイトへのフェールオーバーを実行する方法](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールをインストールする](azacsnap-installation.md)