# ١

# **Handbook 1 — أساسيات Docker و Docker Compose للمشروع**

## **من الصفر لحد تشغيل Backend \+ Frontend وفهم اللي بيحصل**

يا إسلام، ده **الهاند بوك الأول كامل**.  
 هدفه مش إنك تحفظ أوامر Docker، هدفه إنك تفهم:

* Docker بيعمل إيه؟  
* Image يعني إيه؟  
* Container يعني إيه؟  
* Dockerfile دوره إيه؟  
* docker-compose.yml دوره إيه؟  
* ليه عندك Backend و Frontend؟  
* إزاي تقرأ logs؟  
* إزاي تعرف التطبيق شغال على أنهي port؟  
* وإزاي تتعامل مع أوامر زي:  
```bash
   docker compose up  
  docker compose build  
  docker compose ps  
  docker compose down

```
---

# **0\. القصة ببساطة**

إنت عندك مشروع اسمه تقريبًا:

```text
Session-Authoring-Tool

```
والمشروع فيه غالبًا جزئين:

```text
backend  
frontend

```
الـ backend شغال بـ Python/Flask/Gunicorn.

والـ frontend شغال بـ Vue/Vite أو build بيتقدم عن طريق Nginx.

بدل ما تشغل كل جزء يدويًا على جهازك، Docker بيعمل بيئة تشغيل معزولة لكل جزء.

يعني بدل ما تقول:

```bash
cd backend  
python app.py

```
وبعدين:

```bash
cd frontend  
npm run dev

```
Docker بيقولك:

سيبلي أنا أشغلهم في containers، وكل واحد له نظامه واعتماداته والـ ports بتاعته.

---

# **1\. Docker يعني إيه؟**

Docker هو أداة بتخليك تشغل تطبيقك داخل بيئة معزولة اسمها **Container**.

الـ Container شبه جهاز صغير جوه جهازك، لكنه أخف من Virtual Machine.

## **الفرق بين تشغيل عادي وتشغيل Docker**

### **تشغيل عادي على جهازك**

لو شغلت المشروع مباشرة على جهازك، فأنت معتمد على:

* Python الموجود عندك  
* Node الموجود عندك  
* npm الموجود عندك  
* مكتبات النظام عندك  
* PATH عندك  
* إعدادات الشبكة عندك

فلو أنا شغلت نفس المشروع عندي، ممكن يبوظ لأن جهازي مختلف.

---

### **تشغيل داخل Docker**

Docker بيقول:

أنا هجهز بيئة ثابتة ومحددة، فيها نفس Python ونفس Node ونفس packages، وأشغل المشروع جوه البيئة دي.

فبدل ما المشروع يعتمد على جهازك، يعتمد على صورة Docker ثابتة.

---

## **تبسيط سينيور**

تخيل إن المشروع مطعم.

لو شغلته على جهازك مباشرة، فأنت بتطبخ في مطبخ بيتك.  
 ممكن عندك أدوات مختلفة عن غيرك.

أما Docker، فهو بيبعت مع المشروع مطبخ جاهز كامل:

* نفس الفرن  
* نفس الأدوات  
* نفس المقادير  
* نفس طريقة التشغيل

فأي حد يشغله يطلع نفس النتيجة تقريبًا.

---

# **2\. أهم 5 مفاهيم في Docker**

## **2.1 Image**

الـ Image هي قالب جاهز.

مثال:

```text
texlive/texlive:latest  
node:20-slim  
nginx:1.27-alpine

```
دي Images.

الـ Image فيها:

* نظام تشغيل مصغر  
* برامج  
* مكتبات  
* ملفات  
* إعدادات

لكن الـ Image نفسها مش شغالة. هي مجرد قالب.

---

## **2.2 Container**

الـ Container هو نسخة شغالة من Image.

يعني:

Image \= قالب  
Container \= نسخة شغالة من القالب

مثال عملي:

لو عندك image اسمها:

```text
session-authoring-tool-backend  


```
لما تعمل:

```bash
docker compose up

```
Docker يشغل منها container اسمه مثلًا:

```text
session-authoring-tool-backend-1

```
---

## **2.3 Dockerfile**

الـ Dockerfile هو ملف تعليمات بيقول لـ Docker:

ابنيلي Image بالشكل ده.

مثال من مشروعك:

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

RUN ...  
COPY requirements.txt .  
RUN python3.13 -m venv /opt/venv  
COPY . .  
ENV PATH="/opt/venv/bin:$PATH"

EXPOSE 5000

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده بيشرح خطوات بناء backend image.

---

## **2.4 docker-compose.yml**

ده ملف بيقول لـ Docker Compose:

عندي أكتر من خدمة، شغلهم مع بعض.

مثلاً:

* backend  
* frontend  
* database لو موجودة  
* redis لو موجود

في حالتك عندك على الأقل:

```text
backend  
frontend  
  
```
---

## **2.5 Docker Compose**

Docker Compose هو مدير تشغيل لمجموعة containers.

بدل ما تشغل backend لوحده و frontend لوحده، بتقول:

```bash
docker compose up

```
وهو يشغل كل الخدمات الموجودة في `docker-compose.yml`.

---

# **3\. العلاقة بينهم**

خلينا نرتبها:

```text
Dockerfile  
   |  
   | docker build  
   v  
Image  
   |  
   | docker run  
   v  
Container

```
ومع Docker Compose:

```text
docker-compose.yml  
   |  
   | docker compose build  
   v  
```
Images للـ services  
```text
   |  
   | docker compose up  
   v  
```
Containers شغالة مع بعض  
---

# **4\. مثال من مشروعك**

أنت عندك صورتين اتبنوا:

```text
session-authoring-tool-backend  
session-authoring-tool-frontend  


```
وشغالين كـ containers:

```text
session-authoring-tool-backend-1  
session-authoring-tool-frontend-1

```
لما عملت:

```bash
docker compose ps

```
ظهر:

```text
NAME                            	IMAGE                         	SERVICE	STATUS               	PORTS  
session-authoring-tool-backend-1	session-authoring-tool-backend	backend	Up 6 minutes (healthy)   0.0.0.0:5010-\>5000/tcp  
session-authoring-tool-frontend-1   session-authoring-tool-frontend   frontend   Up 6 minutes         	0.0.0.0:5173-\>5173/tcp

```
ده معناه:

* عندك service اسمها `backend`  
* عندك service اسمها `frontend`  
* backend شغال على port داخلي `5000`  
* لكنه متاح عندك على الجهاز من port `5010`  
* frontend شغال ومتاح على port `5173`

---

# **5\. شرح أمر `docker compose ps`**

الأمر:

```bash
docker compose ps

```
## **تفكيك الأمر**

```text
docker

```
ده برنامج Docker نفسه.

```text
compose

```
ده subcommand خاص بتشغيل مشاريع فيها أكتر من service.

```text
ps

```
اختصار process status.  
 يعني اعرض حالة containers الخاصة بالمشروع الحالي.

---

## **الناتج يعني إيه؟**

مثال:

```text
NAME                            	IMAGE                         	COMMAND              	SERVICE	CREATED     	STATUS               	PORTS  
session-authoring-tool-backend-1	session-authoring-tool-backend	"gunicorn --bind 0.0…"   backend	6 minutes ago   Up 6 minutes (healthy)   0.0.0.0:5010-\>5000/tcp  
session-authoring-tool-frontend-1   session-authoring-tool-frontend   "/docker-entrypoint.…"   frontend   6 minutes ago   Up 6 minutes         	0.0.0.0:5173-\>5173/tcp

```
## **شرح الأعمدة**

### **NAME**

اسم الـ container الحقيقي.

مثال:

```text
session-authoring-tool-backend-1

```
Compose بيسمي containers غالبًا بالشكل ده:

```text
project-service-number

```
---

### **IMAGE**

اسم الـ image اللي container اتعمل منها.

مثال:

```text
session-authoring-tool-backend

```
---

### **COMMAND**

الأمر اللي شغال جوه الـ container.

في backend ظهر:

```text
gunicorn --bind 0.0...

```
يعني backend بيشتغل بـ Gunicorn.

---

### **SERVICE**

اسم الخدمة في `docker-compose.yml`.

مثال:

```text
backend  
frontend  
```
---

### **STATUS**

حالة الـ container.

مثال:

```text
Up 6 minutes (healthy)

```
يعني شغال بقاله 6 دقايق، والـ healthcheck ناجح.

---

### **PORTS**

أهم جزء غالبًا.

مثال:

```text
0.0.0.0:5010-\>5000/tcp

```
معناها:

localhost:5010 على جهازك  
رايح إلى  
port 5000 جوه container

يعني لما تفتح:

```text
[http://localhost:5010](http://localhost:5010/)

```
أنت بتدخل على backend container جوه port 5000\.

---

## **تبسيط سينيور**

السطر ده:

```text
0.0.0.0:5010-\>5000/tcp

```
اقرأه كده:

أي request ييجي على جهازك على port 5010، Docker يبعته لجوه container على port 5000\.

---

# **6\. شرح `docker compose up`**

الأمر:

```bash
docker compose up

```
ده أهم أمر يومي تقريبًا.

## **بيعمل إيه؟**

بيقرأ ملف:

```text
docker-compose.yml

```
وبعدين:

1. يشوف الخدمات المطلوبة  
2. يتأكد إن الصور موجودة  
3. يعمل network للمشروع  
4. ينشئ containers  
5. يشغلهم  
6. يعرض logs في نفس التيرمينال

---

## **مثال من اللي حصل معاك**

لما كتبت:

```bash
docker compose up

```
طلع:

```text
[+] up 3/3  
✔ Network session-authoring-tool_default  	Created  
✔ Container session-authoring-tool-backend-1  Created  
✔ Container session-authoring-tool-frontend-1 Created

```
ده معناه:

### **Network اتعملت**

```text
session-authoring-tool_default

```
دي شبكة داخلية بين containers.

الـ frontend يقدر يكلم backend من خلالها.

---

### **Backend container اتعمل**

```text
session-authoring-tool-backend-1

```
---

### **Frontend container اتعمل**

```text
session-authoring-tool-frontend-1

```
---

## **بعد كده بدأ يعرض logs**

مثال:

```text
backend-1  | [INFO] Starting gunicorn 23.0.0  
backend-1  | [INFO] Listening at: [http://0.0.0.0:5000](http://0.0.0.0:5000/)

```
معناه backend اشتغل.

---

# **7\. الفرق بين `docker compose up` و `docker compose up --build`**

## **الأمر العادي**

```bash
docker compose up

```
ده يشغل containers من images الموجودة.

لو image موجودة ومبنية قبل كده، هيستخدمها.

---

## **الأمر مع build**

```bash
docker compose up --build

```
ده بيقول:

قبل ما تشغل، ابني الصور لو محتاجة build.

مفيد لو عدلت في Dockerfile أو dependencies.

---

## **إمتى تستخدم كل واحد؟**

### **استخدم ده في التشغيل اليومي**

```bash
docker compose up

```
### **استخدم ده لو عدلت Dockerfile أو package files**

```bash
docker compose up --build

```
### **تجنب ده إلا للضرورة**

```bash
docker compose build --no-cache

```
لأن `--no-cache` بيجبر Docker يعيد كل حاجة من الأول.

وفي حالتك ده كان بيخلي التحميل ياخد وقت طويل جدًا.

---

# **8\. شرح `docker compose build`**

الأمر:

```bash
docker compose build

```
## **بيعمل إيه؟**

بيبني images الخاصة بالخدمات، لكنه لا يشغل containers.

يعني:

```bash
docker compose build

```
يبني.

لكن:

```bash
docker compose up

```
يشغل.

---

## **مثال**

لو عندك:

```yaml
services:  
  backend:  
	build: ./backend

  frontend:  
	build: ./frontend

```
لما تعمل:

```bash
docker compose build

```
هيبني:

```text
backend image  
frontend image  
```
---

## **بناء خدمة واحدة فقط**

لو عايز تبني backend فقط:

```bash
docker compose build backend

```
لو عايز frontend فقط:

```bash
docker compose build frontend

```
ده مهم جدًا عشان توفر وقت.

---

## **الأمر اللي استخدمته**

```bash
docker compose build --no-cache --progress=plain backend

```
خلينا نفصله.

---

### **`docker`**

برنامج Docker.

### **`compose`**

استخدم Docker Compose.

### **`build`**

ابني image.

### **`--no-cache`**

لا تستخدم الكاش القديم.

يعني كل خطوة في Dockerfile تتعاد من الصفر.

وده سبب إن عندك التحميل اتعاد وبقى بطيء.

### **`--progress=plain`**

اعرض logs بشكل مفصل وواضح.

هو قالك:

```text
--progress is a global compose flag

```
يعني الأفضل تكتبها كده:

```bash
docker compose --progress=plain build --no-cache backend

```
### **`backend`**

ابني خدمة backend فقط.

---

## **الصيغة الأفضل**

```bash
docker compose --progress=plain build backend

```
ولو مضطر تلغي الكاش:

```bash
docker compose --progress=plain build --no-cache backend  
```
---

# **9\. Docker Cache يعني إيه؟**

Docker بيبني الـ image على خطوات اسمها layers.

مثلاً Dockerfile:

```dockerfile
FROM texlive/texlive:latest  
WORKDIR /app  
RUN apt-get update && apt-get install ...  
COPY requirements.txt .  
RUN pip install -r requirements.txt  
COPY . .

```
كل سطر تقريبًا بيعمل layer.

لو Docker لقى إن السطر ومحتواه متغيروش، يستخدم الكاش بدل ما يعيد الخطوة.

---

## **مثال**

لو `requirements.txt` متغيرش، Docker ممكن يستخدم كاش لخطوة:

```dockerfile
RUN pip install -r requirements.txt

```
وده يوفر وقت كبير.

---

## **ليه `--no-cache` خطر في حالتك؟**

لأنك عندك تحميل apt و pip بطيء جدًا.

شفنا:

```text
Fetched 9942 kB in 13min 19s

```
وكمان pip نزل ملفات كبيرة زي:

```text
numpy  
pandas  
pymupdf

```
فلو عملت:

```text
--no-cache

```
هتعيد ده كله.

---

## **قاعدة ذهبية**

استخدم:

```bash
docker compose build backend

```
ولا تستخدم:

```bash
docker compose build --no-cache backend

```
إلا لو:

* الكاش بايظ  
* غيرت حاجة في base image  
* عايز تتأكد إن كل حاجة fresh  
* في dependency مش بتتحدث

---

# **10\. شرح `docker compose down`**

الأمر:

```bash
docker compose down

```
## **بيعمل إيه؟**

بيوقف ويمسح containers والشبكة الخاصة بالمشروع.

يعني:

* يوقف backend container  
* يوقف frontend container  
* يمسحهم  
* يمسح network اللي Compose عملها

لكن غالبًا لا يمسح images.

---

## **الفرق بين Ctrl+C و down**

