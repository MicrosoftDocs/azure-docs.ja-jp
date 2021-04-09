---
title: VMware ソフトウェアのバージョン
description: Azure VMware Solution でサポートされている VMware ソフトウェアのバージョン。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462518"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Azure VMware Solution のプライベート クラウドのクラスターの新しいデプロイで使用される VMware ソフトウェアのバージョンは次のとおりです。

| ソフトウェア              |    Version   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6.7 U3    | 
| ESXi                  |    6.7 U3    | 
| vSAN                  |    6.7 U3    |
| NSX-T <br />**注意:** NSX-T が NSX のサポートされている唯一のバージョンです。               |      2.5     |


新しいクラスターが既存のプライベート クラウドに追加され、現在実行中のソフトウェア バージョンが適用されます。 詳細については、[VMware ソフトウェアの要件](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html)に関するページを参照してください。

プライベート クラウド ソフトウェア バンドルのアップグレードは、VMware のソフトウェア バンドルの最新リリースの単一バージョン内でソフトウェアを保持します。 プライベート クラウド ソフトウェアのバージョンは、個々のソフトウェア コンポーネント (ESXi、NSX-T、vCenter、vSAN) の最新バージョンとは異なる場合があります。 Azure VMware Solution プラットフォーム ソフトウェアの一般的なアップグレード ポリシーおよびプロセスについては、[プライベート クラウドの更新とアップグレード](../concepts-upgrades.md)に関する記事に記載されています。

