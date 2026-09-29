# تحلیل‌گر بازار — بیلد APK از طریق گیت‌هاب

این پروژه فایل HTML اپت رو با Capacitor داخل یه اپ اندرویدی می‌پیچه (WebView بومی) و ساخت APK رو کامل روی سرورهای گیت‌هاب (GitHub Actions) انجام میده — نیازی به نصب Android SDK روی گوشی/Termux نیست.

## مراحل

1. یه ریپازیتوری جدید توی گیت‌هاب بساز (مثلاً `crypto-analyzer-app`).
2. کل محتوای این پوشه رو داخلش push کن:
   ```
   git init
   git add .
   git commit -m "init"
   git branch -M main
   git remote add origin https://github.com/USERNAME/crypto-analyzer-app.git
   git push -u origin main
   ```
3. برو به تب **Actions** توی ریپازیتوری — یه بیلد به اسم «Build Android APK» خودکار اجرا می‌شه (چند دقیقه طول می‌کشه).
4. وقتی تموم شد، پایین همون صفحه‌ی اجرا، بخش **Artifacts** یه فایل به اسم `app-debug-apk` می‌ذاره — دانلودش کن، APK داخلشه. همین رو می‌تونی مستقیم رو گوشی نصب کنی و تست کنی.

## برای انتشار واقعی در گوگل‌پلی (نسخه‌ی امضاشده)

گوگل‌پلی فایل AAB امضاشده می‌خواد، نه APK دیباگ. برای این کار:

1. یه keystore بساز (یک‌بار، و همیشه نگهش دار — گمش کنی دیگه نمی‌تونی اپ رو آپدیت کنی):
   ```
   keytool -genkey -v -keystore release.keystore -alias mykey -keyalg RSA -keysize 2048 -validity 10000
   ```
2. فایلش رو base64 کن: `base64 -w0 release.keystore`
3. توی ریپازیتوری گیت‌هاب، `Settings → Secrets and variables → Actions` این ۴ سکرت رو اضافه کن:
   - `KEYSTORE_BASE64` (خروجی مرحله ۲)
   - `KEYSTORE_PASSWORD`
   - `KEY_ALIAS` (همون `mykey`)
   - `KEY_PASSWORD`
4. یه push جدید بزن — این‌بار Actions یه Artifact دیگه هم به اسم `app-release-aab` می‌سازه که آماده‌ی آپلود به Google Play Console است.
5. توی Play Console یه حساب دولوپر بساز (هزینه یک‌بار ۲۵ دلار)، یه اپ جدید بساز، و فایل `.aab` رو آپلود کن.

## سیستم ورود و اشتراک

این نسخه شامل صفحه‌ی ورود/ثبت‌نام (ایمیل + گوگل)، ۴۸ ساعت استفاده‌ی رایگان،
و صفحه‌ی خرید اشتراک (۳۰۰ هزار تومان یک‌ماهه / ۷۰۰ هزار تومان سه‌ماهه از
طریق زیبال) است. برای فعال‌سازی کامل، فایل‌های پوشه‌ی `backend/` (کنار همین
پوشه در همین zip) باید روی هاست آپلود بشن — راهنمای کاملش تو
`backend/README.md` هست.

⚠️ **پوشه‌ی `backend/` رو به گیت‌هاب push نکن.** چون بعد از پر کردن
`config.php` با رمز دیتابیس و کلیدهای واقعی، اگه این پوشه وارد یه ریپازیتوری
عمومی بشه، اطلاعات حساس لو می‌ره. این پوشه فقط باید مستقیم (با File Manager
یا FTP) روی هاست آپلود بشه؛ برای گیت‌هاب فقط `www/`، `capacitor.config.json`،
`package.json`، `.github/` و `.gitignore` لازمه.

آدرس بک‌اند داخل کد اپ (`www/index.html`، متغیر `SN_API`) روی
`https://sahmyekom.ir/backend/api` تنظیم شده. اگه دامنه فرق کرد، همین یه خط
رو اصلاح کن.

ورود با گوگل نیاز به تنظیم `GOOGLE_CLIENT_ID` (هم در بک‌اند، هم در
`capacitor.config.json`) داره — مراحلش در `backend/README.md` بخش ۵ توضیح
داده شده. تا قبل از تنظیم این بخش، دکمه‌ی گوگل کار نمی‌کنه ولی ورود با
ایمیل/رمز عادی کار می‌کنه.

## تغییر آیکون و اسم اپ

- اسم اپ: مقدار `appName` توی `capacitor.config.json`
- آیکون: بعد از اولین بیلد محلی (یا لوکال با Android Studio)، آیکون‌ها توی `android/app/src/main/res/mipmap-*` هستن — یا از ابزار [icon.kitchen](https://icon.kitchen) یه ست کامل بساز و جایگزین کن.