لو `docker compose up` شغال في التيرمينال، تضغط:

```text
Ctrl + C

```
ده يوقف التشغيل الحالي.

لكن ممكن containers تفضل موجودة بحالة stopped.

أما:

```bash
docker compose down

```
ينظف containers والشبكات.

---

## **متى أستخدم down؟**

لما تخلص شغل على المشروع:

```bash
docker compose down  
```
---

## **هل يمسح البيانات؟**

لو عندك volumes، الأمر العادي:

```bash
docker compose down

```
لا يمسح volumes غالبًا.

لكن لو كتبت:

```bash
docker compose down -v

```
ده يمسح volumes كمان.

خلي بالك من `-v`.

---

# **11\. شرح `docker compose logs`**

لو المشروع شغال في الخلفية أو عايز تشوف logs:

```bash
docker compose logs

```
## **Logs لخدمة معينة**

```bash
docker compose logs backend

```
أو:

```bash
docker compose logs frontend

```
## **متابعة logs مباشرة**

```bash
docker compose logs -f

```
`-f` معناها follow.  
 يعني يفضل يعرض logs الجديدة أول بأول.

---

## **مثال مهم**

لو frontend مش فاتح، تعمل:

```bash
docker compose logs frontend

```
لو backend بيقع:

```bash
docker compose logs backend

```
---

# **12\. شرح `docker compose restart`**

الأمر:

```bash
docker compose restart  


```
بيعمل restart لكل services.

ولو عايز خدمة واحدة:

```bash
docker compose restart backend

```
أو:

```bash
docker compose restart frontend

```
---

## **إمتى أستخدمه؟**

لو غيرت env vars خارجية، أو service علقت، أو عايز restart سريع من غير rebuild.

لكن لو غيرت الكود جوه image ومفيش volume، غالبًا تحتاج rebuild.

---

# **13\. شرح `docker run`**

الأمر ده بيشغل container من image واحدة، بدون Compose.

مثال من اللي عملناه:

```bash
docker run --rm busybox nslookup deb.debian.org

```
## **تفكيك الأمر**

```text
docker

```
برنامج Docker.

```text
run

```
شغل container جديد.

```text
--rm

```
امسح container تلقائيًا بعد ما يخلص.

```text
busybox

```
اسم image خفيفة جدًا فيها أدوات Linux بسيطة.

```text
nslookup deb.debian.org

```
الأمر اللي يتنفذ جوه container.

---

## **ليه استخدمنا `docker run`؟**

عشان نختبر Docker نفسه بعيدًا عن المشروع.

مثلاً:

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
ده بيقول:

هل container يقدر يوصل للإنترنت؟

---

## **مثال آخر**

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
تفكيكها:

### **`docker run`**

شغل container.

### **`--rm`**

امسحه بعد الانتهاء.

### **`debian:stable-slim`**

استخدم Debian image.

### **`bash -lc`**

شغل bash كـ login/command shell.

### **`"apt-get update"`**

الأمر اللي يتنفذ داخل Debian container.

---

# **14\. شرح `docker exec`**

لو container شغال وعايز تدخل جواه:

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
## **تفكيك الأمر**

```bash
docker exec

```
نفذ أمر داخل container شغال.

```text
-it

```
اختصار:

* `-i`: interactive  
* `-t`: terminal

يعني افتح جلسة تفاعلية.

```text
session-authoring-tool-backend-1

```
اسم container.

```text
bash

```
افتح bash shell.

---

## **لو مفيش bash**

بعض images فيها `sh` فقط:

```bash
docker exec -it session-authoring-tool-frontend-1 sh

```
---

## **إمتى أستخدمه؟**

لو عايز تشوف ملفات جوه container:

```text
ls  
pwd  
cat /etc/resolv.conf  
python --version  
```
---

# **15\. Ports في Docker**

دي من أهم النقط اللي حصل فيها لبس عندك.

عندك:

```text
backend   0.0.0.0:5010-\>5000/tcp  
frontend  0.0.0.0:5173-\>5173/tcp

```
## **معنى backend line**

```text
0.0.0.0:5010-\>5000/tcp

```
ده يعني:

```text
Host port: 5010  
Container port: 5000

```
يعني تفتح من جهازك:

```text
[http://localhost:5010](http://localhost:5010/)

```
لكن جوه container التطبيق سامع على:

```text
0.0.0.0:5000

```
---

## **معنى frontend line**

```text
0.0.0.0:5173-\>5173/tcp

```
يعني تفتح:

```text
[http://localhost:5173](http://localhost:5173/)

```
أو في حالتك:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

## **ليه `localhost:5010/session-authoring-tool/` طلع رسالة؟**

لأن `5010` كان backend.

والـ backend قالك:

```text
Frontend bundle not found

```
يعني هو API server، ومش معاه ملفات frontend المبنية.

لكن frontend كان على:

```text
5173

```
---

## **تبسيط سينيور**

الـ port الخارجي هو اللي بتفتحه في المتصفح.

```text
5010 \= backend  
5173 \= frontend  


```
فلو عايز التطبيق المرئي، افتح frontend.

لو عايز API، افتح backend.

---

# **16\. الفرق بين Backend و Frontend Containers**

## **Backend**

مسؤول عن:

* API  
* معالجة الملفات  
* منطق التطبيق  
* Python  
* Flask  
* Gunicorn  
* endpoints تحت `/api`

في logs ظهر:

```text
Starting gunicorn  
Listening at: [http://0.0.0.0:5000](http://0.0.0.0:5000/)  
```
---

## **Frontend**

مسؤول عن:

* واجهة المستخدم  
* HTML/CSS/JS  
* Vue/Vite  
* Nginx أو dev server

في logs ظهر:

```text
nginx/1.27.5  
Configuration complete; ready for start up  
```
---

## **العلاقة بينهم**

المستخدم يفتح frontend.

الـ frontend يكلم backend API.

مثلاً:

```text
Browser  
   |  
   v  
Frontend localhost:5173  
   |  
   v  
```
Backend API localhost:5010 أو backend داخل Docker network  
---

# **17\. Network في Docker Compose**

لما عملت:

```bash
docker compose up

```
ظهر:

```text
Network session-authoring-tool_default Created

```
دي شبكة داخلية.

Compose بيخلي services تكلم بعض بأسماء الخدمات.

يعني frontend ممكن يكلم backend باسم:

```text
backend

```
داخل شبكة Docker.

مش لازم يعرف IP.

---

## **مثال**

لو frontend container عايز يكلم backend جوه Docker، ممكن يستخدم:

```yaml
[http://backend:5000](http://backend:5000/)

```
لأن service اسمها `backend`.

---

## **من المتصفح**

المتصفح بتاعك خارج Docker، فبيستخدم:

```text
[http://localhost:5010](http://localhost:5010/)

```
دي نقطة مهمة.

---

## **تبسيط سينيور**

جوه Docker:

```yaml
backend:5000

```
من جهازك:

```text
localhost:5010

```
---

# **18\. قراءة Docker build logs**

أنت شوفت logs زي:

```dockerfile
\#8 [3/6] RUN ...  
\#9 [4/6] COPY requirements.txt .  
\#10 [5/6] RUN python3.13 -m venv ...

```
## **معنى `[3/6]`**

يعني الخطوة رقم 3 من 6 خطوات في build stage.

---

## **مثال**

```dockerfile
\#8 [3/6] RUN set -eux; ...

```
يعني Docker ينفذ أمر `RUN` في Dockerfile.

---

## **`DONE`**

مثال:

```text
\#8 DONE 1243.7s

```
يعني الخطوة خلصت ونجحت.

---

## **`ERROR`**

لو ظهر:

```text
ERROR [backend 3/6]

```
يبقى الخطوة دي فشلت.

---

## **ازاي تعرف سبب الخطأ؟**

دور على أول error حقيقي، مش آخر ملخص.

مثلاً كان عندك:

```text
Temporary failure resolving 'deb.debian.org'

```
ده السبب الحقيقي القديم.

وبعدها كان:

```text
Package has no installation candidate  


```
ده كان أثر جانبي لأن `apt-get update` فشل.

---

# **19\. فهم Dockerfile بسرعة**

خلينا نشرح Dockerfile بتاع backend بشكل عام.

```dockerfile
FROM texlive/texlive:latest

```
ابدأ من image جاهزة فيها TeX Live.

---

```dockerfile
ENV DEBIAN_FRONTEND=noninteractive

```
خلي أوامر Debian لا تسأل أسئلة أثناء التثبيت.

مفيد في Docker لأن مفيش حد يرد على prompt.

---

```dockerfile
WORKDIR /app

```
أي أوامر بعد كده تشتغل من داخل فولدر:

```text
/app

```
لو الفولدر مش موجود، Docker يعمله.

---

```dockerfile
RUN ...

```
نفذ أمر أثناء بناء image.

مثلاً install packages.

---

```text
COPY requirements.txt .

```
انسخ `requirements.txt` من جهازك إلى `/app/requirements.txt` داخل image.

النقطة `.` معناها المكان الحالي داخل image، وهو `/app`.

---

```dockerfile
RUN python3.13 -m venv /opt/venv

```
اعمل virtual environment في:

```text
/opt/venv

```
---

```dockerfile
ENV PATH="/opt/venv/bin:$PATH"

```
خلي أوامر زي:

```bash
python  
pip  
gunicorn

```
تستخدم اللي جوه venv الأول.

---

```text
EXPOSE 5000

```
توثيق إن التطبيق بيسمع على port 5000 داخل container.

مهم: `EXPOSE` لوحدها لا تفتح port على جهازك.  
 الفتح الحقيقي بيكون في compose بـ `ports`.

---

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده الأمر اللي يتنفذ لما container يشتغل.

معناه:

شغل Gunicorn على:

```text
0.0.0.0:5000

```
واستخدم Flask app من:

```text
app:app

```
غالبًا:

* `app` الأولى \= اسم ملف Python `app.py`  
* `app` الثانية \= اسم متغير Flask app داخل الملف

---

# **20\. أهم أوامر يومية ليك**

## **تشغيل المشروع**

```bash
docker compose up

```
---

## **تشغيل في الخلفية**

```bash
docker compose up -d

```
`-d` معناها detached mode.

يعني يشغل containers ويرجعك للترمينال.

---

## **رؤية الحالة**

```bash
docker compose ps

```
---

## **رؤية logs**

```bash
docker compose logs

```
---

## **متابعة logs**

```bash
docker compose logs -f

```
---

## **logs للـ backend**

```bash
docker compose logs -f backend

```
---

## **logs للـ frontend**

```bash
docker compose logs -f frontend

```
---

## **إيقاف وتنظيف**

```bash
docker compose down

```
---

## **إعادة بناء backend فقط**

```bash
docker compose build backend

```
---

## **إعادة بناء frontend فقط**

```bash
docker compose build frontend

```
---

## **إعادة بناء وتشغيل**

```bash
docker compose up --build

```
---

## **الدخول داخل backend container**

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
---

## **الدخول داخل frontend container**

```bash
docker exec -it session-authoring-tool-frontend-1 sh

```
---

# **21\. أخطاء شائعة جدًا**

## **21.1 فتحت backend بدل frontend**

لو فتحت:

```text
[http://localhost:5010/session-authoring-tool/](http://localhost:5010/session-authoring-tool/)

```
وظهر:

```text
Frontend bundle not found

```
يبقى أنت فاتح backend.

افتح frontend:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

## **21.2 استخدمت `--no-cache` كتير**

ده يعيد التحميل من الصفر.

تجنبه.

---

## **21.3 افتكرت `pyenv` بيأثر على Docker**

لما تعمل:

```text
pyenv shell 3.11.9  


```
ده يغير Python على جهازك فقط.

لكن Docker يستخدم Python الموجود داخل image.

---

## **21.4 package install فشل بعد `apt-get update` فشل**

لو شفت:

```text
Package has no installation candidate

```
متفترضش فورًا إن package مش موجودة.

الأول شوف هل:

```text
apt-get update

```
نجح ولا لأ.

---

# **22\. Checklist عند تشغيل أي مشروع Docker**

استخدم القائمة دي كل مرة.

## **1\. هل containers شغالة؟**

```bash
docker compose ps

```
---

## **2\. هل backend healthy؟**

شوف status:

```text
Up ... (healthy)

```
---

## **3\. أنهي port للـ frontend؟**

من:

```bash
docker compose ps

```
دور على service frontend.

---

## **4\. أنهي port للـ backend؟**

دور على service backend.

---

## **5\. لو الصفحة مش فاتحة**

شوف logs:

```bash
docker compose logs frontend

```
---

## **6\. لو API مش شغال**

```bash
docker compose logs backend

```
---

## **7\. لو عدلت Dockerfile**

```bash
docker compose build backend  
docker compose up  
```
---

# **23\. تمارين عملية**

## **تمرين 1: اعرض الخدمات**

نفذ:

```bash
docker compose ps

```
واكتب عندك:

```text
backend port \= ?  
frontend port \= ?  
```
---

## **تمرين 2: افتح logs للـ backend**

```bash
docker compose logs backend

```
دور على:

```text
Listening at

```
واكتب port الداخلي.

---

## **تمرين 3: افتح logs للـ frontend**

```bash
docker compose logs frontend

```
دور على:

```text
nginx

```
أو:

```text
ready for start up

```
---

## **تمرين 4: ادخل جوه backend**

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
ثم نفذ:

```text
pwd  
ls  
python --version  
which python  
which gunicorn

```
واخرج بـ:

```text
exit

```
---

## **تمرين 5: افهم port mapping**

من نتيجة:

```bash
docker compose ps

```
لو شفت:

```text
0.0.0.0:5010-\>5000/tcp

```
جاوب:

* أفتح من المتصفح port كام؟  
* التطبيق جوه container سامع على port كام؟

الإجابة:

المتصفح: 5010  
داخل container: 5000  
---

# **24\. أوامر مهمة بصيغة Cheatsheet**

## **Start**

```bash
docker compose up

```
## **Start in background**

```bash
docker compose up -d

```
## **Stop and remove containers**

```bash
docker compose down

```
## **Show running services**

```bash
docker compose ps

```
## **Build all services**

```bash
docker compose build

```
## **Build backend only**

```bash
docker compose build backend

```
## **Build frontend only**

```bash
docker compose build frontend

```
## **Build and run**

```bash
docker compose up --build

```
## **Logs**

```bash
docker compose logs

```
## **Follow logs**

```bash
docker compose logs -f

```
## **Backend logs**

```bash
docker compose logs -f backend

```
## **Frontend logs**

```bash
docker compose logs -f frontend

```
## **Enter backend**

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
## **Enter frontend**

```bash
docker exec -it session-authoring-tool-frontend-1 sh

```
---

# **25\. الخلاصة التنفيذية**

في مشروعك تحديدًا:

## **للتشغيل اليومي**

```bash
docker compose up

```
وبعدين افتح:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

## **لمعرفة الـ ports**

