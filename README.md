---

```markdown
# 🐍 Python Projesi - Ortam Kurulumu (uv ile)

Bu proje, hızlı ve verimli Python paket yönetimi için [`uv`](https://github.com/astral-sh/uv) kullanmaktadır.  
`uv`, `pip`, `pip-tools`, `virtualenv`, `venv` ve `pipx` yerine geçebilecek, Rust tabanlı yüksek performanslı bir araçtır.

Bu belge, projeye yeni dahil olan geliştiricilerin ortamı kolayca kurabilmesi için yazılmıştır.

---

## 🔧 Gereksinimler

- **Python 3.8 veya üstü** (proje bu sürümü gerektiriyor)
- **uv paket yöneticisi**

---

## 🚀 1. uv Paket Yöneticisini Kur

Daha önce `uv` yüklü değilse, aşağıdaki komutlardan biriyle hızlıca kurabilirsin:


#### Doğru kurulum komutu:

```bash
# macOS / Linux / WSL
pip install uv

# Alternatif: resmi kurulum scripti (geliştirici tarafından önerilir)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

> Not: `uv` kuruluysa, aşağıdaki komutla sürüm kontrolü yapabilirsin:
>
> ```bash
> uv --version
> ```

---

## 📦 2. Proje Bağımlılıklarını Yüklemek

Proje kök dizinine (`pyproject.toml` veya `requirements.txt` dosyasının bulunduğu yer) geç ve aşağıdaki komutu çalıştır:

```bash
# Sanal ortam oluştur ve bağımlılıkları yükle
uv venv
source .venv/bin/activate  # Linux/macOS
# Veya Windows'ta:
# .venv\Scripts\activate

uv pip install -r requirements.txt
```

> 📝 Eğer projede `pyproject.toml` varsa ve `poetry` benzeri yapı kullanılıyorsa:
>
> ```bash
> uv pip install -e .
> ```

---

## 🔁 Alternatif: Tek Komutla Kurulum

Eğer `requirements.txt` veya `pyproject.toml` dosyanız varsa, aşağıdaki tek komutla sanal ortam ve bağımlılıklar kurulabilir:

```bash
uv sync
```

Bu komut, `requirements.txt` veya `pyproject.toml` temelinde ortamı tam olarak oluşturur (pip-tools benzeri davranış).

---

## 🧪 3. Projeyi Çatırdatmadan Çalıştır

Ortam kurulduktan sonra, projeyi şu şekilde çalıştırabilirsin:

```bash
python main.py
# veya
uv run python main.py
```

> `uv run` komutu, gerekli bağımlılıkları kontrol eder ve doğru ortamda çalıştırır.

---

## 💡 Ekstra İpuçları

- **Sanal ortamı etkinleştirmek**:  
  ```bash
  source .venv/bin/activate    # Linux/macOS
  .venv\Scripts\activate       # Windows
  ```

- **Yeni bir kütüphane eklemek**:
  ```bash
  uv pip install numpy
  ```

- **requirements.txt oluşturmak**:
  ```bash
  uv pip freeze > requirements.txt
  ```

- **Ortamı silmek**:
  ```bash
  rm -rf .venv
  ```

---

## 📚 Daha Fazla Bilgi

- `uv` resmi belgeleri: [https://docs.astral.sh/uv](https://docs.astral.sh/uv)
- GitHub: [https://github.com/astral-sh/uv](https://github.com/astral-sh/uv)

---

## 🙋 Yardım Gerekirse

Eğer bir hata alırsan veya kurulumda takılırsan, ekibinle paylaşmaktan çekinme!

> Bu README, uv ile hızlı başlangıç yapman için hazırlanmıştır.  
> Kolay gelsin! 🌟
