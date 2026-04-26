# Acadimiat — April 2026 Update Email

> RTL Arabic · 600px responsive · Light mode · Outlook-safe

## 📁 الملفات

| File | Purpose |
|---|---|
| `index.html` | الإيميل الكامل — جاهز للإرسال |
| `email.txt` | نسخة Plain Text للـ multipart/alternative |
| `app_mockup.png` | موكاب الداشبورد (مأخوذ ومعالج من App Store) |
| `logo1.png` | شعار أكاديميات |
| `appstore_badge.svg` | شعار App Store الرسمي (احتياطي) |
| `ss_*.jpg` | باقي سكرين شوتس App Store (للاستخدام المستقبلي) |

---

## 🎨 القرارات التصميمية المتخذة

### 1. اللاي‌أوت — جداول (Tables) لا Divs
- استخدمت **HTML Tables** للـ layout كاملاً لأن Outlook 2016/2019 يعتمد محرّك Word لرندر الإيميلات وما يدعم Flexbox / Grid / `position`.
- العمود الواحد على الموبايل عبر `class="stack"` + media query `display:block !important`.

### 2. الـ Inline CSS
- كل ستايل ظاهر مكتوب inline (`style="..."`) — لأن Gmail، Yahoo Mail، وOutlook يحذفون أو يتجاهلون `<style>` في حالات معينة.
- الـ `<style>` في الـ `<head>` يحتوي **فقط** على:
  - `@keyframes` للنقطة النابضة (لا يمكن inline).
  - `@media (max-width:600px)` للـ responsive.
  - `@media (prefers-color-scheme: dark)` للـ dark mode.
  - أي client يحذف `<style>` يفقد **هذه الإضافات فقط** ويبقى التصميم سليم.

### 3. النقطة النابضة (Pulsing Dot)
- استخدمت `@keyframes acadPulse` مع `transform: scale()` و `box-shadow` للريبل.
- **في Outlook**: لا تشتغل الأنيميشن (Outlook يتجاهل `@keyframes`)، لكن النقطة تظهر **ثابتة** وبمظهر طبيعي — degradation سلسة.
- **في Gmail Web / Apple Mail / iOS Mail**: تشتغل بشكل كامل.
- ما استخدمت GIF لأن GIF حجمه أكبر وأقل وضوحاً على Retina.

### 4. زر App Store — Bulletproof Button
- بنيته بـ HTML/CSS بالكامل (مش صورة) لـ 3 أسباب:
  1. يبقى حاد على شاشات Retina.
  2. يدعم Dark Mode تلقائياً (نص أبيض على أسود).
  3. ما يحتاج رفع صورة على CDN.
- **شعار آبل**: SVG inline data-URI — يرند جودة عالية بدون hosting.
- **VML Fallback** للـ Outlook عبر `<v:roundrect>` — يضمن الزر يطلع بنفس الشكل بالضبط في Outlook desktop.

### 5. RTL — `dir="rtl"` و `lang="ar"`
- على body element + كل جدول رئيسي.
- في RTL، `align="left"` يصبح بصرياً يمين، والعكس صحيح. اللوقو وضعته بـ `align="left"` ليظهر **يمين** الشاشة (كما في التصميم الأصلي).

### 6. الموكاب — phone frame مرسوم برمجياً
- نزّلت السكرين شوت الأصلي من App Store (`6.5.1.jpg`).
- بنيت إطار فون أبيض مدوّر (rounded corners + soft shadow + screen bezel) باستخدام PowerShell + System.Drawing + GraphicsPath.
- قصصت محتوى الداشبورد من السكرين شوت ووضعته داخل الإطار مع clipping للحواف المدوّرة.
- النتيجة: `app_mockup.png` (460×920) — موكاب احترافي بإطار جوال كامل (مثل المرجع المطلوب) بدون الحاجة لـ Photoshop أو أصول خارجية.