```bash
docker compose ps

```
---

## **لو عدلت backend**

```bash
docker compose build backend  
docker compose up  
```
---

## **لو عدلت frontend**

```bash
docker compose build frontend  
docker compose up  
```
---

## **لو عايز توقف كل حاجة**

```bash
docker compose down

```
---

# **تبسيط سينيور نهائي**

Docker في مشروعك عامل زي مدير تشغيل.

هو عنده ملف خطة:

```text
docker-compose.yml

```
وفي الخطة دي مكتوب:

* شغل backend  
* شغل frontend  
* اعمل network بينهم  
* افتح ports معينة على الجهاز

الـ backend عندك كان على:

```text
localhost:5010

```
والـ frontend كان على:

```text
localhost:5173

```
أنت لما فتحت `5010` كنت بتكلم السيرفر الخلفي.  
 ولما فتحت `5173` وصلت للواجهة الصح.

أهم عادة لازم تثبتها:

```bash
docker compose ps

```
قبل ما تفتح أي URL.

لأنها بتقولك الحقيقة:

* مين شغال  
* على أنهي port  
* حالته إيه

---

## **الواجب قبل Handbook 2**

نفذ دول واكتب ملاحظاتك:

```bash
docker compose ps

docker compose logs backend --tail=20

docker compose logs frontend --tail=20

docker exec -it session-authoring-tool-backend-1 bash

```
وجوه الـ container:

```text
pwd  
ls  
python --version  
which python  
which gunicorn  
exit

```
لو عملت التمارين دي، هتدخل Handbook 2 بتاع **Docker DNS and Network Debugging** وأنت فاهم الأرضية كويس جدًا.

# Tab 2

# **Handbook 2 — Docker DNS & Network Debugging**

## **تشخيص مشاكل الشبكة و DNS داخل Docker خطوة بخطوة**

يا إسلام، ده **الهاند بوك التاني كامل** من السلسلة، وده مبني على المشكلة الحقيقية اللي حصلت معاك:

في البداية كان Docker بيقع عند:

```text
Temporary failure resolving 'deb.debian.org'

```
وبعدين بعد ما حلّينا DNS ظهر نوع تاني من المشاكل:

```text
Unable to connect to deb.debian.org:http

```
وده مهم جدًا لأن الاتنين شكلهم قريب، لكن معناهم مختلف تمامًا.

---

# **0\. هدف الهاند بوك**

بعد الهاند بوك ده المفروض تبقى قادر تعمل الآتي:

* تفهم يعني إيه DNS.  
* تعرف الفرق بين:  
  * الإنترنت شغال.  
  * DNS شغال.  
  * HTTP/HTTPS شغال.  
* تشخص مشاكل Docker network بنفسك.  
* تستخدم أوامر:  
```text
   nslookup  
  ping  
  cat /etc/resolv.conf  
  scutil --dns  
  apt-get update  
  curl  
  nc  
```
* تعرف إمتى المشكلة من Docker.  
* إمتى من DNS.  
* إمتى من mirror أو HTTP.  
* إمتى من Dockerfile.  
* وتعرف ليه `pyenv` ملوش علاقة بمشكلة Docker network.

---

# **1\. المشكلة اللي حصلت معاك باختصار**

أنت شغلت:

```bash
docker compose up --build

```
والـ backend وقع عند:

```text
Temporary failure resolving 'deb.debian.org'  


```
بعدها جربنا:

```bash
docker run --rm busybox nslookup deb.debian.org

```
وطلع:

```text
;; connection timed out; no servers could be reached

```
بعد كده جربنا:

```bash
docker run --rm busybox ping -c 3 8.8

```
وطلع إن الـ ping شغال.

ده كان معناه:

Docker عنده Internet بالـ IP  
لكن DNS مش شغال

بعدها لقينا إن جهازك بيستخدم DNS داخلي:

```text
172.23.100.226  
172.23.100.225

```
لكن Docker كان متجبر يستخدم:

```text
8.8.8.8  
8.8.4.4

```
ولما خلينا Docker يستخدم DNS بتاع جهازك، المشكلة اتحلت.

---

# **2\. يعني إيه DNS؟**

DNS اختصار:

```text
Domain Name System

```
وظيفته يحوّل اسم الموقع إلى IP.

مثلاً لما تكتب:

```text
deb.debian.org

```
الجهاز لازم يعرف الـ IP الحقيقي بتاعه، مثلًا:

```text
199.232.186.132

```
فـ DNS بيعمل العملية دي:

```text
deb.debian.org  ---\>  199.232.186.132

```
---

## **ليه ده مهم؟**

لأن البرامج غالبًا بتتعامل مع أسماء مواقع مش IPs.

مثلاً:

```text
apt-get update

```
بيحاول يدخل على:

```text
[http://deb.debian.org/debian](http://deb.debian.org/debian)

```
لكن قبل ما يدخل، لازم يعرف:

```text
deb.debian.org

```
ده عنوانه IP إيه.

لو DNS فشل، الاتصال كله يفشل من الأول.

---

## **تبسيط سينيور**

تخيل إنك عايز تروح مكان اسمه:

```text
Debian Repository

```
بس معاك الاسم مش العنوان.

DNS هو الدليل اللي يقولك:

المكان ده عنوانه كذا.

لو الدليل مش شغال، مش هتعرف تروح حتى لو الطريق نفسه مفتوح.

---

# **3\. الفرق بين Internet و DNS و HTTP**

دي أهم نقطة في الهاند بوك.

## **3.1 Internet/IP Connectivity**

يعني الـ container يقدر يوصل لـ IP مباشر.

اختباره:

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
لو اشتغل، يبقى عندك اتصال IP.

مثال ناجح:

```text
64 bytes from 8.8.8.8  
0% packet loss

```
ده معناه:

الإنترنت الأساسي شغال

لكن لا يعني إن DNS شغال.

---

## **3.2 DNS Connectivity**

يعني الـ container يقدر يحوّل اسم domain إلى IP.

اختباره:

```bash
docker run --rm busybox nslookup deb.debian.org

```
لو اشتغل، تشوف حاجة زي:

```text
Name:   debian.map.fastlydns.net  
Address: 199.232.186.132

```
ده معناه:

DNS شغال

---

## **3.3 HTTP/HTTPS Connectivity**

يعني بعد ما عرفنا IP، نقدر نفتح اتصال على port معين.

مثلاً:

```text
http  \= port 80  
https \= port 443

```
اختباره ممكن يكون بـ:

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
أو:

```bash
docker run --rm curlimages/curl:latest curl -I [https://deb.debian.org](https://deb.debian.org/)

```
لو DNS شغال لكن HTTP واقع، تشوف أخطاء زي:

```text
Unable to connect to deb.debian.org:http

```
أو:

```text
Connection timed out  
  
```
---

# **4\. اقرأ الأخطاء صح**

## **الخطأ الأول**

```text
Temporary failure resolving 'deb.debian.org'  


```
ده معناه:

```text
DNS failed

```
يعني Docker مش عارف يحول الاسم إلى IP.

---

## **الخطأ الثاني**

```text
;; connection timed out; no servers could be reached

```
ده ظهر مع:

```text
nslookup

```
معناه:

الـ container حاول يسأل DNS server لكن مفيش رد

الأسباب المحتملة:

* DNS server غلط.  
* DNS محجوب من الشبكة.  
* Docker Desktop عنده config غلط.  
* VPN/Firewall مانع DNS.  
* الشركة/الشبكة سامحة DNS داخلي فقط.

---

## **الخطأ الثالث**

```text
Could not connect to debian.map.fastlydns.net:80  
Unable to connect to deb.debian.org:http

```
ده معناه:

DNS اشتغل، لكن الاتصال على HTTP port 80 فشل

يعني المرحلة دي مش DNS.

دي Network/HTTP/Mirror issue.

---

## **الخطأ الرابع**

```text
Package has no installation candidate

```
ده خادع جدًا.

معناه ممكن يكون:

1. الباكدج مش موجودة فعلًا.  
2. أو `apt-get update` فشل، فـ apt معندوش package lists.  
3. أو أنت على repo مختلفة.  
4. أو architecture مختلفة.  
5. أو اسم package غلط.

في حالتك، السبب كان غالبًا إن `apt-get update` فشل قبلها.

---

# **5\. خطوات التشخيص الصحيحة**

لو Docker build وقع بسبب network، متبدأش تعدل Dockerfile عشوائي.

امشي بالترتيب ده.

---

## **Step 1: اختبر الإنترنت بالـ IP**

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
### **لو فشل**

يبقى المشكلة مش DNS فقط.

دي مشكلة network عامة داخل Docker.

احتمالاتها:

* Docker Desktop network bug.  
* VPN.  
* Firewall.  
* Proxy.  
* Docker network corrupt.  
* الجهاز نفسه مفيهوش route.

### **لو نجح**

يبقى الإنترنت الأساسي شغال، وننتقل لاختبار DNS.

---

## **Step 2: اختبر DNS**

```bash
docker run --rm busybox nslookup deb.debian.org

```
### **لو نجح**

DNS شغال.

### **لو فشل**

زي:

```text
;; connection timed out; no servers could be reached

```
يبقى DNS داخل Docker فيه مشكلة.

---

## **Step 3: شوف Docker بيستخدم DNS إيه**

```bash
docker run --rm busybox cat /etc/resolv.conf

```
في حالتك ظهر:

```text
nameserver 8.8.8.8  
nameserver 8.8.4.4

```
وده كان غلط بالنسبة لشبكتك.

---

## **Step 4: شوف جهازك نفسه بيستخدم DNS إيه**

على macOS:

```text
scutil --dns | grep nameserver

```
عندك ظهر:

```text
nameserver[0] : 172.23.100.226  
nameserver[1] : 172.23.100.225

```
ودي كانت الـ DNS servers الصحيحة.

---

## **Step 5: جرّب DNS الجهاز داخل Docker**

```bash
docker run --rm --dns 172.23.100.226 busybox nslookup deb.debian.org

```
عندك نجح وطلع:

```text
Name:   debian.map.fastlydns.net  
Address: 199.232.186.132

```
ده كان الدليل النهائي إن:

Docker DNS config هو المشكلة

---

# **6\. شرح كل أمر استخدمناه**

## **6.1 الأمر `docker run --rm busybox nslookup deb.debian.org`**

```bash
docker run --rm busybox nslookup deb.debian.org

```
### **تفكيك الأمر**

```text
docker

```
برنامج Docker.

```text
run

```
شغل container جديد.

```text
--rm

```
امسح container بعد ما يخلص.

ده مفيد في الاختبارات السريعة عشان متسيبش containers كتير.

```text
busybox

```
Image صغيرة جدًا فيها أدوات Linux بسيطة.

```text
nslookup deb.debian.org

```
نفذ أمر DNS lookup داخل container.

---

## **استخدامه**

الأمر ده بيجاوب سؤال:

هل Docker container يقدر يعمل DNS resolve؟

---

## **نتيجة ناجحة**

```text
Server:     	172.23.100.226  
Address:    	172.23.100.226:53

Name:   debian.map.fastlydns.net  
Address  
```
---

## **نتيجة فاشلة**

```text
;; connection timed out; no servers could be reached

```
معناها DNS server مش بيرد.

---

# **7\. الأمر `ping`**

```bash
docker run --rm busybox ping -c 3 8.8.8.8  


```
## **تفكيك الجزء المهم**

```text
ping

```
اختبار وصول network.

```text
-c 3

```
ابعت 3 packets فقط.

بدون `-c 3`، الأمر ممكن يفضل شغال لحد ما توقفه.

```text
8.8.8.8

```
IP مباشر لـ Google DNS.

---

## **ليه استخدمنا IP مش domain؟**

لأننا كنا عايزين نختبر الإنترنت بدون DNS.

لو استخدمت:

```text
ping google.com

```
ده يحتاج DNS.

لكن:

```text
ping 8.8.8.8

```
لا يحتاج DNS.

---

## **النتيجة عندك**

```text
3 packets transmitted, 3 packets received, 0% packet loss

```
المعنى:

Docker container عنده network/IP connectivity

---

# **8\. الأمر `cat /etc/resolv.conf`**

```bash
docker run --rm busybox cat /etc/resolv.conf

```
## **`/etc/resolv.conf` ده إيه؟**

ده ملف داخل Linux بيقول للنظام يستخدم DNS servers إيه.

مثال:

```text
nameserver 172.23.100.226  
nameserver 172.23.100.225

```
أي برنامج جوه container لما يحتاج DNS، بيرجع للملف ده.

---

## **النتيجة القديمة عندك**

```text
nameserver 8.8.8.8  
nameserver 8.8.4.4

```
وكان فيها:

```text
Overrides: [nameservers]

```
ده معناه إن Docker Engine config كان مجبر Docker يستخدم DNS معين.

---

## **النتيجة بعد الإصلاح**

```text
nameserver 172.23.100.226  
nameserver 172.23.100.225

```
وده كان التصحيح المطلوب.

---

# **9\. الأمر `scutil --dns`**

على macOS:

```text
scutil --dns | grep nameserver

```
## **`scutil --dns`**

يعرض إعدادات DNS اللي macOS بيستخدمها.

## **`|`**

ده pipe.

معناه:

خد output الأمر الأول وابعته للأمر الثاني.

## **`grep nameserver`**

فلتر النتائج واعرض السطور اللي فيها كلمة `nameserver`.

---

## **النتيجة عندك**

```text
nameserver[0] : 172.23.100.226  
nameserver[1] : 172.23.100.225

```
ودي قلنا لـ Docker يستخدمها.

---

# **10\. إصلاح Docker Engine DNS**

كان عندك Docker Engine config بالشكل ده:

```text
{  
  "builder": {  
	"gc": {  
  	"defaultKeepStorage": "20GB",  
  	"enabled": true  
	}  
  },  
  "dns": [  
	"8.8.8.8",  
	"8.8.4.4"  
  ],  
  "experimental": false  
}

```
غيرناه إلى:

```text
{  
  "builder": {  
	"gc": {  
  	"defaultKeepStorage": "20GB",  
  	"enabled": true  
	}  
  },  
  "dns": [  
	"172.23.100.226",  
	"172.23.100.225"  
  ],  
  "experimental": false  
}  
```
---

## **شرح JSON بسرعة**

```text
"dns": [  
  "172.23.100.226",  
  "172.23.100.225"  
]

```
ده بيقول لـ Docker Engine:

لما containers تحتاج DNS، استخدم السيرفرات دي.

---

## **بعد التعديل**

لازم تعمل:

```text
Apply & Restart

```
من Docker Desktop.

لأن Docker Engine لازم يعيد التشغيل عشان يطبق الإعداد.

---

# **11\. ليه Google DNS ما اشتغلش؟**

أنت جربت:

```bash
docker run --rm --dns 8.8.8.8 busybox nslookup deb.debian.org

```
وفشل.

وجربت:

