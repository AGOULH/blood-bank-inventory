# موقع حملات التبرع — مرسل الواتساب

الملف الوحيد: `index.html` (single-file app، RTL، Firebase compat 10.12.2). القواعد العامة للمشروع في `../CLAUDE.md`.

## مرسل الواتساب (`#/wa`)
- تبويب «مرسل الواتساب» في شريط الموقع → `renderWA()`. كل الكود بين تعليق `مرسل الواتساب (#/wa)` في الـ `<style>` وفي آخر الـ `<script>`، والدوال كلها بادئة `wa*`، والأنماط تحت `.wa` بمتغيرات `--wa-*` (هوية الموقع الأصلي: خط Cairo، أخضر #1BA566، أحمر العمل #9b0007).
- المرجع الأصلي (غير مرفوع): `_reference/whatsappbd/` + `ANALYSIS.md` (الشاشات، النصوص، الألوان، الأخطاء).

### الدخول والأمان
- Firebase Auth بالبريد/كلمة المرور (`firebase-auth-compat` يُحمَّل عند فتح القسم فقط) + قفل PIN من 4–8 أرقام: `SHA-256(salt:pin)` في `wa_settings/main.pinHash/pinSalt`، والفتح لكل جلسة عبر `sessionStorage.wa_unlock`.
- لا بيانات متبرعين في localStorage. تسجيل الخروج يمسح نسخة Firestore المحلية (`terminate` + `clearPersistence`).
- القواعد: `firestore.rules` (قائمة بريد مسموح). تُنشر يدوياً من Firebase Console — لا تغيّر قاعدة `inventory_sync`.

### الـ collections (مجزّأة لتوفير حدود Spark)
| المسار | الشكل |
|---|---|
| `wa_contacts/s0..s9` | `{c:{"9665XXXXXXXX":{n,bt,city,ld,opt,at}}}` — الجزء = آخر رقم في الجوال؛ حد 1MB لكل جزء |
| `wa_templates/{id}` | تعديل رسالة أصلية (`content` فقط، الحذف = استعادة) أو رسالة مخصصة `{title,content,at}` |
| `wa_sends/{runId}` | حملة إرسال `{name,date,place,mapUrl,dc,at,st:{phone:{s,t,ct,ft,n,tpl,rp,bk,at,cs}}}` — `s`: opened/sent/failed/invalid |
| `wa_settings/main` | `{pinHash,pinSalt,dailyLimit,gapMin,gapMax,eligDays,place,mapUrl,activeRun,tplId,daily:{dYYYYMMDD:n}}` |

### قواعد السلوك (لا تكسرها)
- «تم الإرسال» لا تُسجَّل إلا بزر التأكيد `waConfirm('sent')`؛ فتح الرابط يسجّل `opened` فقط.
- نافذة واحدة `window.open(url,'wa_sender')`؛ wa.me على الجوال و `web.whatsapp.com/send` على الكمبيوتر؛ `encodeURIComponent`.
- الحد اليومي (افتراضي 25) يُعدّ الإرسال المؤكد فقط؛ فاصل عشوائي gapMin–gapMax بعد كل نتيجة؛ النقر مطلوب لفتح كل محادثة (لا فتح تلقائي).
- سطر «للتوقف عن استقبال الرسائل أرسل: إيقاف» يُضاف عند الإرسال إن لم يكن في النص؛ `opt:true` يستبعد الرقم من كل شيء.
- تطبيع الأرقام `waNorm()` → `9665XXXXXXXX` أو `{ok:false,why}`؛ اكتشاف الأعمدة `waDetect()` بالعنوان + المحتوى، مع شاشة ربط يدوي.
- المتغيرات: `{الاسم} {الفصيلة} {آخر_تبرع} {موقع_الحملة} {رابط_الخريطة} {تاريخ_الحملة} {مدة_الأهلية}` + القديمة `{اسم} {فصيلة_الدم} {name} {blood_type}`.
- كل زر/حقل له `data-testid` ثابت يبدأ بـ `wa-` (تعتمد عليه أوامر Claude in Chrome) — لا تغيّره.

### الأوامر
`/wa-analyze` · `/wa-fix` · `/wa-template` · `/wa-daily-send` (في `.claude/commands/`).

### المعاينة المحلية
انسخ `index.html` إلى `/tmp/donate_preview` وشغّل سيرفر "donate" من launch.json. النسخة المحلية تتصل بقاعدة البيانات الحقيقية — للاختبار استخدم نسخة معاينة بـ mock لـ Firebase ولا تكتب بيانات تجريبية في المشروع الحقيقي.
