---
title: 仮想マシンの概要
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 仮想マシンとその利点について学習します。 Azure portal から VMware 仮想マシンを管理できます。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141978"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 仮想マシンの概要

CloudSimple では、Azure portal から VMware 仮想マシン (VM) を管理できます。  クラスターまたは vSphere クラスターのリソース プールは、サブスクリプションにマップすることによって Azure を介して管理されます。

Azure から CloudSimple VM を作成するには、VM テンプレートがお使いのプライベート クラウド vCenter に存在している必要があります。  テンプレートは、オペレーティング システムとアプリケーションをカスタマイズするために使用されます。  テンプレート VM は、企業のセキュリティ ポリシーを満たすように強化することができます。  テンプレートを使用して VM を作成し、セルフサービス モデルを使用して Azure portal から VM を使用することができます。

## <a name="benefits"></a>メリット

Azure portal から利用できる CloudSimple 仮想マシンは、ユーザーが VMware 仮想マシンを作成および管理するためのセルフサービス メカニズムを提供します。

* プライベート クラウド vCenter に CloudSimple VM を作成する
* VM プロパティを管理する
  * ディスクを追加/削除する
  * NIC を追加/削除する
* CloudSimple VM の電源操作
  * 電源オンと電源オフ
  * VM をリセットする
* VM を削除する

## <a name="next-steps"></a>次の手順

* [Azure で VMware VM を使用する](quickstart-create-vmware-virtual-machine.md)方法を学習する
* [Azure サブスクリプションをマップする](azure-subscription-mapping.md)方法を学習する
