
1. Odwiedź [portal Azure]. Kliknij kolejno opcje **Przeglądaj wszystkie** > **Aplikacje mobilne** > nowo utworzoną wewnętrzną bazę danych. W ustawieniach aplikacji mobilnej kliknij kolejno opcje **Szybki start** > **Android**. W obszarze **Konfiguruj aplikację klienta** kliknij przycisk **Pobierz**. To spowoduje pobranie kompletnego projektu systemu Android dla aplikacji wstępnie skonfigurowanej do połączenia z wewnętrzną bazą danych. 
2. Otwórz projekt za pomocą środowiska **Android Studio** przy użyciu opcji **Importuj projekt (Eclipse ADT, Gradle itp.)**. Pamiętaj, aby zaznaczyć tę opcję importu, aby uniknąć błędów JDK.
3. Naciśnij przycisk **Uruchom „aplikację”**, aby skompilować projekt i uruchomić aplikację w symulatorze systemu Android.
4. W aplikacji wpisz łatwy do rozpoznania tekst, np. *Ukończ samouczek*, a następnie kliknij przycisk Dodaj. To spowoduje wysłanie żądania POST do wdrożonej wcześniej wewnętrznej bazy danych Azure. Wewnętrzna baza danych wstawia dane z żądania do tabeli SQL TodoItem i zwraca informacje na temat przechowywanych od niedawna elementów do aplikacji mobilnej. W aplikacji mobilnej dane te są wyświetlane na liście. 
   
    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

[portal Azure]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->


