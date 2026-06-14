# AWS S3 CLI Handbook

## Secure Backend + S3 + CLI Reference

> **نسخة مذاكرة ومرجع عملي**
>
> هذا الهاند بوك معمول كمرجع عملي لفهم وإدارة ملفات AWS S3 باستخدام AWS CLI، مع ربطه بفكرة الـ Backend الآمن، والـ `.env`، والـ SDK مثل `boto3`.

---

## بيانات مشروع التدريب

```text
Bucket: session-authoring-tool
Main Prefix: source_materials/
```

---

# Table of Contents

1. [الفكرة الأساسية قبل الأوامر](#1-الفكرة-الأساسية-قبل-الأوامر)
2. [الفرق بين aws s3 و aws s3api](#2-الفرق-بين-aws-s3-و-aws-s3api)
3. [تجهيز AWS CLI](#3-تجهيز-aws-cli)
4. [أوامر العرض والاستكشاف](#4-أوامر-العرض-والاستكشاف)
5. [أوامر الرفع Upload](#5-أوامر-الرفع-upload)
6. [أوامر التحميل Download](#6-أوامر-التحميل-download)
7. [أمر Sync](#7-أمر-sync)
8. [أوامر الحذف Delete](#8-أوامر-الحذف-delete)
9. [أوامر النقل وإعادة التسمية](#9-أوامر-النقل-وإعادة-التسمية)
10. [أوامر s3api المهمة](#10-أوامر-s3api-المهمة)
11. [أوامر Presigned URL](#11-أوامر-presigned-url)
12. [أوامر إنشاء وحذف Buckets](#12-أوامر-إنشاء-وحذف-buckets)
13. [أشهر المشاكل وحلها](#13-أشهر-المشاكل-وحلها)
14. [IAM Policies مهمة](#14-iam-policies-مهمة)
15. [ملف env النهائي للباك اند](#15-ملف-env-النهائي-للباك-اند)
16. [الربط بين CLI والباك اند](#16-الربط-بين-cli-والباك-اند)
17. [Naming Convention مهم لمشروعك](#17-naming-convention-مهم-لمشروعك)
18. [Command Cheat Sheet نهائي](#18-command-cheat-sheet-نهائي)
19. [تدريبات عملية](#19-تدريبات-عملية)
20. [قواعد ذهبية من الثلاث خبراء](#20-قواعد-ذهبية-من-الثلاث-خبراء)
21. [الخلاصة النهائية](#21-الخلاصة-النهائية)
22. [المراجع الرسمية](#22-المراجع-الرسمية)

---

# 1. الفكرة الأساسية قبل الأوامر

## S3 مش File System عادي

في جهازك عندك شكل مألوف زي:

```text
folder/file.zip
```

لكن في AWS S3، أنت لا تتعامل مع فولدرات حقيقية بنفس معنى نظام الملفات في ويندوز أو لينكس.

في S3 عندك 3 مفاهيم أساسية:

```text
Bucket
Object Key
Prefix
```

مثال:

```text
s3://session-authoring-tool/source_materials/lesson1.zip
```

يتقسم كده:

```text
Bucket = session-authoring-tool
Key = source_materials/lesson1.zip
Prefix = source_materials/
Object Name = lesson1.zip
```

## تبسيط سينيور

أي حاجة بعد اسم الـ Bucket تعتبر اسم الملف الكامل جوه S3.

يعني ده:

```text
s3://session-authoring-tool/source_materials/math/lesson1.zip
```

مش معناه بالضرورة إن فيه فولدر حقيقي اسمه `source_materials` وفولدر حقيقي اسمه `math`.

بالنسبة لـ S3 هو Object Key طويل اسمه:

```text
source_materials/math/lesson1.zip
```

لكن AWS Console والـ CLI بيعرضوه كأنه فولدرات عشان يسهل عليك التصفح.

---

# 2. الفرق بين `aws s3` و `aws s3api`

## `aws s3`

دي أوامر High-level، سهلة ومناسبة للشغل اليومي.

أمثلة:

```bash
aws s3 ls
aws s3 cp
aws s3 sync
aws s3 rm
aws s3 mv
```

استخدمها لما تكون عايز:

- ترفع ملف.
- تحمل ملف.
- تعرض محتويات.
- تعمل مزامنة.
- تمسح ملفات.
- تنقل أو تغير اسم ملف.

## `aws s3api`

دي أوامر Low-level أقرب للـ API الحقيقي بتاع S3.

أمثلة:

```bash
aws s3api head-object
aws s3api put-object
aws s3api list-objects-v2
```

استخدمها لما تكون عايز:

- تتأكد إن ملف موجود بدون تحميله.
- تعمل Object فاضي يظهر كأنه فولدر.
- تشوف Metadata.
- تعمل Debugging أعمق.
- تطابق اللي بيحصل في `boto3` أو أي SDK.

## تبسيط سينيور

- `aws s3` = أوامر يومية سهلة.
- `aws s3api` = أوامر أعمق وأقرب للبرمجة والـ SDK.

---

# 3. تجهيز AWS CLI

## Command 1: معرفة إصدار AWS CLI

```bash
aws --version
```

### بيعمل إيه؟

بيأكدلك إن AWS CLI متثبت على جهازك.

### مثال Output

```text
aws-cli/2.15.30 Python/3.11.6 Linux/...
```

### إمتى أستخدمه؟

أول ما تبدأ على جهاز جديد أو لو مش متأكد إن AWS CLI متثبت.

### لو فشل؟

لو ظهر:

```text
aws: command not found
```

يبقى AWS CLI مش متثبت أو مش مضاف في PATH.

---

## Command 2: ربط الجهاز بحساب AWS

```bash
aws configure
```

### بيعمل إيه؟

بيخزن Credentials على جهازك عشان AWS CLI يعرف يكلم AWS.

هيطلب منك:

```text
AWS Access Key ID
AWS Secret Access Key
Default region name
Default output format
```

مثال:

```text
AWS Access Key ID: AKIAxxxxxxxx
AWS Secret Access Key: xxxxxxxxx
Default region name: eu-west-1
Default output format: json
```

### بيخزن فين؟

غالبًا في:

```text
~/.aws/credentials
~/.aws/config
```

### مهم أمنيًا

متحطش المفاتيح دي في GitHub.

دي أسرار زي `.env`.

---

## Command 3: معرفة أنت داخل بأنهي User

```bash
aws sts get-caller-identity
```

### بيعمل إيه؟

بيقولك AWS CLI شغال باسم مين.

### مثال Output

```json
{
  "UserId": "AIDAEXAMPLE",
  "Account": "123456789012",
  "Arn": "arn:aws:iam::123456789012:user/session-cli-user"
}
```

### إمتى أستخدمه؟

قبل أي عملية مهمة، خصوصًا قبل:

- Delete.
- Upload على Production.
- Sync مع `--delete`.

### تبسيط سينيور

الأمر ده زي إنك تسأل:

> أنا ماسك مفاتيح مين دلوقتي؟

---

## Command 4: عرض إعدادات AWS الحالية

```bash
aws configure list
```

### بيعمل إيه؟

بيعرض:

- هل Access Key موجود.
- هل Secret Key موجود.
- Region الحالي.
- Profile الحالي.
- مصدر الإعدادات.

### مثال Output

```text
Name                    Value             Type    Location
----                    -----             ----    --------
profile                 default           manual  --profile
access_key              ****ABCD          shared-credentials-file
secret_key              ****EFGH          shared-credentials-file
region                  eu-west-1         config-file
```

### إمتى أستخدمه؟

لو ظهرلك Error زي:

```text
Unable to locate credentials
```

أو لو حاسس إن Region أو Profile غلط.

---

## Command 5: استخدام Profile مختلف

```bash
aws configure --profile session-dev
```

وبعدين تستخدمه كده:

```bash
aws s3 ls --profile session-dev
```

### بيعمل إيه؟

بيخليك تتعامل مع أكتر من حساب أو بيئة.

مثال:

```text
default
dev
staging
production
```

### مثال عملي

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --profile session-dev
```

### تبسيط سينيور

Profile يعني شخصية مختلفة للـ CLI:

- شخصية Dev.
- شخصية Staging.
- شخصية Production.

---

# 4. أوامر العرض والاستكشاف

## Command 6: عرض كل الـ Buckets

```bash
aws s3 ls
```

### بيعمل إيه؟

بيعرض كل الـ Buckets اللي الـ User الحالي يقدر يشوفها.

### مثال Output

```text
2026-06-14 08:30:12 session-authoring-tool
2026-06-10 11:20:00 another-bucket
```

### إمتى أستخدمه؟

- تتأكد إن credentials صح.
- تعرف اسم الـ Bucket بالضبط.
- تتأكد إن الـ User عنده صلاحية List.

### أشهر Error

```text
AccessDenied
```

معناه الـ User ملوش صلاحية يشوف الـ Buckets.

---

## Command 7: عرض محتويات Bucket

```bash
aws s3 ls s3://session-authoring-tool/
```

### بيعمل إيه؟

بيعرض أول مستوى ظاهر داخل الـ Bucket.

### مثال Output

```text
                           PRE source_materials/
                           PRE logs/
```

### معنى `PRE`

`PRE` اختصار Prefix.

يعني AWS CLI شايف إن فيه مسارات تبدأ بـ:

```text
source_materials/
```

---

## Command 8: عرض Prefix معين

```bash
aws s3 ls s3://session-authoring-tool/source_materials/
```

### بيعمل إيه؟

بيعرض الملفات أو الـ Prefixes الموجودة تحت:

```text
source_materials/
```

### مثال Output

```text
2026-06-14 09:10:00   3045992 150169015780_mt.zip
2026-06-14 09:15:00   1842000 150169015781_mt.zip
                           PRE math_sessions/
```

### إمتى أستخدمه؟

قبل ما تقول للباك اند يدور على ملف.

لو الباك اند بيدور على:

```text
source_materials/150169015780_mt.zip
```

لازم تتأكد إنه موجود هنا.

---

## Command 9: عرض كل الملفات Recursively

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive
```

### بيعمل إيه؟

بيجيب كل الملفات جوه `source_materials/` حتى لو جواها Prefixes فرعية.

### مثال Output

```text
2026-06-14 09:10:00 3045992 source_materials/150169015780_mt.zip
2026-06-14 09:11:00 2045000 source_materials/math_sessions/lesson1.zip
2026-06-14 09:12:00 1099000 source_materials/physics_sessions/lesson2.zip
```

### إمتى أستخدمه؟

لما تكون مش متأكد الملف فين.

---

## Command 10: عرض الأحجام بشكل مفهوم

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive --human-readable
```

### بيعمل إيه؟

بدل ما يعرض الحجم بالـ bytes، يعرضه بشكل أوضح مثل KB/MB/GB.

### مثال

```text
2026-06-14 09:10:00 2.9 MiB source_materials/150169015780_mt.zip
```

---

## Command 11: عرض ملخص عدد وحجم الملفات

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive --summarize
```

### بيعمل إيه؟

في الآخر بيعرض ملخص:

```text
Total Objects: 120
Total Size: 345678900
```

### استخدام مهم

تعرف حجم الداتا قبل Download أو Delete.

---

# 5. أوامر الرفع Upload

## Command 12: رفع ملف واحد

```bash
aws s3 cp 150169015780_mt.zip s3://session-authoring-tool/source_materials/
```

### بيعمل إيه؟

بيرفع الملف المحلي:

```text
150169015780_mt.zip
```

إلى:

```text
source_materials/150169015780_mt.zip
```

### النتيجة داخل S3

```text
Bucket: session-authoring-tool
Key: source_materials/150169015780_mt.zip
```

### إمتى أستخدمه؟

لما يكون عندك ملف Session zip واحد.

---

## Command 13: رفع ملف مع تغيير اسمه

```bash
aws s3 cp session.zip s3://session-authoring-tool/source_materials/150169015780_mt.zip
```

### بيعمل إيه؟

يرفع `session.zip` لكن يخزنه في S3 باسم:

```text
150169015780_mt.zip
```

### مهم جدًا

ده مفيد لو الباك اند متوقع Naming Convention معين.

مثلاً:

```text
{id}_mt.zip
```

---

## Command 14: رفع ملف داخل Prefix فرعي

```bash
aws s3 cp lesson1.zip s3://session-authoring-tool/source_materials/math_sessions/lesson1.zip
```

### بيعمل إيه؟

بيرفع الملف داخل مسار فرعي.

### النتيجة

```text
source_materials/math_sessions/lesson1.zip
```

### هل لازم أعمل `math_sessions/` الأول؟

لا.

S3 هيعرضه كفولدر تلقائيًا لأن الـ Object Key فيه `/`.

---

## Command 15: رفع فولدر كامل

```bash
aws s3 cp ./all_sessions/ s3://session-authoring-tool/source_materials/ --recursive
```

### بيعمل إيه؟

بيرفع كل الملفات الموجودة داخل:

```text
./all_sessions/
```

إلى:

```text
source_materials/
```

### ليه `--recursive` مهمة؟

لأن `cp` بدون `--recursive` بيتعامل مع ملف واحد.

لو المصدر عبارة عن فولدر، لازم تقول له:

> ادخل جوه الفولدر وارفع كل اللي فيه.

---

## Command 16: رفع ملفات Zip فقط

```bash
aws s3 cp . s3://session-authoring-tool/source_materials/ \
  --recursive \
  --exclude "*" \
  --include "*.zip"
```

### بيعمل إيه؟

يرفع فقط الملفات اللي امتدادها:

```text
.zip
```

### شرح السطور

```bash
aws s3 cp .
```

المصدر هو الفولدر الحالي.

```bash
s3://session-authoring-tool/source_materials/
```

الوجهة هي Prefix في S3.

```bash
--recursive
```

ادخل جوه الفولدرات.

```bash
--exclude "*"
```

استبعد كل حاجة.

```bash
--include "*.zip"
```

ارجع اسمح بملفات zip فقط.

### تبسيط سينيور

الترتيب هنا مهم:

1. امنع الكل.
2. اسمح بالـ zip فقط.

---

## Command 17: تجربة الرفع بدون تنفيذ

```bash
aws s3 cp . s3://session-authoring-tool/source_materials/ \
  --recursive \
  --exclude "*" \
  --include "*.zip" \
  --dryrun
```

### بيعمل إيه؟

يعرضلك الملفات اللي كان هيرفعها، لكنه لا يرفع فعليًا.

### إمتى أستخدمه؟

قبل رفع فولدر كبير، خصوصًا لو فيه ملفات كتير ومش عايز ترفع حاجة غلط.

---

# 6. أوامر التحميل Download

## Command 18: تحميل ملف واحد للمكان الحالي

```bash
aws s3 cp s3://session-authoring-tool/source_materials/150169015780_mt.zip .
```

### بيعمل إيه؟

يحمل الملف إلى الفولدر الحالي.

النقطة `.` معناها:

```text
Current Directory
```

---

## Command 19: تحميل ملف داخل فولدر محلي

```bash
aws s3 cp s3://session-authoring-tool/source_materials/150169015780_mt.zip ./downloads/
```

### بيعمل إيه؟

يحمل الملف داخل فولدر:

```text
downloads
```

### ملاحظة

لو فولدر `downloads` مش موجود، اعمله الأول:

```bash
mkdir -p downloads
```

---

## Command 20: تحميل Prefix كامل

```bash
aws s3 cp s3://session-authoring-tool/source_materials/ ./downloads/ --recursive
```

### بيعمل إيه؟

يحمل كل الملفات تحت:

```text
source_materials/
```

إلى:

```text
./downloads/
```

---

# 7. أمر Sync

## Command 21: مزامنة فولدر محلي مع S3

```bash
aws s3 sync ./all_sessions/ s3://session-authoring-tool/source_materials/
```

### بيعمل إيه؟

بيقارن بين الملفات المحلية والملفات على S3.

ويرفع فقط:

- الملفات الجديدة.
- الملفات اللي اتغيرت.

---

## الفرق بين `cp --recursive` و `sync`

### `cp --recursive`

ينسخ الملفات المطابقة مباشرة.

### `sync`

يقارن الأول، وينقل المختلف فقط.

---

## Command 22: Sync مع حذف الزيادة

```bash
aws s3 sync ./all_sessions/ s3://session-authoring-tool/source_materials/ --delete
```

### بيعمل إيه؟

يخلي S3 نسخة مطابقة من الفولدر المحلي.

لو فيه ملف على S3 مش موجود محليًا، هيتحذف.

### خطر جدًا

قبلها استخدم:

```bash
aws s3 sync ./all_sessions/ s3://session-authoring-tool/source_materials/ --delete --dryrun
```

---

## Command 23: Sync ملفات Zip فقط

```bash
aws s3 sync ./all_sessions/ s3://session-authoring-tool/source_materials/ \
  --exclude "*" \
  --include "*.zip"
```

### بيعمل إيه؟

يزامن ملفات zip فقط.

---

# 8. أوامر الحذف Delete

## Command 24: حذف ملف واحد

```bash
aws s3 rm s3://session-authoring-tool/source_materials/wrong_file.zip
```

### بيعمل إيه؟

يحذف Object واحد.

### تحذير

مفيش Recycle Bin.

لو Versioning مش مفعّل، الحذف ممكن يكون نهائي.

---

## Command 25: Preview حذف ملف

```bash
aws s3 rm s3://session-authoring-tool/source_materials/wrong_file.zip --dryrun
```

### بيعمل إيه؟

يقولك كان هيحذف إيه بدون تنفيذ.

---

## Command 26: حذف Prefix كامل

```bash
aws s3 rm s3://session-authoring-tool/source_materials/math_sessions/ --recursive
```

### بيعمل إيه؟

يحذف كل الملفات اللي Key بتاعها يبدأ بـ:

```text
source_materials/math_sessions/
```

### قبل التنفيذ الحقيقي

استخدم:

```bash
aws s3 rm s3://session-authoring-tool/source_materials/math_sessions/ --recursive --dryrun
```

### تبسيط سينيور

أي Delete فيه `--recursive` لازم قبله `--dryrun`.

دي قاعدة أمان.

---

# 9. أوامر النقل وإعادة التسمية

## Command 27: نقل أو Rename ملف

```bash
aws s3 mv s3://session-authoring-tool/source_materials/old.zip s3://session-authoring-tool/source_materials/new.zip
```

### بيعمل إيه؟

ينقل Object من Key إلى Key تاني.

عمليًا ده Copy ثم Delete.

### استخدامه

لو رفعت ملف باسم غلط وتريد تصحيحه.

---

## Command 28: نقل ملف من Prefix إلى Prefix آخر

```bash
aws s3 mv s3://session-authoring-tool/source_materials/temp/lesson1.zip s3://session-authoring-tool/source_materials/final/lesson1.zip
```

### النتيجة

من:

```text
source_materials/temp/lesson1.zip
```

إلى:

```text
source_materials/final/lesson1.zip
```

---

# 10. أوامر `s3api` المهمة

## Command 29: التأكد من وجود ملف بدون تحميله

```bash
aws s3api head-object \
  --bucket session-authoring-tool \
  --key source_materials/150169015780_mt.zip
```

### بيعمل إيه؟

يسأل S3:

> هل الملف ده موجود؟ وهاتلي Metadata بتاعته.

### مثال Output

```json
{
  "AcceptRanges": "bytes",
  "LastModified": "2026-06-14T09:10:00+00:00",
  "ContentLength": 3045992,
  "ContentType": "application/zip",
  "ETag": "\"abc123\""
}
```

### استخدامه في الباك اند

ده نفس معنى `HeadObject` اللي ممكن تستخدمه في `boto3` قبل التحميل.

### لو الملف مش موجود

ممكن يظهر:

```text
An error occurred (404) when calling the HeadObject operation: Not Found
```

أو:

```text
NoSuchKey
```

---

## Command 30: إنشاء Prefix فاضي وهمي

```bash
aws s3api put-object \
  --bucket session-authoring-tool \
  --key source_materials/math_sessions/
```

### بيعمل إيه؟

ينشئ Object فاضي اسمه:

```text
source_materials/math_sessions/
```

### ليه ده بيظهر كفولدر؟

لأن الـ Key بينتهي بـ `/`.

---

## Command 31: عرض Objects كـ JSON

```bash
aws s3api list-objects-v2 \
  --bucket session-authoring-tool \
  --prefix source_materials/
```

### بيعمل إيه؟

يرجع Objects تحت Prefix معين بصيغة JSON.

### مثال Output مختصر

```json
{
  "Contents": [
    {
      "Key": "source_materials/150169015780_mt.zip",
      "LastModified": "2026-06-14T09:10:00+00:00",
      "Size": 3045992
    }
  ]
}
```

### إمتى أستخدمه؟

لما تحتاج Output قابل للمعالجة بالـ Scripts.

---

## Command 32: عرض أسماء الملفات فقط

```bash
aws s3api list-objects-v2 \
  --bucket session-authoring-tool \
  --prefix source_materials/ \
  --query "Contents[].Key" \
  --output text
```

### بيعمل إيه؟

يعرض الـ Keys فقط بدل JSON كامل.

### مثال Output

```text
source_materials/150169015780_mt.zip
source_materials/math_sessions/lesson1.zip
```

---

# 11. أوامر Presigned URL

> استخدمها بحذر، لأنها بتدي رابط مؤقت للوصول للملف.

## Command 33: إنشاء رابط مؤقت لملف

```bash
aws s3 presign s3://session-authoring-tool/source_materials/150169015780_mt.zip --expires-in 300
```

### بيعمل إيه؟

ينشئ URL صالح لمدة 300 ثانية.

### إمتى أستخدمه؟

لو عايز تسمح بتحميل ملف Private لفترة مؤقتة.

### لكن في مشروعك الحالي

لو Architecture بتقول إن الـ Backend هو اللي يحمل ويرجع الملف، يبقى Presigned URL مش ضروري.

---

# 12. أوامر إنشاء وحذف Buckets

> غالبًا مش هتحتاجها يوميًا، لكن لازم تعرفها.

## Command 34: إنشاء Bucket

```bash
aws s3 mb s3://my-new-bucket-name
```

### بيعمل إيه؟

ينشئ Bucket جديد.

### مهم

اسم الـ Bucket لازم يكون Unique عالميًا.

---

## Command 35: حذف Bucket فاضي

```bash
aws s3 rb s3://my-new-bucket-name
```

### بيعمل إيه؟

يحذف Bucket لو فاضي.

---

## Command 36: حذف Bucket بكل محتوياته

```bash
aws s3 rb s3://my-new-bucket-name --force
```

### خطر جدًا

بيمسح المحتويات ثم يحذف الـ Bucket.

متستخدموش على Production إلا وأنت فاهم 100%.

---

# 13. أشهر المشاكل وحلها

## Problem 1: Unable to locate credentials

### الرسالة

```text
Unable to locate credentials
```

### معناها

AWS CLI مش لاقي Access Key و Secret Key.

### الحل

```bash
aws configure
```

ثم:

```bash
aws sts get-caller-identity
```

---

## Problem 2: AccessDenied

### الرسالة

```text
An error occurred (AccessDenied)
```

### معناها

الـ User موجود لكن ملوش صلاحية.

### راجع الصلاحيات حسب العملية

لو بتعمل List:

```bash
aws s3 ls s3://session-authoring-tool/source_materials/
```

تحتاج:

```text
s3:ListBucket
```

لو بتعمل Upload:

```bash
aws s3 cp file.zip s3://session-authoring-tool/source_materials/
```

تحتاج:

```text
s3:PutObject
```

لو بتعمل Download:

```bash
aws s3 cp s3://session-authoring-tool/source_materials/file.zip .
```

تحتاج:

```text
s3:GetObject
```

لو بتعمل Delete:

```bash
aws s3 rm s3://session-authoring-tool/source_materials/file.zip
```

تحتاج:

```text
s3:DeleteObject
```

---

## Problem 3: NoSuchBucket

### معناها

اسم الـ Bucket غلط أو مش في الحساب الحالي.

### الحل

```bash
aws s3 ls
```

وتأكد من الاسم.

---

## Problem 4: NoSuchKey

### معناها

الملف مش موجود بالـ Key ده.

### الحل

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive
```

أو:

```bash
aws s3api head-object \
  --bucket session-authoring-tool \
  --key source_materials/file.zip
```

---

## Problem 5: SignatureDoesNotMatch

### معناها غالبًا

- Secret Key غلط.
- Region غلط.
- System clock فيه مشكلة.
- Credentials قديمة.

### الحل

```bash
aws configure list
```

```bash
aws sts get-caller-identity
```

ولو لسه المشكلة موجودة:

```bash
aws configure
```

---

## Problem 6: رفعت الملف في مكان غلط

### مثال

أنت رفعت هنا:

```text
source_material/lesson.zip
```

لكن الباك اند بيدور هنا:

```text
source_materials/lesson.zip
```

لاحظ الفرق:

```text
source_material
source_materials
```

### الحل

اعرض كل الملفات:

```bash
aws s3 ls s3://session-authoring-tool/ --recursive
```

ثم انقل الملف:

```bash
aws s3 mv s3://session-authoring-tool/source_material/lesson.zip s3://session-authoring-tool/source_materials/lesson.zip
```

---

# 14. IAM Policies مهمة

## Policy قراءة فقط للباك اند

لو الباك اند محتاج يقرأ من:

```text
source_materials/
```

استخدم Policy زي دي:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListOnlySourceMaterialsPrefix",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::session-authoring-tool",
      "Condition": {
        "StringLike": {
          "s3:prefix": "source_materials/*"
        }
      }
    },
    {
      "Sid": "ReadObjectsFromSourceMaterials",
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::session-authoring-tool/source_materials/*"
    }
  ]
}
```

---

## Policy قراءة ورفع بدون حذف

لو الـ CLI User محتاج يرفع ويقرأ:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListSourceMaterialsPrefix",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::session-authoring-tool",
      "Condition": {
        "StringLike": {
          "s3:prefix": "source_materials/*"
        }
      }
    },
    {
      "Sid": "ReadWriteObjectsInSourceMaterials",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::session-authoring-tool/source_materials/*"
    }
  ]
}
```

### مهم

لاحظ إننا محطيناش:

```text
s3:DeleteObject
```

ليه؟

لأن لو الـ User مش محتاج يمسح، متديلوش صلاحية مسح.

ده مبدأ:

```text
Least Privilege
```

---

## Policy فيها حذف

استخدمها فقط لو محتاج فعلًا:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "ListSourceMaterials",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::session-authoring-tool",
      "Condition": {
        "StringLike": {
          "s3:prefix": "source_materials/*"
        }
      }
    },
    {
      "Sid": "ReadWriteDeleteSourceMaterials",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::session-authoring-tool/source_materials/*"
    }
  ]
}
```

---

# 15. ملف `.env` النهائي للباك اند

```env
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_DEFAULT_REGION=eu-west-1

SESSION_AUTHORING_S3_BUCKET=session-authoring-tool
SESSION_AUTHORING_S3_PREFIX=source_materials/
```

وفي `.gitignore`:

```gitignore
.env
```

## قاعدة أمان

```text
Secrets stay on the server only.
```

يعني:

- مفاتيح AWS لا تظهر في Frontend.
- لا تترفع على GitHub.
- لا تتحط Hardcoded داخل الكود.

---

# 16. الربط بين CLI والباك اند

لو رفعت ملف كده:

```bash
aws s3 cp 150169015780_mt.zip s3://session-authoring-tool/source_materials/
```

يبقى الباك اند لازم يدور على:

```text
Bucket = session-authoring-tool
Key = source_materials/150169015780_mt.zip
```

لو الباك اند بيدور على:

```text
source_material/150169015780_mt.zip
```

مش هيلاقيه.

لو الباك اند بيدور على:

```text
prod/source_materials/150169015780_mt.zip
```

برضه مش هيلاقيه.

## تبسيط سينيور

الـ CLI والباك اند لازم يتفقوا على نفس:

```text
Bucket
Prefix
File Name
Region
```

---

# 17. Naming Convention مهم لمشروعك

بما إن السيستم ممكن يدور على:

```text
{id}_mt.zip
```

أو:

```text
{id}.zip
```

يبقى خليك ثابت.

مثال مفضل:

```text
150169015780_mt.zip
```

والأفضل توثق ده:

```text
Session source materials naming: {id}_mt.zip
```

---

# 18. Command Cheat Sheet نهائي

## Setup

```bash
aws --version
```

```bash
aws configure
```

```bash
aws sts get-caller-identity
```

```bash
aws configure list
```

---

## List

```bash
aws s3 ls
```

```bash
aws s3 ls s3://session-authoring-tool/
```

```bash
aws s3 ls s3://session-authoring-tool/source_materials/
```

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive
```

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive --human-readable --summarize
```

---

## Upload

```bash
aws s3 cp file.zip s3://session-authoring-tool/source_materials/
```

```bash
aws s3 cp file.zip s3://session-authoring-tool/source_materials/new-name.zip
```

```bash
aws s3 cp ./folder/ s3://session-authoring-tool/source_materials/ --recursive
```

```bash
aws s3 cp . s3://session-authoring-tool/source_materials/ --recursive --exclude "*" --include "*.zip"
```

---

## Download

```bash
aws s3 cp s3://session-authoring-tool/source_materials/file.zip .
```

```bash
aws s3 cp s3://session-authoring-tool/source_materials/ ./downloads/ --recursive
```

---

## Sync

```bash
aws s3 sync ./folder/ s3://session-authoring-tool/source_materials/
```

```bash
aws s3 sync ./folder/ s3://session-authoring-tool/source_materials/ --delete --dryrun
```

---

## Delete

```bash
aws s3 rm s3://session-authoring-tool/source_materials/file.zip
```

```bash
aws s3 rm s3://session-authoring-tool/source_materials/folder/ --recursive --dryrun
```

```bash
aws s3 rm s3://session-authoring-tool/source_materials/folder/ --recursive
```

---

## Move / Rename

```bash
aws s3 mv s3://session-authoring-tool/source_materials/old.zip s3://session-authoring-tool/source_materials/new.zip
```

---

## s3api

```bash
aws s3api head-object --bucket session-authoring-tool --key source_materials/file.zip
```

```bash
aws s3api put-object --bucket session-authoring-tool --key source_materials/math_sessions/
```

```bash
aws s3api list-objects-v2 --bucket session-authoring-tool --prefix source_materials/
```

---

# 19. تدريبات عملية

## Task 1

اعرف أنت داخل بأنهي AWS User.

### الحل

```bash
aws sts get-caller-identity
```

---

## Task 2

اعرض كل الـ Buckets.

### الحل

```bash
aws s3 ls
```

---

## Task 3

اعرض محتويات `source_materials/`.

### الحل

```bash
aws s3 ls s3://session-authoring-tool/source_materials/
```

---

## Task 4

اعرض كل الملفات داخل `source_materials/` حتى الفرعية.

### الحل

```bash
aws s3 ls s3://session-authoring-tool/source_materials/ --recursive
```

---

## Task 5

ارفع ملف اسمه:

```text
150169015780_mt.zip
```

### الحل

```bash
aws s3 cp 150169015780_mt.zip s3://session-authoring-tool/source_materials/
```

---

## Task 6

اعمل Prefix فرعي اسمه:

```text
math_sessions/
```

### الحل

```bash
aws s3api put-object \
  --bucket session-authoring-tool \
  --key source_materials/math_sessions/
```

---

## Task 7

ارفع ملف داخل `math_sessions`.

### الحل

```bash
aws s3 cp lesson1.zip s3://session-authoring-tool/source_materials/math_sessions/lesson1.zip
```

---

## Task 8

تأكد إن الملف موجود بدون تحميل.

### الحل

```bash
aws s3api head-object \
  --bucket session-authoring-tool \
  --key source_materials/math_sessions/lesson1.zip
```

---

## Task 9

اعمل Preview لحذف `math_sessions`.

### الحل

```bash
aws s3 rm s3://session-authoring-tool/source_materials/math_sessions/ --recursive --dryrun
```

---

## Task 10

نفذ الحذف الحقيقي بعد التأكد.

### الحل

```bash
aws s3 rm s3://session-authoring-tool/source_materials/math_sessions/ --recursive
```

---

# 20. قواعد ذهبية من الثلاث خبراء

## رأي Senior Backend Engineer

أي ملف الباك اند هيدور عليه لازم تعرف:

```text
Bucket
Key
Region
Credentials
```

لو واحد فيهم غلط، التحميل هيفشل.

---

## رأي Cloud / DevOps Engineer

قبل أي أمر خطر، شغل:

```bash
--dryrun
```

خصوصًا مع:

```bash
rm --recursive
sync --delete
```

---

## رأي Security Engineer

متديش صلاحية زيادة.

لو محتاج قراءة فقط:

```text
s3:GetObject
s3:ListBucket
```

لو محتاج رفع:

```text
s3:PutObject
```

لو مش محتاج حذف، متديش:

```text
s3:DeleteObject
```

---

# 21. الخلاصة النهائية

احفظ المعادلة دي:

```text
S3 URI = s3://bucket/key
```

مثال:

```text
s3://session-authoring-tool/source_materials/150169015780_mt.zip
```

يتحول إلى:

```text
Bucket = session-authoring-tool
Key = source_materials/150169015780_mt.zip
```

والقاعدة الأمنية:

```text
Frontend never sees secrets.
Backend owns credentials.
S3 bucket stays private.
CLI is for admin/dev operations.
```

## تبسيط سينيور نهائي

لو فهمت 4 حاجات دول، هتعرف تتعامل مع S3 في أي مشروع:

1. الـ Bucket هو المخزن الكبير.
2. الـ Prefix هو شكل فولدر لكنه مش فولدر حقيقي.
3. الـ Key هو اسم الملف الكامل جوه S3.
4. المفاتيح السرية مكانها السيرفر فقط، مش الفرونت إند.

---

# 22. المراجع الرسمية

- AWS CLI S3 Command Reference: https://docs.aws.amazon.com/cli/latest/reference/s3/
- AWS CLI S3 User Guide: https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html
- AWS CLI S3API Command Reference: https://docs.aws.amazon.com/cli/latest/reference/s3api/
- AWS CLI `put-object` Reference: https://awscli.amazonaws.com/v2/documentation/api/2.13.0/reference/s3api/put-object.html

---

# نهاية الهاند بوك

> راجع قسم Cheat Sheet قبل الشغل السريع، وراجع الأقسام التفصيلية لما تظهر مشكلة أو Error.
