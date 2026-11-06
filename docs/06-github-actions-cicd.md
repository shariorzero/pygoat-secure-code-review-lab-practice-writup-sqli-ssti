# ৬. GitHub Actions দিয়ে CI/CD পাইপলাইনে SAST ইন্টিগ্রেশন

> প্রতিটি Pull Request-এ স্বয়ংক্রিয়ভাবে Semgrep চালিয়ে SQL Injection ও SSTI ধরার সম্পূর্ণ DevSecOps ওয়ার্কফ্লো।

## GitHub Actions দিয়ে SAST ও AI-সহায়তায় লাইভ কোড ফিক্স (PyGoat ল্যাব)

### ১. আমরা আসলে কী করছি?

আগের রেস্টুরেন্টের উদাহরণেই বলি। বাবুর্চি (ডেভেলপার) নতুন রেসিপি (কোড) জমা
দিলে একজন স্বয়ংক্রিয় **ফুড সেফটি ইন্সপেক্টর** (Semgrep) সেটা পড়ে দেখে, রান্না
না করেই। রেসিপিতে "বিষ মেশানোর" মতো কিছু থাকলে সে লাল পতাকা তোলে।

-   **SAST (Static Application Security Testing):** কোড রান না করে, শুধু
    সোর্স কোড পড়ে ভালনারেবিলিটি খোঁজা।
-   **Semgrep:** একটি ফ্রি SAST টুল, যা প্যাটার্ন মিলিয়ে ঝুঁকিপূর্ণ কোড ধরে।
    যেমন: raw() SQL, ইউজার ইনপুট দিয়ে ফাইল লেখা।
-   **GitHub Actions:** GitHub-এর অটোমেশন সিস্টেম। প্রতিটি Pull Request
    (PR) খুললেই সে নিজে থেকে Semgrep চালায়।
-   **Pull Request (PR):** "আমার করা পরিবর্তনগুলো main কোডে মেশাতে চাই,
    দয়া করে দেখুন" এই অনুরোধ।

এই ল্যাবে তিনটি কাজ হবে:

1.  PR খুললেই স্বয়ংক্রিয় স্ক্যান চালু করা
2.  স্ক্যান PyGoat-এর দুটি দুর্বলতা ধরবে: **SQL Injection** (/sql_lab) ও
    **SSTI** (/ssti/lab)
3.  AI-এর পরামর্শ যাচাই করে কোড ঠিক করা এবং স্ক্যান পাস হওয়া দেখা

### ২. দুটি দুর্বলতা বিগিনারের ভাষায়

**SQL Injection:** লগইন ফর্মে ইউজার যা লেখে, কোড সেটা সরাসরি ডাটাবেসের
প্রশ্নের সাথে জুড়ে দেয়। হ্যাকার নামের ঘরে `admin' --` লিখলে প্রশ্নটাই বদলে যায়
এবং পাসওয়ার্ড ছাড়াই ঢুকে পড়ে। এটা অনেকটা ফর্মে নাম না লিখে অর্ডার-স্লিপেই নতুন
নির্দেশ লিখে দেওয়ার মতো।

**SSTI (Server-Side Template Injection):** ইউজারের লেখা টেক্সট দিয়ে
সার্ভার একটি আসল টেমপ্লেট ফাইল বানাচ্ছে। ইউজার `{{ ... }}` বা `{% ... %}`
লিখলে সার্ভার সেটাকে নিজের কোড ভেবে চালিয়ে দেয়। এটা অনেকটা কাস্টমারের লেখা
নোটকেই রান্নার নির্দেশ ভেবে পালন করার মতো।

**ফিক্সের মূল আইডিয়া একই:** *ইউজারের ইনপুট কখনও কোড হবে না, সবসময় শুধু ডাটা
হবে।*

### ৩. সুবিধা ও অসুবিধা

| সুবিধা | অসুবিধা |
|---|---|
| ভুল প্রোডাকশনে যাওয়ার আগেই ধরা পড়ে | **False positive** হতে পারে, অর্থাৎ নিরাপদ কোডকেও ঝুঁকিপূর্ণ বলতে পারে |
| মানুষের রিভিউ-এর উপর নির্ভরতা কমে, প্রতি PR-এ একই মান | শুধু স্ট্যাটিক স্ক্যান, তাই রানটাইম বা লজিক বাগ (যেমন ভুল পারমিশন) সব ধরতে পারে না |
| ফিক্স সস্তা: ডেভেলপ করার সময়ই ঠিক করা যায় | রুল ও কনফিগ বুঝতে সময় লাগে |
| ফ্রি ও সেটআপ সহজ | স্ক্যানে কিছুটা সময় লাগে, PR আটকে থাকে |
| ডেভেলপার হাতে-কলমে শেখে | AI-এর ফিক্স অন্ধভাবে বিশ্বাস করা বিপজ্জনক, যাচাই করতেই হবে |

