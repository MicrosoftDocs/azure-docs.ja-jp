---
title: SAP HANA (L インスタンス) で Kdump を有効にするスクリプト | Microsoft Docs
description: SAP HANA (L インスタンス) HLI Type I、HLI Type II で Kdump を有効にするスクリプト
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4b06cbcbef79e243116bddb33adbcf6476fac8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213370"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>SAP HANA on Azure Large Instances (HLI) の Kdump

kdump の構成と有効化は、明確な原因がないシステム クラッシュのトラブルシューティングを行うために必要な手順です。
システムが突然クラッシュし、ハードウェアやインフラストラクチャの問題によって説明できない場合があります。
このような場合は、オペレーティング システムまたはアプリケーションの問題である可能性があります。kdump を使用すると、SUSE でシステムがクラッシュした原因を突き止めることができます。

## <a name="enable-kdump-service"></a>Kdump サービスを有効にする

このドキュメントでは、Azure HANA L インスタンス (**Type I および Type II**) で Kdump サービスを有効にする方法の詳細について説明します

## <a name="supported-skus"></a>サポートされている SKU

|  HANA L インスタンスの種類   |  OS ベンダー   |  OS パッケージ バージョン   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>前提条件

- Kdump サービスでは、ダンプの書き込みに `/var/crash` ディレクトリが使用されます。このディレクトリに対応するパーティションに、ダンプを格納できる十分なスペースがあることを確認してください。

## <a name="setup-details"></a>セットアップの詳細

- Kdump を有効にするスクリプトについては、[こちら](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)を参照してください。
> [!NOTE]
> このスクリプトは、ラボのセットアップに基づいて作成されたものです。さらにチューニングを行う場合、OS ベンダーにお問い合わせください。
> 個別の LUN が、ダンプの保存のために新規および既存のサーバー用にプロビジョニングされ、スクリプトがその LUN からのファイル システムの構成を処理します。
> Microsoft はダンプを分析する責任を負いません。 お客様は OS ベンダーでチケットを開き、分析を行ってもらう必要があります。

- 次のコマンドを使用して、HANA L インスタンス上でこのスクリプトを実行します

    > [!NOTE]
    > このコマンドを実行するには、sudo 特権が必要です。

    ```bash
    sudo bash enable-kdump.sh
    ```

- Kdump が正常に有効になったというコマンド出力の場合は、システムを再起動して変更を適用してください。

- 特定の操作の実行に失敗したというコマンド出力の場合は、Kdump サービスは有効になっていません。 「[サポートの問題](#support-issue)」セクションを参照してください。

## <a name="test-kdump"></a>Kdump をテストする

> [!NOTE]
>  以下の操作によって、カーネルのクラッシュとシステムの再起動がトリガーされます。

- カーネルのクラッシュをトリガーする

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- システムが正常に再起動したら、`/var/crash` ディレクトリでカーネルのクラッシュ ログを確認します。

- `/var/crash` に現在の日付のディレクトリがある場合、Kdump は正常に有効になっています。

## <a name="support-issue"></a>サポートの問題

スクリプトがエラーで失敗した場合、または Kdump が有効になっていない場合は、Microsoft サポート チームにサービス要求を提出し、次の詳細を伝えます。

* HLI サブスクリプション ID

* サーバー名

* OS ベンダー

* OS バージョン

* カーネル バージョン

## <a name="related-documents"></a>関連ドキュメント
- [kdump の構成](https://www.suse.com/support/kb/doc/?id=3374462)の詳細
