---
title: BAM ファイルの入力を使用したワークフローの送信
titleSuffix: Microsoft Genomics
description: この記事では、入力ファイルが単一の BAM ファイルである場合に、Microsoft Genomics サービスにワークフローを送信する方法を示します。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 5145aa0ffdc4095f178a214f63433e5bcece83b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "72249164"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>BAM ファイルの入力を使用したワークフローの送信

この記事では、入力ファイルが単一の BAM ファイルである場合に、Microsoft Genomics サービスにワークフローを送信する方法を示します。 このトピックでは、`msgen` クライアントを既にインストールして実行していること、また、Azure Storage の使用方法をよく理解していることを前提とします。 提供されたサンプル データを使用してワークフローを正常に送信したら、この記事を読み進める準備は完了です。 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>設定: Azure Storage に BAM ファイルをアップロードする
単一の BAM ファイル *reads.bam* を保持しており、これを Azure にあるお使いのストレージ アカウント *myaccount* に **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>** としてアップロード済みであることを前提としましょう。 API URL とアクセス キーは用意されています。 また、**https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>** に出力を配置したいと考えています。



## <a name="submit-your-job-to-the-msgen-client"></a>ジョブを `msgen` クライアントに送信する 


`msgen` クライアントに提供する必要がある最低限の引数セットを以下に示します (改行は、わかりやすくするために追加されています)。

Windows の場合:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Unix の場合:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


構成ファイルの使用を好む場合、構成ファイルには以下の項目が含まれます。

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

`msgen submit -f config.txt` という呼び出しを使って、`config.txt` ファイルを送信します。

## <a name="next-steps"></a>次のステップ
この記事では、BAM ファイルを Azure Storage にアップロードして、`msgen` Python クライアント経由で Microsoft Genomics サービスにワークフローを送信しました。 ワークフローの送信と Microsoft Genomics サービスで使用できるその他のコマンド関連の詳細情報については、[FAQ](frequently-asked-questions-genomics.md) に関するページをご覧ください。 
