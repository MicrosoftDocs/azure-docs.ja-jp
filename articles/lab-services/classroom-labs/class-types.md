---
title: Azure Lab Services における各種クラスの例 | Microsoft Docs
description: Azure Lab Services を使用してラボを設定できる、いくつかの種類のクラスを提供します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 0e8b203240b4b2d6b67534ab52a7cd4ccf5df571
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976592"
---
# <a name="class-types-overview---azure-lab-services"></a>クラスの種類の概要 - Azure Lab Services
Azure Lab Services を使用すると、クラウド内にクラスルーム ラボ環境をすばやく設定することができます。 このセクションの記事では、Azure Lab Services を使用していくつかの種類のクラスルーム ラボを設定する方法に関するガイダンスを提供します。

## <a name="deep-learning-in-natural-language-processing"></a>自然言語処理でのディープ ラーニング
Azure Lab Services を使用した自然言語処理 (NLP) でのディープ ラーニングに重点を置いたラボを設定できます。 自然言語処理 (NLP) は、コンピューターで翻訳、音声認識、その他の言語認識機能を実行できるようにする、人工知能 (AI) の一種です。 NLP の授業を受けている学生は、Linux 仮想マシン (VM) を使用し、ニューラル ネットワーク アルゴリズムを適用して、書かれた人間の言語を分析するために使用されるディープ ラーニング モデルを開発する方法を学習します。 

この種類のラボの設定方法の詳細については、「[Azure Lab Services を使用した自然言語処理でのディープ ラーニングに重点を置いたラボを設定する](class-type-deep-learning-natural-processing.md)」を参照してください。

## <a name="shell-scripting-on-linux"></a>Linux でのシェル スクリプト
Linux でのシェル スクリプトの作成を教えるためのラボを設定できます。 スクリプトの作成は、管理者がタスクを繰り返さなくて済むようにできる、システム管理の役に立つ部分です。 このサンプル シナリオのクラスでは、従来の bash スクリプトと拡張スクリプトについて説明します。 拡張スクリプトは、bash コマンドと Ruby を組み合わせたスクリプトです。 このアプローチにより、Ruby でデータを渡すことができ、bash コマンドでシェルと対話することができます。 

これらのスクリプト作成のクラスを受講する学生は、Linux 仮想マシンを使用して、Linux の基本を学習し、bash シェル スクリプトの作成にも慣れることができます。 Linux 仮想マシンには、有効になったリモート デスクトップ アクセスが付属しており、[gedit](https://help.gnome.org/users/gedit/stable/) と [Visual Studio Code](https://code.visualstudio.com/) テキスト エディターがインストールされています。

この種類のラボを設定する方法の詳細については、[Linux でのシェル スクリプト作成](class-type-shell-scripting-linux.md)に関するページを参照してください。

## <a name="ethical-hacking"></a>倫理的ハッキング 
エシカル ハッキングのフォレンジクス面に焦点を当てたクラスのためのラボを設定できます。 侵入テストは、悪意のある攻撃者が利用する可能性のある脆弱性を実証するためにシステムまたはネットワークへの侵入を試みる訓練で、エシカル ハッキング コミュニティで使用します。 

エシカル ハッキング クラスの学生は、脆弱性に対する防御のための最新の手法を学習できます。 各学生は、2 つの入れ子になった仮想マシンを持つ Windows Server ホスト仮想マシンを用意し、一方の仮想マシンは **Metaspoiltable** イメージ、もう一方のマシンは [Kali Linux](https://www.kali.org/) イメージを使用します。 Metasploitable 仮想マシンは悪用目的に使用し、Kali 仮想マシンでは、フォレンジック タスクの実行に必要なツールを利用できます。

この種類のラボを設定する方法の詳細については、「[エシカル ハッキング クラスを教えるためのラボを設定する](class-type-ethical-hacking.md)」を参照してください。

## <a name="next-steps"></a>次の手順
次の記事を参照してください。 

- [Azure Lab Services を使用した自然言語処理でのディープ ラーニングに重点を置いたラボを設定する](class-type-deep-learning-natural-processing.md)
- [Linux でのシェル スクリプト](class-type-shell-scripting-linux.md)