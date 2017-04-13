<properties
    pageTitle="套用原則 Azure 資源管理員虛擬機器 |Microsoft Azure"
    description="如何套用原則至 Azure 資源管理員 Windows 虛擬機器"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/13/2016"
    ms.author="singhkay"/>

# <a name="apply-policies-to-azure-resource-manager-virtual-machines"></a>套用原則 Azure 資源管理員虛擬機器

藉由使用原則，組織可以強制執行各種慣例與企業的規則。 強制執行的所需的行為，可協助降低風險時提供成功的組織。 本文中，我們會說明如何使用 Azure 資源管理員原則，以及在您組織的虛擬機器中定義所需的行為。

若要完成此步驟的外框做為下方

1. Azure 資源管理員原則 101
2. 針對您的虛擬機器中定義的原則
3. 建立原則
4. 套用原則

## <a name="azure-resource-manager-policy-101"></a>Azure 資源管理員原則 101

開始使用 Azure 資源管理員原則，建議閱讀下列文章，然後再繼續進行本文中的步驟進行。 本文將說明的基本定義和結構的原則，如何取得評估原則，並提供各種不同的原則定義的範例。

* [使用原則管理資源，並控制存取權](../resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>針對您的虛擬機器中定義的原則

企業版的常見案例的其中一個可能只允許建立虛擬機器測試 LOB 應用程式與特定作業系統的使用者。 使用 Azure 資源管理員原則可以幾個步驟完成這項工作。 在此原則範例中，我們要允許僅在 Windows Server 2012 R2 資料中心虛擬機器建立。 原則定義看起來像下面

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

您可以輕鬆地的情況，您可能會想要允許任何 Windows Server 資料中心的圖像做為儲存的虛擬機器部署修改上述原則下方變更

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>虛擬機器屬性欄位

下表說明可做為您的原則定義中的欄位的虛擬機器屬性。 如需原則欄位的詳細資訊，請參閱下列文章︰

* [欄位與來源](../resource-manager-policy.md#fields-and-sources)


| 欄位名稱     | 描述                                        |
|----------------|----------------------------------------------------|
| imagePublisher | 指定圖像的發行者               |
| imageOffer     | 指定所選的圖像 publisher 優惠 |
| imageSku       | 指定所選優惠的 SKU             |
| imageVersion   | 指定選擇的 SKU 的圖像版本     |

## <a name="create-the-policy"></a>建立原則

原則可以輕鬆地建立使用 REST API 直接或的 PowerShell 指令程式。 建立原則，請參閱下列文章︰

* [建立原則](../resource-manager-policy.md#creating-a-policy)


## <a name="apply-the-policy"></a>套用原則

建立原則之後必須將其套用上定義的範圍。 訂閱、 資源群組或甚至資源，可以是範圍。 套用原則，請參閱下列文章︰

* [建立原則](../resource-manager-policy.md#applying-a-policy)
