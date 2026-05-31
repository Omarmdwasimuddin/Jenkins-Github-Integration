# 🔗 Jenkins + GitHub Integration
### *Webhook দিয়ে Automatic Build Trigger করা*

> **লক্ষ্য:** GitHub-এ কোনো পরিবর্তন করলে Jenkins স্বয়ংক্রিয়ভাবে Build শুরু করবে।

---

## 📋 সম্পূর্ণ Flow একনজরে

```
GitHub Repo (push/edit)
        │
        ▼
   Webhook (ngrok URL)
        │
        ▼
  ngrok (localhost tunnel)
        │
        ▼
  Jenkins (port 8080)
        │
        ▼
  Build Triggered ✅
  Console Output দেখো
```

---

## ধাপসমূহ (Overview)

| # | ধাপ | বিষয় |
|---|-----|-------|
| ১ | Plugin চেক | Git Plugin যাচাই করা |
| ২ | GitHub Repo | নতুন Repository তৈরি |
| ৩ | Jenkins Job | Freestyle Project তৈরি |
| ৪ | SCM Config | GitHub URL ও Branch সেট |
| ৫ | ngrok Setup | Tunnel তৈরি করা |
| ৬ | Webhook | GitHub-এ Webhook যোগ |
| ৭ | Test | Auto-build পরীক্ষা |

---

## ধাপ ১ — Git Plugin যাচাই করা

### ✅ করণীয়:

1. Jenkins Dashboard → **`Manage Jenkins`** ক্লিক করো
2. **`Plugins`** ক্লিক করো
3. **`Install plugins`** ক্লিক করো
4. Search বক্সে লেখো:
   ```
   git plugin
   ```
5. Plugin তালিকায় **Git Plugin** দেখা গেলে — ✅ আর কিছু করতে হবে না

   > 💡 **Note:** Plugin আগে থেকেই installed থাকলে নতুন করে install করার দরকার নেই।

---

## ধাপ ২ — GitHub-এ Repository তৈরি করা

### ✅ করণীয়:

