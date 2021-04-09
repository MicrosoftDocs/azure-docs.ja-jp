---
title: トラブルシューティング ガイド
titleSuffix: Microsoft Genomics
description: エラー メッセージとその解決方法など、Microsoft Genomics を使用するためのトラブルシューティング方法について学習します。
keywords: トラブルシューティング, エラー, デバッグ
services: genomics
author: ruchir
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: c508c10d619cde1a16d89b446c5cfd1a3ce81daf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023825"
---
# <a name="troubleshooting-guide"></a>トラブルシューティング ガイド

Microsoft Genomics サービス MSGEN の使用時に直面する可能性がある一般的な問題のトラブルシューティングのヒントをいくつか以下に示します。

 トラブルシューティングに関連しない FAQ については、[よく寄せられる質問](frequently-asked-questions-genomics.md)のページを参照してください。
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>手順 1: ワークフローに関連付けられたエラー コードを見つける

次を行うことによって、ワークフローに関連付けられたエラー メッセージを見つけることができます。

1. コマンド ライン ツールを使用して、`msgen status` と入力します
2. standardoutput.txt の内容を調べます。

### <a name="1-using-the-command-line-msgen-status"></a>1.コマンドライン `msgen status` を使用する

```bash
msgen status -u URL -k KEY -w ID 
```




必須の引数が 3 つあります。

* URL - API のベース URI
* KEY - Genomics アカウントのアクセス キー
    * URL と KEY を確認するには、Azure Portal に移動して、Microsoft Genomics アカウント ページを開きます。 **[管理]** 見出しの下の **[アクセス キー]** を選択します。 ここに、API URL とアクセス キーの両方が表示されます。

  
* ID - ワークフロー ID
    * ワークフロー ID を検索するには、`msgen list` コマンドを入力します。 構成ファイルが、URL とアクセス キーを格納し、msgen exe と同じ場所にあるものと仮定すると、コマンドは次のようになります。 
        
        ```bash
        msgen list -f "config.txt"
        ```
        このコマンドからの出力は、次のようになります。
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  また、URL と KEY を直接入力する代わりに、構成ファイルのパスを含めることもできます。 これらの引数をコマンド ラインと構成ファイルに含めた場合、コマンド ラインの引数が優先されます。  

ワークフロー ID 1001 で、config.txt ファイルが msgen 実行可能ファイルと同じパスに配置されている場合、コマンドは次のようになります。

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2.Standardoutput.txt の内容を調べる 
問題のワークフローの出力コンテナーを見つけます。 MSGEN によって、各ワークフローの実行後に、`[workflowfilename].logs.zip` フォルダーが作成されます。 フォルダーを展開してその内容を表示します。

* outputFileList.txt - ワークフロー中に生成された出力ファイルの一覧
* standarderror.txt - このファイルは空です。
* standardoutput.txt - ワークフローの実行中に発生したエラーなど、すべての最上位レベルのステータス メッセージが記録されます。
* GATK ログ ファイル - `logs` フォルダー内の他のすべてのファイル

トラブルシューティングでは、standardoutput.txt の内容を調べて、表示されるすべてのエラー メッセージを書き留めます。


## <a name="step-2-try-recommended-steps-for-common-errors"></a>手順 2: 一般的なエラーについて推奨される手順を試す

このセクションでは、Microsoft Genomics サービス (msgen) による一般的なエラー出力と、それらを解決するために使用できる方法について簡単に説明します。 

Microsoft Genomics サービス (msgen) では、次の 2 種類のエラーがスローされる可能性があります。

1. 内部サービス エラー: パラメーターや入力ファイルを修正して解決されない可能性があるサービス内部のエラー。 ワークフローを再送信すると、これらのエラーが修正される可能性があります。
2. 入力エラー: 正しい引数を使用するか、ファイル形式を修正して解決できるエラー。

### <a name="1-internal-service-errors"></a>1.内部サービス エラー

内部サービス エラーは、ユーザーが対処可能なエラーではありません。 ワークフローを再送信できますが、それで機能しない場合は、Microsoft Genomics サポートにお問い合わせください。

| エラー メッセージ                                                                                                                            | 推奨されるトラブルシューティングの手順                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 内部エラーが発生しました。 Try resubmitting the workflow. If you see this error again, contact Microsoft Genomics support for assistance\(内部エラーが発生しました。ワークフローを再送信してみてください。このエラーが再度表示される場合は、Microsoft Genomics サポートにお問い合わせください\) | ワークフローを再送信します。 問題が解決しない場合は、サポート [チケット](file-support-ticket-genomics.md )を作成して、Microsoft Genomics サポートにお問い合わせください。 |

### <a name="2-input-errors"></a>2.入力エラー

これらのエラーは、ユーザーが対処可能です。 ファイル、およびエラー コードの種類に基づいて、Microsoft Genomics サービスが、個別のエラー コードを出力します。 以下に示す推奨されるトラブルシューティングの手順に従います。