```bash
docker run --rm --dns 1.1.1.1 busybox nslookup deb.debian.org

```
وفشل.

لكن لما جربت:

```bash
docker run --rm --dns 172.23.100.226 busybox nslookup deb.debian.org

```
اشتغل.

ده معناه غالبًا إن شبكتك:

* تمنع DNS الخارجي.  
* أو تجبرك تستخدم DNS داخلي.  
* أو VPN/Proxy/Corporate network موجه DNS داخلي.  
* أو الراوتر/الشبكة لا تسمح بالاستعلامات لـ `8.8.8.8:53`.

---

## **تبسيط سينيور**

الإنترنت عندك كان شغال، بس الشبكة بتقول:

لو عايز تسأل عن أسماء المواقع، اسأل DNS بتاعي أنا، مش Google.

Docker كان بيسأل Google، فمفيش رد.

لما خليناه يسأل DNS الشبكة، اشتغل.

---

# **12\. الفرق بين DNS failure و HTTP failure**

دي أهم نقطة من تجربة مشروعك.

## **DNS failure**

الرسالة:

```text
Temporary failure resolving 'deb.debian.org'

```
المعنى:

مش عارف أجيب IP بتاع deb.debian.org

الحل:

* افحص `/etc/resolv.conf`  
* افحص DNS بتاع الجهاز  
* عدل Docker DNS

---

## **HTTP failure**

الرسالة:

```text
Could not connect to debian.map.fastlydns.net:80  
Unable to connect to deb.debian.org:http

```
المعنى:

أنا عرفت IP، بس مش قادر أفتح اتصال HTTP

الحل:

* جرّب HTTPS بدل HTTP.  
* زود retries/timeouts.  
* غير mirror.  
* شوف proxy/VPN/firewall.  
* اختبر port 80/443.

---

# **13\. اختبار `apt-get update`**

استخدمناه كاختبار واقعي.

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
## **ليه ده اختبار مهم؟**

لأنه يشبه اللي Dockerfile بيعمله.

لو نجح:

```text
Fetched ...  
Reading package lists...

```
يبقى Debian repo reachable.

لو فشل بـ DNS:

```text
Temporary failure resolving

```
يبقى DNS.

لو فشل بـ HTTP:

```text
Unable to connect

```
يبقى اتصال HTTP/HTTPS أو mirror.

---

## **شرح الأمر**

```bash
docker run

```
شغل container.

```text
--rm

```
امسحه بعد ما يخلص.

```text
debian:stable-slim

```
Image Debian خفيفة.

```text
bash -lc

```
شغل bash ونفذ الأمر اللي بعده.

```text
"apt-get update"

```
حدث package indexes.

---

# **14\. معنى `apt-get update`**

الأمر:

```text
apt-get update

```
لا يثبت برامج.

هو فقط يحدث قائمة البرامج المتاحة من repositories.

يعني يحمل files زي:

```text
InRelease  
Packages

```
عشان apt يعرف:

* إيه الباكدجات المتاحة؟  
* آخر versions؟  
* dependencies؟  
* package موجودة ولا لا؟

---

## **ليه لو فشل تظهر packages كأنها مش موجودة؟**

لأن apt معندوش index حديث.

فلو بعدها عملت:

```text
apt-get install poppler-utils

```
ممكن يقول:

```text
Package poppler-utils has no installation candidate

```
مش لأن package مش موجودة بالضرورة، لكن لأن قائمة packages مش اتحملت.

---

# **15\. اختبار `apt-cache policy`**

استخدمنا:

```text
apt-cache policy python3.13 python3.13-venv poppler-utils

```
## **بيعمل إيه؟**

يعرض حالة package:

* هل مثبتة؟  
* هل ليها Candidate؟  
* version المتاح؟  
* مصدرها؟

---

## **مثال من عندك**

```text
python3.13:  
  Installed: 3.13.12-1  
  Candidate: 3.13.12-1

```
ده معناه:

python3.13 موجود أصلًا

لكن:

```text
python3.13-venv:  
  Installed: (none)  
  Candidate: (none)

```
ده وقتها كان بسبب إن `apt-get update` فشل.

بعد ما update نجح، `python3.13-venv` اتثبت عادي.

---

# **16\. ليه استخدمنا HTTPS بدل HTTP؟**

الخطأ كان:

```text
Unable to connect to deb.debian.org:http

```
يعني HTTP port 80 كان بيعمل timeout.

فعدلنا sources من:

```text
[http://deb.debian.org](http://deb.debian.org/)

```
إلى:

```text
[https://deb.debian.org](https://deb.debian.org/)

```
يعني استخدام port 443 بدل port 80\.

---

## **في Dockerfile استخدمنا:**

```text
find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +

```
هنشرحها باختصار.

---

## **`find /etc/apt`**

دور داخل فولدر:

```text
/etc/apt

```
وده فيه ملفات مصادر apt.

---

## **`-type f`**

هات الملفات فقط.

---

## **`\( -name "*.list" -o -name "*.sources" \)`**

هات الملفات اللي اسمها ينتهي بـ:

```text
.list

```
أو:

```text
.sources

```
ملحوظة: الأقواس لازم تتعمل escape في shell:

```text
\</span\>\</div\>\<div class="scriptor-paragraph"\>\<span attribution="{\&quot;name\&quot;:\&quot;Copilot\&quot;,\&quot;oid\&quot;:\&quot;E64C3D4F-5E12-4514-AD9B-893A6FAFD00C\&quot;,\&quot;id\&quot;:\&quot;E64C3D4F-5E12-4514-AD9B-893A6FAFD00C\&quot;,\&quot;userInfo\&quot;:{\&quot;name\&quot;:\&quot;Copilot\&quot;,\&quot;oid\&quot;:\&quot;E64C3D4F-5E12-4514-AD9B-893A6FAFD00C\&quot;,\&quot;id\&quot;:\&quot;E64C3D4F-5E12-4514-AD9B-893A6FAFD00C\&quot;},\&quot;timestamp\&quot;:1781162700000,\&quot;dataSource\&quot;:0}"\>  
```
---

## **`-exec sed -i ... {} +`**

نفذ أمر `sed` على الملفات اللي لقيتها.

---

## **`sed -i`**

عدل الملف في مكانه.

---

## **`'s|http://deb.debian.org|https://deb.debian.org|g'`**

استبدل كل:

```text
[http://deb.debian.org](http://deb.debian.org/)

```
بـ:

```text
[https://deb.debian.org](https://deb.debian.org/)

```
---

# **17\. ليه زودنا apt retries/timeouts؟**

استخدمنا:

```text
printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries

```
## **الهدف**

نقول لـ apt:

* لو التحميل فشل، حاول 5 مرات.  
* استنى 180 ثانية قبل ما تعتبر HTTP timeout.  
* استنى 180 ثانية قبل ما تعتبر HTTPS timeout.

---

## **شرح الأمر**

```text
printf

```
يطبع نص منسق.

---

```text
Acquire::Retries "5";

```
عدد مرات إعادة المحاولة.

---

```text
Acquire::http::Timeout "180";

```
مهلة HTTP بالثواني.

---

```text
Acquire::https::Timeout "180";

```
مهلة HTTPS بالثواني.

---

```text
\> /etc/apt/apt.conf.d/80-retries

```
اكتب الإعدادات في ملف apt config.

---

## **ليه ده كان مهم عندك؟**

لأن السرعة كانت بطيئة جدًا:

```text
12.4 kB/s

```
فـ apt كان محتاج يصبر أكتر.

---

# **18\. Docker DNS داخل compose**

ممكن تضيف DNS في `docker-compose.yml`:

```yaml
services:  
  backend:  
	dns:  
  	- 172.23.100.226  
  	- 172.23.100.225

  frontend:  
	dns:  
  	- 172.23.100.226  
  	- 172.23.100.225

```
لكن في حالتك الأفضل كان Docker Engine DNS.

## **ليه؟**

لأن المشكلة كانت أثناء:

```bash
docker compose build

```
مش بس أثناء تشغيل containers.

إعداد DNS في Docker Engine أقوى وأعم.

---

# **19\. الفرق بين build-time network و run-time network**

دي نقطة advanced شوية.

## **Build-time**

ده أثناء:

```bash
docker compose build

```
أو:

```bash
docker build

```
مثلاً Dockerfile يعمل:

```dockerfile
RUN apt-get update

```
ده network أثناء البناء.

---

## **Run-time**

ده بعد ما container يشتغل:

```bash
docker compose up

```
مثلاً التطبيق يكلم API أو database.

---

## **ليه الفرق مهم؟**

لأن ممكن DNS يشتغل في runtime لكن build يفشل، أو العكس.

في حالتك كنا محتاجين DNS شغال أثناء build عشان:

```dockerfile
RUN apt-get update

```
---

# **20\. مشاكل VPN / Proxy / Firewall**

لو Docker يقدر يعمل ping لكن DNS fails، دور على:

* VPN  
* Corporate proxy  
* Cloudflare WARP  
* AdGuard  
* Little Snitch  
* LuLu firewall  
* Antivirus firewall  
* DNS filtering  
* Router policy

---

## **علامات وجود DNS filtering**

* `ping 8.8.8.8` يشتغل.  
* `nslookup` على `8.8.8.8` يفشل.  
* DNS الداخلي يشتغل.  
* DNS خارجي `1.1.1.1` و `8.8.8.8` لا يعمل.

وده كان قريب جدًا من حالتك.

---

# **21\. أوامر طوارئ للتشخيص**

## **عرض DNS داخل container**

```bash
docker run --rm busybox cat /etc/resolv.conf

```
---

## **اختبار DNS default**

```bash
docker run --rm busybox nslookup deb.debian.org

```
---

## **اختبار DNS server معين**

```bash
docker run --rm --dns 172.23.100.226 busybox nslookup deb.debian.org

```
---

## **اختبار الإنترنت بدون DNS**

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
---

## **اختبار apt**

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
---

## **اختبار texlive image تحديدًا**

```bash
docker run --rm texlive/texlive:latest bash -lc "apt-get update"

```
---

## **معرفة DNS بتاع macOS**

```text
scutil --dns | grep nameserver

```
---

# **22\. Decision Tree سريع**

لو ظهر:

```text
Temporary failure resolving

```
اعمل:

```bash
docker run --rm busybox nslookup deb.debian.org

```
لو فشل:

```bash
docker run --rm busybox ping -c 3 8.8.8.8  
  
```
---

## **الحالة A: ping فشل**

المشكلة network عامة.

جرّب:

```bash
docker compose down  
docker network prune

Restart Docker Desktop.

```
راجع VPN/Firewall.

---

## **الحالة B: ping نجح و nslookup فشل**

المشكلة DNS.

اعمل:

```bash
docker run --rm busybox cat /etc/resolv.conf  
scutil --dns | grep nameserver

```
وجرب:

```bash
docker run --rm --dns YOUR_DNS busybox nslookup deb.debian.org

```
لو نجح، عدّل Docker Engine DNS.

---

## **الحالة C: nslookup نجح و apt فشل**

المشكلة مش DNS.

اختبر:

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
لو HTTP timeout، جرب HTTPS أو mirror أو timeout أعلى.

---

# **23\. جدول ذهني للمشاكل**

بدون جدول رسمي، احفظها كده:

```text
ping IP fails  
```
\=\> Network مشكلة عامة

```text
ping IP works + nslookup fails  
```
\=\> DNS مشكلة

```text
nslookup works + http fails  
```
\=\> HTTP/HTTPS أو mirror أو firewall

```text
apt update fails + install says candidate none  
```
\=\> غالبًا package lists مش اتحملت

```bash
build fails but docker run works  
```
\=\> BuildKit/build-time network أو timeout/cache  
---

# **24\. تطبيق مباشر على مشكلتك**

## **المرحلة الأولى**

كان عندك:

```text
Temporary failure resolving 'deb.debian.org'

```
اختبرنا:

```bash
docker run --rm busybox nslookup deb.debian.org

```
فشل.

اختبرنا:

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
نجح.

إذن:

DNS فقط هو المشكلة

---

## **المرحلة الثانية**

لقينا:

```bash
docker run --rm busybox cat /etc/resolv.conf

```
يعرض:

```text
8.8.8.8  
8.8.4.4

```
لكن macOS يعرض:

```text
172.23.100.226  
172.23.100.225

```
جربنا:

```bash
docker run --rm --dns 172.23.100.226 busybox nslookup deb.debian.org

```
نجح.

إذن:

Docker لازم يستخدم DNS بتاع الجهاز

---

## **المرحلة الثالثة**

عدلنا Docker Engine DNS.

بعدها:

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
نجح.

إذن:

DNS اتحل رسميًا

---

## **المرحلة الرابعة**

ظهر:

```text
Unable to connect to deb.debian.org:http

```
ده كان HTTP timeout.

فعدّلنا Dockerfile يستخدم HTTPS وزودنا retries.

وبالفعل build نجح.

---

# **25\. التريكات المهمة**

## **تريك 1: لا تثق في آخر error فقط**

أحيانًا آخر error يكون:

```text
Package has no installation candidate

```
لكن السبب الحقيقي فوقه:

```text
apt-get update failed

```
دايمًا اقرأ أول error حقيقي.

---

## **تريك 2: افصل المشكلة**

لا تختبر بـ `docker compose build` مباشرة كل مرة.

اختبر بحاجات صغيرة:

```bash
docker run --rm busybox nslookup deb.debian.org

```
ثم:

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
ثم ارجع للـ build.

---

## **تريك 3: استخدم IP لاختبار الشبكة بدون DNS**

```text
ping 8.8.8.8

```
لو استخدمت domain، دخلت DNS في الاختبار.

---

## **تريك 4: استخدم نفس base image**

لو backend Dockerfile يبدأ بـ:

```dockerfile
FROM texlive/texlive:latest

```
اختبر نفس image:

```bash
docker run --rm texlive/texlive:latest bash -lc "apt-get update"

```
مش بس Debian stable.

---

## **تريك 5: لو الشبكة بطيئة، زود retries**

في Dockerfile:

```dockerfile
RUN printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries

```
---

## **تريك 6: لا تستخدم `--no-cache` إلا للضرورة**

لأنك هتعيد تحميل كل حاجة.

بعد ما build نجح، استخدم:

```bash
docker compose build backend

```
بدل:

```bash
docker compose build --no-cache backend

```
---

# **26\. تمارين عملية**

## **تمرين 1: اختبر IP connectivity**

نفذ:

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
واكتب:

هل packet loss \= 0%؟

---

## **تمرين 2: اختبر DNS**

```bash
docker run --rm busybox nslookup deb.debian.org  


```
لو نجح، اكتب الـ IP اللي رجع.

---

## **تمرين 3: اعرض DNS داخل Docker**

```bash
docker run --rm busybox cat /etc/resolv.conf

```
واكتب nameservers.

---

## **تمرين 4: اعرض DNS بتاع macOS**

```text
scutil --dns | grep nameserver

```
قارن بينه وبين Docker.