**এটা কেন জরুরি?** ডেভেলপমেন্টের পর যত দেরিতে বাগ ধরা পড়ে, ঠিক করার খরচ তত
বাড়ে। প্রোডাকশনে SQL Injection মানে ডাটা লিক, আইনি ঝামেলা ও সুনামের ক্ষতি।
এই কারণেই এই ধারণাকে বলা হয় **"Shift Left"**: নিরাপত্তা পরীক্ষা পাইপলাইনের
শুরুর দিকে নিয়ে আসা। এটাই DevSecOps-এর মূল কথা।

### ৪. ল্যাব শুরুর আগে প্রস্তুতি (Kali Linux)

**ক) আপনার GitHub-এ PyGoat-এর কপি থাকতে হবে।** GitHub Actions শুধু
GitHub-এ থাকা রিপোতেই চলে, লোকাল ফোল্ডারে নয়। তাই PyGoat রিপো আপনার নিজের
অ্যাকাউন্টে **Fork** করুন এবং লোকাল রিপোর সাথে যুক্ত করুন:

```bash
cd ~/pygoat              # আপনার লোকাল PyGoat ফোল্ডার
git remote -v            # দেখুন origin কোন রিপো
```

origin যদি আসল `adeyosemanputra/pygoat` দেখায়, তাহলে নিজের ফর্কে বদলান:

```bash
git remote set-url origin https://github.com/<আপনার-ইউজারনেম>/pygoat.git
```

**খ) GitHub-এ লগইনের উপায় ঠিক করুন।** পাসওয়ার্ড দিয়ে push কাজ করে না,
**Personal Access Token** লাগবে (GitHub → Settings → Developer settings
→ Tokens, repo ও workflow scope সহ), অথবা SSH key ব্যবহার করুন।

**গ) (ঐচ্ছিক, ক্লাসের আগে) লোকালে Semgrep চালিয়ে দেখে নিন:**

```bash
pipx install semgrep   # না থাকলে: sudo apt install pipx
semgrep scan --config p/django --config p/python introduction/views.py
```

এতে ক্লাসের আগেই দেখে নিতে পারবেন কোন লাইনগুলো ধরা পড়বে।

### ৫. ধাপ ১: Semgrep ওয়ার্কফ্লো যোগ করা

> **নোট:** `returntocorp/semgrep-action` এখন **deprecated**, Semgrep নিজে
> এখন অফিসিয়াল কন্টেইনার ব্যবহারের পরামর্শ দেয়। নিচের ভার্সনটি ডেমোর জন্য
> বেশি নির্ভরযোগ্য, কারণ এটি টোকেন ছাড়াই চলে। YAML ফাইলে ইনডেন্টেশন
> (স্পেস) ঠিক রাখা জরুরি — এক স্পেস ভুল হলেই ওয়ার্কফ্লো ফেল করবে।

ফাইল তৈরি করুন:

```bash
mkdir -p .github/workflows
nano .github/workflows/semgrep.yml
```

এই কনটেন্ট দিন (স্পেস অক্ষত রাখুন):

```yaml
name: Semgrep SAST Scan

on:
  pull_request:
    branches: [ "main", "v2" ]

jobs:
  semgrep:
    name: Scan PR for Django Vulnerabilities
    runs-on: ubuntu-latest
    container:
      image: semgrep/semgrep
    steps:
      - uses: actions/checkout@v4
      - name: Run Semgrep on the introduction app
        run: semgrep scan --config p/django --config p/python --error introduction/
```

**লাইনগুলোর মানে:**

-   `on: pull_request` --- PR খুললে বা আপডেট করলে চলবে
-   `runs-on: ubuntu-latest` --- GitHub-এর একটি ভার্চুয়াল মেশিনে চলবে (আপনার Kali-তে নয়)
-   `actions/checkout@v4` --- রিপোর কোড মেশিনে নামায়
-   `--config p/django --config p/python` --- Django ও Python-এর রেডিমেড নিরাপত্তা রুলসেট
-   `--error` --- কিছু পাওয়া গেলে ধাপটি **ফেল** করবে, এই ফেলই PR-কে "লাল" করে
-   `introduction/` --- শুধু এই অ্যাপ স্ক্যান হবে

### ধাপ ১: GitHub-এ একটি নতুন Repository তৈরি করুন

