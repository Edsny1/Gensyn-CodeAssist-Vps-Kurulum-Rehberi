# CodeAssist VPS Kurulum Rehberi

**Hazırlayan:** OshVanK  
**Güncellenme:** Kasım 2025

## 🖥️ Sistem Gereksinimleri

- **RAM**: Minimum 12GB (16GB önerilir)
- **Disk**: Minimum 10GB SSD
- **İşletim Sistemi**: Ubuntu 20.04/22.04 LTS

> 💡 **İpucu**: Uygun fiyatlı VPS sunucular için [servarica.com](https://servarica.com) adresini ziyaret edebilirsiniz.

---

## 📋 Kurulum Adımları

### Adım 1: Sistemi Güncelleyin

VPS'inize SSH ile bağlandıktan sonra aşağıdaki komutları sırasıyla çalıştırın:

```bash
sudo apt-get update && sudo apt-get upgrade -y
```

### Adım 2: Gerekli Bağımlılıkları Yükleyin

```bash
sudo apt install curl iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip ufw screen gawk -y
```

### Adım 3: Docker Kurulumu

Önce eski Docker sürümlerini temizleyin:

```bash
sudo apt update -y && sudo apt upgrade -y
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

Docker GPG anahtarını ekleyin:

```bash
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg -y
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

Docker repository'sini ekleyin:

```bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Docker'ı yükleyin ve test edin:

```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

Docker'ın düzgün çalıştığını test edin:

```bash
sudo docker run hello-world
```

Docker servisini başlatın ve otomatik başlaması için aktif edin:

```bash
sudo systemctl enable docker
sudo systemctl restart docker
```

---

## 🔧 UV Paket Yöneticisi Kurulumu

### Adım 4: UV'yi Yükleyin

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

PATH değişkenine ekleyin:

```bash
echo 'export PATH=$PATH:/root/.local/bin' >> ~/.bashrc
source ~/.bashrc
```

---

## 📦 CodeAssist Kurulumu

### Adım 5: Repository'yi Klonlayın

```bash
git clone https://github.com/gensyn-ai/codeassist.git
```

Proje dizinine girin:

```bash
cd codeassist
```

---

## 🔑 HuggingFace Token Oluşturma

CodeAssist'i çalıştırmak için **yazma izinli** bir HuggingFace token'ına ihtiyacınız var.

### Adım 6: HuggingFace Token Alın

1. **[HuggingFace](https://huggingface.co/)** web sitesine gidin ve hesap oluşturun (yoksa)
2. Sağ üst köşedeki profil fotoğrafınıza tıklayın
3. **"Access Tokens"** seçeneğine tıklayın
4. **"Create new token"** butonuna tıklayın
5. Token türü olarak **"Write"** seçin
6. Token'a bir isim verin (örn: "codeassist-vps")
7. **"Create"** butonuna tıklayın
8. Oluşturulan token'ı kopyalayın ve güvenli bir yere kaydedin

---

## 🚀 CodeAssist'i Başlatma

### Adım 7: Uygulamayı Çalıştırın

**Standart port (3000) için:**

```bash
uv run run.py
```

**Farklı port kullanmak için (örn: aynı sunucuda RL-Swarm çalışıyorsa):**

```bash
uv run run.py --port 3001
```

### Önemli Notlar:

- Program HuggingFace token'ınızı soracaktır
- Token'ı yapıştırdığınızda **ekranda görünmeyecektir** - bu normaldir
- Kurulum birkaç dakika sürebilir, sabırlı olun

![Token Girişi](https://github.com/user-attachments/assets/b17cd961-c42b-436f-a74c-b7bc5e36851f)

Kurulum tamamlandığında şu mesajı göreceksiniz:

![Kurulum Tamamlandı](https://github.com/user-attachments/assets/9fbce663-bbe9-48ef-88ce-566dc41f378c)

---

## 🌐 Port Yönlendirme (SSH Tunneling)

### Adım 8: Yerel Bilgisayarınızdan Bağlantı Kurun

**Windows kullanıcıları için:** WSL (Windows Subsystem for Linux) açın  
**Mac kullanıcıları için:** Terminal uygulamasını açın

Aşağıdaki komutu çalıştırın (kendi bilgilerinizle değiştirerek):

**Standart port (3000) için:**

```bash
ssh -L 8000:localhost:8000 -L 8008:localhost:8008 -L 3000:localhost:3000 -L 8001:localhost:8001 kullanici_adiniz@VPS_IP_adresiniz
```

**Port 3001 kullanıyorsanız:**

```bash
ssh -L 8000:localhost:8000 -L 8008:localhost:8008 -L 3001:localhost:3001 -L 8001:localhost:8001 kullanici_adiniz@VPS_IP_adresiniz
```

**Örnek kullanım:**

```bash
ssh -L 8000:localhost:8000 -L 8008:localhost:8008 -L 3000:localhost:3000 -L 8001:localhost:8001 root@192.168.1.100
```

![SSH Örneği](https://github.com/user-attachments/assets/202ab744-ec79-4de5-b573-cc3029896a50)

### Bağlantı Adımları:

1. `Are you sure you want to continue connecting (yes/no/[fingerprint])?` sorusuna **"yes"** yazın
2. VPS şifrenizi girin (şifre yazarken ekranda görünmez)
3. Başarılı bağlantı sonrası şu ekranı göreceksiniz:

![Başarılı Bağlantı](https://github.com/user-attachments/assets/08484943-d8eb-43b7-9d57-054e0ff89218)

---

## 💻 CodeAssist Kullanımı

### Adım 9: Tarayıcıda Açın

Kullandığınız porta göre aşağıdaki adresleri tarayıcınızda açın:

- Port 3000 kullanıyorsanız: **http://localhost:3000**
- Port 3001 kullanıyorsanız: **http://localhost:3001**

![CodeAssist Arayüzü](https://github.com/user-attachments/assets/04c31b01-43ee-476c-8fcd-ea0fe1fbf624)

### Çözüm Gönderme:

- Sol tarafta problemi göreceksiniz
- Sağ tarafta çözümünüzü yazın
- **"Submit Solution"** butonuna tıklayın
- **En az 2-3 çözüm gönderin**
- ⚠️ **Önemli:** Çözümleri direkt kopyala-yapıştır yapmayın, kendi çözümlerinizi yazın

![Çözüm Gönderme](https://github.com/user-attachments/assets/8f9d3031-61e5-44e1-ab5c-c1a927c23415)

---

## ✅ Kurulumu Tamamlama

### Adım 10: Modeli HuggingFace'e Yükleyin

Çözümlerinizi gönderdikten sonra:

1. VPS terminaline geri dönün
2. **CTRL + C** tuşlarına basın
3. Model otomatik olarak HuggingFace'e yüklenecektir

![Model Yükleme](https://github.com/user-attachments/assets/926075d0-f351-49cd-97f4-77e686991494)

### Puan Takibi:

Puanlarınızı dashboard'dan takip edebilirsiniz:
- Aynı e-posta adresi ile giriş yapın
- OTP doğrulaması yapın
- Puanlarınızı ve istatistiklerinizi görüntüleyin

---

## 🆘 Sorun Giderme

### Docker Çalışmıyor
```bash
sudo systemctl status docker
sudo systemctl restart docker
```

### Port Zaten Kullanımda
```bash
# Portu kullanan işlemi bulun
sudo lsof -i :3000
# İşlemi sonlandırın
sudo kill -9 PID_NUMARASI
```

### UV Komutu Bulunamıyor
```bash
source ~/.bashrc
# veya
export PATH=$PATH:/root/.local/bin
```

---

## 📝 Notlar

- Kurulum sırasında sabırlı olun, container'ların oluşturulması zaman alabilir
- HuggingFace token'ınızı kimseyle paylaşmayın
- Düzenli olarak sistem güncellemelerini yapın
- Yedek almayı unutmayın

---

## 🔗 Faydalı Bağlantılar

- [Gensyn CodeAssist GitHub](https://github.com/gensyn-ai/codeassist)
- [HuggingFace](https://huggingface.co/)
- [Docker Dökümantasyonu](https://docs.docker.com/)

---

**Hazırlayan:** [OshVanK](https://github.com/OshVanK)  
**Lisans:** MIT  
**Son Güncelleme:** Kasım 2025

⭐ Bu rehber işinize yaradıysa GitHub'da yıldız vermeyi unutmayın!
