---
description: إعادة تحميل موقع «مرسل رسائل التبرع بالدم» الأصلي ومقارنته بقسم مرسل الواتساب المحلي
---
أعد تحليل الموقع الأصلي https://whatsappbd-b62ewyem.manus.space/ وقارنه بقسم `#/wa` في `index.html`.

1. حمّل `index.html` بـ curl إلى `_reference/whatsappbd/new/` واستخرج مسار ملف JS/CSS منه (أسماء الـ bundle تتغير مع كل نشر)، وحمّلهما.
2. قارن اسم الـ bundle بالموجود في `_reference/whatsappbd/assets/`. إن لم يتغير: قل ذلك وتوقف.
3. إن تغيّر: شغّل `npx prettier@3 --parser babel` على الـ JS، وابحث عن كود التطبيق (ابحث عن `Home.tsx` و`localStorage` و`wa.me`)، وقارنه بـ `_reference/whatsappbd/bundle.pretty.js` (الأسطر ~111611–113761).
4. اعرض جدول فروقات: الشاشات، النصوص العربية، الألوان، الرسائل الافتراضية، مفاتيح التخزين، منطق Excel/الأرقام/الروابط — وما يلزم نقله للقسم المحلي.
5. حدّث `_reference/whatsappbd/ANALYSIS.md` بقسم «تغييرات <التاريخ>». لا تعدّل `index.html` بدون موافقة. `_reference/` لا يُرفع للمستودع.
