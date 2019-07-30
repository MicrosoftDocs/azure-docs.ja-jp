---
title: HPC アプリケーションのスケール - Azure Virtual Machines | Microsoft Docs
description: Azure VM 上で HPC アプリケーションをスケールする方法について説明します。
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707837"
---
# <a name="scaling-hpc-applications"></a>HPC アプリケーションのスケール

Azure 上の HPC アプリケーションの最適なスケールアップとスケールアウトのパフォーマンスには、特定のワークロードに対するパフォーマンスの調整と最適化の実験が必要です。 このセクションと VM シリーズ固有のページでは、アプリケーションをスケールするための一般的なガイダンスについて説明します。

## <a name="compiling-applications"></a>アプリケーションのコンパイル

必須ではありませんが、適切な最適化フラグを使用してアプリケーションをコンパイルすると、HB および HC シリーズの VM で最高のスケールアップ パフォーマンスを達成できます。

### <a name="amd-optimizing-cc-compiler"></a>AMD Optimizing C/C++ Compiler

AMD Optimizing C/C++ Compiler (AOCC) コンパイラ システムは、グローバルな最適化、ベクトル化、プロシージャ間の分析、ループ変換、コード生成を含む高度な最適化、マルチスレッド、およびプロセッサのサポートを提供しています。 AOCC コンパイラ バイナリは、GNU C ライブラリ (glibc) バージョン 2.17 以降がインストールされた Linux システムに適しています。 コンパイラ スイートは、C/C++ コンパイラ (clang)、Fortran コンパイラ (FLANG)、および Clang への Fortran フロント エンド (Dragon Egg) で構成されています。

### <a name="clang"></a>Clang

Clang は、前処理、解析、最適化、コード生成、アセンブリ、およびリンクを処理する C、C++、および Objective-C のコンパイラです。 Clang は、AMD の Zen ベースの x86 アーキテクチャに最適なコード生成と調整を行うことができる `-march=znver1` フラグをサポートしています。

### <a name="flang"></a>FLANG

FLANG コンパイラは、AOCC スイートに最近追加された (2018 年 4 月に追加された) ものであり、現在は開発者がダウンロードしてテストするためのプレリリース段階です。 AMD では Fortran 2008 に基づいて GitHub バージョンの FLANG (https://github.com/flangcompiler/flang) ) が拡張されます。 FLANG コンパイラは、すべての Clang コンパイラ オプションとその他の FLANG 固有のコンパイラ オプションをサポートしています。

### <a name="dragonegg"></a>DragonEgg

DragonEgg は GCC のオプティマイザーとコード ジェネレーターを LLVM プロジェクトのものと置き換える gcc プラグインです。 AOCC に付属する DragonEgg は gcc-4.8.x で動作し、x86-32/x86-64 ターゲット向けにテストされており、さまざまな Linux プラットフォームで正常に使用されています。

GFortran は、GCC GIMPLE 中間表現 (IR) を生成する前処理、解析、およびセマンティック分析を担当する Fortran プログラムの実際のフロントエンドです。 DragonEgg は、GFortran コンパイル フローにプラグインする GNU プラグインです。 これには GNU プラグイン API が実装されています。 このプラグイン アーキテクチャでは、DragonEgg がコンパイラ ドライバーになり、コンパイルのさまざまな段階が推進されます。  ダウンロードとインストールの手順を実行した後は、次を使用して Dragon Egg を呼び出すことができます。 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI コンパイラ
PGI Community Edition バージョン 17 は AMD EPYC で動作することが確認されています。 PGI コンパイル バージョンの STREAM は、プラットフォームの全メモリ帯域幅を提供します。 新しい Community Edition 18.10 (2018 年 11 月) も同様に機能するはずです。 以下は Intel コンパイラで最適にコンパイルすることができるサンプル CLI です。

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel コンパイラ
Intel コンパイラ バージョン 18 は AMD EPYC で動作することが確認されています。 以下は Intel コンパイラで最適にコンパイルすることができるサンプル CLI です。

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC コンパイラ 
HPC の場合、AMD は GCC コンパイラ 7.3 以降を推奨します。 RHEL/CentOS 7.4 に含まれる 4.8.5 などの古いバージョンは推奨されません。 GCC 7.3 以降では、HPL、HPCG、および DGEMM テストで非常に高いパフォーマンスを達成できます。

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>アプリケーションのスケーリング 

次の提案は、最適なアプリケーションのスケーリング効率、パフォーマンス、および整合性に適用されます。

* (自動バランス アプローチとは対照的に) シーケンシャルなピン留めアプローチを使用して、コア 0-59 にプロセスをピン留めします。 
* Numa/Core/HwThread によるバインディングは、既定のバインディングよりも優れています。
* ハイブリッドの並列アプリケーション (OpenMP + MPI) の場合は、CCX あたり 4 スレッドと 1 つの MPI ランクを使用します。
* 純粋な MPI アプリケーションの場合は、最適なパフォーマンスを得るために CCX ごとに 1-4 MPI ランクで実験します。
* メモリ帯域幅の影響を非常に受けやすいアプリケーションでは、CCX あたりのコア数を減らしてメリットが得られる場合があります。 これらのアプリケーションでは、CCX あたり 3 コアまたは 2 コアを使用してメモリ帯域幅の競合を減らし、実際の環境でより高いパフォーマンスや、より一貫したスケーラビリティを実現できます。 特に、MPI Allreduce はここからメリットが得られる可能性があります。
* 非常に大規模な実行の場合、UD またはハイブリッド RC + UD トランスポートを使用することをお勧めします。 多くの MPI ライブラリ/ランタイム ライブラリでは、これが内部的に実行されます (UCX や MVAPICH2 など)。 大規模な実行の場合は、トランスポート構成を確認します。

## <a name="next-steps"></a>次の手順

Azure での [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) の詳細を確認する。