1.  [GitHub](https://github.com)-এ সাইন ইন করে ডানপাশের **+** আইকন থেকে
    **New repository**-তে ক্লিক করুন।
2.  **Repository name** দিন (যেমন: my-project বা pygoat-custom)।
3.  **Public** নাকি **Private** রাখবেন সিলেক্ট করুন।
4.  **"Initialize this repository with a README"** টিকচিহ্নটি **ফাঁকা
    রাখুন** (কারণ আপনার লোকাল ফোল্ডারে ইতিমধ্যেই কোড রয়েছে)।
5.  **Create repository** বাটনে ক্লিক করুন।

### ধাপ ২: লোকাল ফোল্ডারে Git কনফিগার ও কমিট করুন

আপনার কম্পিউটারের টার্মিনাল খুলে প্রোজেক্টের ফোল্ডারে প্রবেশ করুন:

```bash
cd ~/path/to/your/project-folder
```

যদি ফোল্ডারটিতে আগে থেকে Git সেটআপ না করা থাকে, তবে নিচের কমান্ডগুলো একে একে চালান:

```bash
# ১. Git ইনিশিয়ালাইজ করুন
git init

# ২. সব ফাইল ট্র্যাকিংয়ে যোগ করুন
git add .

# ৩. কোড কমিট (Save) করুন
git commit -m "Initial commit of local files"

# ৪. ডিফল্ট ব্রাঞ্চের নাম main করুন
git branch -M main
```

### ধাপ ৩: GitHub-এর সাথে লোকাল ফোল্ডার যুক্ত করে Push করুন

১. GitHub-এ নতুন বানানো রিপোজিটরির URL-টি কপি করুন (দেখতে কিছুটা এমন হবে:
`https://github.com/your-username/repository-name.git`)।

২. আপনার টার্মিনালে লোকাল রিপোর সাথে GitHub-কে রিমোট লিঙ্ক হিসেবে যোগ করুন:

```bash
git remote add origin https://github.com/your-username/repository-name.git
```

৩. এবার আপনার লোকাল কোড GitHub-এ আপলোড (Push) করুন:

```bash
git push -u origin main
```

### কাস্টম বা বিদ্যমান রিপোজিটরির ক্ষেত্রে (Remote Reset)

যদি আপনার লোকাল ফোল্ডারটি আগে থেকেই অন্য কোনো রিপোর (যেমন মূল PyGoat-এর)
সাথে যুক্ত থাকে এবং আপনি নিজের ফর্কে না পাঠিয়ে নতুন রিপোতে পাঠাতে চান, তাহলে
রিমোট URL-টি বদলে নিন:

```bash
git remote set-url origin https://github.com/your-username/repository-name.git
git push -u origin main
```

### পাসওয়ার্ড সংক্রান্ত সমস্যা হলে (Personal Access Token / SSH)

**এই ফাইলটি আগে main ব্রাঞ্চে পাঠান**, যাতে পরে PR খুললেই স্ক্যান চালু হয়:

```bash
git checkout main
git add .github/workflows/semgrep.yml
git commit -m "ci: add Semgrep SAST workflow"
git push origin main
```

### ৬. ধাপ ২: PR খুলে স্ক্যান লাইভ দেখানো

**১. নতুন ব্রাঞ্চ খুলুন ও ছোট পরিবর্তন করুন:**

```bash
git checkout -b fix/sast-demo
echo "# SAST demo" >> README.md
git add README.md
git commit -m "docs: trigger SAST demo"
git push -u origin fix/sast-demo
```

**২. GitHub-এ গিয়ে** "Compare & pull request" চাপুন, base হিসেবে main
রেখে PR তৈরি করুন।

**৩. PR পেজের নিচে** চেকস্ট্যাটাস দেখুন (হলুদ ঘূর্ণি, তারপর লাল ✗)।
**Details** চাপলে লগে দেখা যাবে Semgrep ঠিক কোন ফাইলের কোন লাইনে কোন রুল
ভেঙেছে। স্টুডেন্টদের এই লগটাই দেখান।

**৪. ব্লক করার ব্যাপারে:** চেক ফেল করলেই PR আপনা-আপনি merge-বন্ধ হয় না। ব্লক
করতে রিপোর **Settings → Branches → Branch protection rule** (main-এর
জন্য) এ গিয়ে "Require status checks to pass" চালু করে Scan PR for Django
Vulnerabilities সিলেক্ট করতে হবে। এটা ক্লাসে একবার দেখিয়ে দিলে ভালো।

### ৭. ধাপ ৩: ফিক্সের আগে দুর্বলতা নিজ চোখে দেখা (Exploit)

PyGoat লোকালি চালান:

```bash
cd ~/pygoat
python3 manage.py runserver
# অথবা Docker হলে: docker compose up
```

ব্রাউজারে `http://127.0.0.1:8000` খুলে লগইন করুন।

**SQL Injection ডেমো (/sql_lab):** নামের ঘরে `admin'--` (বা ল্যাবের
নির্দেশিত পেলোড) লিখুন, পাসওয়ার্ড যা-ই দিন। ঢুকে যাওয়া বা ডাটা দেখা গেলে
দুর্বলতা কনফার্ম।

**SSTI ডেমো (/ssti/lab):** ব্লগের লেখার ঘরে এই নিরীহ পেলোড দিন: `{% now
"Y" %}`। পেজে লেখাটা না দেখিয়ে **বর্তমান সাল** দেখালে বুঝবেন সার্ভার আপনার
ইনপুটকে কোড হিসেবে চালিয়েছে। এটাই SSTI।

এই "আগের অবস্থা" স্ক্রিনশট বা লাইভ দেখিয়ে রাখুন, ফিক্সের পর তুলনা করতে কাজে
লাগবে।