---

## **تمرين 5: اختبر apt**

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
لو نجح، اكتب آخر سطر.

غالبًا:

```text
Reading package lists...

```
---

## **تمرين 6: اختبر base image بتاع المشروع**

```bash
docker run --rm texlive/texlive:latest bash -lc "apt-get update"

```
راقب هل بيستخدم `http` ولا `https`، وهل التحميل سريع ولا بطيء.

---

# **27\. Cheatsheet سريع**

## **DNS داخل Docker**

```bash
docker run --rm busybox cat /etc/resolv.conf

```
## **DNS lookup**

```bash
docker run --rm busybox nslookup deb.debian.org

```
## **DNS lookup بسيرفر معين**

```bash
docker run --rm --dns 172.23.100.226 busybox nslookup deb.debian.org

```
## **IP connectivity**

```bash
docker run --rm busybox ping -c 3 8.8.8.8

```
## **macOS DNS**

```text
scutil --dns | grep nameserver

```
## **apt test**

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
## **image-specific apt test**

```bash
docker run --rm texlive/texlive:latest bash -lc "apt-get update"

```
## **compose status**

```bash
docker compose ps

```
## **build backend**

```bash
docker compose --progress=plain build backend

```
---

# **28\. تبسيط سينيور نهائي**

مشاكل Docker network تتشخص كأنك بتسأل 3 أسئلة بالترتيب:

## **السؤال الأول**

هل الـ container عنده إنترنت بالـ IP؟

```text
ping 8.8.8.8

```
لو لا، المشكلة network عامة.

---

## **السؤال الثاني**

هل الـ container يقدر يترجم أسماء المواقع؟

```text
nslookup deb.debian.org

```
لو لا، المشكلة DNS.

---

## **السؤال الثالث**

هل يقدر يفتح HTTP/HTTPS بعد ما عرف الـ IP؟

```text
apt-get update

```
لو لا، المشكلة mirror/HTTP/firewall/timeout.

---

# **29\. خلاصة مشكلتك في سطر واحد**

المشكلة بدأت بـ:

```text
Docker DNS misconfiguration

```
ثم بعد إصلاح DNS ظهرت:

```text
Slow/unstable Debian repository access over HTTP

```
والحل كان:

```text
Use correct internal DNS + switch apt sources to HTTPS + increase apt retries/timeouts

```
---

# **30\. واجب قبل Handbook 3**

نفذ الأوامر دي واحفظ نواتجها في ملف ملاحظات:

```bash
docker run --rm busybox cat /etc/resolv.conf

docker run --rm busybox nslookup deb.debian.org

docker run --rm busybox ping -c 3 8.8.8.8

scutil --dns | grep nameserver

docker run --rm debian:stable-slim bash -lc "apt-get update"

```
وبعدها شغل:

```bash
docker compose ps

```
هدف الواجب إنك تبقى قادر تقول:

* Docker بيستخدم DNS إيه؟  
* الجهاز بيستخدم DNS إيه؟  
* DNS شغال ولا لا؟  
* الإنترنت شغال ولا لا؟  
* apt شغال ولا لا؟

---

لو جاهز، الهاند بوك التالت هيكون عن

# Tab 3

# **Handbook 3 — Backend Dockerfile Deep Dive**

## **شرح Dockerfile بتاع الـ Backend سطر بسطر \+ التريكات \+ أفضل الممارسات**

يا إسلام، ده **الهاند بوك التالت كامل**.  
 هنا هنفهم Dockerfile بتاع الـ backend بتاعك بعمق، مش مجرد نعدله ونمشي.

الـ Dockerfile اللي كان عندك:

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

RUN apt-get update \</span\>  
	&& apt-get install -y --no-install-recommends \</span\>  
   	python3.13 \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils \</span\>  
	&& rm -rf /var/lib/apt/lists/\*

COPY requirements.txt .

RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PATH="/opt/venv/bin:$PATH"

EXPOSE 5000

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]  


```
والنسخة اللي عدلناها بقت تقريبًا:

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

RUN set -eux; \</span\>  
	printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries; \</span\>  
	find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +; \</span\>  
	apt-get update; \</span\>  
	apt-get install -y --no-install-recommends \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils; \</span\>  
	rm -rf /var/lib/apt/lists/\*

COPY requirements.txt .

RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PATH="/opt/venv/bin:$PATH"

EXPOSE 5000

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]  
```
---

# **1\. هدف Dockerfile أصلًا**

الـ Dockerfile هو **وصفة بناء Image**.

يعني بدل ما تقول يدويًا:

```text
apt-get update  
apt-get install ...  
python -m venv ...  
pip install ...  
gunicorn ...

```
بتكتب كل ده في ملف واحد اسمه:

```text
Dockerfile

```
وبعدين Docker يبني منه image.

## **الفكرة العامة**

```text
Dockerfile  
   |  
   | docker build  
   v  
Image  
   |  
   | docker compose up  
   v  
Container  
```
---

## **تبسيط سينيور**

Dockerfile عامل زي recipe.

لو بتعمل أكلة:

* هات دقيق.  
* هات مياه.  
* سخن الفرن.  
* حط المكونات.  
* استنى.  
* قدم الأكل.

في Docker:

* هات base image.  
* ثبت packages.  
* انسخ الكود.  
* ثبت dependencies.  
* حدد أمر التشغيل.

---

# **2\. السطر الأول: `FROM`**

```dockerfile
FROM texlive/texlive:latest

```
## **يعني إيه `FROM`؟**

أي Dockerfile لازم يبدأ من base image.

السطر ده بيقول:

ابني الـ image بتاعتي فوق image جاهزة اسمها `texlive/texlive:latest`.

---

## **يعني إيه `texlive/texlive`؟**

دي image جاهزة فيها TeX Live.

TeX Live هو distribution ضخم لأدوات LaTeX.

غالبًا مشروعك محتاجه عشان:

* يولد PDF.  
* يتعامل مع ملفات LaTeX.  
* يعمل compile لمحتوى تعليمي.  
* يستخدم أدوات زي `pdflatex`, `xelatex`, `lualatex`.

---

## **يعني إيه `latest`؟**

```text
texlive/texlive:latest

```
`latest` tag معناها:

هات أحدث نسخة متاحة من الصورة دي حسب Docker Hub.

لكن خلي بالك: `latest` مش دايمًا معناها "أفضل" أو "أكثر استقرارًا".

---

## **مشكلة `latest`**

لو image اتحدثت بكرة، build بتاعك ممكن يتغير بدون ما أنت تغير Dockerfile.

يعني النهارده:

```text
texlive/texlive:latest \= Python 3.13.12

```
بكرة ممكن تبقى:

```text
Python 3.14

```
أو Debian sources تتغير.

وده ممكن يكسر build.

---

## **التريكة الاحترافية**

في production، الأفضل تثبت version أو digest.

مثلاً بدل:

```dockerfile
FROM texlive/texlive:latest

```
ممكن تستخدم digest ظهر عندك في logs:

```dockerfile
FROM texlive/texlive:latest@sha256:2f9918a156cfc6c7527590e06f69bfb4fbc827278e4736c90e567382855d7cd1

```
لكن ده advanced شوية.

---

## **تبسيط سينيور**

`FROM` هو الأرض اللي بتبني عليها البيت.

لو الأرض بتتغير كل يوم، البيت ممكن يطلع مختلف.

عشان كده `latest` مريح في التطوير، لكنه خطر في production.

---

# **3\. السطر الثاني: `ENV DEBIAN_FRONTEND=noninteractive`**

```dockerfile
ENV DEBIAN_FRONTEND=noninteractive

```
## **يعني إيه `ENV`؟**

`ENV` بتضيف environment variable داخل image/container.

يعني بتقول للنظام:

```text
DEBIAN_FRONTEND=noninteractive

```
---

## **يعني إيه `DEBIAN_FRONTEND`؟**

ده متغير خاص بأنظمة Debian/Ubuntu.

أثناء تثبيت packages بـ `apt-get install`، بعض البرامج ممكن تسأل أسئلة.

مثلاً:

```text
Choose timezone  
Restart services?  
Configure package?

```
لكن أثناء Docker build مفيش إنسان يجاوب.

فبنكتب:

```dockerfile
ENV DEBIAN_FRONTEND=noninteractive

```
عشان نقول:

يا Debian، متسألنيش أسئلة. استخدم defaults.

---

## **ليه مهم؟**

لو مفيش السطر ده، build ممكن يعلق.

يعني تلاقي Docker واقف ومستني input مش ظاهر بوضوح.

---

## **تريكة مهمة**

ممكن بدل ما تخليه global، تستخدمه داخل RUN فقط:

```dockerfile
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y ...

```
لكن استخدامه كـ `ENV` شائع وبسيط.

---

# **4\. السطر الثالث: `WORKDIR`**

```dockerfile
WORKDIR /app

```
## **يعني إيه `WORKDIR`؟**

ده بيحدد فولدر العمل داخل الـ image.

أي أمر بعده هيشتغل من داخل:

```text
/app

```
لو `/app` مش موجود، Docker هيعمله.

---

## **مثال**

بعد:

```dockerfile
WORKDIR /app

```
السطر ده:

```text
COPY requirements.txt .

```
معناه:

انسخ requirements.txt إلى /app/requirements.txt

لأن النقطة `.` معناها `/app`.

---

## **ليه منستخدمش `RUN mkdir /app && cd /app`؟**

ممكن، بس غلط عمليًا.

لأن `cd` داخل `RUN` بيأثر فقط على نفس الأمر، مش على الأوامر التالية.

مثلاً:

```bash
RUN cd /app  
RUN pwd

```
السطر التاني مش مضمون يبقى داخل `/app`.

الأصح:

```dockerfile
WORKDIR /app

```
---

## **تبسيط سينيور**

`WORKDIR` يعني:

من هنا ورايح، اعتبرني واقف في فولدر `/app`.

---

# **5\. أول `RUN`: تثبيت Linux packages**

النسخة القديمة:

```dockerfile
RUN apt-get update \</span\>  
	&& apt-get install -y --no-install-recommends \</span\>  
   	python3.13 \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils \</span\>  
	&& rm -rf /var/lib/apt/lists/\*

```
دي أهم خطوة كانت بتقع معاك.

خلينا نشرحها جزء جزء.

---

## **5.1 يعني إيه `RUN`؟**

```dockerfile
RUN ...

```
بتنفذ أمر أثناء بناء image.

أي حاجة تحصل في `RUN` بتتحفظ في image layer.

مثلاً:

```dockerfile
RUN apt-get install curl

```
بعدها image يبقى فيها `curl`.

---

## **5.2 الأمر `apt-get update`**

```text
apt-get update

```
ده لا يثبت برامج.

ده بيحمّل package indexes.

يعني apt يعرف:

* البرامج المتاحة.  
* versions.  
* dependencies.  
* مصادر البرامج.

---

## **ليه لازم قبل install؟**

لأن `apt-get install` محتاج يعرف packages متاحة منين.

لو `apt-get update` فشل، ممكن بعدها تشوف:

```text
Package has no installation candidate

```
حتى لو package موجودة فعلًا.

---

## **5.3 `&&`**

```text
apt-get update && apt-get install ...

```
`&&` معناها:

نفذ الأمر اللي بعدي فقط لو اللي قبلي نجح.

يعني لو:

```text
apt-get update

```
فشل، متكملش install.

---

## **ليه استخدمنا backslash `\`؟**

```dockerfile
RUN apt-get update \</span\>  
	&& apt-get install ...

```
الـ backslash معناها كمل الأمر في السطر اللي بعده.

بدونها، Docker هيعتبر السطر خلص.

بنستخدمها عشان readability.

---

## **5.4 `apt-get install -y`**

```text
apt-get install -y

```
`-y` معناها:

وافق تلقائيًا على التثبيت.

بدون `-y`، apt ممكن يسأل:

```text
Do you want to continue? [Y/n]

```
وفي Docker build مفيش حد يرد.

---

## **5.5 `--no-install-recommends`**

```text
apt-get install -y --no-install-recommends

```
Debian packages ليها:

* dependencies ضرورية.  
* recommends مش ضرورية لكنها مقترحة.

`--no-install-recommends` معناها:

ثبت الضروري فقط.

وده يقلل حجم image.

---

## **مثال**

بدونها، ممكن تثبت packages زيادة كتير.

معها، image تبقى أخف.

---

# **6\. شرح packages في Dockerfile**

## **6.1 `python3.13`**

```text
python3.13

```
ده Python runtime.

لكن في حالتك اكتشفنا إنه موجود أصلًا داخل:

```dockerfile
FROM texlive/texlive:latest

```
لما عملنا:

```text
apt-cache policy python3.13

```
ظهر:

```text
Installed: 3.13.12-1

```
يعني تثبيته مرة تانية غير ضروري.

---

## **6.2 `python3.13-venv`**

```text
python3.13-venv

```
دي package مهمة عشان الأمر ده يشتغل:

```text
python3.13 -m venv /opt/venv

```
بدونها ممكن يظهر error زي:

```text
ensurepip is not available

```
أو:

```text
No module named venv

```
---

## **6.3 `curl`**

```text
curl

```
أداة تستخدم لعمل HTTP requests من command line.

مثلاً:

```text
curl [http://localhost:5000](http://localhost:5000/)

```
أحيانًا تستخدمها في healthcheck أو debugging.

في حالتك ظهر إنها كانت مثبتة بالفعل:

```text
curl is already the newest version

```
لكن وجودها مش مضر.

---

## **6.4 `poppler-utils`**

```text
poppler-utils

```
دي package مهمة جدًا لو المشروع بيتعامل مع PDFs.

بتوفر أدوات زي:

```text
pdftotext  
pdfinfo  
pdftoppm

```
مثلاً Python packages أو كود backend ممكن يحتاج يحول PDF لصورة أو يقرأ معلومات PDF.

---

# **7\. تنظيف apt lists**

```text
rm -rf /var/lib/apt/lists/\*

```
## **ليه بنعمل كده؟**

بعد:

```text
apt-get update

```
apt بيحمل package indexes في:

```text
/var/lib/apt/lists/

```
دي ملفات مؤقتة كبيرة.

بعد ما تثبت packages، مش محتاجها داخل final image.

فنمشيها:

```text
rm -rf /var/lib/apt/lists/\*

```
عشان نقلل حجم image.

---

## **مهم**

لازم التنظيف يكون في نفس `RUN` اللي فيه `apt-get update`.

يعني صح:

```dockerfile
RUN apt-get update \</span\>  
	&& apt-get install -y curl \</span\>  
	&& rm -rf /var/lib/apt/lists/\*

```
غلط أو أقل فائدة:

```dockerfile
RUN apt-get update  
RUN apt-get install -y curl  
RUN rm -rf /var/lib/apt/lists/\*

```
ليه؟

لأن كل `RUN` بيعمل layer، والملفات ممكن تفضل في layer قديمة.

---

