
# نصب Extroot روی روتر Mango – به‌نام آرش اردیبهشت

این آموزش برای نصب **Extroot** در OpenWRT روی **روتر GL‑MT300N‑V2 (Mango)** با **فلش USB فرمت ext4** طراحی شده.  
در این روش، حافظه داخلی دستگاه حفظ می‌شود و فلش جدید به‌عنوان overlay استفاده می‌شود.

---

## 🔧 مراحل اصلی:

1. نصب پکیج‌های مورد نیاز:
   ```bash
   opkg update
   opkg install kmod-usb-storage block-mount e2fsprogs
   ```
2. وصل و فرمت فلش USB (فرمت ext4)

3. گرفتن UUID فلش با دستور:
   ```bash
   blkid
   ```
   یا:
   ```bash
   block info
   ```

4. ویرایش فایل `/etc/config/fstab`:

   فایل رو با ویرایشگری مثل `vi` یا `nano` باز کن:

   ```bash
   vi /etc/config/fstab
   ```

   یا

   ```bash
   nano /etc/config/fstab
   ```

   سپس اطمینان حاصل کن که تنظیمات مربوط به حافظه داخلی دستگاه **دست‌نخورده باقی مونده** و فقط یک `config mount` جدید برای فلش USB اضافه شده باشه، مشابه نمونه زیر:

   ```bash
   config mount
     option target '/rom'
     option device '/dev/mtdblockX'     # حافظه داخلی دستگاه (ممکنه mtdblock3 یا مشابه باشه)
     option fstype 'jffs2'
     option options 'ro'
     option enabled '1'

   config mount
     option target '/overlay'
     option uuid 'اینجا_UUID_فلش_تو_رو_قرار_بده'
     option fstype 'ext4'
     option options 'rw,sync'
     option enabled '1'
     option is_rootfs '1'
   ```

5. اعمال تنظیمات و ریبوت روتر:

   ```bash
   /etc/init.d/fstab boot
   reboot
   ```

---

## ⚠️ نکته حیاتی:

- **تنظیم مربوط به حافظه داخلی رو حذف نکنید!**  
- فقط یک `config mount` جدید اضافه کن که فلش USB مورد استفاده قرار بگیره.  
- اینطوری اگر فلش نبود، روتر از حافظه داخلی fallback می‌کنه.

---

## 🧠 مزیت‌ها:

- حافظه‌ی روتر افزایش می‌یابد  
- نصب Passwall2 یا سایر پکیج‌ها بدون مشکل و با سرعت بیشتر  
- کمتر درگیر مشکلات در mount و گذراندن از حافظه داخلی می‌شویم

---

## 🧩 دلیل خطای رایج:

بسیاری فقط یک ورودی `fstab` دارند و آن را ادیت می‌کنند.  
در نتیجه:
- فلش mount نمی‌شود
- overlay خراب می‌شود
- سیستم instable بالا می‌آید

مهم‌ترین نکته: تنظیم پارتیشن داخلی رو **حفظ کنید** و تنها یک entry جدید برای فلش اضافه کنید.

---

## 📌 مثال واقعی:

فرض کنید خروجی `blkid` این باشه:

```
/dev/sda1: UUID="a1b2c3d4‑e5f6‑7890‑abcd‑ef1234567890" TYPE="ext4"
```

آنگاه entry فلش این‌طور خواهد بود:

```bash
config mount
  option target '/overlay'
  option uuid 'a1b2c3d4‑e5f6‑7890‑abcd‑ef1234567890'
  option fstype 'ext4'
  option options 'rw,sync'
  option enabled '1'
  option is_rootfs '1'
```

---

## 🧑‍💻 اسکریپت پیشنهادی:

می‌تونم برات اسکریپتی بنویسم که:
- UUID رو به‌صورت خودکار پیدا کنه
- `fstab` رو بدون حذف دستی خط حافظه داخلی تنظیم کنه
- Extroot رو خودکار فعال کنه

اگه خواستی بگو انجام بدم ✌️