1. **[github.com](https://github.com)** এ লগইন করো
2. উপরে ডান কোণায় **`+`** আইকন → **`New repository`** ক্লিক করো
3. Repository তথ্য দাও:

   ```
   Repository name : Test-Jenkins
   Visibility       : ● Public   (এটা সিলেক্ট করো)
   ```

4. **`Create repository`** বাটনে ক্লিক করো
5. তৈরি হওয়া Repo-র URL কপি করে রাখো:
   ```
   https://github.com/তোমার-username/Test-Jenkins.git
   ```

---

## ধাপ ৩ — Jenkins-এ নতুন Job তৈরি করা

### ✅ করণীয়:

1. Jenkins Dashboard-এ **`New Item`** ক্লিক করো
2. Item name দাও:
   ```
   Github Jenkins
   ```
3. Project type সিলেক্ট করো:
   ```
   ● Freestyle project   ← এটা সিলেক্ট করো
   ```
4. **`OK`** ক্লিক করো

---

## ধাপ ৪ — Source Code Management ও Build Config

### ✅ করণীয় (ধাপে ধাপে):

#### 4A — Git URL যোগ করা

1. **`Source Code Management`** সেকশনে যাও
2. **`Git`** সিলেক্ট করো
3. **Repository URL** বক্সে GitHub Repo URL পেস্ট করো:
   ```
   https://github.com/তোমার-username/Test-Jenkins.git
   ```
4. **Branch Specifier** বক্সে লেখো:
   ```
   */main
   ```

#### 4B — Webhook Trigger চালু করা

1. **`Build Triggers`** সেকশনে যাও
2. নিচের অপশনটি টিক দাও:
   ```
   ☑  GitHub hook trigger for GITScm polling
   ```

#### 4C — Build Step যোগ করা

1. **`Build Steps`** সেকশনে যাও
2. **`Add build step`** → **`Execute Windows batch command`** সিলেক্ট করো
3. Command বক্সে লেখো:
   ```batch
   echo Build triggered from GitHub!
   ```

#### 4D — Save করা

1. **`Save`** বাটনে ক্লিক করো ✅

---

## ধাপ ৫ — ngrok দিয়ে Tunnel তৈরি করা

> **কেন ngrok দরকার?**
> Jenkins চলছে `localhost:8080`-এ। GitHub সেই local address-এ পৌঁছাতে পারে না।
> ngrok একটি **Public URL** তৈরি করে যা GitHub Webhook পাঠাতে পারবে।

### ✅ 5A — ngrok Download করা

1. Browser-এ যাও:
   ```
   https://ngrok.com/download/windows
   ```
2. **Download** বাটনে ক্লিক করো
3. `.zip` ফাইল Download হবে
4. Zip ফাইলে **Right Click → Extract All** করো
5. Extract করা ফোল্ডারে `ngrok.exe` ফাইলে ক্লিক করো
6. **CMD (Command Prompt)** খুলে যাবে

---

### ✅ 5B — ngrok Account ও Authtoken সেট করা

1. Browser-এ আবার যাও:
   ```
   https://ngrok.com/download/windows?tab=download
   ```
2. **`Add your authtoken`** সেকশনে **`Sign up`** ক্লিক করো
3. Account তৈরির প্রক্রিয়া সম্পন্ন করো
4. Dashboard-এ **`Your Authtoken`** ক্লিক করো
5. Token কপি করো

   ![ngrok config add-authtoken command in CMD](https://imgur.com/WDvbXHD.png)

6. CMD-এ নিচের কমান্ড চালাও (Token পেস্ট করো):
   ```cmd
   ngrok config add-authtoken YOUR_AUTHTOKEN_HERE
   ```

   ![ngrok Authtoken Page](https://imgur.com/3Vm4jpz.png)

   > ✅ সফল হলে লেখা আসবে: `Authtoken saved to configuration file`

---

### ✅ 5C — Tunnel চালু করা

CMD-এ নিচের কমান্ড চালাও:
```cmd
ngrok http 8080
```

নিচের মতো Output আসবে:

```
Session Status     online
Account            তোমার নাম
Forwarding         https://xxxx-xx-xxx-xxx-xx.ngrok-free.app → http://localhost:8080
```

![ngrok http 8080 tunnel running](https://imgur.com/FWDj0zT.png)

> 📌 **`Forwarding`** লাইনের **`https://xxxx...ngrok-free.app`** URL টি কপি করে রাখো।
> এটাই GitHub Webhook-এ দিতে হবে।

---

## ধাপ ৬ — GitHub Webhook যোগ করা

### ✅ করণীয়:

1. GitHub-এ **`Test-Jenkins`** Repo-তে যাও
2. **`Settings`** ট্যাবে ক্লিক করো
3. বাম মেনু থেকে **`Webhooks`** ক্লিক করো
4. **`Add webhook`** বাটনে ক্লিক করো
5. **Payload URL** বক্সে ngrok URL পেস্ট করো এভাবে:

   ```
   https://xxxx-xx-xxx-xxx-xx.ngrok-free.app/github-webhook/
   ```

   > ⚠️ শেষে `/github-webhook/` অবশ্যই যোগ করতে হবে!

6. বাকি সব Settings **Default** রাখো:
   ```
   Content type : application/x-www-form-urlencoded
   Events       : Just the push event ✅
   Active        : ✅ (checked)
   ```

7. **`Add webhook`** বাটনে ক্লিক করো

   ![GitHub Add Webhook configuration](https://imgur.com/D9cgu0R.png)

   > ✅ সফল হলে Webhook-এর পাশে **সবুজ টিক (✓)** দেখাবে।

---

## ধাপ ৭ — Test করা (Auto Build)

### ✅ করণীয়:

1. GitHub-এ **`Test-Jenkins`** Repo-তে যাও
2. **`README.md`** ফাইলে ক্লিক করো
3. **পেন্সিল আইকন (Edit)** ক্লিক করো
4. কিছু একটা পরিবর্তন করো, যেমন:
   ```
   Testing Jenkins Auto Build - পরিবর্তন ১
   ```
5. **`Commit changes`** বাটনে ক্লিক করো

---

### ✅ Jenkins-এ Build চেক করা:

1. Jenkins Dashboard-এ **`Github Jenkins`** Job-এ যাও
2. **Build History**-তে নতুন Build দেখা যাবে 🟢
3. Build নম্বরে ক্লিক করো → **`Console Output`** ক্লিক করো

   নিচের মতো Output দেখা যাবে:

   ```
   ┌──────────────────────────────────────────────────────────┐
   │  Console Output                                           │
   ├──────────────────────────────────────────────────────────┤
   │  Started by GitHub push by your-username                  │
   │  Building in workspace C:\...                             │
   │                                                           │
   │  Cloning repository https://github.com/.../Test-Jenkins   │
   │  > git checkout -f main                                   │
   │                                                           │
   │  C:\...> echo Build triggered from GitHub!                │
   │  Build triggered from GitHub!                             │
   │                                                           │
   │  Finished: SUCCESS ✅                                     │
   └──────────────────────────────────────────────────────────┘
   ```

   ![Jenkins Console Output - Build triggered from GitHub](https://imgur.com/X8FdOFv.png)

> 🎉 **"Build triggered from GitHub!"** দেখা গেলে Integration সফল!

---

## 🗺️ সম্পূর্ণ Integration Flow

```
GitHub README.md Edit
         │
         ▼  (Push Event)
    GitHub Webhook
         │
         ▼  (HTTP POST)
  ngrok Public URL
  (https://xxxx.ngrok-free.app/github-webhook/)
         │
         ▼  (Tunnel)
  localhost:8080
  Jenkins Server
         │
         ▼
  "Github Jenkins" Job
  Triggered Automatically
         │
         ▼
  git checkout main
  (code pull from GitHub)
         │
         ▼
  echo Build triggered from GitHub!
         │
         ▼
  Finished: SUCCESS ✅
```

---

## ⚠️ সাধারণ সমস্যা ও সমাধান

| সমস্যা | সম্ভাব্য কারণ | সমাধান |
|--------|--------------|--------|
| Webhook-এ লাল ✗ দেখাচ্ছে | URL ভুল বা ngrok বন্ধ | ngrok চালু আছে কিনা চেক করো |
| Build Trigger হচ্ছে না | `/github-webhook/` নেই | Payload URL-এর শেষে `/github-webhook/` যোগ করো |
| Branch error | Branch `master` না `main` | Branch Specifier `*/main` নাকি `*/master` চেক করো |
| ngrok session expired | Free plan-এ সময়সীমা আছে | CMD বন্ধ করে আবার `ngrok http 8080` চালাও ও Webhook URL আপডেট করো |
| Authtoken error | Token সঠিক নয় | ngrok Dashboard থেকে Token আবার কপি করো |

---

## 📝 মূল বিষয় (Key Takeaways)

- ✅ **Git Plugin** — Jenkins-কে GitHub-এর সাথে সংযুক্ত করে
- ✅ **ngrok** — Local Jenkins-কে ইন্টারনেটে Public করে
- ✅ **Webhook** — GitHub Push হলে Jenkins-কে স্বয়ংক্রিয়ভাবে জানায়
- ✅ **`/github-webhook/`** — Jenkins-এর নির্দিষ্ট Webhook endpoint
- ✅ **Console Output** — Build-এর প্রতিটি ধাপ বিস্তারিত দেখায়

---

> 💡 **পরবর্তী শিক্ষণীয় বিষয়:** Jenkins Pipeline, Docker Integration, এবং Automated Testing।

*📌 এই Tutorial-এ ngrok ব্যবহার করা হয়েছে Development/Testing-এর জন্য। Production-এ Jenkins একটি Public Server-এ থাকবে।*