# **8\. النسخة المحسنة من أول RUN**

عدّلناها إلى:

```dockerfile
RUN set -eux; \</span\>  
	printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries; \</span\>  
	find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i '

```
نشرحها.

---

## **8.1 `set -eux`**

```text
set -eux

```
دي shell options.

### **`-e`**

لو أي أمر فشل، أوقف الـ RUN فورًا.

### **`-u`**

لو استخدمت variable مش متعرف، اعتبره error.

### **`-x`**

اطبع كل أمر قبل تنفيذه.

وده مفيد جدًا في logs.

---

## **ليه استخدمناه؟**

عشان أثناء build تشوف بوضوح إيه اللي بيتنفذ.

زي اللي ظهر عندك:

```text
+ apt-get update  
+ apt-get install ...  
```
---

## **8.2 إعداد apt retries/timeouts**

```text
printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries

```
ده كتب ملف config لـ apt.

## **معناه**

```text
Acquire::Retries "5";

```
لو download فشل، حاول 5 مرات.

```text
Acquire::http::Timeout "180";

```
استنى 180 ثانية للـ HTTP.

```text
Acquire::https::Timeout "180";

```
استنى 180 ثانية للـ HTTPS.

---

## **ليه احتجناه؟**

لأن الاتصال عندك كان بطيء جدًا:

```text
12.4 kB/s

```
فلو apt impatient، هيفشل.

---

## **8.3 تحويل HTTP إلى HTTPS**

```text
find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +

```
ده بيدور في apt source files ويغير:

```text
[http://deb.debian.org](http://deb.debian.org/)

```
إلى:

```text
[https://deb.debian.org](https://deb.debian.org/)

```
---

## **ليه عملنا كده؟**

لأن الخطأ كان:

```text
Unable to connect to deb.debian.org:http

```
يعني port 80 كان بيعمل timeout.

باستخدام HTTPS نروح على port 443\.

---

# **9\. `COPY requirements.txt .`**

```text
COPY requirements.txt .

```
## **يعني إيه `COPY`؟**

ينسخ ملف من جهازك إلى image.

الصيغة:

```text
COPY source destination

```
هنا:

```text
source \= requirements.txt  
destination \= .

```
والنقطة `.` تعني `WORKDIR`، يعني:

```text
/app

```
فالنتيجة:

```text
/app/requirements.txt

```
---

## **ليه بننسخ requirements.txt قبل COPY . . ؟**

دي تريكة Docker cache مهمة جدًا.

لو عملت:

```text
COPY . .  
RUN pip install -r requirements.txt

```
أي تغيير في أي ملف في المشروع هيكسر cache بتاع pip install.

لكن الأفضل:

```text
COPY requirements.txt .  
RUN pip install -r requirements.txt  
COPY . .

```
كده لو غيرت كود Python فقط، Docker مش هيعيد pip install طالما `requirements.txt` متغيرش.

---

## **تبسيط سينيور**

بنقول لـ Docker:

ثبت dependencies الأول بناءً على requirements.txt.  
 لو الكود اتغير بس dependencies متغيرتش، متعيدش التثبيت.

وده يوفر وقت كبير جدًا.

---

# **10\. إنشاء virtual environment**

```dockerfile
RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin

```
دي خطوة Python الأساسية.

---

## **10.1 `python3.13 -m venv /opt/venv`**

```text
python3.13 -m venv /opt/venv

```
## **معناها**

استخدم Python 3.13 وشغل module اسمها `venv`.

اعمل virtual environment في:

```text
/opt/venv

```
---

## **يعني إيه virtual environment؟**

بيئة Python منفصلة.

بدل ما تثبت packages في Python النظام، بتثبتها داخل:

```text
/opt/venv

```
---

## **ليه نستخدم venv داخل Docker؟**

سؤال مهم جدًا.

ناس تقول:

Docker نفسه isolated، ليه أعمل venv؟

الإجابة: مش دايمًا لازم، لكن مفيد لأنه:

* يفصل packages بتاعت التطبيق عن system Python.  
* يخلي PATH واضح.  
* يقلل مشاكل Debian Python packages.  
* يخلي `gunicorn`, `flask`, `pandas` جوه مكان واحد.

---

## **10.2 `/opt/venv/bin/pip install --upgrade pip setuptools wheel`**

```text
/opt/venv/bin/pip install --upgrade pip setuptools wheel

```
## **ليه نستخدم path كامل؟**

لأننا قبل `ENV PATH`، لو كتبت:

```text
pip

```
ممكن تستخدم pip النظام مش pip بتاع venv.

فبنكتب:

```text
/opt/venv/bin/pip

```
عشان نضمن.

---

## **packages دي إيه؟**

### **`pip`**

مدير تثبيت Python packages.

### **`setuptools`**

أداة build/install لكتير من Python packages.

### **`wheel`**

صيغة package جاهزة للتثبيت أسرع من build from source.

---

## **ليه نحدثهم؟**

عشان بعض packages تحتاج أحدث build tooling.

خصوصًا packages زي:

* numpy  
* pandas  
* pymupdf

---

## **10.3 `pip install --no-cache-dir -r requirements.txt`**

```text
/opt/venv/bin/pip install --no-cache-dir -r requirements.txt

```
## **`-r requirements.txt`**

ثبت كل dependencies المكتوبة في الملف.

مثلاً عندك:

```text
flask==3.1.3flaskpandas==3.0.3  
pymupdf==1.27.2.3  
gunicorn==23.0.0  
numpy==2.4.4  
```
---

## **`--no-cache-dir`**

يقول لـ pip:

متحتفظش بكاش download داخل image.

ده يقلل حجم image.

---

## **عيب `--no-cache-dir`**

لو بتبني كتير، ممكن الكاش يساعد.

لكن داخل Docker، غالبًا نعتمد على Docker layer cache مش pip cache.

في حالتك، التحميل كان بطيء، فممكن نفكر في advanced caching لاحقًا، لكن كبداية `--no-cache-dir` كويس.

---

# **11\. `COPY . .`**

```text
COPY . .

```
## **معناها**

انسخ كل ملفات backend context إلى `/app`.

لو Dockerfile موجود في backend والـ context هو backend، يبقى ينسخ محتوى backend.

---

## **خطر `COPY . .`**

لو مفيش `.dockerignore` كويس، ممكن تنسخ حاجات مش محتاجها:

* `.git`  
* `__pycache__`  
* `.venv`  
* ملفات test ضخمة  
* ملفات PDF كبيرة  
* node\_modules  
* dist  
* secrets

---

## **لازم يكون عندك `.dockerignore`**

مثال جيد:

```text
**pycache**/  
\*.pyc  
.pytest_cache/  
.venv/  
venv/  
.env  
.git/  
.gitignore  
.DS_Store  
node_modules/  
dist/  
```
---

## **ليه مهم؟**

لأن Docker بيرسل build context كله للـ Docker daemon.

لو context كبير، build يبطأ.

---

# **12\. `ENV PATH="/opt/venv/bin:$PATH"`**

```dockerfile
ENV PATH="/opt/venv/bin:$PATH"

```
## **معناها**

ضيف `/opt/venv/bin` في بداية PATH.

يعني لما container يشغل:

```bash
python  
pip  
gunicorn

```
يدور الأول في:

```text
/opt/venv/bin

```
---

## **ليه مهم؟**

لأن `gunicorn` اتثبت داخل venv من requirements.

لو PATH مش متعدل، السطر ده:

```text
ENTRYPOINT ["gunicorn", ...]

```
ممكن يفشل:

```text
exec: "gunicorn": executable file not found

```
---

## **بعد السطر ده**

الأمر:

```text
which gunicorn

```
داخل container غالبًا يرجع:

```text
/opt/venv/bin/gunicorn

```
---

# **13\. `EXPOSE 5000`**

```text
EXPOSE 5000

```
## **معناها**

ده metadata بيقول:

التطبيق داخل container متوقع يسمع على port 5000\.

---

## **هل `EXPOSE` يفتح port على جهازك؟**

لا.

ده مهم جدًا.

السطر ده لا يجعل التطبيق متاح على `localhost`.

اللي يفتح port فعليًا هو docker compose:

```text
ports:  
  - "5010:5000"  
```
---

## **الفرق**

```text
EXPOSE 5000

```
توثيق داخل image.

```text
ports:  
  - "5010:5000"

```
فتح فعلي من host إلى container.

---

# **14\. `ENTRYPOINT`**

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
## **يعني إيه `ENTRYPOINT`؟**

ده الأمر اللي container يشغله لما يبدأ.

يعني لما تعمل:

```bash
docker compose up

```
الـ backend container يشغل:

```text
gunicorn --bind 0.0.0.0:5000 app:app

```
---

## **ليه مكتوب JSON array؟**

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
دي اسمها exec form.

أفضل من shell form:

```text
ENTRYPOINT gunicorn --bind 0.0.0.0:5000 app:app

```
لأن exec form:

* تتعامل أفضل مع signals.  
* مناسبة لإيقاف container.  
* أقل مشاكل shell parsing.

---

## **شرح Gunicorn command**

```text
gunicorn --bind 0.0.0.0:5000 app:app

```
### **`gunicorn`**

WSGI server لتشغيل تطبيقات Python web.

بدل Flask dev server.

---

### **`--bind 0.0.0.0:5000`**

معناها:

اسمع على كل network interfaces داخل container، port 5000\.

---

## **ليه `0.0.0.0` مش `127.0.0.1`؟**

لو التطبيق سمع على:

```text
127.0.0.1

```
داخل container، غالبًا مش هيكون reachable من خارج container.

لازم:

```text
0.0.0.0

```
عشان Docker يقدر يوجه requests له.

---

### **`app:app`**

دي مهمة.

غالبًا معناها:

```text
module_name:variable_name

```
يعني:

```text
app.py : app

```
لو عندك ملف:

```text
from flask import Flask

app \= Flask(**name**)

```
Gunicorn يشغله بـ:

```text
gunicorn app:app

```
الأولى اسم الملف بدون `.py`.

الثانية اسم object داخل الملف.

---

# **15\. لماذا backend اشتغل؟**

في logs ظهر:

```text
backend-1  | [INFO] Starting gunicorn 23.0.0  
backend-1  | [INFO] Listening at: [http://0.0.0.0:5000](http://0.0.0.0:5000/)  
backend-1  | [INFO] Booting worker with pid: 7  
Container session-authoring-tool-backend-1 Healthy

```
ده معناه:

* `gunicorn` موجود.  
* PATH شغال.  
* `app:app` اتقرأ.  
* Flask app اشتغل.  
* port 5000 شغال.  
* healthcheck نجح.

---

# **16\. Dockerfile النهائي المقترح حاليًا**

دي نسخة مناسبة جدًا لحالتك الحالية:

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

RUN set -eux; \</span\>  
	printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries; \</span\>  
	find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +; \</span\>  
	apt-get update; \</span\>  
	apt-get install -y --no-install-recommends \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils; \</span\>  
	rm -rf /var/lib/apt/lists/\*

COPY requirements.txt .

RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PATH="/opt/venv/bin:$PATH"

EXPOSE 5000

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]  
```
---

# **17\. تحسين إضافي: استخدم `CMD` مع `ENTRYPOINT`؟**

حاليًا عندك:

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده تمام.

لكن ممكن تقسيم احترافي:

```text
ENTRYPOINT ["gunicorn"]  
CMD ["--bind", "0.0.0.0:5000", "app:app"]

```
## **الفرق**

`ENTRYPOINT` الأمر الأساسي.

`CMD` arguments الافتراضية.

ده يسمح إنك تغير arguments بسهولة وقت التشغيل.

لكن للمشروع الحالي، النسخة الحالية كافية.

---

# **18\. تحسين إضافي: workers في Gunicorn**

حاليًا:

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده بيستخدم worker واحد غالبًا.

ممكن تضيف workers:

```text
ENTRYPOINT ["gunicorn", "--workers", "2", "--bind", "0.0.0.0:5000", "app:app"]

```
لكن خلي بالك:

* workers أكتر \= memory أكتر.  
* في dev مش ضروري.  
* في production بنحسبها حسب CPU.

---

# **19\. تحسين مهم: ترتيب COPY للـ cache**

الترتيب الحالي كويس:

```text
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

```
ده أفضل من:

```text
COPY . .  
RUN pip install -r requirements.txt

```
لأن الترتيب الحالي يستفيد من Docker cache.

---

# **20\. تحسين مهم: `.dockerignore`**

لازم جدًا تتأكد إن عندك `.dockerignore`.

اقترح للـ backend:

```text
**pycache**/  
*.pyc*  
.pyo  
*.pyd*

