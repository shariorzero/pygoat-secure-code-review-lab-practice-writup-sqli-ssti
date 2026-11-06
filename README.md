<div id="top"></div>

<!-- PROJECT SHIELDS -->
[![Platform][ostad-shield]][ostad-url]
[![License: Educational][license-shield]][license-url]
[![PyGoat][pygoat-shield]][pygoat-url]
[![Semgrep][semgrep-shield]][semgrep-url]

<br />
<div align="center">
  <h3 align="center">Secure Code Review & DevSecOps Lab — PyGoat (SQL Injection & SSTI)</h3>

  <p align="center">
    ম্যানুয়াল কোড রিভিউ, AI-সহায়তা এবং Semgrep SAST দিয়ে GitHub Actions CI/CD পাইপলাইনে
    অ্যাপ্লিকেশন সিকিউরিটি শেখানোর একটি সম্পূর্ণ ক্লাস ওয়াকথ্রু (বাংলা)
    <br />
    A complete, hands-on class write-up on finding and fixing <strong>SQL Injection</strong> and
    <strong>Server-Side Template Injection (SSTI)</strong> in a real open-source Django app
    (<a href="https://github.com/adeyosemanputra/pygoat">PyGoat</a>), then automating the
    check with Semgrep inside a GitHub Actions pipeline.
    <br />
    <br />
    <a href="docs/01-lab-environment-setup.md"><strong>শুরু করুন — ল্যাব সেটআপ »</strong></a>
    ·
    <a href="#class-outline--class-outline">Class Outline</a>
    ·
    <a href="https://github.com/your-org/your-repo-name/issues">Report Issue</a>
  </p>
</div>

---

## সূচিপত্র (Table of Contents)

<details>
  <summary>Click to expand</summary>
  <ol>
    <li><a href="#about-this-repo--এই-রিপো-সম্পর্কে">About This Repo</a></li>
    <li><a href="#tools--arsenal-used">Tools & Arsenal Used</a></li>
    <li><a href="#repository-structure">Repository Structure</a></li>
    <li>
      <a href="#class-outline--class-outline">Class Outline</a>
      <ul>
        <li><a href="docs/01-lab-environment-setup.md">১. ল্যাব এনভায়রনমেন্ট সেটআপ (Git · VS Code · pyenv · PyGoat)</a></li>
        <li><a href="docs/02-sql-injection-case.md">২. কেস স্টাডি: SQL Injection (/sql_lab)</a></li>
        <li><a href="docs/03-ssti-case.md">৩. কেস স্টাডি: Server-Side Template Injection (/ssti/lab)</a></li>
        <li><a href="docs/04-ai-assisted-code-review.md">৪. AI-সহায়তায় কোড রিভিউ</a></li>
        <li><a href="docs/05-semgrep-sast-scanning.md">৫. Semgrep দিয়ে SAST স্ক্যানিং</a></li>
        <li><a href="docs/06-github-actions-cicd.md">৬. GitHub Actions CI/CD-তে SAST ইন্টিগ্রেশন</a></li>
      </ul>
    </li>
    <li><a href="#getting-started">Getting Started</a></li>
    <li><a href="#disclaimer--সতর্কীকরণ">Disclaimer</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>

---

## About This Repo / এই রিপো সম্পর্কে

