# 1. RESTful API'sine Kısa Bir Bakış
## 1.1. İlk projeyi oluşturma
- BookStore Klasörü ve içinde bir BookStore çözümü oluşturun.
- BookStore/BookStoreApi Klasörü ve BookStoreApi "webapi" (NET6.0) projesini oluşturup çözüme ekleyin.
- Örnek modeli ve denetleyiciyi silin.
- Modeller (oluştur) klasörü altına bir Kitap modeli ekleyin.
- Denetleyiciler klasörü altına bir BooksController (Boş WebApi Denetleyicisi) ekleyin.
### 1.1.1. Bellek İçi Sahte Veri Ekleme
- Bir Veri klasörü ekleyin
- Veri klasörüne statik bir ApplicationContext sınıfı ekleyin
- Denetleyiciye Crud yöntemlerini ekleyin.
- **Patch fiili için** JsonPatchDocument kullanılacaktır,
- bunun için şu iki paketin kurulu olması gerekir:
		- Microsoft.AspNetCore.Mvc.NewtonsoftJson (6.0.10)
		- Microsoft.AspNetCore.JsonPatch (6.0.10
- Paketler kurulduktan sonra Program.cs içerisindeki servislere NewtonsoftJson eklenmelidir:
bu satırı değiştir
		```C#
		builder.Services.AddControllers();
		```
		to this:
		```C#
		builder.Services.AddControllers()
			.AddNewtonsoftJson();
		```
- PATCH isteği nasıl gönderilir? Aşağıda vücut tapınağı bulunmaktadır, bunun bir liste olduğuna dikkat edin:
	```Json
		[
		  {
			"operationType": 0,
			"path": "string",
			"op": "string",
			"from": "string",
			"value": "string"
		  }
		]
	```
	- Example operation1:
	```Json
		[
		  {
			"path": "price",
			"op": "replace",
			"value": 150
		  }
		]
	```
	- Example operation2:
	```Json
		[
		  {
			"path": "price",
			"op": "replace",
			"value": 150
		  },
		  {
			"path": "title",
			"op": "replace",
			"value": "New Book Title"
		  }
		]
	```
# 2. Postacıyı Kullanmak
- Çalışma Alanları sekmesi altında çalışma alanı oluşturun.
- Bir Ad ve açıklama tanımlayın
- Kişisel seç
- Çalışma alanı altında koleksiyonları seçin ve ardından bir koleksiyon oluşturmak için "+" düğmesine tıklayın. Bunu "Kitaplar" olarak adlandırın
- Koleksiyonda Yeni İstek'e tıklayın ve denetleyicideki her işlem için ilgili http istek fiiliyle yeni bir istek oluşturun. Her isteği kaydetmeyi unutmayın.
## 2.1. Global ve Koleksiyon değişkenleri
- URL'lerin tekrarlanmaması için değişkenler kullanabiliriz.
- Herhangi bir istek URL'si alanında https://localhost:PORT'u seçin. Değişken olarak ayarla'ya tıklayın, bir ad tanımlayın (baseUrl) ve global'i seçin. Alandaki URL'nin bu kısmı şu şekilde değişecek: {{baseUrl}}, diğer tüm istekleri bununla değiştirin. (örneğin: {{baseUrl}}/api/books/2)
- Koleksiyon değişkenleri aynı şekilde tanımlanabilir, koleksiyon değişkenleri yalnızca tanımlandığı koleksiyon altında geçerlidir.
- Değişkenler isteklerin gövdelerinde de kullanılabilir.
## 2.2. Postacıyı kullanarak testler
- Postacının test işlevi istekler için kullanılabilir,
- Seçilen bir isteğin Testler sekmesi altında aşağıdaki gibi test fonksiyonları açıklanabilir:
	```js
	pm.test("Status code 200", function(){
    	pm.response.to.have.status(200)
    })
	```
## 2.3. Rastgele Fonksiyonlar
- Postacı kullanılarak sahte veriler üretilebilir ve seçilen istekler yinelemeler kullanılarak birden çok kez gönderilebilir.
- 100 defa istek göndermek,
- Koleksiyon adının ... seçeneğine tıklayın veya koleksiyon adına (Kitaplar) sağ tıklayın,
- Çalıştırma koleksiyonunu seçin
- Tekrarlanacak İstekler'i seçin.

-Gönderiyi kullanarak sahte veriler oluşturmaya yönelik örnek bir istek gövdesi:
	```Json
	{
	  "id": {{$randomInt}},
	  "title": "{{$randomWords}}",
	  "price": {{$randomPrice}}
	}
	```
	
# 3. Yeni Bir API Projesi ve EntityFramework Çekirdeği
- VS veya terminali kullanarak yeni bir ASP.NET Core Web API projesi ekleyin. Özellikler:
-NET6.0
- Kimlik doğrulama yok
- Https için yapılandırın
- Denetleyicileri Kullan
- OpenAPI desteğini etkinleştirin
- WeatherForecast modelini ve denetleyiciyi silin.
- Solution Explorer'da bu projeyi başlangıç ​​projesi olarak ayarlayın.

## 3.1. Varlık Çerçevesi Çekirdeği
- Microsoft.EntityFrameworkCore paketini ekleyin, nuget paket yöneticisini veya Paket Yönetici Konsolunu kullanın (aşağıdaki gibi):
''''
Kurulum Paketi Microsoft.EntityFrameworkCore -Sürüm 6.0.10 -ProjectName WebApi
''''
- appsettings.json dosyasına "ConnectionStrings" ekleyin.
- Microsoft.EntityFrameworkCore.SqlServer paketini projeye nuget veya PMC ile ekleyin:
''''
Kurulum Paketi Microsoft.EntityFrameworkCore.SqlServer -Sürüm 6.0.10 -ProjectName WebApi
```
## 3.2. Taşıma Oluştur
- Geçişleri yönetmek için Microsoft.EntityFrameworkCore.Tools Paketini ekleyin. Bu paket, geçişler ve veritabanı işlemleriyle ilgili komut setleri için gereklidir. PMC'de:
''''
Kurulum Paketi Microsoft.EntityFrameworkCore.Tools -Sürüm 6.0.10 -ProjectName WebApi
''''
- Kod öncelikli yaklaşım için ihtiyaç duyulan diğer bir paket ise tasarım paketidir. Bu paket uygulama için gereklidir. PMC'de:
''''
Kurulum Paketi Microsoft.EntityFrameworkCore.Design -Sürüm 6.0.10 -ProjectName WebApi
''''
- PMC'de geçiş ve veritabanı oluşturun (Varsayılan proje olarak WebApi seçilmelidir):
```
	Add-Migration Init
	Update-Database
```
- Bu noktada appsettings.json dosyasındaki bağlantı dizesinde bir noktalama işareti hatası oluştu. Veritabanı Güncellemenin başarılı olması için düzeltilmesi gerekir.

- BookConfig'i oluşturduktan ve OnModelCreating yöntemini geçersiz kıldıktan sonra "SeedData" geçişini ekleyin ve veritabanını güncelleyin.

## 3.3. Veri manipülasyonu
- Denetleyici Yöntemleri oluşturun.
- Postacıyı test amacıyla kullanmak için, oluşturduğumuz çalışma alanının istek koleksiyonu beseUrl değişkeninin port numarası değiştirilerek kullanılabilir.
- PATCH fiili ve yöntemi için NewtonsoftJson ve JsonPatch paketlerini kurun ve Program.cs'ye hizmet kaydını ekleyin.
# 4. Katmanlı Mimari
## 4.1. Varlıklar Katmanı
- Varlıklar adlı bir Sınıf Kitaplığı projesi ekleyin
- WebApi/Models klasörü altındaki Book sınıfını Entities/Models'a taşıyın ve WebApi/Models klasörünü silin.
- WebApi projesine Varlıklara referans ekleyin ve Book.class'ı ve çözümlemeleri taşırken ad alanlarını düzenlemeyi unutmayın.
## 4.2. Depo Katmanı
- Depolar adlı bir Sınıf Kitaplığı projesi ekleyin
- Bu projenin altına bir Sözleşmeler klasörü ekleyin ve içine IRepositoryBase adında genel bir arayüz ekleyin.
- CRUD yöntemi imzalarını ekleyin.
- EfCore klasörünü ekleyin ve RepositoryContext'i WebApi'den bu klasöre taşıyın.
- Kullanımları çözmek için EntitiyFrameworkCore paketini WebApi'den kaldırın ve paketi Repositories projesine ekleyin.
- Bu projedeki Varlıklara referans ekleyin.
- WebApi projesinden bu projeye referans ekleyin.
- Config klasörünü ve BookConfig sınıfını Repositories/EfCore Project'e taşıyın.
- WebApi projesindeki Geçişler klasörünü silin.
- Tüm kullanımları çözün.
- EfCore klasörü altında IRepositoryBase'i uygulayan soyut bir RespositoryBase genel sınıfı oluşturun.
- Yöntemleri uygulamak için içine (korumalı) RepositoryContext enjekte edin.
- Create IBookRepository, Sözleşmeler kapsamında IRepositoryBase'i genişletir.
- Create BookRepository, IBookRepository'yi uygular ve BookRepositoryBase'i genişletir
- Sözleşmeler altında IRepositoryManager oluşturun
- Create RepositoryManager, EfCore altında IRepositoryManager'ı uygular
### 4.2.1. Yavaş yüklenme
- Refactor RepositoryManager'ı
### 4.2.2. Hizmet Uzantıları
- WebApi projesi altında Uzantılar klasörü oluşturun.
- Uzantılar klasörü altında ServicesExtensions oluşturun.
### 4.2.3. Depo Yöneticilerinin Denetleyiciye Entegrasyonu
- Context yerine RepositoryManager'ı kullanmak için BookController'ı enjekte edin ve düzenleyin.
- IoC için ServicesExtension sınıfına bir yöntem ekleyin
## 4.3. Hizmetler Katmanı
- Hizmetler adlı bir sınıf kitaplığı ekleyin
- Sözleşmeler adlı bir klasör ekleyin
- IBookService adlı bir arayüz ekleyin
- BookService adlı bir sınıf ekleyin, IBookService'i uygular
- Bir IServiceManager arayüzü ekleyin
- ServiceManager'ın IServiceManager'ı uyguladığı bir sınıf ekleyin
- Varlıklar projesine referans ekleyin
- ServicesExtensions'a bir ConfigurationServiceManger yöntemi ekleyin
- Bunu Program.cs'ye ekleyin
- ServiceManager'ı BooksController'a enjekte edin
- BooksController Yöntemlerini buna göre düzenleyin
## 4.4. Sunum Katmanı
- Çözümün altına Sunum adlı bir sınıf kütüphanesi projesi ekleyin
- Mevcut Class1 sınıfını AssemblyReference olarak yeniden adlandırın
- Denetleyiciler klasörünü ekle
- WebApi projesindeki BooksController'ı bu klasöre taşıyın ve WebApi'den silin
- Bu projeye Microsoft.AspNetCore.Mvc.Core 2.2.5 paketini ekleyin
- Daha önce kurulmuş olan jsonPatch paketini bu projeye ekleyin, WebApi'den kaldırın
- Varlıklar ve Hizmetler projelerine referans ekleyin
- Bu projeyi WebApis referanslarına ekleyin
- Program.cs'de şu satırı değiştirin:
	```C#
	builder.Services.AddControllers()
    .AddNewtonsoftJson();
	```
	to this:
	```C#
	builder.Services.AddControllers()
    .AddApplicationPart(typeof(Presentation.AssemblyReference).Assembly)
    .AddNewtonsoftJson();
	```
## 4.5. Depo Bağlamı Fabrikası
- Veritabanını bırak
- Ef'i WebApi'den ayrı bir projeye kaldırdığımız için WebApi'de geçiş oluşturamıyoruz (her ne kadar istemediğimiz Depolarda oluşturabilsek de).
- WebApi altında bir ContextFactory klasörü oluşturun
- İçinde bir RepositoryContextFactory sınıfı oluşturun.
- Bu fabrikada ConfigurationBuilder'ı yapın
- Bu fabrikada DbContextOptionsBuilder'ı yapın
- Init geçişini oluşturun ve veritabanını güncelleyin

# 5 NLog ile Günlük Kaydı
- ILoggerService arayüzünü Hizmetlere/Sözleşmelere ekleyin
- NLog.Extensions.Logging paketini Hizmet projesine yükleyin
- ILoggerService'i uygulayan LoggerManager sınıfı ekleyin
- Nlog.config xml dosyasını projeye ekleyin
- Add record to Program.cs
	```C#
	LogManager.LoadConfiguration(string.Concat(Directory.GetCurrentDirectory(),"/nlog.config"));

	```
- IoC için ServicesExtensions'a bir Uzantı Yöntemi ekleyin
- IoC Yapılandırmasını Program.cs'ye ekleyin
## 5.1. Logger'ı projede kullanma
- Gereken yerlere kayıt satırları ekleyin.


# 6. Küresel İstisna İşleme
## 6.1. Modelleme Hataları ve Hata Ayrıntıları
- Varlıklar projesine bir ErrorModel klasörü ekleyin
- Bu klasöre bir ErrorDetails sınıfı ekleyin
## 6.2. İstisna İşleyicisini Kullanma
- WebApi projesindeki Extensions klasörüne bir ExceptionMiddlewareExtensions sınıfı ekleyin
- Şimdilik tüm İstisna Durum kodlarını 500'e ayarlayın.
- ConfigurationExceptionHandler için Program.cs'yi yapılandırın
- Bu adımlardan sonra uygulama test edilebilir. Uygulamada atılan İstisnalar ErrorDetails olarak serileştirilecek ve StatusCode 500 olarak ayarlanacak: Dahili Sunucu Hatası
- Try-catch bloklarını kaldırın
## 6.3. Özel İstisnalar
- Varlıklar altına İstisnalar klasörü ekleyin
- Bir NotFound soyut sınıfı eklemek, İstisna'yı buna genişletir.
- BookNotFound mühürlü sınıfının eklenmesi NotFound'u genişletir
- Ana projenin Extensions klasöründe ExceptionMiddlewareExtensions sınıfına dönün ve Exception tipine göre düzenleyin.
- BookNotFoundException'ı oluşturmak için hata atan veya bulunamadı yanıtları döndüren Refactor İlgili yöntemler.
- İsteğe bağlı: Yeni bir özel İstisna ekleyin; BadRequestException
# 7. AutoMapper Uygulaması
- Hizmetler projesine automapper.Extensions.Microsoft.DependencyInjection ver 12.0.0 sürümünü yükleyin.
- Program.cs'de hizmetlere kayıt ekleyin
- Entities projesine DataTransferObjects klasörünü ekleyin
- Bu klasöre "kaydet" yapmak için BookUpdateD'yi ekleyin. Dto'lar manipülasyon amaçlı değildir, dolayısıyla kayıt olarak tanımlanırlar. Kayıtlar sınıflara çok benzer, aynı zamanda referans türleridir. Kayıttaki özellikler için set blokları tanımlamıyoruz, bunun yerine init blokları tanımlıyoruz. Böylece kayıt türleri değişmez türler haline gelir.
- WebApi projesi altına Utilities/AutoMapper klasörlerini ekleyin.
- MappingProfile ekle, Profil sınıfını AutoMapper klasörüne genişletir.
- CreateMap yöntemini kullanarak yapıcıya eşlemeler ekleyin.
- BookUpdateDto'yu kullanmak için BooksController ve Service katmanını yeniden düzenleyin
# 8. İçerik Müzakereleri
- Müşterilerden Gelen İstekler için çeşitli formatları destekleyin veya desteklemeyin
- Json, Xml gibi formatları göz önünde bulundurun ve kabul edin veya kabul etmeyin ve buna göre yanıt verin.
- Kabul başlığını yapılandır
- Şu anda uygulama içerik anlaşmasına kapalıdır; bu, istemcinin istediği format ne olursa olsun (application/json, text/csv, text/xml, application/xml vb.), uygulamanın application/json formatını döndürdüğü anlamına gelir.
- Program.cs Yapılandırma denetleyicilerinde,
- içerik anlaşmasını etkinleştirmek için RespectBrowserAcceptHeader = true ekleyin.
- biçimin yanıt 406 koduyla kabul edilmediğine ilişkin geri bildirim göndermek için ReturnHttpNotAcceptable = true ekleyin.
- Yanıtları xml formatında göndermek için AddXmlDataContractSerializerFormatters'ı ekleyin
- Artık Api Json veya xml formatlarında yanıt verebilmektedir.
## 8.1. Serileştirilebilirlik
- Şimdilik Api, serileştirilemediği için yapıcı tarafından tanımlanan kayıt nesnelerinin listesini (açıkça tanımlanmış hiçbir destek yok) xml formatında döndüremiyor.
- Bu sorunu çözmek için Serileştirilebilir özellik ekleyin. Ancak bu sefer xml isteklerinin sonucu oldukça karmaşık olacak.
- Mutlak çözüm, sınıfları veya kayıtları varsayılan kurucular ve özelliklerle tanımlamaktır.
## 8.2. Özel Formatlayıcı
- WebApi'nin Utilities klasörü altına Formatters klasörünü ekleyin
- Csv yanıt formatlarını desteklemek için CsvOutputFormatter : TextOutputFormatter sınıfını ekleyin
- MediaTypeHeaderValue sorununu Microsoft (Sistem değil) kullanarak çözümleyin
- Yalnızca Liste veya bireysel içerikleri destekleyebiliriz.
- Yanıta hangi özelliklerin ekleneceğini seçebiliriz.
- IMvcBuilderExtensions'ı Uzantılar klasörüne ekleyin.
- Program.cs'yi yapılandırın
- Bunun yalnızca BookDto türü için çalışacağına dikkat edin, dolayısıyla GetOneBook isteğinin çalışması için BookDto'yu döndürecek denetleyici yöntemini yeniden düzenlememiz gerekir.

# 9. Açıklamalarla Doğrulama
- Entities'deki DataTransferObjects klasörüne yeni bir soyut kayıt modeli olan BookManipulationDto'yu ekleyin.
- Özelliklerine doğrulama ek açıklamaları ekleyin.
- BookUpdateDto'yu BookManipulationDto'ya genişletin
- BookCreateDto:BookManipulationDto'yu ekleyin
- Hizmet Katmanını Düzenle,
- IBookService'i düzenleyin
- BookManager'ı düzenleyin
- Sunum Katmanında BooksController'ı düzenleyin
- Başarısız doğrulamalar için varsayılan 404 Yanıt durum kodunu Bastırmak için (ModelState.Invalid koşulu) Program.cs'yi yapılandırın
- UnprocessableEntity yanıtını (Coce 422) döndürmek için BooksController'ı yeniden düzenleyin
- PatchOneBook yönteminin ekstra adımlara ihtiyacı vardır.
- NewtonsoftJson paketini WebApi'den kaldırın ve Sunum projesine yükleyin.
- PatchOneBook yönteminin gövdesindeki ApplyTo yöntemine ModelState argümanını ekleyin.
- IBookService'e bir yöntem imzası ekleyin ve bunu BookManager'da uygulayın
- Denetleyicideki PatchOneBook yöntemini yeniden düzenleyin.
# 10. Eşzamansız Programlama
- APM: Asenkron Programlama Modeli
- EAP: Olay Tabanlı Asenkron Programlama
- TAP: Görev Tabanlı Eşzamansız Programlama
## 10.1. Görev Tabanlı Eşzamansız Programlama (TAP)
Aynı İş Parçacığında ardışık düzende işlenen senkronize görevler. Her Senkronizasyon isteği, İş Parçacığı havuzundaki bir İş Parçacığına atanır. Havuzda kullanılabilir bir iş parçacığı yoksa, senkronizasyon isteğinin önceki isteğin tamamlanmasını ve atanan iş parçacığının havuza dönmesini beklemesi gerekir.
Eşzamansız isteğin farkı, eşzamansız isteğin önceki işlemin tamamlanmasını beklemesine gerek olmamasıdır. Boru hattındaki her adım bir iş parçacığı tarafından işlenir.
- Depolar katmanında, IBookRepository ve BookRepository'yi yeniden düzenleyin, Get yöntemlerini yeniden düzenleyin, yeniden ayarlanan türleri Görev'e sarın
- IRepositoryManager ve RepositoryManager'da Save yöntemini yeniden düzenleyin.
- Hizmetler katmanında Yöntemleri aynı şekilde Rafactor.
- Sunum katmanındaki Yöntemleri yeniden düzenleyin.

# 11. Eylem Filtreleri
## 11.1. Eylem Filtresi
- ActionFilters klasörünü Sunum katmanına ekleyin
- ValidationFilterAttribute sınıfı oluşturun: ActionFilterAttribute
- Temel sınıfın gerekli yöntemlerini geçersiz kılın
- Denetleyicinin Oluşturma ve Güncelleme yöntemlerine ValidationFilter niteliğini ekleyin
- BadRequest ve UnprocessibleEntity bloklarını kaldırın.
- Program.cs'ye IoC kaydı ekleyin
## 11.2. Günlük Filtresi
- LogDetails modelini Entities/LogModel klasörüne ekleyin.
- LogFilterAttribute eklenmesi, ActionFilterAttribute'u Sunum/ActionFilters'a genişletir
- Ana projedeki ServicesExtensions'a Filtre Nitelikleri için bir yöntem ekleyin.
- Hizmetlere eklemek için Program.cs'den bu yöntemi çağırın.
- Tüm eylemleri günlüğe kaydetmek için BooksController'a özellik ekleyin.
# 12. Sayfalandırma ve Cors
## 12.1. Temel Sayfalandırma
- Entities projesine requestFeatures klasörünü ekleyin,
- Bu klasöre requestParameters soyut sınıfını ekleyin
- BookParameters eklemek, requestParameters'ı genişletir
- BookParameters'ı kullanmak için IBooksRepository refactor GetAll yöntemi imzalarında
- IBookRepository uygulamasını yeniden düzenleyin
- Yöntemleri Sunum katmanına kadar yeniden düzenleyin.
## 12.2. Meta veriler ve sayfalanmış liste
- Varlıklar projesindeki requestFeatures'a MetaData ve PagedList sınıflarını ekleyin
- Tüm katmanların GetAll yöntemlerini yeniden düzenleyin.
## 12.3. Cors (Çapraz Kökenli kaynak paylaşımı) Yapılandırması
- ServicesExtensions'a ManageCors yöntemini ekleyin
- Program.cs'ye ConfigurationCors çağrısını ekleyin
# 14. Filtreleme
- Entities projesinde BookParameters sınıfına özellikler ekleyin
- İstisnalar klasöründe BadRequestException'ı soyut yapın ve bu sınıfın iki alt sınıfını oluşturun. WebApi/Extensions klasöründeki ExceptionMiddlewareExtensions sınıfını uygun şekilde yeniden düzenleyin.
- Repositories projesinde BookRepositoriesExtensions sınıfını ekleyin
- BookRepository'de Extension yöntemini kullanarak yöntemi yeniden düzenleyin.

# 15. Arama
- BookParameters'a bir SearchTerm özelliği ekleyin.
- GetAll yöntemine "Arama" yöntemini eklemek için BookRepository'yi yeniden düzenleyin.
- Depolar projesinde Uzantılar adında bir klasör oluşturun
- BookRepositoryExtensions sınıfını klasöre taşıyın
- BookRepositoryExtensions sınıfına bir "Arama" yöntemi ekleyin.

# 16. Sıralama
- Entities projesindeki requestParameters'a OrderBy özelliğini ekleyin
- Varsayılan bir OrderBy = "Id" ayarlamak için BookParameters sınıfına bir kurucu ekleyin
- Repositories projesindeki BookRepositoryExtensions'a SortBy adlı bir yöntem ekleyin
- ..books?orderby=title,price gibi sorguları işleyen yöntemi yazın
- Yöntemdeki "BindingFlags" sorununu Sistem değil, Yansıma kullanarak çözmeyi unutmayın
- System.Linq.Dynamic.Core 1.2.23 sürümünü yükleyin
- Bu paketi yükledikten sonra OrderBy otomatik olarak çözülmezse System.Linq.Dynamic.Core kullanarak manuel olarak ekleyin.
- BookRepository'yi yeniden düzenlemek için bu yöntemi kullanın.
- Repositories projesinde, OrderQueryBuilder sınıfını Extensions klasörüne ekleyin.
- BookRepositoryExtensions sınıfını yeniden düzenlemek için bu Builders yöntemini kullanın.

# 17. Veri Şekillendirme
Veri şekillendirme, tüm API'lerin ihtiyaç duyduğu önemli bir özellik değildir. Bu özellik sayesinde müşterinin hangi kaynak alanlarını istediğini seçmesine izin verebiliriz.
- Bu özelliği tüm kaynaklar için etkinleştirmek için, İstek parametrelerine Alanlar özelliğini ekleyin veya belirli kaynaklar için etkinleştirmek istiyorsanız, bu özelliği o kaynağın parametrelerine ekleyin. (örneğin: yalnızca Book için bu desteği BookParameters sınıfına ekleyin)
- Hizmet Sözleşmeleri katmanına bir arayüz ekleyin
- Bu arayüzü Hizmetler katmanına uygulayın.
- Sorguda istenen özellik adlarını alın
- Kaynak türünün genel ve örnek özelliklerini yansıma yoluyla alın
- Hangi özelliklerin talep edildiğini belirlemek ve bunlardan bir koleksiyon oluşturmak için bunları karşılaştırın.
- İstenen özelliklerin değerlerini getirin.
- Bu özelliklerden ve değerlerinden bir ExpandoOblect oluşturun ve döndürün
-Bir koleksiyon istenirse, bunu her nesne için yapın ve döndürülecek bir ExpandoObjects koleksiyonu oluşturun.
- ExpandoObject, çalışma zamanında oluşturulacak dinamik bir nesne türüdür.
- WebApi/Extensions'taki ServicesExtensions sınıfında IoC için bir uzantı yöntemi oluşturun
- Program.cs'de bu yöntemi çağırın
IBookService'de, ExpandoObject'i döndürmek için IBookService'in imzasını değiştirin
- Uygulamayı da değiştirin, sınıfa şekillendiriciyi enjekte edin ve GetAll yöntemini yeniden düzenleyin.
- Yapıcının artık başka bir parametreye ihtiyacı olması nedeniyle ServiceManager'ı değiştirin.
# 18. Hateoas (Uygulama Durumunun Motoru Olarak Hipermedya)
Hypermedia desteğine sahip olmak için,
- Varlıklar projesi, LinkModels klasörünü ekleyin, klasöre Link sınıfını ekleyin
- Aynı klasörde LinkResourceBase ve LinkCollectionWrapper sınıfları oluşturun
- Modeller klasöründe Entity sınıfını oluşturun
- Aynı klasörde ShapedEntity'yi oluşturun.
- Sunulan hizmetlerde, ExpanoObject yerine Shaped varlığını kullanmak için IDataShaper'ı yeniden düzenleyin ve uygulamayı yeniden düzenleyin.
- LinkResponse sınıfını Varlıklar'daki LinkModels klasörüne ekleyin
- ServicesExtensions'ta AddCustomMediaTypes yöntemini ekleyin ve bunu Program.cs'de çağırın
- ValidateMediaTypeAttribute sınıfını Sunum katmanındaki ActionFilters klasörüne ekleyin. Microsoft'u kullanarak if ifadesindeki MediaTypeHeaderValue sorununu çözümleyin.
- Bu ValidateMediaTypeAttribute'u ServicesExtensions.cs'deki YapılandırActionFilters çağrısına ekleyin
- Bu özniteliği Sunum katmanının BooksController'ındaki GetAllBooksAsync yöntemine ekleyin.
- Entities projesine Microsoft.AspNetCore.Mvc.Abstractions sürüm 2.2.0 paketini yükleyin.
- Hizmet katmanında sözleşmelere IBookLinks arayüzünü ekleyin. Microsoft.AspNetCore.Http'yi kullanarak HttpContext'i çözümleyin.
- Daha sonra BookLinks sınıfını uygulayın, CreateForBook özel yöntemi daha sonra uygulanacaktır.
- Varlıklar'daki DataTransferObjects klasöründe bir LinkParameters kayıt türü oluşturun.
- BookLinks'in IoC kaydını Program.cs'ye ekleyin
- Hizmet katmanında:
- BookManager özel alanlarını ve yapıcısını değiştirin.
- GetAll yöntemini değiştir
- IBookManager arayüzünü değiştirin
- ServiceManager yapıcısını değiştirin
- Sunum katmanında:
- BooksController GetAll yöntemini değiştirin
- ValidateMediaTypeAttribute ve Program.cs'yi düzeltin, BookLinks sınıfının CreateForBook yöntemine Mock bağlantıları ekleyin.
- BookLinks sınıfında CreateForBook yöntemini uygulayın
- CreateForBooks adında yeni bir özel yöntem oluşturun ve bu yöntemle ReturnLinkedBooks'u yeniden düzenleyin.

# 19. Http SEÇENEKLERİ ve HEAD İstekleri
## 19.1. SEÇENEKLER fiil
- Sunum katmanında
- HttpOptions özniteliğine sahip GetBooksOptions adlı yeni bir yöntem ekleyin.
- Seçenek isteği, Müşteriye hangi Http fiillerine izin verildiğini bildirir.
## 19.2. BAŞ fiil
- Baş fiil, bir istek ve yanıtın Başlıkları ile ilgilidir. Hiç kimse.
- GET fiili ile aynı özelliklere sahiptir.
- HEAD için bir yönteme gerek yok. GetAllBooks Yöntemi üzerindeki HttpHead niteliğini HttpGet niteliğinin üzerine ekleyin.
- Yalnızca başlıkları döndürür

# 20. Kök Belgeler
- Sunum katmanına bir RootController ekleyin
- GetRoot yöntemini oluşturun.
- MediaType destek satırlarını WebApi/Extensions klasöründeki ServicesExtensions sınıfına ekleyin.

# 21. Versiyonlama
- Microsoft.AspNetCore.Mvc.Versioning'i (5.0.0) sunum katmanına yükleyin.
- ServiesExtensions'ta bir Sürüm Oluşturma yapılandırma yöntemi ekleyin. Bunu Program.cs'ye ekle
## 21.1. Kitaplar V2 - Parametrelerle
- BooksV2Controller'ı denetleyicilere ekleyin
- BooksController'a Sürüm 1.0 ve BooksV2Controller'a 2.0 özelliği eklendi
- GetAllBooksAsync yöntemi oluşturun
- IBookService'e yöntem ekleyin ve bunu BookService'de uygulayın
- Yöntemi Depolar katmanına aktarın.
## 21.2. Kitaplar V2 - URL'li
- Rota özelliğine {v:apiversiyon}/ ekleyin.
## 21.3. Kitaplar V2 - Başlıklı
- Rota niteliklerinden {v:apiversiyon}/ öğesini kaldırın, eskisi gibi yapın.
- ServicesExtensions'ta ApiVersionReader satırını AddApiVersioning yöntemine ekleyin.
## 21.4. Sürümlerin Kullanımdan Kaldırılması
- ApiVersion Özniteliğine Kullanımdan Kaldırılmış = true değerini ekleyin.
## 21.5. Sözleşmeler
- ServicesExtensions'ta Convensions satırlarını ekleyin
- ApiVersion Niteliklerini Kaldır...
# 22. Önbelleğe Alma
- BooksController'ın GetAll yöntemine ResponseCache niteliğini ekleyin. Bunu yaptığınızda yanıta yeni bir başlık eklenecektir, bu da yanıtın önbelleğe alınabileceği anlamına gelir.
- ServicesExtensions'a bir ConfigurationResponseCaching yöntemi ekleyin ve bunu Program.cs'de çağırın
- Program.cs'deki Cors yapılandırma satırının altına app.UseResponseCaching() öğesini ekleyin
- Postacıda test edin (postacı ayarlarında önbellek başlığı gönderme seçeneğinin kapalı olduğundan emin olun), ilk yanıtta önbellek süresini gösteren bir "Önbellek Kontrolü" başlığı görüyoruz. Başka bir istek gönderirsek ek bir "Yaş" başlığı görürüz.
- Çeşitli kaynaklar için Önbellek Profilleri oluşturabiliriz.
- Program.cs'de AddControllers yönteminde önbellek profilleri yapılandırmasını ekleyin.
- CacheProfile parametresiyle ResponseCache niteliğini ekleyin.
- Üzerinde kendi ResponseCache özelliği bulunduğundan, getall yöntemi dışındaki yanıtlarda 300 saniye süreli önbellek göreceğiz.
## 22.1. Marvin ile önbelleğe alma
- Marvin.Cache.Headers (6.0.0) paketini Sunum katmanına yükleyin.
- ServicesExtensions'a bir ConfigurationHttpCacheHeaders yöntemi ekleyin
- Bunu Program.cs'den çağırın ve Cors satırının altına UseHttpCacheHeaders() satırını ekleyin (öyle olduğundan emin olun).
- Uygulamayı test edin, Yanıtta 3 başlık daha olacak; ETag, Süresi Doluyor, Son Değiştirilme Tarihi.
- ResponseCache nitelikleri artık kaldırılabilir.
- Varsayılan yapılandırma genel önbellektir, süresi 60 saniyedir. Bunları Services.Extensions dosyasının config'inden değiştirebiliriz ve/veya HttpCacheExpiration niteliğini parametrelerle kullanabiliriz.


# 23. Hız Sınırlaması
İsteklerin oranını sınırlayabiliriz. 429 durum koduyla yanıt vereceğiz Çok fazla istek.
- AspNetCoreRateLimit -Version 4.0.1 paketini WebApi projesine yükleyin.
- Program.cs'de Hizmetlere AddMemoryCache'i ekleyin.
- ServicesExtensions'a bir yapılandırma yöntemi ekleyin
- Program.cs'ye bu yönteme bir çağrı ekleyin
- Cors'tan önce Program.cs'ye bir UseIpRateLimiting çağrısı ekleyin.
- Test sırasında yanıt başlıklarında yeni parametreler olacak;
X-Rate-Limit-Limit, X-Rate-Limit-Kalan, X-Rate-Limit-Sıfırlama.
- İstek sayısı tanımlanan dönemdeki oranı aştığında API, Çok Fazla İstek durum kodu 429'u döndürecektir.

# 24. Kimlik Doğrulama ve Yetkilendirme
Kimlik Doğrulama ~ Giriş, Yetkilendirme ~ İzinler
Bu konuda JSON Web Token (JWT) ile kimlik çerçevesi kullanılacaktır.
## 24.1. Kimlik
- Identity projesine Microsoft.AspNetCore.Identity.EntityFrameworkCore (6.0.0) paketini yükleyin.
- Entities projesinde Models klasörüne User : IdentityUser sınıfını ekleyin.
- Repositories projesinde RepositoryContext : DbContext öğesini IdentityDbContext<User> öğesinden devralacak şekilde değiştirin ve OnModelCreating yöntemini yeniden düzenleyin.
- WebApi projesinde Identity için yeni bir yapılandırma yöntemi ekleyin
- Program.cs'de
- Kimlik Doğrulama Ekle
- Kimlik Yapılandır
- UseAuthorization'dan önce UseAuthentication
- PM'de bir geçiş ekleyin. Varsayılan (hedef) projenin WebApi olduğundan emin olun
- Tablolar oluşturmak için Güncelle-Veritabanı komutunu kullanın.
## 24.2. Rolleri Tanımlama
Depolar projesinde
- RoleConfiguration sınıfını EfCore.Config klasörüne ekleyin, rollere ekleyin.
- RepositoryContext'te, RoleConfiguration'ı ekleyin veya tüm tür yapılandırmalarını almak için Assembly'yi kullanın.
- Geçiş ekleme ve veritabanını güncelleme
## 24.3. Kullanıcı
- Entities projesi DataTransferObjects klasörüne UserForRegistrationDto'yu ekleyin
- Bu sınıf için eşlemeyi WebApi'nin Utilities klasöründeki MappingProfile'a ekleyin
- Hizmetler katmanında,
- sözleşmelere bir IAuthenticationService arayüzü ekleyin. AuthenticationManager sınıfını ekleyin.
- IServiceManager'a IAuthenticationService ekleyin
- AuthenticationManager'ı ServiceManager'a ekleyin
- Sunum katmanında,
- Yeni bir denetleyici AuthenticationController ve RegisterUser yöntemini ekleyin.
## 24.5. JSON Web Belirteci (JWT)
- WebApi projesinde JwtSettings'i appsettings.json'a ekleyin
- Microsoft.AspNetCore.Authentication.JwtBearer (6.0.0) paketini WebApi projesine yükleyin.
- ServicesExtensions'a bir yapılandırma yöntemi ekleyin ve Program.cs'yi çağırın

## 24.6. Uç Noktaların Güvenliğini Sağlama
- Yöntemin güvenliğini sağlamak için GetAll yöntemine Authorize niteliğini ekleyin.

## 24.7. Kimlik Doğrulama ve JWT
- Varlıklar projesinde, Dtos klasörüne UserForAuthenticationDto kayıt türünü ekleyin.
- Kullanıcıyı doğrulamak için Hizmetler Katmanına gidin,
- IAuthenticationService'e ValidateUser yöntemi imzasını ekleyin
- AuthenticationManager'da yöntemi uygulayın
- IAuthenticationService'e CreateToken imzasını ekleyin
- System.IdentityModel.Tokens.Jwt (6.14.1) paketini yükleyin
- AuthenticationManager'da CreateToken yöntemini uygulayın
- Sunum katmanında,
- HttpPost özniteliğine sahip bir Kimlik Doğrulama (giriş) yöntemi ekleyin
- BooksController yöntemlerine rol kimlik doğrulamaları ekleyin.
## 24.8. Jetonu Yenile
- Varlıklar projesinde, RefreshToken ve RefreshTokenExpiryTime özelliklerini User'a ekleyin.
- Bir geçiş ekleyin ve veritabanını güncelleyin
- TokenDto adında bir Dto ekleyin
- Hizmetler katmanında, IAuthenticationService arayüzünün CreateToken yöntemini yeniden düzenleyin ve dize yerine TokenDto'yu döndürecek şekilde uygulanmasını sağlayın.
- Sunum katmanında, TokenDto'ya dönmek için AuthenticationController'ın Authenticate yöntemini yeniden düzenleyin.
- Hizmetler katmanında, IAuthenticationService'e RefreshToken adlı bir Yöntem ekleyin ve bunu AuthenticationManager'da uygulayın.
- Yöntemi uygularken Varlıklar/İstisnalar'da bir RefreshTokenBadRequestException oluşturun.
- Sunum katmanında, AuthenticationController'da Refresh adlı yeni bir gönderi yöntemi oluşturun.
# 25. Belgeler
## 25.1. Swagger'ı Yapılandırma
- WebApi projesinde, ServicesExtensions'a ConfigurationSwagger yöntemini ekleyin.
- Program.cs'deki kayıtları değiştirin (app.UseSwaggerUI, ConfigurationSwagger)
- Sunum katmanında Denetleyiciler'e gidin. Bir denetleyicinin hangi sürüme ait olduğunu tanımlamak için ApiExplorerSettings Özniteliğini ekleyin.

# 26. Kaynakları ve Özellikleri Genişletme
## 26.1. Kategori ve Yazar Ekleme, Kitabı Genişletme
- Varlıklar Projesi:
- Yazar ve Kategori modellerini ekleyin.
-RequestFeatures'ta AuthorParameters oluşturun
- Kategori ve Yazar için Dto'lar oluşturun
- BookLinkParameters'ı Dtos'a ekleyin
- Özel istisnalar ekleyin.
-Depo Katmanı:
- RepositoryContext'te Kategoriler ve Yazarlar için DbSet'leri ekleyin
- Sözleşmelere Yazar ve Kategori için arayüzler ekleyin
- EfCore'a CategoryRepository ve AuthorRepository'yi ekleyin
- Uzantılar klasörüne bir AuthorRepositoryExtensions sınıfı ekleyin
- IRepositoryManager ve RepositoryManager'a Yazar ve Kategori ekleyin
- Yazar ve Kategori için Yapılandırmalar ekleyin.
- WebApi katmanı:
- Geçiş ekleyin, veritabanını bırakın ve veritabanını güncelleyin.
- Eşlemeler Ekle
- AuthorLinks için Kapsamlı IoC kaydı ekleyin.
- Hizmetler katmanı:
- Sözleşmelere ICategoryService, IAuthorService, IAuthorlinks'i ekleyin ve bunların uygulamalarının Yönetici sınıflarını ekleyin.
- Sunum katmanı,
- Denetleyiciler oluşturun, Kimlik Doğrulamaları ve sürüm oluşturmayı ekleyin...
- API'yi test edin
## 26.2 İlişkiler, Bire Çok İlişki
- Bir Kitabın bir Kategorisi vardır, bir kategorinin birçok kitabı vardır.
- Bir Kitabın bir Yazarı vardır, bir yazar birçok kitap yayınlayabilir.
- Kitaba Kategori Kimliği, Kategori, Yazar Kimliği ve Yazar ekleyin.
- Kategori ve Yazara Kitap ekleyin.
- BookDetailsDto'yu ekleyin ve eşleyiciyi Book'tan buna eşleyecek şekilde yapılandırın.
- Kitap'a Kategori ve Yazar gezinme özelliklerini dahil etmek için Depoyu yapılandırın.
-Refactor BooksController tüm kitap ayrıntılarını alır ve AuthorId ve CategoryId ile kitap oluşturur.
# 27 Dosya İşlemleri
## Yüklemek
- FilesController adında yeni bir denetleyici oluşturun, bir Upload POST yöntemi ekleyin.
- WebApi projesinde bir Medya klasörü oluşturun.
## İndirmek
- FilesController'da bir Get yöntemi oluşturun.
