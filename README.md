# Codyssey

Codyssey, kodlama dünyasında adeta bir keşif ve macera yolculuğuna çıkmanızı sağlayan, Docker tabanlı hazır geliştirme
ortamı sunan bir projedir. PHP tabanlı projeleriniz (Laravel, pure PHP veya diğer framework’ler) için tamamen
özelleştirilebilir bir ortam sunarak geliştirme sürecinizi basitleştirir ve sağlam bir altyapı oluşturur. Proje, PHP,
Nginx, MySQL, Redis ve Memcached gibi güçlü servislerle donatılmış olup, her biri log, config ve (gerektiğinde) data
dizinleriyle desteklenerek projelerinizi etkileyici ve sorunsuz bir şekilde hayata geçirmenize olanak tanır.

## Özellikler

- **Hazır Geliştirme Ortamı:** Docker ile önceden yapılandırılmış, anında kullanılabilir bir geliştirme ortamı.
- **Kolay Kurulum:** Minimum yapılandırma gereksinimi ile hızlı başlangıç.
- **Modüler Yapı:** Projenizi ihtiyaçlarınıza göre genişletebilir ve özelleştirebilirsiniz.
- **Çeşitli Teknolojiler:** PHP, Node.js, Python gibi diller ve araçlar ile entegre çalışma imkanı.

---

## Proje Yapısı

Proje, aşağıdaki dizin yapısıyla gelir:

```bash
Codyssey/
├── docker-compose.yml       # Tüm servisleri tanımlayan Docker Compose dosyası
├── dockify/                 # Docker altyapı ve yapılandırma dosyaları
│   ├── Dockerfile           # PHP ortamını oluşturan Dockerfile
│   ├── config/              # Servislerin yapılandırma dosyaları
│   │   ├── php/             # PHP ve PHP-FPM ayarları
│   │   │   ├── php.ini
│   │   │   ├── php-fpm.conf
│   │   ├── nginx/           # Nginx web sunucusu ayarları
│   │   │   ├── nginx.conf
│   │   ├── mysql/           # MySQL veritabanı ayarları
│   │   │   ├── my.cnf
│   │   ├── redis/           # Redis önbellek ayarları
│   │   │   ├── redis.conf
│   │   ├── memcached/       # Memcached önbellek ayarları
│   │   │   ├── memcached.conf
│   ├── data/                # Kalıcı veri saklama dizinleri
│   │   ├── mysql/           # MySQL veritabanı dosyaları
│   │   ├── redis/           # Redis kalıcı veri dosyaları (RDB/AOF)
│   ├── logs/                # Servis log dosyaları
│   │   ├── php/             # PHP ve PHP-FPM logları
│   │   ├── nginx/           # Nginx logları
│   │   ├── mysql/           # MySQL logları
│   │   ├── redis/           # Redis logları
│   │   ├── memcached/       # Memcached logları
├── /                        # Proje kodlarının yer aldığı ana dizin
├── README.md                # Bu dosya
```

- **Ana Dizin (`./`)**: Proje kodlarınızı buraya yerleştirin; Docker konteynerlerinde `/var/www` ile eşleşir.
- **`dockify/`**: Docker servislerinin yapılandırma, veri ve log dosyalarını barındırır. "Dockify", Docker ile
  basitleştirme (simplify) fikrini yansıtır.

---

## Servisler ve Kullanımları

Codyssey, aşağıdaki servisleri sağlar:

### 1. PHP (`dockify-php`)

- **Açıklama**: PHP-FPM tabanlı bir uygulama sunucusu. PHP projelerinizi (Laravel, pure PHP vb.) çalıştırmak için kullanılır.
- **Port**: `8000` (ana makineden erişilebilir).
- **Bağımlılıklar**: MySQL, Redis ve Memcached’e bağlıdır.
- **Dizinler**:
    - `dockify/config/php/php.ini`: PHP ayarlarını özelleştirir (örneğin, `memory_limit`, `error_reporting`).
    - `dockify/config/php/php-fpm.conf`: PHP-FPM ayarları (örneğin, `pm.max_children`).
    - `dockify/logs/php/`: PHP ve PHP-FPM logları burada saklanır.

### 2. Nginx (`dockify-nginx`)

- **Açıklama**: Web sunucusu olarak çalışır, PHP dosyalarını `dockify-php` üzerinden işler ve statik dosyaları sunar.
- **Port**: `8080` (HTTP istekleri için).
- **Bağımlılıklar**: `dockify-php`’ye bağlıdır.
- **Dizinler**:
    - `dockify/config/nginx/nginx.conf`: Nginx yapılandırma dosyası (örneğin, PHP yönlendirme, gzip ayarları).
    - `dockify/logs/nginx/`: Erişim ve hata logları burada saklanır.

