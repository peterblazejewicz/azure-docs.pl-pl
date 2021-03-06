## <a name="overview"></a>Omówienie
Azure File Storage to usługa, która umożliwia korzystanie z udziałów plików w chmurze przy użyciu standardowego [protokołu bloku komunikatów serwera (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx). Obsługiwane są wersje 2.1 i 3.0 protokołu SMB. W usłudze Magazyn plików Azure można migrować starsze aplikacje korzystające z udziałów plików na platformę Azure szybko i bez kosztownych modyfikacji oprogramowania. Aplikacje uruchomione na maszynach wirtualnych lub w ramach usług w chmurze platformy Azure, a także na klientach lokalnych mogą instalować udziały plików w chmurze tak samo jak aplikacja na komputerze instalująca typowy udział SMB. Dowolna liczba składników aplikacji może następnie równocześnie zainstalować udział Magazynu plików i uzyskiwać do niego dostęp.

Ponieważ udział Magazynu plików to standardowy udział plików SMB, aplikacje działające na platformie Azure mają dostęp do danych w udziale za pośrednictwem interfejsów API we/wy systemu plików. Dzięki temu programiści mogą wykorzystać istniejący kod i własne umiejętności, aby zmigrować istniejące aplikacje. Specjaliści IT mogą użyć poleceń cmdlet programu PowerShell do tworzenia i instalowania udziałów magazynu plików oraz do zarządzania nimi w ramach administracji aplikacjami platformy Azure.

Udziały plików platformy Azure można tworzyć przy użyciu witryny [Azure Portal](https://portal.azure.com), poleceń cmdlet programu PowerShell usługi Azure Storage, bibliotek klienckich usługi Azure Storage lub interfejsu API REST usługi Azure Storage. Ponadto ponieważ te udziały plików są udziałami SMB, można z nich korzystać za pośrednictwem standardowych i znanych interfejsów API systemu plików.

<!--HONumber=Oct16_HO3-->


