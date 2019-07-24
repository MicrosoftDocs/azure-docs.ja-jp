---
title: Azure Migrate のコレクター アプライアンスのアップグレード | Microsoft Docs
description: Azure Migrate Collector アプライアンスのアップグレードに関する情報を提供します。
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648675"
---
# <a name="collector-appliance-updates"></a>コレクター アプライアンスの更新

この記事には、[Azure Migrate](migrate-overview.md) の Collector アプライアンスのアップグレード情報がまとめられています。

Azure Migrate Collector は、Azure に移行する前の評価を目的として、オンプレミス vCenter 環境を検出するために使用される軽量アプライアンスです。 [詳細情報](concepts-collector.md)。

## <a name="how-to-upgrade-the-appliance"></a>アプライアンスをアップグレードする方法

OVA を再度ダウンロードせずに、Collector を最新バージョンにアップグレードできます。

1. アプライアンスにあるすべてのブラウザー ウィンドウと開かれているファイル/フォルダーを閉じます。
2. この記事内の以降に示した更新プログラムの一覧から、最新のアップグレード パッケージをダウンロードします。
3. ダウンロードしたパッケージが安全であることを確認するために、管理者コマンド ウィンドウを開き、以下のコマンドを実行して、zip ファイルのハッシュを生成します。 生成されたハッシュは、その特定のバージョンのハッシュと一致する必要があります。

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    例:**C:\>CertUtil -HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Collector アプライアンス VM に zip ファイルをコピーします。
5. Zip ファイルを右クリックして、**[すべて展開]** を選択します。
6. **[Setup.ps1]** > **[Run with PowerShell]** を右クリックし、インストールの指示に従います。

## <a name="collector-update-release-history"></a>コレクターの更新プログラムのリリース履歴

### <a name="continuous-discovery-upgrade-versions"></a>継続的な検出:アップグレード バージョン

#### <a name="version-101014-released-on-03292019"></a>バージョン 1.0.10.14 (2019 年 3 月 29 日リリース)

UI の機能強化は、ほとんど含まれていません。

アップグレード [パッケージ 1.0.10.14](https://aka.ms/migrate/col/upgrade_10_14) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>バージョン 1.0.10.12 (2019 年 3 月 13 日リリース)

アプライアンス内で Azure クラウドを選択する際の問題に対する修正が含まれています。

アップグレード [パッケージ 1.0.10.12](https://aka.ms/migrate/col/upgrade_10_12) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>1 回限りの検出 (現在は非推奨):以前のアップグレード バージョン

> [!NOTE]
> 1 回限りの検出アプライアンスは推奨されなくなりました。この方法は、パフォーマンス データ ポイントの可用性と収集された平均パフォーマンス カウンターの vCenter Server の統計設定に依存しており、その結果、Azure への移行のために VM のサイズが小さくなったためです。

#### <a name="version-10916-released-on-10292018"></a>バージョン 1.0.9.16 (2018 年 10 月 29 日リリース)

アプライアンスの設定中に発生する PowerCLI の問題の修正プログラムが含まれています。

アップグレード [パッケージ 1.0.9.16](https://aka.ms/migrate/col/upgrade_9_16) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>バージョン 1.0.9.14

アップグレード [パッケージ 1.0.9.14](https://aka.ms/migrate/col/upgrade_9_14) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>バージョン 1.0.9.13

アップグレード [パッケージ 1.0.9.13](https://aka.ms/migrate/col/upgrade_9_13) のハッシュ値

**アルゴリズム** | **ハッシュ値**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>次の手順

- Collector アプライアンスについて、[さらに詳しく学習](concepts-collector.md)します。
- VMware VM の[評価を実行](tutorial-assessment-vmware.md)します。