### 7. Light Mode فقط (بناءً على طلبك)
- `<meta name="color-scheme" content="light only">` يخبر Apple Mail / iOS / Gmail بعدم تطبيق dark inversion.
- لـ Outlook.com (يستخدم selectors `[data-ogsc]` / `[data-ogsb]`) ثبّتُّ الألوان الفاتحة صراحةً لمنع أي قلب تلقائي.
- النتيجة: التصميم يظهر بنفس الألوان الفاتحة في كل الـ clients حتى لو المستخدم مفعّل دارك مود في جهازه.

### 8. الخطوط
- Stack: `'Segoe UI', Tahoma, Arial, Verdana, sans-serif`
- Segoe UI ممتاز للعربية على Windows.
- Tahoma fallback ممتاز للعربية على macOS/iOS.
- **ما استخدمت Google Fonts** — لأنها تنحظر في Outlook ومعظم clients، وتسبب FOUT.

---

## 🔧 ما يحتاج تعبئة قبل الإرسال

| Placeholder | الوصف |
|---|---|
| `{{owner_name}}` | اسم صاحب الأكاديمية — استبدله بالـ merge tag الفعلي حسب ESP (مثل `{{first_name}}` في Mailchimp، `%recipient_name%` في SendGrid، إلخ). |
| `{{unsubscribe_url}}` | رابط إلغاء الاشتراك — معظم ESPs يولّدها تلقائياً (`{{unsubscribe}}` في Mailchimp، `<%asm_group_unsubscribe_raw_url%>` في SendGrid). |
| الصور (`logo1.png`, `app_mockup.png`) | ارفعهم على CDN ثابت (Bunny / Cloudflare R2 / S3) واستبدل المسارات النسبية بـ URLs مطلقة (`https://...`) قبل الإرسال — الإيميلات تتطلب absolute URLs. |

---

## ✉️ A/B Testing — Subject Lines المقترحة

كل subject تحت 50 حرف (يظهر كاملاً في Gmail mobile):

| الـ Variant | الفرضية | Open-Rate Driver |
|---|---|---|
| **A** — `🎉 تطبيق أكاديميات الجديد متاح الآن` | الإثارة + إيموجي | فضول + خبر جديد |
| **B** — `أصبح بإمكانك إدارة أكاديميتك من جوالك` | فائدة مباشرة | Pain-point relief |
| **C** — `جديد أبريل: تطبيق أكاديميات + تحسينات Stripe` | قائمة تحديثات | Information seeking |
| **D** — `{{owner_name}}، تطبيقك الجديد على آب ستور` | تخصيص بالاسم | Personalization |
| **E** — `تابع مبيعاتك لحظة بلحظة من جوالك 📊` | Outcome + visual | Benefit-led |

**Preheader** المقترح (يظهر بعد الـ subject في الإنبوكس):
> `تطبيق أكاديميات الجديد متاح الآن على آب ستور — تابع مبيعاتك ومعاملاتك من جوالك.`

**استراتيجية الاختبار**:
1. ابدأي بـ A vs D على 20% من القائمة.
2. الفائز يُرسل للباقي (80%).
3. سجّلي Open Rate + CTR للموازنة.

---

## 📈 نصائح لتحسين معدل الفتح (Open Rate)

1. **From Name واضح**: استخدمي `Acadimiat` أو `فريق أكاديميات` — لا تستخدمي `noreply@` كاسم.
2. **From Email موثوق**: استخدمي دومين أكاديميات (`updates@acadimiat.com`) — لا تستخدمي Gmail/Outlook عام.
3. **SPF + DKIM + DMARC**: تأكدي من الإعداد الصحيح — Gmail الآن يرفض الإيميلات الجماعية بدونها.
4. **توقيت الإرسال**: للجمهور العربي/الخليجي، **الثلاثاء أو الأربعاء صباحاً (9-11 ص بتوقيت الرياض)** يعطي أعلى معدل فتح.
5. **List Hygiene**: نظّفي القائمة من الـ bounces والـ inactive (>180 يوم) قبل الإرسال — يحسّن الـ sender reputation.
6. **Preheader مدروس**: مكتوب أعلى الإيميل في `<div style="display:none">` — يظهر في الإنبوكس بعد الـ subject ويزيد الـ open rate حتى 20%.
7. **Subject تحت 50 حرف**: يظهر كاملاً على الموبايل (الموبايل = 70%+ من الفتحات).
8. **اختبار `seedbox`**: أرسلي أولاً لحسابات اختبار في Gmail / Outlook / Yahoo / Apple Mail قبل الإرسال الجماعي.
9. **List-Unsubscribe Header**: أضيفي header الـ `List-Unsubscribe` و `List-Unsubscribe-Post` لـ one-click unsubscribe (مطلب جديد من Gmail/Yahoo لمرسلين 5K+/يوم).

