---
title: Tworzenie maszyny wirtualnej platformy Azure przy użyciu programu PowerShell | Microsoft Docs
description: Korzystając z programu Azure PowerShell i usługi Azure Resource Manager, można w łatwy sposób utworzyć nową maszynę wirtualną z systemem Windows Server.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: davidmu

---
# Tworzenie maszyny wirtualnej z systemem Windows przy użyciu usługi Resource Manager i programu PowerShell
W tym artykule pokazano, jak szybko utworzyć maszynę wirtualną platformy Azure z systemem Windows Server i wymagane przez nią zasoby przy użyciu usługi [Resource Manager](../resource-group-overview.md) i programu PowerShell. 

Wszystkie kroki przedstawione w tym artykule są wymagane do utworzenia maszyny wirtualnej, a ich wykonanie powinno zająć około 30 minut.

## Krok 1. Instalowanie programu Azure PowerShell
Zobacz [How to install and configure Azure PowerShell](../powershell-install-configure.md) (Jak zainstalować i skonfigurować program Azure PowerShell), aby uzyskać informacje na temat instalowania najnowszej wersji programu Azure PowerShell, wybierania subskrypcji i logowania się do konta.

## Krok 2. Tworzenie grupy zasobów
Najpierw należy utworzyć grupę zasobów.

1. Pobierz listę dostępnych lokalizacji, w których można utworzyć zasoby.
   
        Get-AzureRmLocation | sort Location | Select Location
   
    Powinny zostać wyświetlone informacje podobne do następujących:
   
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus
2. Zastąp wartość **$locName** lokalizacją z listy. Utwórz zmienną.
   
        $locName = "centralus"
3. Zastąp wartość **$rgName** nazwą nowej grupy zasobów. Utwórz zmienną i grupę zasobów.
   
        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

## Krok 3. Tworzenie konta magazynu
[Konto magazynu](../storage/storage-introduction.md) jest niezbędne do przechowywania wirtualnego dysku twardego używanego przez utworzoną maszynę wirtualną.

1. Zastąp wartość **$stName** nazwą konta magazynu. Sprawdź unikatowość nazwy.
   
        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName
   
    Jeśli to polecenie zwróci wartość **True**, proponowana nazwa jest unikatowa na platformie Azure. Nazwy kont usługi Storage muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery.
2. Teraz uruchom polecenie w celu utworzenia konta magazynu.
   
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName

## Krok 4. Tworzenie sieci wirtualnej
Wszystkie maszyny wirtualne są częścią [sieci wirtualnej](../virtual-network/virtual-networks-overview.md).

1. Zastąp wartość **$subnetName** nazwą podsieci. Utwórz zmienną i podsieć.
   
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
2. Zastąp wartość **$vnetName** nazwą sieci wirtualnej. Utwórz zmienną i sieć wirtualną z podsiecią.
   
        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
   
    Użyj wartości odpowiednich dla aplikacji i środowiska.

## Krok 5. Tworzenie publicznego adresu IP i interfejsu sieciowego
Aby umożliwić komunikację z maszyną wirtualną w sieci wirtualnej, potrzebujesz [publicznego adresu IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) i interfejsu sieciowego.

1. Zastąp wartość **$ipName** nazwą publicznego adresu IP. Utwórz zmienną i publiczny adres IP.
   
        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
2. Zastąp wartość **$nicName** nazwą publicznego interfejsu sieciowego. Utwórz zmienną i interfejs sieciowy.
   
        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

## Krok 6. Tworzenie maszyny wirtualnej
Teraz, gdy masz wszystkie elementy, możesz utworzyć maszynę wirtualną.

1. Uruchom polecenie służące do ustawiania nazwy konta administratora i hasła dla maszyny wirtualnej.
   
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
   
    Hasło musi mieć długość od 12 do 123 znaków i musi zawierać co najmniej jedną małą literę, jedną wielką literę, jedną cyfrę i jeden znak specjalny. 
2. Zastąp wartość **$vmName** nazwą maszyny wirtualnej. Utwórz zmienną i konfigurację maszyny wirtualnej.
   
        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
   
    Aby wyświetlić listę dostępnych rozmiarów maszyny wirtualnej, zobacz [Sizes for virtual machines in Azure](virtual-machines-windows-sizes.md) (Rozmiary maszyn wirtualnych na platformie Azure).
3. Zastąp wartość **$compName** nazwą komputera maszyny wirtualnej. Utwórz zmienną i dodaj do konfiguracji informacje dotyczące systemu operacyjnego.
   
        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
4. Zdefiniuj obraz, którego należy użyć do aprowizowania maszyny wirtualnej. 
   
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
   
    Aby uzyskać więcej informacji na temat wybierania obrazów do użycia, zobacz [Navigate and select Windows virtual machine images in Azure with PowerShell or the CLI](virtual-machines-windows-cli-ps-findimage.md) (Przechodzenie do obrazów maszyn wirtualnych systemu Windows na platformie Azure przy użyciu programu PowerShell lub interfejsu wiersza polecenia).
5. Dodaj do konfiguracji utworzony interfejs sieciowy.
   
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
6. Zastąp wartość **$blobPath** ścieżką i nazwą pliku wirtualnego dysku twardego w magazynie. Plik wirtualnego dysku twardego jest zazwyczaj przechowywany w kontenerze, na przykład **vhds/WindowsVMosDisk.vhd**. Utwórz zmienne.
   
        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
7. Zastąp wartość **$diskName** nazwą dysku systemu operacyjnego. Utwórz zmienną i dodaj do konfiguracji informacje dotyczące dysku.
   
        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
8. Na zakończenie utwórz maszynę wirtualną.
   
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm
   
    W oknie programu PowerShell powinna być widoczna grupa zasobów i wszystkie jej zasoby w witrynie Azure Portal oraz stan oznaczający powodzenie:
   
        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Następne kroki
* Jeśli wystąpiły problemy dotyczące wdrożenia, następnym krokiem powinno być zapoznanie się z artykułem [Troubleshooting resource group deployments with Azure Portal](../resource-manager-troubleshoot-deployments-portal.md) (Rozwiązywanie problemów z wdrożeniami grup zasobów za pomocą witryny Azure Portal).
* Aby dowiedzieć się, jak zarządzać utworzoną maszyną wirtualną, zobacz [Manage virtual machines using Azure Resource Manager and PowerShell](virtual-machines-windows-ps-manage.md) (Zarządzanie maszynami wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell).
* Aby utworzyć maszynę wirtualną przy użyciu szablonu, skorzystaj z informacji podanych w artykule [Create a Windows virtual machine with a Resource Manager template](virtual-machines-windows-ps-template.md) (Tworzenie maszyny wirtualnej systemu Windows przy użyciu szablonu usługi Resource Manager).

<!--HONumber=Sep16_HO5-->


