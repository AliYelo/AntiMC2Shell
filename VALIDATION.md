# 0.1.0 doğrulama kaydı

Tarih: 24 Kasım 2026. Ortam: Windows x64, JDK 25, Minecraft 26.3, Fabric Loader 0.20.1, Loom 1.19.4, Gradle 9.8.2.

- Temiz derleme başarılı.
- Paketlenmiş dağıtım JAR'ı gerçek Knot/Fabric/Mixin yükleyicisinde hem geliştirme hem normal çalışma ayarıyla sınandı.
- 138 kontrol ve 1.042 TTF sağlayıcısı yüklemesi geçti. Sahte kaynak yöneticisi herhangi bir erişimde testi durduracak şekilde ayarlandı; toplam kaynak erişimi **0**.
- Farklı ad alanları, uzantısız/farklı uzantılı konumlar, tekrar yükleme, null kaynak yöneticisi ve sekiz eşzamanlı işçi sınandı. Boş sağlayıcı ve sayaç sonuçları doğrulandı.
- Dönüştürülmüş gerçek sınıf bytecode'u incelendi: iki native glyph çağrısından önce koşulsuz `flags | FT_LOAD_NO_HINTING` uygulanıyor.
- Resmi vanilla istemcinin 11.383 sınıfı statik incelendi; beklenen üç font oluşturma/yükleme çağrısı bulundu, ek ilgili çağrı bulunmadı.
- Dağıtım JAR'ında test giriş noktası veya test sınıfları bulunmadığı doğrulandı.

Dağıtım SHA-256:

```text
cc0b373da15b34acb2e62a2da23d68c4fcd7f31a3b816675482647309a5d708f  antim2shell-0.1.0.jar
```

Saldırı deposundan dosya indirilmedi, saldırı kodu veya paketi çalıştırılmadı. Test sırasında font verisi açılmadı. Oyun penceresi, gerçek sunucu bağlantısı, görsel uyumluluk ve diğer modlarla birlikte çalışma test edilmedi. Bu sonuçlar tüm güvenlik açıklarına karşı garanti değildir.

Yeniden çalıştırma: JDK 25 ile `gradlew.bat --no-daemon clean build`. Test raporu `build/reports/security-test.txt` altında oluşur.
