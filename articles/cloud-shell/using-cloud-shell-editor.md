---
title: Azure Cloud Shell エディターの使用 | Microsoft Docs
description: Azure Cloud Shell エディターの使用方法の概要。
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832337"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Azure Cloud Shell エディターの使用

Azure Cloud Shell には、オープンソース [Monaco Editor](https://github.com/Microsoft/monaco-editor) から構築された統合ファイル エディターが含まれています。 Cloud Shell エディターでは、言語の強調表示、コマンド パレット、ファイル エクスプローラーなどの機能をサポートしています。

![Cloud Shell エディター](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>ファイルを開く

単純なファイルの作成および編集の場合は、Cloud Shell ターミナルで `code .` を実行することで、エディターを起動します。 この操作により、ターミナルでエディターが開きアクティブな作業ディレクトリが設定されます。

編集を迅速に行うためにファイルを直接開くには、ファイル エクスプローラーを通さず、`code <filename>` を実行してエディターを開きます。

UI ボタンを使用してエディターを開くには、ツールバーで `{}` エディター アイコンをクリックします。 これにより、エディターが開き、既定ではファイル エクスプローラーが開き `/home/<user>` ディレクトリが表示されます。

## <a name="closing-the-editor"></a>エディターを閉じる

エディターを閉じるには、エディターの右上にある [`...`] アクション パネルを開き、[`Close editor`] を選択します。

![エディターを閉じる](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>コマンド パレット

コマンド パレットを起動するには、エディターにフォーカスが設定されたときに `F1` キーを押します。 アクション パネルからコマンド パレットを開くこともできます。

![コマンド パレット](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Monaco Editor に投稿する

Cloud Shell エディターでの言語強調表示のサポートは、[Monaco Editor](https://github.com/Microsoft/monaco-editor) の Monarch 構文定義の使用ではアップストリーム機能を介してサポートされています。 投稿を行う方法については、[Monaco 共同作成者ガイド](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Cloud Shell で Bash のクイックスタートを試してみる](quickstart.md)
- [統合された Cloud Shell ツールの完全な一覧を確認する](features.md)
