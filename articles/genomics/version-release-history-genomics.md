---
title: バージョン リリース履歴
titleSuffix: Microsoft Genomics
description: 修正プログラムおよび新しい機能に関する Microsoft Genomics Python クライアントの更新のリリース履歴。
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "76991084"
---
# <a name="version-release-history"></a>バージョン リリース履歴
Microsoft Genomics チームは、修正プログラムおよび新しい機能のために Microsoft Genomics Python クライアントを更新します。 

## <a name="latest-release"></a>最新リリース
現在の Python クライアントは、バージョン 0.9.0 です。 これは 2019 年 2 月 6 日にリリースされ、GATK 3.5 および GATK4 を使用したワークフローの実行がサポートされています。 gVCF 出力がサポートされ、オプションの引数を使用して出力圧縮を行うことができます。


## <a name="release-history"></a>リリース履歴 
Microsoft Genomics Python クライアントの新しいバージョンは、ほぼ年に 1 回リリースされます。 Microsoft Genomics Python クライアントの新しいバージョンがリリースされると、このページの修正プログラムと機能の一覧が更新されます。 新しいバージョンがリリースされると、以前のバージョンは少なくとも 90 日間、引き続きサポートされます。 以前のバージョンがサポートされなくなった場合は、このページで告知されます。 

### <a name="version-090"></a>バージョン 0.9.0
バージョン 0.9.0 には、出力圧縮のサポートが含まれています。 これは、vcf または gvcf の出力で `-bgzip` に続けて `-tabix` を実行するのと同じです。 詳細については、[よく寄せられる質問](frequently-asked-questions-genomics.md)をご覧ください。 

### <a name="version-081"></a>バージョン 0.8.1
バージョン 0.8.1 には、軽微なバグの修正が含まれています。  

### <a name="version-080"></a>バージョン 0.8.0
バージョン 0.8.0 には、GATK4 と gVCFs 出力のサポートが含まれています。  

### <a name="version-074"></a>バージョン 0.7.4
バージョン 0.7.4 には、`config.txt` の入力で、アカウント キーの代わりに SAS トークンを受け入れることのサポートが含まれています。 詳細については、[SAS トークン入力のクイック スタート](quickstart-input-sas.md)に関する記事を参照してください。 

### <a name="version-073"></a>バージョン 0.7.3
バージョン 0.7.3 には、軽微なバグの修正が含まれています。

### <a name="version-072"></a>バージョン 0.7.2
バージョン 0.7.2 は初期バージョンです。 2017 年 11 月 1 日にリリースされました。
