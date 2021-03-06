---
title: "虛擬機器的多個 IP 位址 - PowerShell | Microsoft Docs"
description: "了解如何使用 PowerShell 對虛擬機器指派多個 IP 位址 | Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 11e490ee3b2d2f216168e827154125807a61a73f
ms.openlocfilehash: 39b45b276c50922878e9918b225f6fa399d42edf


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>使用 PowerShell 對虛擬機器指派多個 IP 位址

> [!div class="op_single_selector"]
> * [入口網站](virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
> * [CLI](virtual-network-multiple-ip-addresses-cli.md)
>

Azure 虛擬機器 (VM) 可附加一或多個網路介面 (NIC)。 任何 NIC 都可以獲派一或多個靜態或動態公用及私人 IP 位址。 將多個 IP 位址指派給 VM 可啟用下列功能：

* 在單一伺服器上，以不同 IP 位址和 SSL 憑證裝載多個網站或服務。
* 做為網路虛擬設備，例如防火牆或負載平衡器。
* 能夠將任何 NIC 的任何私人 IP 位址新增到 Azure Load Balancer 後端集區。 在過去，只能將主要 NIC 的主要 IP 位址新增到後端集區。 若要深入了解如何負載平衡多個 IP 設定，請參閱[負載平衡多個 IP 組態](../load-balancer/load-balancer-multiple-ip.md)文章。

連接到 VM 的每個 NIC 皆有一或多個 IP 組態與其相關聯。 每個組態會獲派一個靜態或動態私人 IP 位址。 每個組態可能也有一個相關聯的公用 IP 位址資源。 公用 IP 位址資源會獲派動態或靜態 IP 位址。 若要深入了解 Azure 中的 IP 位址，請閱讀 [Azure 中的 IP 位址](virtual-network-ip-addresses-overview-arm.md)文章。

本文說明如何使用 PowerShell，將多個 IP 位址指派給透過 Azure Resource Manager 部署模型建立的 VM。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

## <a name="scenario"></a>案例
建立具有單一 NIC 的 VM，並連接至虛擬網路。 VM 需要三個不同的「私人」IP 位址和兩個「公用」IP 位址。 IP 位址會指派給下列 IP 組態︰

* **IPConfig-1：**指派「動態」私人 IP 位址 (預設) 和「靜態」公用 IP 位址。
* **IPConfig-2：**指派「靜態」私人 IP 位址和「靜態」公用 IP 位址。
* **IPConfig-3：**指派「動態」私人 IP 位址，沒有公用 IP 位址。
  
    ![多個 IP 位址](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

建立 NIC 時，IP 組態會與 NIC 產生關聯，而建立 VM 時，NIC 會連結至 VM。 此案例使用的 IP 位址類型只是舉例說明。 您可以指派需要的任何 IP 位址和指派類型。

## <a name="a-name--createacreate-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>建立有多個 IP 位址的 VM

後續步驟說明如何使用多個 IP 位址建立範例 VM，如案例中所述。 視您的實作而定，變更變數名稱和 IP 位址類型。

1. 開啟 PowerShell 命令提示字元，在單一 PowerShell 工作階段內完成本節中其餘的步驟。 如果您尚未安裝和設定 PowerShell，請先完成 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 文章中的步驟。
2. 若要註冊以進行預覽，請將電子郵件傳送至[多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，並註明您的訂用帳戶 ID 與用途。 請勿嘗試完成剩餘步驟︰
    - 除非收到電子郵件，通知已接受您進行預覽
    - 除非遵循您收到之電子郵件中的指示
3. 完成[建立 Windows VM](../virtual-machines/virtual-machines-windows-ps-create.md) 文章中的步驟 1-4。 不要完成步驟 5 (建立公用 IP 資源和網路介面)。 如果您變更該文章中使用的任何變數名稱，請同時變更其餘步驟中的變數名稱。 若要建立 Linux VM，請選取 Linux 作業系統，而不是 Windows。
4. 輸入下列命令，建立一個變數來存放建立 Windows VM 文章的步驟 4 (建立 VNet) 中所建立的子網路物件︰

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
5. 定義您想要指派給 NIC 的 IP 組態。 您可以視需要新增、移除或變更組態。 案例中描述下列組態︰

    **IPConfig-1**

    輸入後續命令以建立︰
    - 使用靜態公用 IP 位址的公用 IP 位址資源
    - 使用公用 IP 位址資源和動態私人 IP 位址的 IP 組態

    ```powershell
    $myPublicIp1     = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    請注意前一個命令中的 `-Primary` 參數。 當您對 NIC 指派多個 IP 組態時，必須將一個組態指派為 *Primary*。

    > [!NOTE]
    > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。
    >

    **IPConfig-2**

    在您建立的子網路上，將後續 **$IPAddress** 變數的值變更為可用且有效的地址。 若要檢查位址 10.0.0.5 在子網路上是否可用，輸入命令 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`。 如果位址可用，則輸出會傳回 *True*。 如果無法使用，則輸出會傳回 *False* 和可用位址的清單。 輸入下列命令來建立具有靜態公用 IP 位址和靜態私人 IP 位址的新公用 IP 位址資源，以及新 IP 組態︰
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = 10.0.0.5
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    輸入下列命令來建立具有動態私人 IP 位址與無公用 IP 位址的 IP 組態︰

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
6. 輸入下列命令，使用上一個步驟中定義的 IP 組態來建立 NIC：

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > 雖然本文會將所有 IP 組態指派給單一 NIC，您也可以指派多個 IP 組態給 VM 中的任何 NIC。 閱讀[建立具有多個 NIC 的 VM](virtual-network-deploy-multinic-arm-ps.md) 文章以了解如何建立具有多個 NIC 的 VM。

7. 完成[建立 VM](../virtual-machines/virtual-machines-windows-ps-create.md) 文章的步驟 6。 

    > [!WARNING]
    > 建立 VM 文章的步驟 6 會失敗，如果︰
    > - 您已將名為 $myNIC 的變數變更為本文步驟 6 中的其他項目。
    > - 您還沒有完成本文和建立 VM 文章中的先前步驟。
    >
8. 輸入下列命令，可檢視私人 IP 位址與指派給 NIC 的公用 IP 位址資源︰

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。 請勿將公用 IP 位址新增至作業系統。

## <a name="a-nameaddaadd-ip-addresses-to-a-vm"></a><a name="add"></a>將 IP 位址新增至 VM

您可以完成後續步驟，將私人和公用 IP 位址新增至 NIC。 下列各節中的範例假設您已經有一個 VM，其具有本文中的[案例](#Scenario)所述的三項 IP 組態，您不需要進行設定。

1. 開啟 PowerShell 命令提示字元，在單一 PowerShell 工作階段內完成本節中其餘的步驟。 如果您尚未安裝和設定 PowerShell，請先完成 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 文章中的步驟。
2. 若要註冊以進行預覽，請將電子郵件傳送至[多個 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，並註明您的訂用帳戶 ID 與用途。 請勿嘗試完成剩餘步驟︰
    - 除非收到電子郵件，通知已接受您進行預覽
    - 除非遵循您收到之電子郵件中的指示
3. 將下列 $Variables 的「值」變更為您想要對其新增 IP 位址的 NIC 名稱，以及 NIC 所在的資源群組和位置︰

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    如果您不知道要變更的 NIC 名稱，請輸入下列命令，然後變更先前變數的值︰

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
4. 輸入下列命令建立變數，並將它設定為現有 NIC︰

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
5. 在下列命令中，將 myVNet 和 mySubnet 變更為NIC 所連線的 VNet 和子網路的名稱。 輸入命令來擷取 NIC 所連線的 VNet 和子網路物件︰

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    如果您不知道 NIC 所連接的 VNet 或子網路名稱，輸入下列命令︰
    ```powershell
    $mynic.IpConfigurations
    ```
    尋找與下列傳回輸出中文字類似的文字︰

        Subnet   : {
                     "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"

    在此輸出中，myVnet 是 VNet 且 mySubnet 是 NIC 所連線的子網路。

6. 根據您的需求，完成下列其中一個章節中的步驟︰

    **新增私人 IP 位址**

    若要將私人 IP 位址新增至 NIC，您必須建立 IP 組態。 下列命令會建立具有靜態 IP 位址 10.0.0.7 的組態。 如果您想要新增動態私人 IP 位址，請在輸入命令之前移除 `-PrivateIpAddress 10.0.0.7`。 指定靜態 IP 位址時，它必須是子網路未使用的位址。 建議您先輸入 `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet` 命令來測試地址，確定它為可用。 如果 IP 位址可用，則輸出會傳回 *True*。 如果無法使用，則輸出會傳回 False 和可用位址的清單。

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```

    使用唯一組態名稱和私人 IP 位址 (適用於具有靜態 IP 位址的組態)，視需要建立最多的組態。

    完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。

    **新增公用 IP 位址**

    將公用 IP 位址與新的 IP 組態或現有的 IP 組態產生關聯，便可新增公用 IP 位址。 視需要完成後續其中一節的步驟。

    > [!NOTE]
    > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。
    >

    **將公用 IP 位址資源與新的 IP 組態產生關聯**
    
    每當您在新的 IP 組態中新增公用 IP 位址時，也必須新增私人 IP 位址，因為所有的 IP 組態都必須有一個私人 IP 位址。 您可以新增現有的公用 IP 位址資源，或建立一個新的資源。 若要建立新的公用 IP 位址資源，請輸入下列命令：
    
    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    若要建立具有動態私人 IP 位址和相關聯的 *myPublicIp3* 公用 IP 位址資源的新 IP 組態，請輸入下列命令︰

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
     $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

    **將公用 IP 位址資源與現有的 IP 組態產生關聯**

    公用 IP 位址資源只能與尚未關聯的 IP 組態產生關聯。 您可以輸入下列命令，判斷 IP組態是否有相關聯的公用 IP 位址︰

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```

    在傳回的輸出中，尋找類似接下來的這一行︰

        Name       PrivateIpAddress PublicIpAddress                                           Primary
        ----       ---------------- ---------------                                           -------
        IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
        IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
        IpConfig-3 10.0.0.6                                                                     False

    由於 IpConfig 3 的 **PublicIpAddress** 欄是空白，目前沒有與其相關聯的公用 IP 位址資源。 您可以將現有的公用 IP 位址資源新增至 IpConfig-3，或輸入下列命令以建立一個︰

    ```powershell
    $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    ```

    輸入下列命令，將公用 IP 位址資源與名為 *IpConfig-3* 的現有 IP 組態產生關聯：
    
    ```powershell
    Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
    ```

7. 輸入下列命令，可設定具有新 IP 組態的 NIC︰

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

8. 輸入下列命令，以檢視指派給 NIC 的私人 IP 位址和公用 IP 位址資源︰

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
9. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。 請勿將公用 IP 位址新增至作業系統。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]


<!--HONumber=Dec16_HO1-->