| ファイルの種類 | エラー コード | エラー メッセージ                                                                           | 推奨されるトラブルシューティングの手順                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Any          | 701        | Read [readId] has [numberOfBases] bases, but the limit is [maxReadLength]\(読み取り [readId] に [numberOfBases] ベースありますが、制限は [maxReadLength] です\)           | このエラーの最も一般的な理由は、2 つの読み取りの連結につながるファイルの破損です。 入力ファイルを確認してください。 |
| BAM          | 200        |   ファイル '[yourFileName]' を読み取ることができません。                                                                                       | BAM ファイルの形式を確認してください。 正しく書式設定されたファイルを使用して、ワークフローを再送信します。                                                                           |
| BAM          | 201        |  Unable to read BAM file [File_name].                                                                                      |BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                            |
| BAM          | 202        | Unable to read BAM file [File_name]. File too small and missing header.\(BAM ファイル [File_name] を読み取ることができません。ファイルが小さすぎ、ヘッダーがありません。\)                                                                                        | BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                            |
| BAM          | 203        |   Unable to read BAM file [File_name]. Header of file was corrupt.\(BAM ファイル [File_name] を読み取ることができません。ファイルのヘッダーが破損しています。\)                                                                                      |BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                           |
| BAM          | 204        |    Unable to read BAM file [File_name]. Header of file was corrupt.\(BAM ファイル [File_name] を読み取ることができません。ファイルのヘッダーが破損しています。\)                                                                                     | BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                           |
| BAM          | 205        |    Unable to read BAM file [File_name]. Header of file was corrupt.\(BAM ファイル [File_name] を読み取ることができません。ファイルのヘッダーが破損しています。\)                                                                                     | BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                            |
| BAM          | 206        |   Unable to read BAM file [File_name]. Header of file was corrupt.\(BAM ファイル [File_name] を読み取ることができません。ファイルのヘッダーが破損しています。\)                                                                                      | BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                            |
| BAM          | 207        |  Unable to read BAM file [File_name]. File truncated near offset [offset].\(BAM ファイル [File_name] を読み取ることができません。ファイルはオフセット [offset] 付近で切り詰められています。\)                                                                                       | BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                            |
| BAM          | 208        |   Invalid BAM file. The ReadID [Read_Id] has no sequence in file [File_name].\(BAM ファイルが無効です。ファイル [File_name] 内の ReadID [Read_Id] にシーケンスがありません。\)                                                                                      | BAM ファイルの形式を確認してください。  正しく書式設定されたファイルを使用して、ワークフローを送信します。                                                                             |
| FASTQ        | 300        |  Unable to read FASTQ file. [File_name] doesn't end with a newline.\(FASTQ ファイルを読み取ることができません。[File_name] が改行で終わっていません。\)                                                                                     | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                           |
| FASTQ        | 301        |   Unable to read FASTQ file [File_name]. FASTQ record is larger than buffer size at offset: [_offset]\(FASTQ ファイル [File_name] を読み取ることができません。FASTQ レコードがオフセット: [_offset] でバッファー サイズより大きくなっています\)                                                                                      | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                         |
| FASTQ        | 302        |     FASTQ Syntax error. File [File_name] has a blank line.\(FASTQ 構文エラー。ファイル [File_name] には空白行があります。\)                                                                                    | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                         |
| FASTQ        | 303        |       FASTQ Syntax error. File[File_name] has an invalid starting character at offset: [_offset],  line type: [line_type], character: [_char]\(FASTQ 構文エラー。ファイル [File_name] には、オフセット: [_offset]、行タイプ: [line_type]、文字: [_char] に無効な開始文字があります\)                                                                                  | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                         |
| FASTQ        | 304      |  FASTQ Syntax error at readID [_ReadID].  First read of batch doesn’t have readID ending in /1 in file [File_name]\(readID [_ReadID] での FASTQ 構文エラー。バッチの最初の読み取りでファイル [File_name] 内に /1 で終わる readID がありません\)                                                                                       | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                         |
| FASTQ        | 305        |  FASTQ Syntax error at readID [_readID]. Second read of batch doesn’t have readID ending in /2 in file [File_name]\(readID [_ReadID] での FASTQ 構文エラー。バッチの 2 番目の読み取りでファイル [File_name] 内に /2 で終わる readID がありません\)                                                                                      | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                          |
| FASTQ        | 306        |  FASTQ Syntax error at readID [_ReadID]. First read of pair doesn’t have an ID that ends in /1 in file [File_name]\(readID [_ReadID] での FASTQ 構文エラー。ペアの最初の読み取りでファイル [File_name] 内に /1 で終わる ID がありません\)                                                                                       | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                          |
| FASTQ        | 307        |   FASTQ Syntax error at readID [_ReadID]. ReadID doesn’t end with /1 or/2. File [File_name] can't be used as a paired FASTQ file.\(readID [_ReadID] での FASTQ 構文エラー。ReadID が /1 または /2 で終わっていません。ファイル [File_name] をペアの FASTQ ファイルとして使用できません。\)                                                                                      |FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                          |
| FASTQ        | 308        |  FASTQ read error. Reads of both ends responded differently. Did you choose the correct FASTQ files?\(FASTQ 読み取りエラー。両端の読み取りの応答が異なります。正しい FASTQ ファイルを選択しましたか?\)                                                                                       | FASTQ ファイルの形式を修正して、ワークフローを再送信します。                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>手順 3: Microsoft Genomics サポートに問い合わせる

ジョブのエラーが解決しない場合や、その他の質問がある場合は、Azure Portal の Microsoft Genomics サポートに問い合わせてください。 サポート要求を送信する方法の詳細については、[こちら](file-support-ticket-genomics.md)を参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Genomics サービスに関する一般的な問題のトラブルシューティングと解決方法を学びました。 詳細情報とその他の一般的な FAQ については、「[よく寄せられる質問](frequently-asked-questions-genomics.md)」を参照してください。 
