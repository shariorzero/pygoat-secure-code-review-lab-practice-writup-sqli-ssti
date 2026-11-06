# ৪. AI-সহায়তায় কোড রিভিউ (AI-Assisted Code Review)

> ChatGPT/Claude/Copilot ব্যবহার করে Source → Guard → Sink মেথডোলজিতে ভালনারেবল কোড রিভিউ।

#### **পর্ব ৩: AI কোড রিভিউ এবং Semgrep SAST স্ক্যানিং (৬০ -- ৯০ মিনিট)**

**ধাপ ১: AI দিয়ে চেকলিস্ট ও কোড রিভিউ (৬০--৭৫ মিনিট)**

ChatGPT, Claude বা GitHub Copilot খুলে sql_lab এবং ssti_lab-এর কোডটুকু কপি করে নিচের প্রম্পটটি দিন:

```text
Act as a Senior Application Security Engineer. Review the following
Django view functions from an open-source project using the Source ->
Guard -> Sink methodology:

1. Identify all security vulnerabilities (specifically looking for
ORM/SQL injection and Template Injection).

2. Point out any deceptive or incomplete security checks (like partial
ORM usage or empty sanitizer functions).

3. Provide the exact secure refactored Django code without breaking
existing functionality.
```

-   **আলোচনা:** স্টুডেন্টদের দেখান কীভাবে আমরা ম্যানুয়ালি যে বাগ দুটি ধরতে ৩০
    > মিনিট সময় নিয়েছি, AI তা ১০ সেকেন্ডে ধরে ফেলল এবং filter_blog() যে
    > খালি সেটিও চিহ্নিত করল।

---