**🇧🇩 বাংলায়:**
এই রিপোজিটরিটি [Ostad.app](https://ostad.app) প্ল্যাটফর্মের একটি সাইবার সিকিউরিটি ক্লাসের
লেকচার ম্যাটেরিয়াল ও ল্যাব ওয়াকথ্রু। এখানে OWASP-এর ইচ্ছাকৃতভাবে দুর্বল একটি Django
অ্যাপ্লিকেশন **[PyGoat](https://github.com/adeyosemanputra/pygoat)** ব্যবহার করে দেখানো হয়েছে:

- কীভাবে **ম্যানুয়াল কোড রিভিউ**-এর মাধ্যমে Source → Guard → Sink পদ্ধতিতে **SQL Injection**
  এবং **Server-Side Template Injection (SSTI)** চিহ্নিত ও এক্সপ্লয়েট করা যায়,
- কীভাবে **AI (ChatGPT/Claude/Copilot)** দিয়ে সেকেন্ডের মধ্যে একই বাগ ধরা যায়,
- কীভাবে **Semgrep** নামক একটি ফ্রি SAST টুল দিয়ে পুরো কোডবেস স্ক্যান করা যায়, এবং
- কীভাবে সেই স্ক্যানকে **GitHub Actions**-এর মাধ্যমে প্রতিটি Pull Request-এ স্বয়ংক্রিয় করে
  একটি DevSecOps পাইপলাইন তৈরি করা যায়।

**🇬🇧 In English:**
This repository is the class material and lab walkthrough for a cybersecurity course delivered
on the [Ostad.app](https://ostad.app) platform. Using OWASP's intentionally vulnerable Django
application **[PyGoat](https://github.com/adeyosemanputra/pygoat)**, it walks through finding
and fixing **SQL Injection** and **Server-Side Template Injection (SSTI)** using manual code
review (the Source → Guard → Sink method), AI-assisted review, Semgrep static analysis (SAST),
and finally wiring that scan into a **GitHub Actions** CI/CD pipeline that runs automatically on
every Pull Request.

<p align="right">(<a href="#top">back to top</a>)</p>

### Tools & Arsenal Used

* [![Kali Linux][Kali-shield]][Kali-url]
* [![Python][Python-shield]][Python-url]
* [![Django][Django-shield]][Django-url]
* [![VS Code][VSCode-shield]][VSCode-url]
* [![Semgrep][Semgrep-shield2]][Semgrep-url]
* [![GitHub Actions][GHA-shield]][GHA-url]

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Repository Structure

```text
.
├── README.md
├── LICENSE
├── docs/
│   ├── 01-lab-environment-setup.md      # Git, VS Code, pyenv, Python 3.10, PyGoat venv setup
│   ├── 02-sql-injection-case.md         # Class recap + Case 1: SQL Injection (/sql_lab)
│   ├── 03-ssti-case.md                  # Case 2: Server-Side Template Injection (/ssti/lab)
│   ├── 04-ai-assisted-code-review.md    # AI-assisted (ChatGPT/Claude/Copilot) code review
│   ├── 05-semgrep-sast-scanning.md      # Semgrep install, CLI scanning, Manual vs SAST vs AI
│   └── 06-github-actions-cicd.md        # Semgrep + GitHub Actions CI/CD pipeline, PR demo
└── images/                              # All screenshots referenced by the docs (36 images)
```

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Class Outline / Class Outline

এই ক্লাসটি তিনটি পর্বে ভাগ করা (মোট আনুমানিক ৯০ মিনিট):

| পর্ব (Part) | সময় | বিষয় | ডকুমেন্ট |
|---|---|---|---|
| প্রস্তুতি | — | Lab environment: Git, VS Code, pyenv, Python 3.10, PyGoat (v2) venv install | [01](docs/01-lab-environment-setup.md) |
| পর্ব ১ | ০০–২৫ মিনিট | রিক্যাপ (Source/Sink/Guard) + PyGoat দ্রুত পরিচিতি | [02](docs/02-sql-injection-case.md) |
| পর্ব ২ | ২৫–৫৫ মিনিট | ম্যানুয়াল কোড রিভিউ ও লাইভ এক্সপ্লয়েট — SQL Injection ও SSTI | [02](docs/02-sql-injection-case.md), [03](docs/03-ssti-case.md) |
| পর্ব ৩ | ৬০–৯০ মিনিট | AI কোড রিভিউ, Semgrep SAST স্ক্যানিং ও GitHub Actions CI/CD | [04](docs/04-ai-assisted-code-review.md), [05](docs/05-semgrep-sast-scanning.md), [06](docs/06-github-actions-cicd.md) |

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Getting Started

নিজের লোকাল এনভায়রনমেন্টে এই ল্যাবটি প্র্যাকটিস করতে নিচের ধাপগুলো অনুসরণ করুন।

### Prerequisites

* **Attacker/Lab Machine:** Kali Linux (বা WSL2 / যেকোনো Debian-based Linux)
* **Target Application:** [PyGoat](https://github.com/adeyosemanputra/pygoat) (`v2` branch), চালানো হবে লোকালি একটি Python venv-এর ভেতর
* **Editor:** Visual Studio Code
* **SAST Tool:** [Semgrep](https://semgrep.dev/)
* **CI/CD:** একটি GitHub অ্যাকাউন্ট (GitHub Actions ব্যবহারের জন্য)

### Clone This Repo

```sh
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```

এরপর ধাপে ধাপে এগোনোর জন্য **[docs/01-lab-environment-setup.md](docs/01-lab-environment-setup.md)**
ফাইলটি দিয়ে শুরু করুন।

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Disclaimer / সতর্কীকরণ

এই ম্যাটেরিয়ালটি শুধুমাত্র **শিক্ষামূলক ও এথিক্যাল** উদ্দেশ্যে তৈরি। এখানে দেখানো সব
এক্সপ্লয়েট শুধুমাত্র **PyGoat**-এর মতো ইচ্ছাকৃতভাবে দুর্বল (intentionally vulnerable)
ল্যাব অ্যাপ্লিকেশনে, নিজের লোকাল/আইসোলেটেড এনভায়রনমেন্টে প্র্যাকটিস করার জন্য। অনুমতি
ছাড়া কোনো থার্ড-পার্টি সিস্টেমে এই কৌশলগুলো প্রয়োগ করা বেআইনি।

This material is for **educational and ethical** purposes only. All techniques shown here are
meant to be practiced against intentionally vulnerable lab applications such as PyGoat, in your
own local/isolated environment. Applying these techniques against any third-party system without
explicit authorization is illegal.

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Contributing

এই ক্লাস ম্যাটেরিয়ালে কোনো তথ্য সংযোজন, সংশোধন বা নতুন ল্যাব যোগ করতে চাইলে নির্দ্বিধায়
Pull Request পাঠাতে পারেন।

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/ImproveLab`)
3. Commit your Changes (`git commit -m 'docs: add notes on X'`)
4. Push to the Branch (`git push origin feature/ImproveLab`)
5. Open a Pull Request

<p align="right">(<a href="#top">back to top</a>)</p>

---

## License

Distributed under an educational-use license — see [`LICENSE`](LICENSE) for details.
Intended strictly for ethical, authorized security training.

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Contact

**Instructor / Course:** Lead Cybersecurity Instructor, [Ostad.app](https://ostad.app)
Project Link: [https://github.com/your-username/your-repo-name](https://github.com/your-username/your-repo-name)

<p align="right">(<a href="#top">back to top</a>)</p>

---

## Acknowledgments

* [PyGoat — OWASP Vulnerable Django Application](https://github.com/adeyosemanputra/pygoat)
* [Semgrep — Lightweight Static Analysis (SAST)](https://semgrep.dev/)
* [GitHub Actions Documentation](https://docs.github.com/en/actions)
* [Ostad.app](https://ostad.app)
* [Best-README-Template](https://github.com/othneildrew/Best-README-Template)

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- MARKDOWN LINKS & BADGES -->
[ostad-shield]: https://img.shields.io/badge/Platform-Ostad.app-2D6CDF?style=for-the-badge
[ostad-url]: https://ostad.app
[license-shield]: https://img.shields.io/badge/License-Educational%20Use-yellow?style=for-the-badge
[license-url]: LICENSE
[pygoat-shield]: https://img.shields.io/badge/Target%20App-PyGoat-red?style=for-the-badge
[pygoat-url]: https://github.com/adeyosemanputra/pygoat
[semgrep-shield]: https://img.shields.io/badge/SAST-Semgrep-0B5A2E?style=for-the-badge
[semgrep-url]: https://semgrep.dev/

[Kali-shield]: https://img.shields.io/badge/Kali_Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white
[Kali-url]: https://www.kali.org/
[Python-shield]: https://img.shields.io/badge/Python_3.10-3776AB?style=for-the-badge&logo=python&logoColor=white
[Python-url]: https://www.python.org/
[Django-shield]: https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=white
[Django-url]: https://www.djangoproject.com/
[VSCode-shield]: https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white
[VSCode-url]: https://code.visualstudio.com/
[Semgrep-shield2]: https://img.shields.io/badge/Semgrep-000000?style=for-the-badge&logo=semgrep&logoColor=white
[GHA-shield]: https://img.shields.io/badge/GitHub_Actions-2088FF?style=for-the-badge&logo=githubactions&logoColor=white
[GHA-url]: https://docs.github.com/en/actions
