---
title: Azure Virtual Machines での TmaxSoft OpenFrame の概要
description: Azure Virtual Machines (VM) 上の TmaxSoft OpenFrame 環境を使用して、IBM z/OS メインフレーム ワークロードをリホストします。
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950673"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Azure 上の TmaxSoft OpenFrame の概要

既存のメインフレームの資産を、TmaxSoft OpenFrame を使用して Microsoft Azure に移動します。 この一般的なリホスト ソリューションでは、Azure 上にエミュレーション環境が作成されて、アプリケーションをすばやく移行できます。 再フォーマットの必要はありません。

## <a name="openframe-rehosting-environment"></a>OpenFrame のリホスト環境

開発、デモ、テスト、運用ワークロード用の OpenFrame 環境を、Azure 上にセットアップします。 次の図で示すように、OpenFrame には、Azure 上にメインフレーム エミュレーション環境を作成する複数のコンポーネントが含まれています。 たとえば、OpenFrame オンライン サービスでは、IBM Customer Information Control System (CICS) などのインフレーム ミドルウェアが置き換えられます。 OpenFrame Batch とその TJES コンポーネントでは、IBM メインフレームの Job Entry Subsystem (JES) が置き換えられます。 

![OpenFrame のリホスト プロセス](media/openframe-01.png)

> [!NOTE]
> Azure 上で OpenFrame 環境を実行するには、TmaxSoft の有効な製品ライセンスまたは試用版ライセンスが必要です。

## <a name="openframe-components"></a>OpenFrame のコンポーネント

Azure 上の OpenFrame 環境には、次のコンポーネントが含まれます。

- OFMiner を含む **移行ツール**。メインフレームのアセットを分析し、それらを Azure に移行するソリューションです。
- **コンパイラ**。OFCOBOL はメインフレームの COBOL プログラムを解釈するコンパイラであり、OFPLI はメインフレームの PL/I プログラムを解釈し、OFASM はメインフレームのアセンブラー プログラムを解釈するコンパイラです。
- **フロントエンド** コンポーネント。Java Enterprise Edition 6.OFGW に認定されている Web アプリケーション サーバーである Java Enterprise User Solution (JEUS) と、3270 リスナーを提供する OpenFrame ゲートウェイ コンポーネントが含まれます。
- **アプリケーション** 環境。 OpenFrame Base は、システム全体を管理するミドルウェアです。 OpenFrame Server Type C (OSC) は、メインフレームのミドルウェアと IBM CICS を置き換えます。
- **リレーショナル データベース**。Tibero (図)、Oracle Database、Microsoft SQL Server、IBM Db2、MySQL。 OpenFrame アプリケーションは、Open Database Connectivity (ODBC) プロトコルを使ってデータベースと通信します。
- TACF による **セキュリティ**。システムおよびリソースへのユーザー アクセスを制御するサービス モジュールです。 
- **OFManager** は、Web 環境で OpenFrame の運用と管理の機能を提供するソリューションです。

![OpenFrame アーキテクチャ](media/openframe-02.png)

## <a name="next-steps"></a>次のステップ

- [Azure 上に TmaxSoft OpenFrame をインストールする](./install-openframe-azure.md)
