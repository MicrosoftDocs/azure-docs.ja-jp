---
title: チュートリアル - Ansible を使用して Azure の仮想マシン スケール セットを自動的にスケーリングする
description: Ansible を使用して Azure の自動スケール機能で仮想マシン スケール セットをスケーリングする方法について説明します。
keywords: ansible, azure, devops, bash, プレイブック, スケール, 自動スケール, 仮想マシン, 仮想マシン スケール セット, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 784cb532c11b16c820336ceeaf8d38f0225c832f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242108"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>チュートリアル:Ansible を使用して Azure の仮想マシン スケール セットを自動的にスケーリングする

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

VM インスタンスの数を自動的に調整する機能を[自動スケール](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)と呼びます。 自動スケールのメリットは、アプリケーションのパフォーマンスを監視して最適化するための管理上のオーバーヘッドが削減されることです。 自動スケールは、要求に応じて構成することも、定義されたスケジュールで構成することもできます。 Ansible を使用して、肯定的なカスタマー エクスペリエンスを得られる、許容可能なパフォーマンスを定義する自動スケール ルールを指定することができます。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 自動スケール プロファイルの定義
> * 定期的なスケジュールに基づいた自動スケール
> * アプリのパフォーマンスに基づいた自動スケール
> * 自動スケール設定情報の取得 
> * 自動スケール設定の無効化

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>スケジュールに基づいた自動スケール

スケール セットで自動スケールを有効にするには、最初に自動スケール プロファイルを定義します。 このプロファイルでは、スケール セット容量の既定値、最小値、および最大値が定義されます。 これらの制限により、VM インスタンスが継続的に作成されないようにしてコストを制御し、許容されるパフォーマンスと、スケールイン イベントに残るインスタンスの最小数のバランスをとることができます。 

Ansible を使用すると、特定の日付または定期的なスケジュールでスケール セットをスケーリングできます。

このセクションのプレイブック コードでは、毎週月曜日の 10 時 00 分に VM インスタンスの数を 3 つに増やします。

次のプレイブックを `vmss-auto-scale.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>パフォーマンス データに基づく自動スケーリング

アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 Ansible を使用すると、監視するメトリック (CPU 使用率、ディスク使用量、アプリの読み込み時間など) を制御できます。 パフォーマンス メトリックのしきい値、定期的なスケジュール、または特定の日付に基づいて、スケール セットでスケールインやスケールアウトを行うことができます。 

このセクションのプレイブック コードでは、毎週月曜日の 18 時 00 分に過去 10 分間の CPU ワークロードをチェックします。 

このプレイブックでは、CPU 使用率のメトリックに基づいて次のいずれかのアクションを実行します。

- VM インスタンスの数を 4 にスケールアウトする
- VM インスタンスの数を 1 にスケールインする

次のプレイブックを `vmss-auto-scale-metrics.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>自動スケール設定情報の取得 

このセクションのプレイブック コードでは、`azure_rm_autoscale_facts` モジュールを使用して自動スケール設定の詳細を取得します。

次のプレイブックを `vmss-auto-scale-get-settings.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>自動スケール設定の無効化

自動スケール設定を無効にするには 2 つの方法があります。 1 つは `enabled` キーを `true` から `false` に変更する方法です。 もう 1 つは設定を削除する方法です。

このセクションのプレイブック コードでは、自動スケール設定を削除します。 

次のプレイブックを `vmss-auto-scale-delete-setting.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [チュートリアル:Ansible を使用して Azure Virtual Machine Scale Sets のカスタム イメージを更新する](./ansible-vmss-update-image.md)