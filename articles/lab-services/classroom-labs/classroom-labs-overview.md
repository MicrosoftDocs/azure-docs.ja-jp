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
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 964f5624a3a588a1520a08365c5fa9c783114e48
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958832"
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

## <a name="next-steps"></a>次の手順
Azure Lab Services を使用してクラスルーム ラボを作成するために必要なラボ アカウントを設定します。

- [ラボ アカウントを設定する](tutorial-setup-lab-account.md)
