---
title: "Azure Cloud Shell (プレビュー) の制限 | Microsoft Docs"
description: "Azure Cloud Shell の制限の概要"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e42841b126a9df9240bec3f489589d5ce4a6db80
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="limitations-of-azure-cloud-shell"></a>Azure Cloud Shell の制限
Azure Cloud Shell には、次の既知の制限があります。

## <a name="system-state-and-persistence"></a>システム状態と永続化
Cloud Shell セッションを提供するマシンは一時的であり、セッションが 20 分間非アクティブの状態になると、リサイクルされます。 Cloud Shell では、ファイル共有をマウントする必要があります。 そのため、Cloud Shell にアクセスするには、ご利用のサブスクリプションでストレージ リソースをセットアップできることが必要です。 その他の考慮事項:
* マウントされたストレージでは、`$Home` ディレクトリまたは `clouddrive` ディレクトリ内の変更のみが永続化されます。
* ファイル共有は、[割り当て済みリージョン](persisting-shell-storage.md#mount-a-new-clouddrive)内からのみマウントできます。
* Azure Files は、ローカル冗長ストレージと geo 冗長ストレージのアカウントのみをサポートします。

## <a name="user-permissions"></a>ユーザーのアクセス許可
権限は、sudo アクセスのない、通常のユーザーとして設定されます。 `$Home` ディレクトリ外のインストールはすべて失われます。
`clouddrive` ディレクトリ内の `git clone` などの特定のコマンドには適切なアクセス許可がありませんが、`$Home` ディレクトリにはアクセス許可があります。

## <a name="browser-support"></a>ブラウザーのサポート
Cloud Shell では、Microsoft Edge、Microsoft Internet Explorer、Google Chrome、Mozilla Firefox、および Apple Safari の最新バージョンがサポートされます。 プライベート モードの Safari はサポートされません。

## <a name="copy-and-paste"></a>コピーと貼り付け
Ctrl + C と Ctrl + V は、Windows マシン上の Cloud Shell でコピー/貼り付けのショートカットとして機能しません。Ctrl + Insert と Shift + Insert を使用して、それぞれコピーと貼り付けを実行します。

右クリックしてコピー/貼り付けのオプションを使用することもできますが、右クリックの機能はブラウザー固有のクリップボード アクセスに依存します。

## <a name="editing-bashrc"></a>.bashrc の編集
.bashrc を編集すると Cloud Shell で予期しないエラーが発生する可能性があるため、編集には注意が必要です。

## <a name="bashhistory"></a>.bash_history
bash コマンドの履歴は、Cloud Shell セッションの中断または同時セッションにより、一致していない場合があります。

## <a name="usage-limits"></a>Usage limits (使用状況の制限)
Cloud Shell は対話型のユース ケースを想定しています。 そのため、実行時間の長い非対話型セッションは、警告なしで終了します。

## <a name="network-connectivity"></a>ネットワーク接続
Cloud Shell での待ち時間はローカル インターネット接続の影響によるもので、Cloud Shell は送信されるすべての命令の実行を引き続き試行します。

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md)

