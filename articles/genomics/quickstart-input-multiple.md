---
title: 複数の入力を使用するワークフローを送信する
titleSuffix: Microsoft Genomics
description: この記事では、入力ファイルが同じサンプルの複数の FASTQ ファイルまたは BAM ファイルである場合に、Microsoft Genomics サービスにワークフローを送信する方法を示します。
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: b426015906a8e17674123c0c3ad2fccb9c43798f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "72248560"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>同じサンプルに属する複数の入力を使用するワークフローを送信する

この記事では、入力ファイルが、**同じサンプルに属する** 複数の FASTQ ファイルまたは BAM ファイルである場合に、Microsoft Genomics サービスにワークフローを送信する方法を示します。 たとえば DNA 解析装置上の複数のレーンで **同じサンプル** を実行した場合、レーンごとに一対の FASTQ ファイルが装置から出力される可能性があります。 これらの FASTQ ファイルを連結してから配列およびバリアントの呼び出しを行う代わりに、これらすべての入力を `msgen` クライアントに直接送信することができます。 この場合、`msgen` クライアントからの出力は、.bam、.bai、.vcf を含んだ **単一のファイル群** となることが考えられます。 

ただし、FASTQ ファイルと BAM ファイルを同じ送信に混在させることは **できません**。 また、複数の個体から得た複数の FASTQ (または BAM) ファイルを送信することは **できません**。 

この記事では、`msgen` クライアントを既にインストールして実行していること、また、Azure Storage の使用方法をよく理解していることを前提とします。 提供されたサンプル データを使用してワークフローを正常に送信したら、この記事を読み進める準備は完了です。 


## <a name="multiple-bam-files"></a>複数の BAM ファイル

### <a name="upload-your-input-files-to-azure-storage"></a>Azure Storage に入力ファイルをアップロードする
入力として使用する BAM ファイルが複数あり (*reads.bam*、*additional_reads.bam*、*yet_more_reads.bam*)、それらを Azure のストレージ アカウントである *myaccount* にアップロード済みであるとします。 API URL とアクセス キーは用意されています。 また、**https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>** に出力を配置したいと考えています。


### <a name="submit-your-job-to-the-msgen-client"></a>ジョブを `msgen` クライアントに送信する 

--input-blob-name-1 引数にすべての BAM ファイルの名前を渡すことで、複数の BAM ファイルを送信できます。 すべてのファイルが同じサンプルに属している必要がありますが、ファイルの順序は重要ではありません。 次のセクションでは、Windows と Unix のコマンド ライン、および構成ファイルを使ったサンプルの送信について詳しく説明します。 わかりやすくするために改行が追加されています。


Windows の場合:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
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
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

`msgen submit -f config.txt` という呼び出しを使って、`config.txt` ファイルを送信します。


## <a name="multiple-paired-fastq-files"></a>複数の FASTQ ファイルのペア

### <a name="upload-your-input-files-to-azure-storage"></a>Azure Storage に入力ファイルをアップロードする
入力として使用する FASTQ ファイルのペアが複数ある (*reads_1.fq.gz* と *reads_2.fq.gz*、*additional_reads_1.fq.gz* と *additional_reads_2.fq.gz*、*yet_more_reads_1.fq.gz* と *yet_more_reads_2.fq.gz*) とします。 それらを Azure のストレージ アカウントである *myaccount* にアップロード済みで、API URL とアクセス キーは用意されています。 また、**https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>** に出力を配置したいと考えています。


### <a name="submit-your-job-to-the-msgen-client"></a>ジョブを `msgen` クライアントに送信する 

FASTQ ファイルのペアは同じサンプルに属しているだけでなく、一緒に処理する必要があります。  ファイル名の順序は、--input-blob-name-1 と --input-blob-name-2 に引数として渡される場合に重要になります。 

次のセクションでは、Windows と Unix のコマンド ライン、および構成ファイルを使ったサンプルの送信について詳しく説明します。 わかりやすくするために改行が追加されています。


Windows の場合:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
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
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


構成ファイルの使用を好む場合、構成ファイルには以下の項目が含まれます。

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

`msgen submit -f config.txt` という呼び出しを使って、`config.txt` ファイルを送信します。

## <a name="next-steps"></a>次のステップ
この記事では、複数の BAM ファイルまたは FASTQ ファイルのペアを Azure Storage にアップロードして、`msgen` Python クライアント経由で Microsoft Genomics サービスにワークフローを送信しました。 ワークフローの送信と Microsoft Genomics サービスで使用できるその他のコマンド関連の詳細情報については、[FAQ](frequently-asked-questions-genomics.md) に関するページをご覧ください。 