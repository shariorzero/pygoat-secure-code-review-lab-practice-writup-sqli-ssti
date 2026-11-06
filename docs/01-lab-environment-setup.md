# ১. ল্যাব এনভায়রনমেন্ট সেটআপ (Lab Environment Setup)

> Kali Linux-এ Git, VS Code, pyenv/Python 3.10 এবং PyGoat (OWASP vulnerable Django app) ইনস্টল করার ধাপে ধাপে গাইড।

## PyGoat Lab Setup — Git, pyenv, Python venv

আপনার Kali Linux-এ সম্পূর্ণ Python Virtual Environment (venv) ব্যবহার করে
PyGoat (v2 ব্রাঞ্চ) সেটআপ করার ধাপে ধাপে গাইড নিচে দেওয়া হলো:

Git ইন্সটলের কমান্ড

Kali Linux-এ Git ইনস্টল করার কমান্ড:

```bash
sudo apt update && sudo apt install git -y
```

![Screenshot](images/image14.png)

ইনস্টলেশন সফল হয়েছে কিনা তা যাচাই করতে রান করুন:

```bash
git --version
```

![Screenshot](images/image3.png)

### **ধাপ ২: কোড ক্লোন করা এবং v2 ব্রাঞ্চে যাওয়া**

Docker-এর ঝামেলা এড়াতে আমরা সহজ v2 ব্রাঞ্চটি ব্যবহার করব। টার্মিনালে পর পর
কমান্ডগুলো দিন:

```bash
git clone https://github.com/adeyosemanputra/pygoat.git
cd pygoat
git checkout v2
```

![Screenshot](images/image17.png)

### **ধাপ ১: প্রয়োজনীয় বিল্ড টুলস ও ডিপেন্ডেন্সি ইনস্টল করা**

পাইথন সোর্স থেকে কম্পাইল করার জন্য নিচের প্যাকেজগুলো দরকার হবে:

```bash
sudo apt install build-essential libssl-dev zlib1g-dev libbz2-dev 
libreadline-dev libsqlite3-dev curl git libncursesw5-dev xz-utils 
tk-dev libxml2-dev libxmlsec1-dev libffi-dev -y
```

![Screenshot](images/image25.png)

### **ধাপ ২: pyenv ইনস্টল করা**

```bash
curl https://pyenv.run | bash
```

![Screenshot](images/image31.png)

### **ধাপ ৩: টার্মিনাল এনভায়রনমেন্ট সেটআপ করা**

নিচের কমান্ডগুলো কপি করে একসাথে টার্মিনালে পেস্ট করে এন্টার দিন:

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
echo '[[ -d $PYENV_ROOT/bin ]] && export
PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
source ~/.bashrc
```

![Screenshot](images/image13.png)

### **ধাপ ৪: পাইথন ৩.১০ ইনস্টল করা এবং প্রজেক্টে সেট করা**

এখন এক কমান্ডেই পাইথন ৩.১০.১৩ ইনস্টল করে নিন (এটি হতে একটু সময় নিতে পারে):

```bash
pyenv install 3.10.13
```

![Screenshot](images/image19.png)

ইনস্টলেশন শেষ হলে আপনার ~/pygoat ফোল্ডারে গিয়ে পাইথন ৩.১০ লোকালি সেট করে
দিন:

```bash
cd ~/pygoat
pyenv local 3.10.13
```

![Screenshot](images/image32.png)

### **ধাপ ৫: পাইথন ভার্সন যাচাই ও নতুন venv তৈরি**

এখন চেক করে দেখুন সঠিক পাইথন সেট হয়েছে কি না:

```bash
python --version
```

*এটি এখন নিশ্চিতভাবে আউটপুট দেখাবে: Python 3.10.13*

![এটি এখন নিশ্চিতভাবে আউটপুট দেখাবে: Python 3.10.13](images/image9.png)

এরপর পাইথন ৩.১০ দিয়েই আপনার প্রজেক্টের ভার্চুয়াল এনভায়রনমেন্ট বানিয়ে ফেলুন:

```bash
python -m venv pygoat-venv
source pygoat-venv/bin/activate
```

এখন আর কোনো এরর ছাড়াই আপনি pip install -r requirements.txt চালিয়ে
PyGoat-এর কাজ চালিয়ে যেতে পারবেন! কোনো ধাপে আটকে গেলে আমাকে জানান।

![PyGoat-এর কাজ চালিয়ে যেতে পারবেন! কোনো ধাপে আটকে গেলে আমাকে জানান।](images/image8.png)

---

---

# PYGOAT installation

### **ধাপ ৪: requirements.txt ফাইল প্যাচ করা**

PostgreSQL এবং Heroku-এর লাইব্রেরিগুলো লিনাক্সে ইনস্টল হতে অনেক সময় এরর দেয়।
যেহেতু আমরা ডিফল্ট SQLite ব্যবহার করব, তাই এগুলো মুছে ফেলতে হবে। টার্মিনালে
nano এডিটর দিয়ে ফাইলটি খুলুন:

```bash
nano requirements.txt
```

![Screenshot](images/image16.png)
ফাইলের ভেতর থেকে django-heroku এবং psycopg2 (বা psycopg2-binary) লেখা
লাইন দুটি খুঁজে পুরোপুরি মুছে দিন।

![লাইন দুটি খুঁজে পুরোপুরি মুছে দিন।](images/image26.png)

-   সেভ করে বের হওয়ার জন্য কীবোর্ডে চাপুন: Ctrl + O -> Enter -> Ctrl + X।

### **ধাপ ৫: settings.py ফাইল কনফিগার করা**

Heroku-এর সেটিংস নিষ্ক্রিয় করতে হবে। nano এডিটর দিয়ে সেটিংস ফাইলটি খুলুন:

```bash
nano pygoat/settings.py
```

ফাইলের একেবারে নিচে গিয়ে নিচের লাইন দুটির শুরুতে `#` বসিয়ে দিন (কমেন্ট আউট করুন):

