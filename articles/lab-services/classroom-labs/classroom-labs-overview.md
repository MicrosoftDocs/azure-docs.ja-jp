---
title: Azure Lab Services の概要 | Microsoft Docs
description: Lab Services を利用すると、開発者、テスト担当者、教育者、学生、その他のユーザーが使用できるラボを仮想マシンで簡単に作成、管理、セキュリティ保護できることを説明します。
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
ms.openlocfilehash: 67ebce2130bf5594d3c8c671934f7141a02eb60d
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694668"
---
# <a name="introduction-to-classroom-labs"></a>クラスルーム ラボの概要
Azure Lab Services を使用すると、クラウド内にクラスルーム ラボ環境をすばやく設定することができます。 教師はクラスルーム ラボを作成し、Windows または Linux 仮想マシンをプロビジョニングし、必要なソフトウェアとツール ラボをクラスにインストールし、学生が利用できるようにします。 クラスの学生はラボの仮想マシン (VM) に接続し、プロジェクト、課題、教室での演習に使用します。 

クラスルーム ラボは、Azure によって管理されているマネージド ラボの種類です。 仮想マシン (VM) の作成から、エラーの処理やインフラストラクチャのスケーリングまで、マネージド ラボの種類用のインフラストラクチャの管理はすべてサービス自体が担当します。 ユーザーは、必要なインフラストラクチャの種類を指定し、クラスに必要なツールやソフトウェアをインストールします。 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Azure のインフラストラクチャとスケールの自動管理 
Azure Lab Services はマネージド サービスです。つまり、ラボの基盤となるインフラストラクチャのプロビジョニングと管理が、サービスによって自動的に処理されます。 管理者は、ユーザーにとって適切なラボ体験を準備することだけに集中できます。 その他の処理と、対象者に対するラボの仮想マシンのロールアウトはサービスにお任せください。 クリック 1 つで、ラボを数百もの仮想マシンにスケーリングできます。

## <a name="simple-experience-for-your-lab-users"></a>ラボ ユーザーのためのシンプルなエクスペリエンス 
ラボに招待されたユーザーは、ラボ内で付与されたリソースに即座にアクセスできます。 ユーザーに必要なのは、サインインして、複数のラボにまたがってアクセス権を持つ仮想マシンの全リストを表示することだけです。 1 つのボタンをクリックするだけで、仮想マシンに接続して作業を開始できます。 ユーザーは Azure サブスクリプションがなくても、このサービスを使用できます。 

## <a name="cost-optimization-and-tracking"></a>コストの最適化と追跡  
ラボ ユーザーが仮想マシンを使用できる時間数を正確に制御することによって、予算を抑えることができます。 ラボでのスケジュールを設定して、指定した時間帯だけユーザーに仮想マシンの使用を許可したり、自動シャットダウンと起動の時刻が繰り返されるように設定したりできます。 個々のユーザーの使用量を追跡して制限を設定してください。

## <a name="example-class-types"></a>各種クラスの例
Azure Lab Services を使用すると、さまざまな種類のクラスを対象としたラボをセットアップできます。 Azure Lab Services でラボをセットアップできる各種クラスの例については、[Azure Lab Services における各種クラスの例](class-types.md)の記事を参照してください。 

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してクラスルーム ラボを作成するために必要なラボ アカウントを設定します。

- [ラボ アカウントを設定する](tutorial-setup-lab-account.md)