*.pytest_cache/*  
*.coverage*  
*htmlcov/*

*.venv/*  
*venv/*  
*env/*

*.env*  
*.env.*

.git/  
.gitignore

.DS_Store

node_modules/  
dist/  
build/

\*.log

```
لو عندك ملفات PDF ضخمة لا تدخل في build، أضف:

```text
\*.pdf

```
لكن فقط لو التطبيق لا يحتاجها داخل image.

---

# **21\. أوامر تشخيص Dockerfile**

## **ابني backend فقط**

```bash
docker compose --progress=plain build backend

```
## **ابنيه من غير cache عند الضرورة فقط**

```bash
docker compose --progress=plain build --no-cache backend

```
## **شغل المشروع**

```bash
docker compose up

```
## **ادخل container**

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
## **اختبر Python**

```bash
python --version  
which python

```
متوقع:

```text
/opt/venv/bin/python

```
## **اختبر Gunicorn**

```text
which gunicorn  
gunicorn --version

```
متوقع:

```text
/opt/venv/bin/gunicorn

```
## **اختبر poppler**

```text
pdfinfo -v  
pdftotext -v  
```
---

# **22\. فهم Docker build cache مع Dockerfile بتاعك**

Docker بيقسم Dockerfile إلى layers.

تقريبًا:

```dockerfile
Layer 1: FROM texlive  
Layer 2: WORKDIR /app  
Layer 3: apt-get install packages  
Layer 4: COPY requirements.txt  
Layer 5: pip install  
Layer 6: COPY source code  
Layer 7: ENV/EXPOSE/ENTRYPOINT

```
لو غيرت ملف Python فقط، المفروض Docker يعيد:

```text
COPY . .

```
وما يعيدش:

```text
apt-get install  
pip install

```
طالما:

* Dockerfile قبلها متغيرش.  
* requirements.txt متغيرش.  
* ما استخدمتش `--no-cache`.

---

## **أهم نصيحة**

بعد ما build نجح، لا تستخدم:

```text
--no-cache

```
إلا للضرورة.

لأن عندك الإنترنت بطيء جدًا، وده هيعيد:

* apt download.  
* pip download.

---

# **23\. مشاكل شائعة في Dockerfile ده**

## **مشكلة 1: `python3.13-venv` مش موجود**

لو ظهر:

```text
Package python3.13-venv has no installation candidate

```
اسأل الأول:

هل `apt-get update` نجح؟

لو لا، أصلح network/repo.

لو نعم، جرب:

```text
python3-venv

```
واستخدم:

```text
python3 -m venv /opt/venv

```
---

## **مشكلة 2: `gunicorn: not found`**

سببها غالبًا:

* gunicorn مش موجود في requirements.txt.  
* pip install فشل.  
* PATH مش متعدل.  
* venv مش متعمل.

اختبر:

```bash
docker exec -it session-authoring-tool-backend-1 bash  
which gunicorn  
```
---

## **مشكلة 3: backend يسمع على `127.0.0.1`**

لو غيرت bind إلى:

```text
127.0.0.1:5000

```
ممكن التطبيق مايبقاش reachable.

خليه:

```text
0.0.0.0:5000

```
---

## **مشكلة 4: `Frontend bundle not found`**

دي مش من Dockerfile backend مباشرة.

دي حصلت لما فتحت backend port:

```text
localhost:5010

```
بدل frontend port:

```text
localhost:5173

```
---

# **24\. نسخة Advanced: base image محلية لتوفير الوقت**

عشان apt/pip بطيئين عندك، ممكن تعمل base image مرة واحدة.

## **`Dockerfile.base`**

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

RUN set -eux; \</span\>  
	printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries; \</span\>  
	find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +; \</span\>  
	apt-get update; \</span\>  
	apt-get install -y --no-install-recommends \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils; \</span\>  
	rm -rf /var/lib/apt/lists/\*

```
ابنيها:

```bash
docker build -f backend/Dockerfile.base -t sat-texlive-python-base:local backend

```
وبعدين Dockerfile الأساسي يبدأ بـ:

```dockerfile
FROM sat-texlive-python-base:local

```
ده يخلي تثبيت apt يحصل مرة واحدة.

---

# **25\. نسخة Advanced أكثر: build wheels مسبقًا**

عندك packages كبيرة:

```text
numpy  
pandas  
pymupdf

```
ممكن تعمل wheelhouse محلي أو pip cache mount، لكن ده نخليه في Handbook 7 بتاع optimization.

---

# **26\. Checklist لمراجعة أي Dockerfile Backend**

قبل ما تعتمد Dockerfile، اسأل:

## **Base image**

* هل image مناسبة؟  
* هل `latest` مقبول؟  
* هل محتاج pin version؟

## **System packages**

* هل packages ضرورية؟  
* هل `apt-get update` و install في نفس RUN؟  
* هل استخدمت `--no-install-recommends`؟  
* هل نظفت `/var/lib/apt/lists/*`؟

## **Python**

* هل Python version واضح؟  
* هل venv معمول؟  
* هل pip installs من requirements؟  
* هل PATH متعدل للـ venv؟

## **Cache**

* هل `requirements.txt` بيتنسخ قبل `COPY . .`؟  
* هل `.dockerignore` موجود؟

## **Runtime**

* هل `EXPOSE` صحيح؟  
* هل `ENTRYPOINT` صحيح؟  
* هل app بتسمع على `0.0.0.0`؟  
* هل `gunicorn` موجود؟

---

# **27\. تمارين عملية**

## **تمرين 1: افحص Python داخل backend**

بعد تشغيل المشروع:

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
ثم:

```bash
python --version  
which python  
pip --version  
which pip

```
المفروض تلاقيهم داخل:

```text
/opt/venv

```
---

## **تمرين 2: افحص Gunicorn**

داخل container:

```text
which gunicorn  
gunicorn --version  
```
---

## **تمرين 3: افحص poppler**

داخل container:

```text
pdfinfo -v  
pdftotext -v  
```
---

## **تمرين 4: افحص ملفات التطبيق**

داخل container:

```text
pwd  
ls -la

```
لازم تكون داخل:

```text
/app

```
---

## **تمرين 5: افهم `app:app`**

داخل container:

```text
ls

```
دور على:

```text
app.py

```
ثم:

```bash
python -c "from app import app; print(app)"

```
لو اشتغل، يبقى `app:app` صحيح.

---

# **28\. Cheatsheet Dockerfile**

## **Build backend**

```bash
docker compose --progress=plain build backend

```
## **Run**

```bash
docker compose up

```
## **Enter backend**

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
## **Check Python**

```bash
python --version  
which python

```
## **Check app**

```bash
python -c "from app import app; print(app)"

```
## **Check Gunicorn**

```text
gunicorn --version  
which gunicorn

```
## **Check ports**

```bash
docker compose ps

```
---

# **29\. تبسيط سينيور نهائي**

Dockerfile بتاع backend بيعمل 7 حاجات رئيسية:

1. يبدأ من image فيها TeX Live.  
2. يجهز Debian للتثبيت بدون أسئلة.  
3. يحدد `/app` كمجلد شغل.  
4. يثبت system packages المطلوبة.  
5. يعمل Python virtual environment.  
6. يثبت Python dependencies.  
7. يشغل Flask app عن طريق Gunicorn.

المشكلة اللي حصلت معاك كانت مش إن Dockerfile فكرته غلط، لكن:

* `apt` كان بيستخدم HTTP ومش مستقر عندك.  
* Docker DNS كان محتاج تعديل.  
* الاتصال كان بطيء جدًا.  
* `python3.13` كان موجود أصلًا، فثبتنا فقط `python3.13-venv`.

---

# **30\. الواجب قبل Handbook 4**

نفذ الأوامر دي واكتب نتائجها:

```bash
docker compose ps

docker exec -it session-authoring-tool-backend-1 bash

```
ثم داخل container:

```text
pwd  
ls -la  
python --version  
which python  
pip --version  
which pip  
which gunicorn  
gunicorn --version  
pdfinfo -v  
exit

```
وبعدين افتح Dockerfile واقرأه من فوق لتحت واسأل نفسك عند كل سطر:

هل السطر ده build-time ولا run-time؟  
هل بيأثر على cache؟  
هل محتاج network؟  
هل بيزود حجم image؟  
هل ضروري لتشغيل التطبيق؟  
---

## **القادم**

```text
**Handbook 4 — apt-get, Debian Repos, and Package Debugging**

```
وده هيبقى عميق جدًا في:

* `apt-get update`  
* `apt-get install`  
* repositories  
* `sources.list`  
* `Candidate: none`  
* الفرق بين package مش موجودة و package index فاشلة  
* mirrors  
* HTTP/HTTPS  
* timeouts/retries  
* وشرح أخطاء Debian اللي ظهرت عندك بالتفصيل.

# Tab 4

تمام يا إسلام.  
 بدل ما نكمل Handbook 5 و 6 و 7 منفصلين، هعملك **آخر Handbook جامع** فيه كل اللي فاضل بشكل مرتب:

# **Final Handbook — Python venv \+ pip داخل Docker \+ تشغيل Full Stack \+ Optimization**

## **الهاند بوك النهائي لتقفيل السلسلة**

الهاند بوك ده هيغطي 3 أجزاء كبار:

1. **Python Virtual Environment و pip داخل Docker**  
2. **تشغيل المشروع كاملًا: Backend \+ Frontend \+ Ports \+ Logs**  
3. **Optimization وتسريع Docker Builds وتريكات Senior**

---

# **Part 1 — Python venv و pip داخل Docker**

## **1\. السطر الأساسي عندك**

في Dockerfile عندك:

```dockerfile
RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

```
ده من أهم أسطر الـ backend image.

خلينا نفهمه جزء جزء.

---

# **2\. يعني إيه Virtual Environment؟**

الـ virtual environment أو `venv` هي بيئة Python مستقلة.

بدل ما تثبت packages في Python النظام، بتثبتها في فولدر مخصوص.

في مشروعك:

```text
/opt/venv

```
يعني كل packages زي:

```text
flask  
gunicorn  
pandas  
numpy  
pymupdf  
requests

```
اتثبتت داخل:

```text
/opt/venv/lib/python3.13/site-packages

```
---

## **ليه نعمل venv داخل Docker؟**

سؤال مهم جدًا.

ممكن تقول:

Docker أصلًا isolated، ليه أعمل venv جوه Docker؟

الإجابة: مش إجباري، لكنه مفيد جدًا.

### **فوائد venv داخل Docker**

1. يفصل Python packages الخاصة بالتطبيق عن Python النظام.  
2. يقلل مشاكل Debian system packages.  
3. يخلي `gunicorn` و `flask` واضحين إنهم تابعين للتطبيق.  
4. يسهل التحكم في PATH.  
5. يخلي البيئة أنضف وأكثر قابلية للتوقع.

---

## **تبسيط سينيور**

Docker هو شقة مستقلة.  
 والـ venv هو أوضة مستقلة جوه الشقة.

ممكن تعيش في الشقة كلها، لكن الأوضة بتخلي حاجتك مترتبة ومفصولة.

---

# **3\. شرح الأمر**

```text
python3.13 -m venv /opt/venv

```
## **`python3.13`**

استخدم Python 3.13.

في حالتك كان موجود أصلًا داخل image:

```text
texlive/texlive:latest

```
---

## **`-m`**

معناها:

شغل module من Python.

---

## **`venv`**

دي module مسؤولة عن إنشاء virtual environments.

---

## **`/opt/venv`**

ده المكان اللي هيتم فيه إنشاء البيئة.

بعد الأمر ده، هيظهر فولدر:

```text
/opt/venv

```
جواه:

```text
bin/  
lib/  
pyvenv.cfg

```
أهم فولدر:

```text
/opt/venv/bin

```
لأنه يحتوي على:

```bash
python  
pip  
gunicorn  
flask  
```
---

# **4\. لماذا احتجنا `python3.13-venv`؟**

لأن الأمر:

```text
python3.13 -m venv /opt/venv

```
محتاج package من Debian اسمها:

```text
python3.13-venv

```
بدونها ممكن يظهر error متعلق بـ:

```text
ensurepip

```
أو إن `venv` غير متاح بشكل كامل.

وده سبب إننا ثبتنا:

```text
python3.13-venv

```
---

# **5\. شرح pip upgrade**

```text
/opt/venv/bin/pip install --upgrade pip setuptools wheel

```
## **ليه كتبنا `/opt/venv/bin/pip`؟**

عشان نضمن إننا بنستخدم pip اللي جوه venv، مش pip النظام.

لو كتبت:

```text
pip install ...

```
قبل ما نعدل PATH، ممكن النظام يستخدم pip تاني.

لكن:

```text
/opt/venv/bin/pip

```
واضحة وصريحة.

---

## **packages دي إيه؟**

### **`pip`**

مدير تثبيت Python packages.

---

### **`setuptools`**

أداة مهمة لبناء وتثبيت بعض Python packages.

---

### **`wheel`**

صيغة جاهزة لـ Python packages.

لما package تكون wheel، تثبيتها أسرع لأنها مش بتحتاج build من source.

---

## **ليه نحدثهم؟**

لأن بعض packages الكبيرة أو الحديثة تحتاج tooling حديث.

خصوصًا packages زي:

```text
numpy  
pandas  
pymupdf  
```
---

# **6\. شرح install requirements**

```text
/opt/venv/bin/pip install --no-cache-dir -r requirements.txt

```
## **`-r requirements.txt`**

معناها:

ثبت كل packages الموجودة في ملف requirements.txt.

مثال:

```text
flask==3.1.3  
gunicorn==23.0.0  
numpy==2.4.4  
pandas==3.0.3  
pymupdf==1.27.2.3  
```
---

## **`--no-cache-dir`**

بتقول لـ pip:

متخزنش download cache داخل image.

ده يقلل حجم image.

---

## **هل `--no-cache-dir` دايمًا أحسن؟**

في Docker images غالبًا نعم لتقليل الحجم.

لكن في حالتك الإنترنت بطيء، فممكن في optimization نستخدم Docker BuildKit cache mount بدل pip cache العادي.

---

# **7\. لماذا `pip install` كان بطيء؟**

في logs عندك ظهرت packages كبيرة:

```text
numpy 15.7 MB  
pandas 10.4 MB  
pymupdf 24.3 MB

```
والتحميل كان بسرعات قليلة جدًا:

```text
35 kB/s  
200 kB/s

```
فطبيعي خطوة pip تأخذ وقت طويل.

---

# **8\. أهمية ترتيب Dockerfile مع pip**

الترتيب الحالي ممتاز:

```text
COPY requirements.txt .

RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . .

```
ليه؟

لأن Docker هيعمل cache لخطوة pip طالما `requirements.txt` متغيرش.

لو غيرت كود Python فقط، Docker مش هيعيد pip install.

---

## **الغلط الشائع**

```text
COPY . .

RUN pip install -r requirements.txt

```
ده وحش لأن أي تغيير في أي ملف يكسر cache ويعيد pip install.

---

# **9\. ENV PATH**

```dockerfile
ENV PATH="/opt/venv/bin:$PATH"

```
ده معناه:

لما container يدور على أوامر زي `python`, `pip`, `gunicorn`، يدور الأول داخل `/opt/venv/bin`.

بعد السطر ده، داخل container:

```text
which python

```
المفروض يطلع:

```text
/opt/venv/bin/python

```
و:

```text
which gunicorn

```
يطلع:

```text
/opt/venv/bin/gunicorn

```
---

# **10\. اختبارات مهمة داخل backend container**

بعد تشغيل المشروع:

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
نفذ:

```bash
python --version  
which python  
pip --version  
which pip  
which gunicorn  
gunicorn --version

```
المفروض تشوف إنهم من:

```text
/opt/venv

```
---

# **Part 2 — تشغيل المشروع كاملًا وفهم Frontend/Backend**

## **11\. عندك خدمتين رئيسيتين**

من:

```bash
docker compose ps

```
ظهر:

```text
backend	0.0.0.0:5010-\>5000/tcp  
frontend   0.0.0.0:5173-\>5173/tcp

```
يعني:

```text
Backend  \= localhost:5010  
Frontend \= localhost:5173  
```
---

# **12\. Backend هو إيه؟**

الـ backend عندك Python Flask app شغال بـ Gunicorn.

في logs ظهر:

```text
Starting gunicorn 23.0.0  
Listening at: [http://0.0.0.0:5000](http://0.0.0.0:5000/)  
Booting worker with pid: 7

```
ده معناه:

* Gunicorn اشتغل.  
* التطبيق بيسمع داخل container على port `5000`.  
* Docker عامل mapping من جهازك:

```text
localhost:5010 -\> container:5000

```
---

# **13\. Frontend هو إيه؟**

الـ frontend container شغال غالبًا بـ Nginx أو Vite setup حسب compose.

في logs ظهر:

```text
nginx/1.27.5  
Configuration complete; ready for start up

```
ومن `docker compose ps`:

```text
0.0.0.0:5173-\>5173/tcp

```
إذًا تدخل على:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

# **14\. لماذا `localhost:5010/session-authoring-tool/` قال Frontend bundle not found؟**

لأن `5010` هو backend.

أنت فتحت backend route، والbackend قال:

```text
Frontend bundle not found

```
معناها:

أنا backend، ومتوقع ألاقي ملفات frontend مبنية في `frontend/dist/`، لكنها مش عندي.

لكن عندك frontend container منفصل، فالرابط الصح كان:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

## **تبسيط سينيور**

عندك بابين:

```text
5010 \= API / Backend  
5173 \= UI / Frontend

```
لو دخلت من باب API وطلبت الصفحة، السيرفر يقولك:

أنا مش صفحة، أنا backend.

---

# **15\. أهم أمر لمعرفة الرابط الصح**

```bash
docker compose ps

```
دايمًا اقرأ `PORTS`.

مثال:

```text
0.0.0.0:5010-\>5000/tcp

```
يعني افتح من المتصفح:

```text
localhost:5010

```
والتطبيق داخل container على:

```text
5000

```
---

# **16\. أوامر تشغيل يومية**

## **تشغيل المشروع**

```bash
docker compose up

```
---

## **تشغيل في الخلفية**

```bash
docker compose up -d

```
---

## **معرفة الحالة**

```bash
docker compose ps

```
---

## **عرض logs**

```bash
docker compose logs

```
---

## **متابعة logs live**

```bash
docker compose logs -f

```
---

## **Logs للـ backend فقط**

```bash
docker compose logs -f backend

```
---

## **Logs للـ frontend فقط**

```bash
docker compose logs -f frontend

```
---

## **إيقاف المشروع**

```bash
docker compose down

```
---

# **17\. متى تستخدم build؟**

لو غيرت Dockerfile:

```bash
docker compose build backend  
docker compose up

```
لو غيرت requirements.txt:

```bash
docker compose build backend  
docker compose up

```
لو غيرت frontend package.json:

```bash
docker compose build frontend  
docker compose up

```
لو غيرت كود فقط، حسب هل عندك volumes/watch ولا لأ. لو مفيش volumes، اعمل build للخدمة المعنية.

---

# **18\. تجنب `--no-cache`**

ده مهم جدًا في حالتك.

لا تستخدم:

```bash
docker compose build --no-cache

```
إلا لو مضطر.

لأن ده هيعيد:

* `apt-get update`  
* `apt-get install`  
* `pip install`  
* `npm ci`

وانت عندك الإنترنت كان بطيء جدًا.

استخدم بدلًا منه:

```bash
docker compose build backend

```
أو:

```bash
docker compose build frontend

```
---

# **19\. Healthcheck**

ظهر عندك:

```text
Container session-authoring-tool-backend-1 Healthy

```
ده معناه إن `docker-compose.yml` فيه غالبًا healthcheck للـ backend.

الـ healthcheck وظيفته يتأكد إن الخدمة شغالة فعلًا، مش مجرد container قام.

مثلاً ممكن يكون بيفحص endpoint زي:

```text
/health

```
أو يعمل curl داخلي.

---

# **20\. Debug لو backend وقع**

شوف logs:

```bash
docker compose logs backend --tail=100

```
أو live:

```bash
docker compose logs -f backend

```
ادخل container:

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
اختبر:

```bash
python --version  
which gunicorn  
python -c "from app import app; print(app)"  
```
---

# **21\. Debug لو frontend وقع**

شوف logs:

```bash
docker compose logs frontend --tail=100

```
اختبر الرابط:

```text
curl -I [http://localhost:5173/](http://localhost:5173/)

```
أو:

```text
curl -I [http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

# **22\. Debug لو frontend شغال لكن API مش شغال**

اختبر backend:

```text
curl -I [http://localhost:5010](http://localhost:5010/)

```
أو لو عندك health endpoint:

```text
curl [http://localhost:5010/health](http://localhost:5010/health)

```
لو frontend بيحاول يكلم API باسم غلط، راجع env vars في frontend أو nginx template.

---

# **Part 3 — Optimization و Senior Tricks**

## **23\. المشكلة الأكبر عندك كانت البطء**

شفنا:

```text
Fetched 9942 kB in 13min 19s

```
و pip كان بينزل ببطء.

فأهم هدف بعد التشغيل:

إزاي مانعيدش التحميل ده كل مرة؟

---

# **24\. استخدم Docker cache صح**

أهم قاعدة:

حط الملفات الأقل تغييرًا فوق  
وحط الملفات الأكثر تغييرًا تحت

مثلاً:

```text
COPY requirements.txt .  
RUN pip install -r requirements.txt  
COPY . .

```
ده ممتاز.

لأن الكود بيتغير كتير، لكن requirements أقل تغييرًا.

---

# **25\. اعمل Base Image محلية**

عشان `apt-get install` كان بطيء، اعمل image فيها system dependencies مرة واحدة.

## **`backend/Dockerfile.base`**

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

RUN set -eux; \</span\>  
	printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries; \</span\>  
	find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +; \</span\>  
	apt-get update; \</span\>  
	apt-get install -y --no-install-recommends \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils; \</span\>  
	rm -rf /var/lib/apt/lists/\*

```
ابنيها:

```bash
docker build -f backend/Dockerfile.base -t sat-texlive-python-base:local backend

```
ثم Dockerfile الأساسي يبدأ بـ:

```dockerfile
FROM sat-texlive-python-base:local

```
ويشيل خطوة apt الطويلة.

---

# **26\. Dockerfile بعد استخدام base image**

```dockerfile
FROM sat-texlive-python-base:local

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

COPY requirements.txt .

RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PATH="/opt/venv/bin:$PATH"

EXPOSE 5000

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده هيخلي build أسرع لأن apt step اتعملت مرة واحدة.

---

# **27\. `.dockerignore` مهم جدًا**

اعمل أو راجع ملف:

```text
backend/.dockerignore

```
وخليه مثلًا:

```text
**pycache**/  
*.pyc*  
.pyo  
*.pyd*