---

## ⚠️ تحذيرات Spam Triggers

النص الحالي **نظيف نسبياً**، لكن انتبهي:

| ❌ تجنبي | ✅ استخدمي بدلها |
|---|---|
| "مجاني!!!" مع علامات تعجب متعددة | "بدون رسوم" أو "متاح الآن" |
| كلمات بالكابيتال الكامل بالإنجليزي (FREE, BUY NOW) | عادي |
| "اضغط هنا" متكرر | روابط بنصوص وصفية |
| نسبة صور > 60% من النص | احرصي على نسبة 60% نص / 40% صور |
| إخفاء النص بنفس لون الخلفية | لا تفعليها أبداً (Gmail يكتشفها) |
| روابط مختصرة (bit.ly, tinyurl) | روابط كاملة من دومينك أو موثوقة |
| إيموجيات كثيرة في الـ subject (>2) | إيموجي واحد كافي |
| "عرض محدود!" / "فرصة أخيرة!" | "عرض جديد" / "متاح الآن" |

**فحص قبل الإرسال**:
- استخدمي [mail-tester.com](https://www.mail-tester.com) — يعطيكي درجة من 10. اهدفي لـ 9+.
- [glockapps.com](https://glockapps.com) لاختبار التسليم في Inbox vs Spam.

---

## ✅ Compatibility — تم اختباره ذهنياً على

- ✅ **Gmail** (Web, iOS, Android) — كل الـ tags المستخدمة مدعومة.
- ✅ **Apple Mail** (macOS, iOS) — يدعم dark mode + animations.
- ✅ **Outlook 365 / Web** — مدعوم.
- ✅ **Outlook 2016 / 2019** — VML fallback للزر، animation تتعطل بسلاسة (نقطة ثابتة).
- ✅ **Yahoo Mail** — يحذف بعض `<style>` لكن inline يضمن السلامة.
- ✅ **Samsung Mail** — مبني على blink، يدعم كل شيء.
- ✅ **Thunderbird** — مدعوم بالكامل.

**اختبار قبل الإطلاق**: ارفعي على [Litmus](https://litmus.com) أو [Email on Acid](https://www.emailonacid.com) لرؤية renders فعلية — لا أحد يستطيع اختبار 30+ client يدوياً.

---

## 📐 الأبعاد والمواصفات

- العرض: 600px desktop / 100% mobile.
- نقطة التحول: `max-width: 600px`.
- حجم الخط الأساسي: 15px (desktop) / 16px (mobile).
- أحجام الصور:
  - Logo: 150×40px (max-height في الدارك مود)
  - Mockup: 200×300px desktop / 280px max على الموبايل
- ألوان البراند:
  - Primary: `#702EA4` (بنفسجي)
  - Soft bg: `#f4f1f7`
  - Pill bg: `#e9e1ef`
  - Code bg: `#ece4f1`

---

## 🚀 خطوات النشر

1. ارفعي `logo1.png` و `app_mockup.png` على CDN.
2. استبدلي `src="logo1.png"` و `src="app_mockup.png"` بـ URLs مطلقة.
3. استبدلي `{{owner_name}}` و `{{unsubscribe_url}}` بـ merge tags الـ ESP.
4. اختبري على [mail-tester.com](https://www.mail-tester.com) — اهدفي 9+/10.
5. أرسلي seed test على 4-5 حسابات (Gmail, Outlook, Apple Mail, Yahoo).
6. اطلقي A/B subject line على 20% من القائمة.
7. الفائز يطلع للـ 80% المتبقية.