```python
# import django_heroku
# django_heroku.settings(locals())
```

![Screenshot](images/image18.png)

![Screenshot](images/image1.png)

একই ফাইলে `ALLOWED_HOSTS` লেখা লাইনটি খুঁজে সেটি মুছে নিচের মতো করে দিন:

```python
ALLOWED_HOSTS = ['*']
```

![Screenshot](images/image28.png)

-   সেভ করে বের হয়ে আসুন (Ctrl + O -> Enter -> Ctrl + X)।

### **ধাপ ৬: প্যাকেজ ইনস্টল ও ডেটাবেস মাইগ্রেশন**

এখন আপনার ভার্চুয়াল এনভায়রনমেন্টে প্রয়োজনীয় সব প্যাকেজ ইনস্টল করুন এবং ডেটাবেস
রেডি করুন:

```bash
pip install -r requirements.txt
python manage.py migrate
```

![Screenshot](images/image29.png)

### **ধাপ ৭: অ্যাডমিন অ্যাকাউন্ট তৈরি ও সার্ভার রান**

লগইন করার জন্য একটি ডেমো অ্যাকাউন্ট তৈরি করে নিন:

```bash
python manage.py createsuperuser
```

*(এখানে Username হিসেবে alice দিন, ইমেইল ফাঁকা রাখুন এবং Password হিসেবে
alicepass123 দিন।)*

![alicepass123 দিন।)](images/image36.png)

সবশেষে সার্ভার চালু করুন:

```bash
python manage.py runserver 127.0.0.1:8000
```

এখন আপনার Kali Linux-এর ফায়ারফক্স বা অন্য যেকোনো ব্রাউজারে গিয়ে
**[http://127.0.0.1:8000](http://127.0.0.1:8000)** লিখে এন্টার দিলেই
PyGoat-এর ওয়েবসাইটটি লাইভ দেখতে পাবেন! কাজ শেষে টার্মিনালে Ctrl + C চেপে
সার্ভার বন্ধ করতে পারবেন এবং deactivate লিখে ভার্চুয়াল এনভায়রনমেন্ট থেকে বের
হতে পারবেন।

![হতে পারবেন।](images/image5.png)

![হতে পারবেন।](images/image20.png)

---

# VS Code installation

কালী লিনাক্সে (Kali Linux) .deb ফাইলের মাধ্যমে খুব সহজে Visual Studio Code
(VS Code) ডাউনলোড এবং ইনস্টল করার ধাপগুলো নিচে দেওয়া হলো:

১. **.deb ফাইল ডাউনলোড করুন:** আপনার ব্রাউজার থেকে [[VS Code-এর অফিসিয়াল
ডাউনলোড পেজে]{.underline}](https://code.visualstudio.com/Download) যান
এবং লিনাক্স সেকশন থেকে **.deb (Debian, Ubuntu)** বাটনে ক্লিক করে ফাইলটি
ডাউনলোড করুন। *নিশ্চিতকরণ:* আপনার Downloads ফোল্ডারে code_..._amd64.deb
নামের একটি ফাইল জমা হবে।

![নামের একটি ফাইল জমা হবে।](images/image27.png)

২. **টার্মিনাল (Terminal) ওপেন করে Downloads ফোল্ডারে যান:** টার্মিনাল চালু
করে নিচের কমান্ডটি লিখে Enter চাপুন:

```bash
cd ~/Downloads
```

*নিশ্চিতকরণ:* আপনার টার্মিনালের প্রম্পট পরিবর্তন হয়ে ~/Downloads$ দেখাবে।

![নিশ্চিতকরণ: আপনার টার্মিনালের প্রম্পট পরিবর্তন হয়ে ~/Downloads$ দেখাবে।](images/image24.png)

৩. **ইনস্টলার কমান্ড রান করুন:**apt কমান্ড ব্যবহার করে ফাইলটি ইনস্টল করুন (এটি
নিজে থেকেই প্রয়োজনীয় সব এক্সট্রা প্যাকেজ বা ডিপেন্ডেন্সি ঠিক করে নেবে):

```bash
sudo apt install ./code_*.deb
```

![Screenshot](images/image33.png)

আপনার কাছে পাসওয়ার্ড চাইলে কালী লিনাক্সের পাসওয়ার্ড দিন (টাইপ করার সময়
স্ক্রিনে কিছু দেখা যাবে না) এবং Enter চাপুন। মাঝখানে ডিস্ক স্পেস ব্যবহারের
অনুমতি চাইলে Y লিখে Enter দিন। *নিশ্চিতকরণ:* ইনস্টলেশন শেষ হলে টার্মিনালে
কোনো এরর ছাড়াই প্রসেসটি সম্পন্ন হবে।

৪. **VS Code চালু করুন:** ইনস্টল হয়ে গেলে টার্মিনালে নিচের কমান্ডটি লিখে অথবা
অ্যাপ্লিকেশন মেনু থেকে সার্চ করে VS Code ওপেন করুন:

![অ্যাপ্লিকেশন মেনু থেকে সার্চ করে VS Code ওপেন করুন:](images/image35.png)

*নিশ্চিতকরণ:* আপনার স্ক্রিনে Visual Studio Code-এর উইন্ডোটি চালু হবে।

---