*.pytest_cache/*  
*.coverage*  
*htmlcov/*

*.venv/*  
*venv/*  
*env/*

*.env*  
*.env.*

.git/  
.gitignore

.DS_Store

node_modules/  
dist/  
build/

\*.log

```
لو فيه ملفات كبيرة مش محتاجها في image، ضيفها.

---

## **ليه `.dockerignore` مهم؟**

لأن Docker بيبعت build context للـ daemon.

لو context كبير، build يبقى أبطأ.

---

# **28\. تثبيت versions في requirements**

وجود versions pinned زي:

```text
flask==3.1.3  
gunicorn==23.0.0  
pandas==3.0.3

```
مفيد لأنه يخلي build قابل للتكرار.

لكن خلي بالك:

* versions الحديثة جدًا ممكن لا تكون مدعومة على كل Python versions.  
* Python 3.13 يحتاج wheels متوافقة.  
* على ARM64 لازم packages يكون لها wheels مناسبة.

---

# **29\. Python 3.13 مميزات ومخاطر**

أنت استخدمت:

```text
Python 3.13

```
وده اشتغل عندك.

لكن في مشاريع production أحيانًا Python 3.11 أو 3.12 يكون أكثر استقرارًا من ناحية packages.

لو package معينة فشلت مستقبلًا مع 3.13، ممكن تفكر تستخدم base image بـ Python 3.11/3.12 أو تضبط requirements.

---

# **30\. لا تعتمد على `latest` في production**

عندك:

```dockerfile
FROM texlive/texlive:latest

```
ده كويس للتطوير، لكنه ممكن يتغير.

الأفضل في production تثبت tag أو digest.

مثلاً logs عندك كانت فيها digest:

```text
sha256:2f9918a156cfc6c7527590e06f69bfb4fbc827278e4736c90e567382855d7cd1

```
فممكن تستخدم:

```dockerfile
FROM texlive/texlive:latest@sha256:2f9918a156cfc6c7527590e06f69bfb4fbc827278e4736c90e567382855d7cd1

```
ده يخلي الصورة ثابتة جدًا.

---

# **31\. Gunicorn workers**

حاليًا:

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده غالبًا worker واحد.

ممكن تضيف workers:

```text
ENTRYPOINT ["gunicorn", "--workers", "2", "--bind", "0.0.0.0:5000", "app:app"]

```
لكن لا تزود عشوائيًا.

قاعدة تقريبية:

```text
workers \= 2 \* CPU cores + 1

```
لكن داخل Docker Desktop وبيئة development، 1 أو 2 كفاية.

---

# **32\. ENTRYPOINT vs CMD**

حاليًا:

```text
ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

```
ده مقبول.

نسخة أكثر مرونة:

```text
ENTRYPOINT ["gunicorn"]  
CMD ["--bind", "0.0.0.0:5000", "app:app"]

```
الميزة إنك تقدر تغير arguments بسهولة من compose.

لكن للمشروع الحالي، خليك على البسيط.

---

# **33\. تشغيل في الخلفية**

بدل ما التيرمينال يفضل ماسك logs:

```bash
docker compose up -d

```
ثم تابع logs:

```bash
docker compose logs -f

```
لإيقاف:

```bash
docker compose down

```
---

# **34\. أوامر تنظيف بحذر**

## **حذف containers stopped**

```bash
docker container prune

```
## **حذف networks غير مستخدمة**

```bash
docker network prune

```
## **حذف build cache**

```bash
docker builder prune

```
## **حذف أشياء كثيرة غير مستخدمة**

```bash
docker system prune

```
خلي بالك: ده ممكن يمسح images/cache غير مستخدمة ويخلي builds الجاية أبطأ.

---

# **35\. لا تستخدم `docker system prune` إلا لما تكون فاهم**

لأنك لو مسحت cache، هتعيد تحميل:

* Debian packages  
* Python packages  
* Node packages

وده عندك مكلف زمنيًا.

---

# **36\. Checklist تشغيل المشروع**

كل مرة تشغل:

```bash
docker compose up

```
ثم:

```bash
docker compose ps

```
تأكد من:

```text
backend healthy  
frontend up  
```
ports واضحة

افتح:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

# **37\. Checklist لو فيه مشكلة**

## **الصفحة مش بتفتح**

```bash
docker compose ps  
docker compose logs frontend --tail=100  
```
---

## **API مش شغال**

```bash
docker compose logs backend --tail=100  
curl -I [http://localhost:5010](http://localhost:5010/)  
```
---

## **Build بطيء**

اسأل:

* هل استخدمت `--no-cache`؟  
* هل `requirements.txt` اتغير؟  
* هل Dockerfile اتغير؟  
* هل cache اتمسح؟  
* هل الإنترنت بطيء؟

---

## **DNS رجعت تبوز**

```bash
docker run --rm busybox cat /etc/resolv.conf  
docker run --rm busybox nslookup deb.debian.org  
scutil --dns | grep nameserver

```
لو DNS الشبكة تغير، عدل Docker Engine DNS.

---

# **38\. أفضل نسخة Dockerfile حاليًا**

```dockerfile
FROM texlive/texlive:latest

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /app

RUN set -eux; \</span\>  
	printf 'Acquire::Retries "5";\\nAcquire::http::Timeout "180";\\nAcquire::https::Timeout "180";\\n' \> /etc/apt/apt.conf.d/80-retries; \</span\>  
	find /etc/apt -type f −name"∗.list"−o−name"∗.sources"-name "\*.list" -o -name "\*.sources" -exec sed -i 's|[http://deb.debian.org|https://deb.debian.org|g](http://deb.debian.org%7Chttps//deb.debian.org%7Cg)' {} +; \</span\>  
	apt-get update; \</span\>  
	apt-get install -y --no-install-recommends \</span\>  
   	python3.13-venv \</span\>  
   	curl \</span\>  
   	poppler-utils; \</span\>  
	rm -rf /var/lib/apt/lists/\*

COPY requirements.txt .

RUN python3.13 -m venv /opt/venv \</span\>  
	&& /opt/venv/bin/pip install --upgrade pip setuptools wheel \</span\>  
	&& /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PATH="/opt/venv/bin:$PATH"

EXPOSE 5000

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]  
```
---

# **39\. Cheatsheet نهائي**

## **تشغيل**

```bash
docker compose up

```
## **تشغيل في الخلفية**

```bash
docker compose up -d

```
## **إيقاف**

```bash
docker compose down

```
## **الحالة**

```bash
docker compose ps

```
## **logs**

```bash
docker compose logs -f

```
## **backend logs**

```bash
docker compose logs -f backend

```
## **frontend logs**

```bash
docker compose logs -f frontend

```
## **build backend**

```bash
docker compose build backend

```
## **build frontend**

```bash
docker compose build frontend

```
## **build مع logs واضحة**

```bash
docker compose --progress=plain build backend

```
## **تجنب إلا للضرورة**

```bash
docker compose build --no-cache

```
## **دخول backend**

```bash
docker exec -it session-authoring-tool-backend-1 bash

```
## **اختبار Python**

```bash
python --version  
which python

```
## **اختبار Gunicorn**

```text
which gunicorn  
gunicorn --version

```
## **اختبار PDF tools**

```text
pdfinfo -v  
pdftotext -v

```
## **اختبار DNS**

```bash
docker run --rm busybox nslookup deb.debian.org

```
## **اختبار apt**

```bash
docker run --rm debian:stable-slim bash -lc "apt-get update"

```
---

# **40\. ملخص الرحلة كلها**

إحنا بدأنا بمشكلة:

```text
Temporary failure resolving 'deb.debian.org'

```
ودي كانت DNS.

اكتشفنا إن Docker كان بيستخدم:

```text
8.8.8.8  
8.8.4.4

```
لكن جهازك بيستخدم:

```text
172.23.100.226  
172.23.100.225

```
فعدلنا Docker Engine DNS.

بعد كده ظهر:

```text
Unable to connect to deb.debian.org:http

```
ودي كانت HTTP timeout.

فعدلنا Dockerfile لاستخدام HTTPS وزودنا timeouts/retries.

بعد كده ثبتنا:

```text
python3.13-venv  
poppler-utils

```
وبعدين pip ثبت:

```text
flask  
gunicorn  
numpy  
pandas  
pymupdf

```
وفي الآخر شغلنا:

```bash
docker compose up

```
والـ backend اشتغل على:

```text
localhost:5010

```
والـ frontend اشتغل على:

```text
localhost:5173

```
والرابط الصحيح كان:

```text
[http://localhost:5173/session-authoring-tool/](http://localhost:5173/session-authoring-tool/)

```
---

# **تبسيط سينيور نهائي**

لو هتلخص كل اللي اتعلمته في قاعدة واحدة:

لما Docker يقع، متعدّلش عشوائي.  
 افصل المشكلة: DNS؟ Network؟ apt؟ pip؟ ports؟ frontend/backend؟

والترتيب الصح للتشخيص:

```bash
1\. docker compose ps  
2\. logs  
3\. DNS test  
4\. apt test  
5\. Dockerfile layer  
6\. pip install  
7\. ports  
8\. browser URL

```
أنت كده عديت رحلة debugging حقيقية جدًا، وفيها شغل قريب من اللي بيحصل في production فعلًا.