### 3. MySQL (`dockify-mysql`)

- **Açıklama**: Veritabanı servisi, projelerinizin veri saklama ihtiyacını karşılar.
- **Port**: `3306` (veritabanı bağlantıları için).
- **Ortam Değişkenleri**:
    - `MYSQL_ROOT_PASSWORD: "root"`: Root kullanıcısının şifresi.
- **Dizinler**:
    - `dockify/data/mysql/`: Veritabanı dosyaları burada kalıcı olarak saklanır.
    - `dockify/config/mysql/my.cnf`: MySQL ayarları (örneğin, `innodb_buffer_pool_size`).
    - `dockify/logs/mysql/`: MySQL logları.

### 4. Redis (`dockify-redis`)

- **Açıklama**: Önbellekleme ve kuyruk yönetimi için hızlı bir in-memory veri deposu.
- **Port**: `6379` (Redis istemcileri için).
- **Dizinler**:
    - `dockify/config/redis/redis.conf`: Redis ayarları (örneğin, `maxmemory`, `appendonly`).
    - `dockify/data/redis/`: Kalıcı veri dosyaları (`dump.rdb` veya `appendonly.aof`) burada saklanır.
    - `dockify/logs/redis/`: Redis logları (`redis.log`).

### 5. Memcached (`dockify-memcached`)

- **Açıklama**: Hafif bir önbellekleme sistemi, basit anahtar-değer çiftlerini RAM’de tutar.
- **Port**: `11211` (Memcached istemcileri için).
- **Dizinler**:
    - `dockify/config/memcached/memcached.conf`: Memcached ayarları (örneğin, `memory_limit`, `connections`).
    - `dockify/logs/memcached/`: Memcached logları (`memcached.log`).
    - **Not**: Memcached in-memory çalıştığı için bir `data/` dizini yoktur.

---

## Kurulum ve Kullanım

### Gereksinimler

- [Docker](https://www.docker.com/) ve [Docker Compose](https://docs.docker.com/compose/) yüklü olmalıdır.
- Git yüklü olmalıdır.

### Adımlar

### 1. Repoyu Klonlama
```bash
git clone https://github.com/kullanici/Codyssey.git
cd Codyssey
```
### 2. Ortamı Başlatma
   Docker Compose ile tüm servisleri başlatın:
```bash
docker-compose up -d --build
```
- -d: Arka planda çalıştırır.
- --build: İlk kurulumda veya Dockerfile değiştiğinde imajı yeniden oluşturur.

### 3. Proje Kodlarını Ekleme
- Kodlarınızı Codyssey/ ana dizinine yerleştirin. Bu dizin, dockify-php ve dockify-nginx servislerinde /var/www ile eşleşir.
- Örnek: Bir Laravel projesi kurmak için:

#### 1. PHP konteynerine bağlanın:
```bash
docker exec -it dockify-php bash
```
#### 2. Composer ile Laravel kurun:
```bash
composer create-project --prefer-dist laravel/laravel /var/www
```
### 4. Konteynerleri Durdurma
- Servisleri durdurmak için:
```bash
docker-compose down
```
- Veriler (dockify/data/) ve loglar (dockify/logs/) korunur.

## Özelleştirme 
- Portlar: docker-compose.yml içindeki ports değerlerini değiştirerek ana makine portlarını özelleştirebilirsiniz (
örneğin, 8080:80 → 80:80).
- Yapılandırma: dockify/config/ altındaki dosyaları düzenleyerek servis ayarlarını değiştirebilirsiniz (örneğin, PHP
belleğini artırma, Redis kalıcılığını kapatma).
- Servisler: İhtiyacınıza göre yeni servisler ekleyebilir (örneğin PostgreSQL) veya mevcut olanları kaldırabilirsiniz (
örneğin Memcached).
- 
## Logları Görüntüleme
- Docker loglarını görmek için:
```bash
docker logs dockify-<servis>
``` 
Örnek: docker logs dockify-nginx
- Dosya tabanlı loglar için dockify/logs/<servis>/ dizinine bakın.

## Notlar
- Veri Kalıcılığı: MySQL ve Redis için data/ dizinleri verileri saklar. Memcached yalnızca RAM’de çalışır.
- Ağ: Tüm servisler dockify-network adlı bir bridge ağında çalışır, birbirleriyle kolayca iletişim kurabilir.
- Hafiflik: alpine tabanlı imajlar (Nginx, Redis, Memcached) kullanılarak disk ve bellek kullanımı optimize edilmiştir.

Sorularınız veya özelleştirme talepleriniz için repoya bir issue açabilirsiniz. Kodlama yolculuğunuzda Codyssey sizinle!