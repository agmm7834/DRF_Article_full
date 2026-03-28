# 🚀 Django REST Framework — Bosqichma-Bosqich Qo'llanma

---

## 📑 Mundarija

1. [API nima?](#1-api-nima)
2. [DRF nima va nega kerak?](#2-drf-nima)
3. [Loyiha tayyorlash](#3-loyiha-tayyorlash)
4. [Model yaratish](#4-model-yaratish)
5. [Serializer nima?](#5-serializer-nima)
6. [View nima?](#6-view-nima)
7. [URL sozlash](#7-url-sozlash)
8. [GenericView — kamroq kod, ko'proq natija](#8-genericview)
9. [ViewSet — eng samarali usul](#9-viewset)
10. [Authentication — kim ekanligingizni aniqlash](#10-authentication)
11. [Permission — ruxsat berish](#11-permission)
12. [Filtering va Search — qidirish](#12-filtering-va-search)
13. [Pagination — sahifalash](#13-pagination)
14. [Nested Serializer — bog'langan ma'lumotlar](#14-nested-serializer)
15. [File Upload — fayl yuklash](#15-file-upload)
16. [Custom Action — maxsus endpoint'lar](#16-custom-action)
17. [Testing — testlash](#17-testing)
18. [Swagger — API hujjati](#18-swagger)
19. [Best Practices — eng yaxshi amaliyotlar](#19-best-practices)
20. [Loyiha strukturasi](#20-loyiha-strukturasi)

---

## 1. API nima?

**API** (Application Programming Interface) — bu dasturlar orasidagi "aloqa tili".

### Hayotiy misol

Siz restoranga kirdingiz:
- Siz — **mijoz** (frontend, mobil ilova)
- Ofitsiant — **API** (so'rov qabul qiladi, javob qaytaradi)
- Oshpaz — **server** (ma'lumotlarni tayyorlaydi)

Siz ofitsiantga buyurtma berasiz → ofitsiant oshpazga yetkazadi → oshpaz tayyorlaydi → ofitsiant sizga olib keladi.

### Texnik tilda

**Frontend** (React, mobil ilova) serverga so'rov yuboradi, server javob qaytaradi. Bu aloqa HTTP protokoli orqali bo'ladi.

```
Frontend  →  HTTP so'rov  →  Server (API)  →  Database
Frontend  ←  JSON javob   ←  Server (API)  ←  Database
```

### HTTP Metodlar

API bilan ishlashda 5 ta asosiy metod bor:

| Metod | Nima qiladi | Misol |
|-------|-------------|-------|
| `GET` | Ma'lumot **olish** | Barcha mahsulotlar ro'yxatini ko'rish |
| `POST` | Yangi ma'lumot **yaratish** | Yangi mahsulot qo'shish |
| `PUT` | Ma'lumotni **to'liq yangilash** | Mahsulotning barcha ma'lumotini o'zgartirish |
| `PATCH` | Ma'lumotni **qisman yangilash** | Faqat narxini o'zgartirish |
| `DELETE` | Ma'lumotni **o'chirish** | Mahsulotni bazadan o'chirish |

### JSON nima?

API ma'lumotlarni **JSON** formatida yuboradi va qabul qiladi. JSON — bu oddiy matn formatidagi ma'lumot:

```json
{
    "id": 1,
    "name": "iPhone 15",
    "price": 12000000,
    "is_active": true
}
```

Python'dagi dictionary'ga juda o'xshaydi, to'g'rimi? 😊

---

## 2. DRF nima?

**Django REST Framework (DRF)** — bu Django ustiga qurilgan kutubxona. U sizga **API yaratishni juda osonlashtiradi**.

### DRF siz nima qila olasiz?

- 📱 Mobil ilovalar uchun backend
- 🌐 React/Vue/Angular frontend uchun API
- 🤖 Telegram bot uchun API
- 🔗 Boshqa dasturlar bilan ma'lumot almashish

### Nega oddiy Django emas, DRF kerak?

Oddiy Django **HTML sahifalar** qaytaradi. Lekin mobil ilova yoki React ilovaga HTML kerak emas — ularga **JSON ma'lumot** kerak. DRF aynan shu uchun yaratilgan.

```
Oddiy Django:    So'rov → View → HTML sahifa qaytaradi
DRF:             So'rov → View → JSON ma'lumot qaytaradi
```

---

## 3. Loyiha tayyorlash

### 3.1 Virtual environment (virtual muhit)

Virtual muhit — bu har bir loyihaga alohida Python paktetlarini o'rnatish imkonini beradi. Bu bilan loyihalar bir-biriga xalaqit bermaydi.

```bash
# 1. Papka yarating va unga kiring
mkdir my_project
cd my_project

# 2. Virtual muhit yarating
python -m venv venv

# 3. Aktivlashtiring (Windows)
venv\Scripts\activate

# Aktivlashganini bilish: terminal boshida (venv) yozuvi paydo bo'ladi
```

### 3.2 Paketlarni o'rnatish

```bash
pip install django               # Django o'zi
pip install djangorestframework   # DRF
```

### 3.3 Django loyiha yaratish

```bash
# Loyiha yaratish (config — sozlamalar papkasi nomi)
django-admin startproject config .

# App yaratish (products — ilovamiz nomi)
python manage.py startapp products
```

> 💡 `.` (nuqta) — loyihani HOZIRGI papkada yaratadi. Qo'ymasangiz qo'shimcha papka yaratiladi.

### 3.4 `settings.py` ga DRF ni qo'shish

`config/settings.py` faylini oching va `INSTALLED_APPS` ga qo'shing:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # DRF ni qo'shamiz
    'rest_framework',

    # Bizning ilovamiz
    'products',
]
```

### 3.5 DRF sozlamalari

`settings.py` ning eng oxiriga qo'shing:

```python
# DRF ning umumiy sozlamalari
REST_FRAMEWORK = {
    # Sahifalash (har bir sahifada nechta element bo'lishi)
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 20,
}
```

> Boshqa sozlamalarni keyinroq, kerak bo'lganda qo'shamiz.

---

## 4. Model yaratish

**Model** — bu database'dagi jadval (table). Har bir maydon (field) — jadvalning ustuni (column).

### 4.1 Oddiy model

`products/models.py` faylini oching:

```python
from django.db import models

class Product(models.Model):
    # CharField — qisqa matn (max_length majburiy)
    name = models.CharField(max_length=200, verbose_name="Nomi")

    # TextField — uzun matn (limit yo'q)
    description = models.TextField(blank=True, verbose_name="Tavsifi")
    # blank=True — bu maydon bo'sh bo'lishi mumkin

    # DecimalField — aniq kasrli son (narxlar uchun ideal)
    price = models.DecimalField(
        max_digits=12,       # Jami 12 ta raqam
        decimal_places=2,    # Verguldan keyin 2 ta
        verbose_name="Narxi"
    )

    # BooleanField — True yoki False
    is_active = models.BooleanField(default=True, verbose_name="Faolmi")

    # DateTimeField — sana va vaqt
    created_at = models.DateTimeField(auto_now_add=True, verbose_name="Yaratilgan")
    # auto_now_add=True — faqat YARATILGANDA avtomatik sana qo'yiladi

    updated_at = models.DateTimeField(auto_now=True, verbose_name="Yangilangan")
    # auto_now=True — har safar SAQLANGANDA avtomatik sana yangilanadi

    class Meta:
        verbose_name = "Mahsulot"
        verbose_name_plural = "Mahsulotlar"
        ordering = ['-created_at']  # Eng yangisi birinchi

    def __str__(self):
        return self.name  # Admin panelda ko'rinadigan nom
```

### 4.2 Bog'langan modellar (ForeignKey)

```python
class Category(models.Model):
    name = models.CharField(max_length=100)
    slug = models.SlugField(unique=True)
    # slug — URL'da ishlatiladigan nom: "elektronika", "kiyimlar"

    def __str__(self):
        return self.name


class Product(models.Model):
    name = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    price = models.DecimalField(max_digits=12, decimal_places=2)

    # ForeignKey — "Bu mahsulot qaysi kategoriyaga tegishli?"
    # on_delete=models.CASCADE — kategoriya o'chirilsa, mahsulotlar ham o'chadi
    # related_name='products' — kategoriyadan mahsulotlarga bog'lanish nomi
    category = models.ForeignKey(
        Category,
        on_delete=models.CASCADE,
        related_name='products'
    )

    is_active = models.BooleanField(default=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.name
```

### `related_name` nima?

`related_name='products'` — bu teskari bog'lanish uchun kerak:

```python
# Kategoriyaga tegishli barcha mahsulotlarni olish:
category = Category.objects.get(id=1)
category.products.all()  # <- related_name ishlatilmoqda

# Agar related_name bermasangiz, Django avtomatik qo'yadi:
# category.product_set.all()  # bu ham ishlaydi, lekin xunuk
```

### 4.3 Migratsiya (database'ga yozish)

```bash
# 1. Migratsiya fayllarini yaratish
python manage.py makemigrations

# 2. Database'ga qo'llash
python manage.py migrate

# 3. Admin foydalanuvchi yaratish
python manage.py createsuperuser
```

---

## 5. Serializer nima?

**Serializer** — DRF ning eng muhim tushunchasi. U **ikki vazifani** bajaradi:

1. **Serialization**: Python ob'ekt → JSON (ma'lumotni yuborish)
2. **Deserialization**: JSON → Python ob'ekt (ma'lumotni qabul qilish va tekshirish)

### Hayotiy misol

```
Serializer — bu TARJIMON.

Database'da ma'lumot bor (Python ob'ekt):
    Product(id=1, name="iPhone", price=12000000)

Frontend JSON'ni tushunadi:
    {"id": 1, "name": "iPhone", "price": 12000000}

Serializer bu ikkisi orasida tarjima qiladi!
```

### 5.1 Birinchi Serializer

`products/serializers.py` nomli YANGI fayl yarating:

```python
from rest_framework import serializers
from .models import Product, Category

class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product                   # Qaysi model bilan ishlaydi
        fields = ['id', 'name', 'price',  # Qaysi maydonlar JSON'da ko'rinadi
                  'description', 'category',
                  'is_active', 'created_at']
        read_only_fields = ['id', 'created_at']
        # read_only — faqat o'qish uchun, frontend o'zgartira olmaydi
```

**Bu serializer nima qiladi?**

```python
# 1) Database'dan ma'lumot oldi:
product = Product.objects.get(id=1)
# product = <Product: iPhone 15>

# 2) Serializer uni JSON ga aylantiradi:
serializer = ProductSerializer(product)
serializer.data
# {"id": 1, "name": "iPhone 15", "price": "12000000.00", ...}

# 3) Frontend'dan kelgan JSON'ni tekshiradi:
data = {"name": "Samsung S24", "price": 9000000, "category": 1}
serializer = ProductSerializer(data=data)
serializer.is_valid()   # True yoki False
serializer.save()       # Database'ga saqlaydi
```

### 5.2 `fields` tanlovlari

```python
class Meta:
    model = Product

    # 1-usul: Aniq ro'yxat (TAVSIYA ETILADI)
    fields = ['id', 'name', 'price']

    # 2-usul: Barcha maydonlar
    fields = '__all__'

    # 3-usul: Ayrimlarini chiqarib tashlash
    exclude = ['updated_at']   # updated_at DAN BOSHQA hammasi
```

> ⚠️ `fields = '__all__'` ishlatmaslik tavsiya etiladi — chunki keyinchalik sezgir (sensitive) maydon qo'shsangiz, u avtomatik API'ga chiqib ketadi.

### 5.3 Validatsiya (tekshirish)

Frontend'dan kelgan ma'lumotni tekshirish:

```python
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'description', 'category']

    # 1) Bitta maydonni tekshirish — metod nomi: validate_<maydon_nomi>
    def validate_price(self, value):
        """Narx 0 dan katta bo'lishi kerak"""
        if value <= 0:
            raise serializers.ValidationError("Narx musbat son bo'lishi kerak!")
        return value

    def validate_name(self, value):
        """Nom kamida 2 belgidan iborat bo'lishi kerak"""
        if len(value) < 2:
            raise serializers.ValidationError("Nom juda qisqa!")
        return value

    # 2) Bir nechta maydonni BIRGA tekshirish
    def validate(self, data):
        """Umumiy tekshirish"""
        if data.get('name') and 'test' in data['name'].lower():
            raise serializers.ValidationError("Nomda 'test' so'zi bo'lmasligi kerak!")
        return data
```

### 5.4 SerializerMethodField — qo'shimcha maydon hisoblash

Ba'zan database'da bo'lmagan, lekin **hisoblab chiqariladigan** maydonlar kerak:

```python
class ProductSerializer(serializers.ModelSerializer):
    # Bu maydon database'da yo'q — biz o'zimiz hisoblaymiz
    discount_price = serializers.SerializerMethodField()
    category_name = serializers.SerializerMethodField()

    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'discount_price', 'category_name']

    def get_discount_price(self, obj):
        """10% chegirmali narx. Metod nomi: get_<maydon_nomi>"""
        # obj — bu hozirgi Product ob'ekti
        return float(obj.price) * 0.9

    def get_category_name(self, obj):
        """Kategoriya nomini qaytarish"""
        return obj.category.name if obj.category else None
```

**Natija:**
```json
{
    "id": 1,
    "name": "iPhone 15",
    "price": "12000000.00",
    "discount_price": 10800000.0,
    "category_name": "Telefonlar"
}
```

### 5.5 Alohida Serializer'lar — ro'yxat va batafsil

Real loyihalarda ro'yxat uchun kamroq, batafsil ko'rish uchun ko'proq ma'lumot beriladi:

```python
# Ro'yxat uchun — tez, yengil
class ProductListSerializer(serializers.ModelSerializer):
    category_name = serializers.CharField(source='category.name', read_only=True)
    # source='category.name' — ForeignKey orqali boshqa modelning maydonini olish

    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'category_name']


# Batafsil ko'rish uchun — to'liq ma'lumot
class ProductDetailSerializer(serializers.ModelSerializer):
    category_name = serializers.CharField(source='category.name', read_only=True)

    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'description',
                  'category', 'category_name', 'is_active',
                  'created_at', 'updated_at']


# Yaratish/yangilash uchun
class ProductCreateSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['name', 'price', 'description', 'category']
```

---

## 6. View nima?

**View** — bu so'rovni qabul qilib, javob qaytaradigan funksiya yoki klass.

```
Frontend so'rov yuboradi → URL topiladi → VIEW ishlaydi → Javob qaytadi
```

### 6.1 Function-Based View (funksiya asosidagi)

Eng sodda usul — o'rganish uchun yaxshi:

```python
# products/views.py
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework import status
from .models import Product
from .serializers import ProductSerializer

@api_view(['GET', 'POST'])   # Qaysi HTTP metodlar ruxsat etilgan
def product_list(request):
    """
    GET  — barcha mahsulotlar ro'yxati
    POST — yangi mahsulot yaratish
    """

    if request.method == 'GET':
        # 1. Database'dan barcha mahsulotlarni olish
        products = Product.objects.all()

        # 2. Python ob'ektlarni JSON ga aylantirish
        #    many=True — ko'p ob'ekt (ro'yxat)
        serializer = ProductSerializer(products, many=True)

        # 3. JSON javob qaytarish
        return Response(serializer.data)

    elif request.method == 'POST':
        # 1. Frontend'dan kelgan ma'lumotni serializerga berish
        serializer = ProductSerializer(data=request.data)

        # 2. Ma'lumotni tekshirish (validatsiya)
        if serializer.is_valid():
            # 3. Database'ga saqlash
            serializer.save()
            # 4. Yaratilgan ob'ektni qaytarish (201 = Created)
            return Response(serializer.data, status=status.HTTP_201_CREATED)

        # Xatoliklar bo'lsa — 400 qaytarish
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'DELETE'])
def product_detail(request, pk):
    """
    Bitta mahsulot bilan ishlash.
    pk — primary key (ID raqami), URL'dan keladi
    """

    # Mahsulotni topish
    try:
        product = Product.objects.get(pk=pk)
    except Product.DoesNotExist:
        return Response(
            {'error': 'Mahsulot topilmadi!'},
            status=status.HTTP_404_NOT_FOUND
        )

    if request.method == 'GET':
        # Bitta mahsulotni qaytarish
        serializer = ProductSerializer(product)
        return Response(serializer.data)

    elif request.method == 'PUT':
        # Mahsulotni yangilash
        serializer = ProductSerializer(product, data=request.data)
        #                              ↑ eski    ↑ yangi ma'lumot
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        # Mahsulotni o'chirish
        product.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
        # 204 = No Content (muvaffaqiyatli o'chirildi, javob yo'q)
```

### 6.2 Class-Based APIView (klass asosidagi)

Xuddi shu narsa, lekin klass shaklida — kodingiz tartibli bo'ladi:

```python
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from .models import Product
from .serializers import ProductSerializer

class ProductListAPIView(APIView):
    """
    GET  /products/  — barcha mahsulotlar
    POST /products/  — yangi mahsulot
    """

    def get(self, request):
        products = Product.objects.all()
        serializer = ProductSerializer(products, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ProductSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class ProductDetailAPIView(APIView):
    """
    GET    /products/5/  — bitta mahsulot
    PUT    /products/5/  — yangilash
    DELETE /products/5/  — o'chirish
    """

    def get_object(self, pk):
        """Mahsulotni topish — bir joyda yozamiz, har yerda ishlatamiz"""
        try:
            return Product.objects.get(pk=pk)
        except Product.DoesNotExist:
            return None

    def get(self, request, pk):
        product = self.get_object(pk)
        if not product:
            return Response({'error': 'Topilmadi'}, status=404)
        serializer = ProductSerializer(product)
        return Response(serializer.data)

    def put(self, request, pk):
        product = self.get_object(pk)
        if not product:
            return Response({'error': 'Topilmadi'}, status=404)
        serializer = ProductSerializer(product, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=400)

    def delete(self, request, pk):
        product = self.get_object(pk)
        if not product:
            return Response({'error': 'Topilmadi'}, status=404)
        product.delete()
        return Response(status=204)
```

> Ko'rdingizmi? Har safar xuddi shu kodni yozyapmiz: "ob'ektni top, serializerga ber, javob qaytar". Bu takrorlanuvchi kodni kamaytirish uchun DRF **GenericView** lar yaratgan. 👇

---

## 7. URL sozlash

View'larni yaratdik — endi ularga **manzil** (URL) berishimiz kerak.

### 7.1 App URL'lari

`products/urls.py` nomli YANGI fayl yarating:

```python
# products/urls.py
from django.urls import path
from . import views

urlpatterns = [
    # Function-based view uchun:
    path('products/', views.product_list, name='product-list'),
    path('products/<int:pk>/', views.product_detail, name='product-detail'),
    #              ↑ bu URL'dan pk ni oladi
    #     /products/5/  →  pk = 5

    # YOKI Class-based view uchun:
    # path('products/', views.ProductListAPIView.as_view(), name='product-list'),
    # path('products/<int:pk>/', views.ProductDetailAPIView.as_view(), name='product-detail'),
]
```

### 7.2 Asosiy URL'lar

`config/urls.py` ni oching:

```python
# config/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),

    # products app ning URL'larini ulash
    path('api/v1/', include('products.urls')),
    # Endi URL'lar: /api/v1/products/ va /api/v1/products/5/
]
```

### 7.3 Sinab ko'rish

```bash
# Serverni ishga tushirish
python manage.py runserver

# Brauzerda oching:
# http://127.0.0.1:8000/api/v1/products/
# DRF ning chiroyli interfeysini ko'rasiz!
```

---

## 8. GenericView — kamroq kod, ko'proq natija

DRF da eng ko'p takrorlanadigan amallar uchun tayyor **Generic View'lar** bor:

| Generic View | Nima qiladi | HTTP Metod |
|-------------|-------------|------------|
| `ListAPIView` | Ro'yxat | GET |
| `CreateAPIView` | Yaratish | POST |
| `RetrieveAPIView` | Bitta olish | GET |
| `UpdateAPIView` | Yangilash | PUT, PATCH |
| `DestroyAPIView` | O'chirish | DELETE |
| `ListCreateAPIView` | Ro'yxat + Yaratish | GET, POST |
| `RetrieveUpdateDestroyAPIView` | Olish + Yangilash + O'chirish | GET, PUT, PATCH, DELETE |

### Misol

```python
from rest_framework import generics
from .models import Product
from .serializers import ProductListSerializer, ProductCreateSerializer, ProductDetailSerializer

class ProductListCreateView(generics.ListCreateAPIView):
    """
    GET  /products/  — ro'yxat
    POST /products/  — yaratish

    Shuncha yozish kifoya! DRF qolgan hammasini o'zi qiladi.
    """
    queryset = Product.objects.all()

    def get_serializer_class(self):
        # POST uchun boshqa serializer
        if self.request.method == 'POST':
            return ProductCreateSerializer
        # GET uchun boshqa serializer
        return ProductListSerializer


class ProductDetailView(generics.RetrieveUpdateDestroyAPIView):
    """
    GET    /products/5/  — bitta mahsulot (batafsil)
    PUT    /products/5/  — to'liq yangilash
    PATCH  /products/5/  — qisman yangilash
    DELETE /products/5/  — o'chirish

    Bu 4 ta amalni BIR klass qiladi!
    """
    queryset = Product.objects.all()
    serializer_class = ProductDetailSerializer
```

> Solishtirib ko'ring: **APIView** da 50+ qator yozdik, **GenericView** da 15 qator yetarli!

### `perform_create` — yaratishda qo'shimcha logika

```python
class ProductListCreateView(generics.ListCreateAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductCreateSerializer

    def perform_create(self, serializer):
        """
        Mahsulot yaratilganda avtomatik hozirgi
        foydalanuvchini owner sifatida belgilash
        """
        serializer.save(owner=self.request.user)
        # request.user — hozir tizimga kirgan foydalanuvchi
```

---

## 9. ViewSet — eng samarali usul

**ViewSet** — barcha CRUD amallarini **bitta klass** da birlashtiradi.

### CRUD nima?

| Harf | Ma'nosi | HTTP Metod |
|------|---------|------------|
| **C** | Create (Yaratish) | POST |
| **R** | Read (O'qish) | GET |
| **U** | Update (Yangilash) | PUT, PATCH |
| **D** | Delete (O'chirish) | DELETE |

### 9.1 ModelViewSet

```python
from rest_framework import viewsets
from .models import Product
from .serializers import (
    ProductListSerializer,
    ProductDetailSerializer,
    ProductCreateSerializer
)

class ProductViewSet(viewsets.ModelViewSet):
    """
    BARCHA CRUD amallari bir joyda!

    list          → GET    /products/         (ro'yxat)
    create        → POST   /products/         (yaratish)
    retrieve      → GET    /products/5/       (bitta olish)
    update        → PUT    /products/5/       (to'liq yangilash)
    partial_update → PATCH /products/5/       (qisman yangilash)
    destroy       → DELETE /products/5/       (o'chirish)
    """
    queryset = Product.objects.all()

    def get_serializer_class(self):
        """Har bir amal uchun mos serializer"""
        if self.action == 'list':
            return ProductListSerializer
        elif self.action in ['create', 'update', 'partial_update']:
            return ProductCreateSerializer
        return ProductDetailSerializer
        # self.action — hozirgi amal nomi: 'list', 'create', 'retrieve', ...

    def perform_create(self, serializer):
        serializer.save(owner=self.request.user)
```

### 9.2 ViewSet uchun URL — Router

ViewSet bilan **Router** ishlatiladi. Router avtomatik URL'lar yaratadi:

```python
# products/urls.py
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
# 'products' — URL prefiksi, ProductViewSet — view
router.register('products', views.ProductViewSet, basename='product')

# Router avtomatik yaratadi:
# /products/        → list, create
# /products/{id}/   → retrieve, update, partial_update, destroy

urlpatterns = [
    path('', include(router.urls)),
]
```

### Qaysi View turini tanlash kerak?

| Vaziyat | Tavsiya | Sababi |
|---------|---------|--------|
| To'liq CRUD kerak | `ModelViewSet` | Eng kam kod |
| Faqat ro'yxat + yaratish | `ListCreateAPIView` | 2 ta amal kifoya |
| Murakkab logika kerak | `APIView` | To'liq nazorat |
| Juda oddiy endpoint | `@api_view` funksiya | Eng sodda |

---

## 10. Authentication — kim ekanligingizni aniqlash

**Authentication** — bu "Siz kimsiz?" degan savolga javob beradi.

### 10.1 Custom User Model

> ⚠️ **MUHIM**: Har bir Django loyihada **birinchi ish** sifatida Custom User model yarating. Keyinchalik o'zgartirish juda qiyin!

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser
from django.db import models

class User(AbstractUser):
    """Django'ning standart User modeliga qo'shimcha maydonlar"""
    phone = models.CharField(max_length=15, blank=True, null=True, unique=True)
    avatar = models.ImageField(upload_to='avatars/', blank=True, null=True)
    bio = models.TextField(blank=True)

    def __str__(self):
        return self.username
```

```python
# settings.py — ALBATTA qo'shing!
AUTH_USER_MODEL = 'accounts.User'
# Bu Django'ga "User model sifatida accounts.User ni ishlatgin" deydi
```

### 10.2 JWT Authentication (eng mashhur)

**JWT** (JSON Web Token) — zamonaviy API'larda eng ko'p ishlatiladigan autentifikatsiya usuli.

```
Qanday ishlaydi:
1. Foydalanuvchi login + parol yuboradi
2. Server 2 ta token qaytaradi:
   - access token  (30 daqiqa ishlaydi — API so'rovlar uchun)
   - refresh token (7 kun ishlaydi — access tokenni yangilash uchun)
3. Har bir so'rovda access token yuboriladi
4. Access token muddati tugasa, refresh token bilan yangilanadi
```

O'rnatish:

```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
from datetime import timedelta

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=30),  # 30 daqiqa
    'REFRESH_TOKEN_LIFETIME': timedelta(days=7),      # 7 kun
    'ROTATE_REFRESH_TOKENS': True,
    # True — har safar refresh qilganda yangi refresh token ham beriladi
    'AUTH_HEADER_TYPES': ('Bearer',),
    # So'rovda "Authorization: Bearer <token>" shaklida yuboriladi
}
```

### 10.3 Register va Login

```python
# accounts/serializers.py
from rest_framework import serializers
from django.contrib.auth import get_user_model
from django.contrib.auth.password_validation import validate_password

User = get_user_model()
# get_user_model() — AUTH_USER_MODEL da ko'rsatilgan modelni oladi

class RegisterSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, validators=[validate_password])
    # write_only=True — parol JSON javobda KO'RINMAYDI (xavfsizlik!)
    password_confirm = serializers.CharField(write_only=True)

    class Meta:
        model = User
        fields = ['username', 'email', 'phone', 'password', 'password_confirm']

    def validate(self, data):
        if data['password'] != data['password_confirm']:
            raise serializers.ValidationError({
                'password_confirm': "Parollar bir xil emas!"
            })
        return data

    def create(self, validated_data):
        validated_data.pop('password_confirm')  # Bu maydon database'da yo'q
        user = User.objects.create_user(**validated_data)
        # create_user — parolni XESH qiladi (xavfsiz saqlaydi)
        # create — parolni ODDIY MATN sifatida saqlaydi (XAVFLI!)
        return user
```

```python
# accounts/views.py
from rest_framework import generics
from rest_framework.permissions import AllowAny

class RegisterView(generics.CreateAPIView):
    serializer_class = RegisterSerializer
    permission_classes = [AllowAny]  # Hamma ro'yxatdan o'ta oladi
```

```python
# accounts/urls.py
from django.urls import path
from rest_framework_simplejwt.views import (
    TokenObtainPairView,    # Login — access + refresh token olish
    TokenRefreshView,       # Access token yangilash
)
from . import views

urlpatterns = [
    path('register/', views.RegisterView.as_view(), name='register'),
    path('login/', TokenObtainPairView.as_view(), name='login'),
    path('token/refresh/', TokenRefreshView.as_view(), name='token-refresh'),
]
```

### 10.4 Frontend'da ishlatish

```javascript
// 1. Login
const loginResponse = await fetch('/api/v1/auth/login/', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username: 'ali', password: 'parol123' })
});
const tokens = await loginResponse.json();
// tokens = { access: "eyJhbG...", refresh: "eyJhbG..." }

// 2. API ga so'rov yuborish (access token bilan)
const products = await fetch('/api/v1/products/', {
    headers: {
        'Authorization': `Bearer ${tokens.access}`
        //                  ↑ "Bearer " so'zi kerak!
    }
});

// 3. Access token eskirganda yangilash
const refreshResponse = await fetch('/api/v1/auth/token/refresh/', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ refresh: tokens.refresh })
});
const newTokens = await refreshResponse.json();
// newTokens = { access: "yangi_token..." }
```

---

## 11. Permission — ruxsat berish

**Permission** — bu "Sizga ruxsat bormi?" degan savolga javob beradi.

```
Authentication: "Siz kimsiz?"  → Ali
Permission:     "Ali ga ruxsat bormi?" → Ha/Yo'q
```

### 11.1 DRF ning tayyor ruxsatlari

```python
from rest_framework.permissions import (
    AllowAny,                    # Hamma uchun (login bilan, loginsiz ham)
    IsAuthenticated,             # Faqat tizimga kirganlar
    IsAdminUser,                 # Faqat admin (is_staff=True)
    IsAuthenticatedOrReadOnly,   # Kirganlar yoza oladi, boshqalar faqat o'qiy oladi
)

class ProductViewSet(viewsets.ModelViewSet):
    # Variant 1: Hammaga ruxsat
    permission_classes = [AllowAny]

    # Variant 2: Faqat kirganlar
    permission_classes = [IsAuthenticated]

    # Variant 3: Har bir amal uchun alohida ruxsat
    def get_permissions(self):
        if self.action in ['list', 'retrieve']:
            return [AllowAny()]           # Ko'rish — hamma uchun
        return [IsAuthenticated()]         # Yaratish/yangilash — faqat kirganlar
```

### 11.2 Custom Permission (o'zimiz yozamiz)

```python
# core/permissions.py
from rest_framework.permissions import BasePermission, SAFE_METHODS

class IsOwnerOrReadOnly(BasePermission):
    """
    Ob'ekt egasi o'zgartira oladi.
    Boshqalar faqat ko'ra oladi.
    """

    message = "Faqat egasi o'zgartira oladi!"  # Xato xabari

    def has_object_permission(self, request, view, obj):
        # SAFE_METHODS = ('GET', 'HEAD', 'OPTIONS') — o'qish metodlari
        if request.method in SAFE_METHODS:
            return True   # O'qish — hamma uchun ruxsat

        # Yozish — faqat egasi uchun
        return obj.owner == request.user
```

```python
# View'da ishlatish:
class ProductViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthenticated, IsOwnerOrReadOnly]
```

---

## 12. Filtering va Search — qidirish

Foydalanuvchilarga ma'lumotlarni **filtrlash**, **qidirish** va **tartiblash** imkonini berish.

### 12.1 O'rnatish

```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'django_filters',
]

REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',   # Filtrlash
        'rest_framework.filters.SearchFilter',                  # Qidirish
        'rest_framework.filters.OrderingFilter',                # Tartiblash
    ],
}
```

### 12.2 Oddiy filtrlash

```python
class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer

    # Aniq qiymat bo'yicha filtrlash
    filterset_fields = ['category', 'is_active']
    # Endi ishlaydi: /products/?category=1&is_active=true

    # Matn bo'yicha qidirish
    search_fields = ['name', 'description']
    # Endi ishlaydi: /products/?search=telefon

    # Tartiblash
    ordering_fields = ['price', 'created_at', 'name']
    ordering = ['-created_at']  # Default tartib (eng yangi birinchi)
    # Endi ishlaydi: /products/?ordering=price (arzon → qimmat)
    #                /products/?ordering=-price (qimmat → arzon)
```

### 12.3 Murakkab filtrlash (FilterSet)

```python
# products/filters.py
import django_filters
from .models import Product

class ProductFilter(django_filters.FilterSet):
    # Narx oralig'i
    min_price = django_filters.NumberFilter(field_name='price', lookup_expr='gte')
    # gte = greater than or equal (kattaroq yoki teng)
    max_price = django_filters.NumberFilter(field_name='price', lookup_expr='lte')
    # lte = less than or equal (kichikroq yoki teng)

    # Kategoriya nomi bo'yicha (qisman mos kelsa ham topadi)
    category_name = django_filters.CharFilter(
        field_name='category__name',   # __ bilan bog'langan modelga kirish
        lookup_expr='icontains'        # icontains = katta-kichik harfga e'tibor bermaydi
    )

    class Meta:
        model = Product
        fields = ['category', 'is_active']
```

```python
# views.py da ishlatish
class ProductViewSet(viewsets.ModelViewSet):
    queryset = Product.objects.all()
    filterset_class = ProductFilter  # filterset_fields o'rniga
```

```
So'rov misollari:
/products/?min_price=10000&max_price=50000    — 10K-50K orasidagi narxlar
/products/?category_name=telefon               — "telefon" so'zi bor kategoriya
/products/?search=samsung&ordering=-price      — Samsung qidirish, qimmatdan arzon
```

---

## 13. Pagination — sahifalash

Agar database'da 10 000 ta mahsulot bo'lsa, hammasini birdaniga yuborish sekin bo'ladi. **Pagination** yordamida ma'lumotlarni sahifalarga bo'lamiz.

### 13.1 PageNumberPagination (eng oddiy)

```python
# core/pagination.py
from rest_framework.pagination import PageNumberPagination

class StandardPagination(PageNumberPagination):
    page_size = 20                   # Har sahifada 20 ta
    page_size_query_param = 'page_size'  # Frontend o'zi belgilashi mumkin
    max_page_size = 100              # Eng ko'pi 100 ta
```

```python
# settings.py da global o'rnatish YOKI view da alohida
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'core.pagination.StandardPagination',
    'PAGE_SIZE': 20,
}
```

**Javob formati:**
```json
{
    "count": 150,
    "next": "http://localhost:8000/api/v1/products/?page=2",
    "previous": null,
    "results": [
        {"id": 1, "name": "iPhone 15", "price": "12000000.00"},
        {"id": 2, "name": "Samsung S24", "price": "9000000.00"}
    ]
}
```

```
So'rov misollari:
/products/               — 1-sahifa (default)
/products/?page=3        — 3-sahifa
/products/?page_size=50  — har sahifada 50 ta
```

---

## 14. Nested Serializer — bog'langan ma'lumotlar

Ba'zan bir ob'ektning ichida **bog'langan ob'ektlarni** ham ko'rsatish kerak.

### 14.1 Oddiy (faqat ID ko'rsatadi)

```python
class ProductSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'category']  # category = faqat ID
```

```json
{
    "id": 1,
    "name": "iPhone 15",
    "price": "12000000.00",
    "category": 3
}
```

### 14.2 Nested (kategoriya ma'lumotlari bilan)

```python
class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug']


class ProductDetailSerializer(serializers.ModelSerializer):
    # ForeignKey ob'ektini to'liq ko'rsatish
    category = CategorySerializer(read_only=True)

    class Meta:
        model = Product
        fields = ['id', 'name', 'price', 'category']
```

```json
{
    "id": 1,
    "name": "iPhone 15",
    "price": "12000000.00",
    "category": {
        "id": 3,
        "name": "Telefonlar",
        "slug": "telefonlar"
    }
}
```

### 14.3 Teskari bog'lanish (kategoriyaning mahsulotlari)

```python
class CategoryWithProductsSerializer(serializers.ModelSerializer):
    # related_name='products' orqali bog'langan mahsulotlar
    products = ProductListSerializer(many=True, read_only=True)
    products_count = serializers.SerializerMethodField()

    class Meta:
        model = Category
        fields = ['id', 'name', 'slug', 'products', 'products_count']

    def get_products_count(self, obj):
        return obj.products.count()
```

```json
{
    "id": 3,
    "name": "Telefonlar",
    "slug": "telefonlar",
    "products_count": 2,
    "products": [
        {"id": 1, "name": "iPhone 15", "price": "12000000.00"},
        {"id": 5, "name": "Samsung S24", "price": "9000000.00"}
    ]
}
```

---

## 15. File Upload — fayl yuklash

### 15.1 Settings

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

```python
# config/urls.py (pastiga qo'shing)
from django.conf import settings
from django.conf.urls.static import static

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

### 15.2 Rasmli Serializer

```python
class ProductCreateSerializer(serializers.ModelSerializer):
    class Meta:
        model = Product
        fields = ['name', 'price', 'description', 'category', 'image']

    def validate_image(self, value):
        if value:
            if value.size > 5 * 1024 * 1024:           # 5MB limit
                raise serializers.ValidationError("Rasm 5MB dan katta bo'lmasin!")
            allowed = ['image/jpeg', 'image/png', 'image/webp']
            if value.content_type not in allowed:
                raise serializers.ValidationError("Faqat JPG, PNG, WebP!")
        return value
```

### 15.3 Frontend'dan yuklash

```javascript
const formData = new FormData();
formData.append('name', 'iPhone 15');
formData.append('price', 12000000);
formData.append('category', 3);
formData.append('image', fileInput.files[0]);  // <input type="file">

fetch('/api/v1/products/', {
    method: 'POST',
    headers: { 'Authorization': `Bearer ${token}` },
    // Content-Type qo'YMANG — FormData o'zi qo'yadi
    body: formData
});
```

---

## 16. Custom Action — maxsus endpoint'lar

CRUD'dan tashqari qo'shimcha endpoint'lar kerak bo'lsa, `@action` dekoratoridan foydalaning:

```python
from rest_framework.decorators import action

class ProductViewSet(viewsets.ModelViewSet):
    # ... asosiy sozlamalar

    @action(detail=False, methods=['get'], url_path='my-products')
    # detail=False — ro'yxat darajasida: /products/my-products/
    # detail=True  — ob'ekt darajasida:  /products/5/some-action/
    def my_products(self, request):
        """Faqat o'zim yaratgan mahsulotlar"""
        products = self.get_queryset().filter(owner=request.user)
        serializer = ProductListSerializer(products, many=True)
        return Response(serializer.data)

    @action(detail=True, methods=['post'])
    def toggle_active(self, request, pk=None):
        """Mahsulotni aktiv/noaktiv qilish: POST /products/5/toggle_active/"""
        product = self.get_object()
        product.is_active = not product.is_active
        product.save()
        status_text = "aktivlashtirildi" if product.is_active else "noaktiv qilindi"
        return Response({'message': f"Mahsulot {status_text}!"})
```

---

## 17. Testing — testlash

### API testlari yozish

```python
# products/tests.py
from rest_framework.test import APITestCase
from rest_framework import status
from django.urls import reverse
from django.contrib.auth import get_user_model
from .models import Product, Category

User = get_user_model()

class ProductAPITest(APITestCase):

    def setUp(self):
        """Har bir test OLDIDAN ishga tushadi — test uchun ma'lumot tayyorlaydi"""
        self.user = User.objects.create_user(username='testuser', password='test123')
        self.category = Category.objects.create(name='Test', slug='test')
        self.product = Product.objects.create(
            name='Test Mahsulot', price=100000,
            category=self.category, owner=self.user
        )
        self.client.force_authenticate(user=self.user)
        # force_authenticate — test uchun login qiladi

    def test_list_products(self):
        url = reverse('product-list')   # URL nomidan manzilni oladi
        response = self.client.get(url)
        self.assertEqual(response.status_code, status.HTTP_200_OK)

    def test_create_product(self):
        url = reverse('product-list')
        data = {'name': 'Yangi', 'price': 50000, 'category': self.category.id}
        response = self.client.post(url, data)
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(Product.objects.count(), 2)  # 1 + 1 yangi

    def test_unauthenticated_cannot_create(self):
        self.client.force_authenticate(user=None)  # Logout
        url = reverse('product-list')
        response = self.client.post(url, {'name': 'Test', 'price': 10000})
        self.assertEqual(response.status_code, status.HTTP_401_UNAUTHORIZED)
```

```bash
# Testlarni ishga tushirish
python manage.py test
python manage.py test products  # Faqat products app
```

---

## 18. Swagger — API hujjati

**Swagger** — API'ning interaktiv hujjatini yaratadi. Frontend dasturchilarga juda qulay!

### O'rnatish

```bash
pip install drf-yasg
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'drf_yasg',
]
```

```python
# config/urls.py
from drf_yasg.views import get_schema_view
from drf_yasg import openapi
from rest_framework import permissions

schema_view = get_schema_view(
    openapi.Info(
        title="My API",
        default_version='v1',
        description="Loyiha API hujjati",
    ),
    public=True,
    permission_classes=(permissions.AllowAny,),
)

urlpatterns = [
    # ...
    path('swagger/', schema_view.with_ui('swagger'), name='swagger'),
    path('redoc/', schema_view.with_ui('redoc'), name='redoc'),
]
```

Brauzerda `http://localhost:8000/swagger/` oching — chiroyli interaktiv hujjat tayyor!

---

## 19. Best Practices — eng yaxshi amaliyotlar

### 19.1 N+1 muammosi (ENG MUHIM!)

```python
# ❌ YOMON — har bir mahsulot uchun alohida SQL so'rov yuboriladi
products = Product.objects.all()
# 100 ta mahsulot = 1 (mahsulotlar) + 100 (kategoriya) = 101 ta SQL!

# ✅ YAXSHI — select_related bilan BIR SQL da olinadi
products = Product.objects.select_related('category').all()
# 100 ta mahsulot = 1 ta SQL!

# ViewSet'da ishlatish:
class ProductViewSet(viewsets.ModelViewSet):
    def get_queryset(self):
        return Product.objects.select_related('category')
        # select_related  — ForeignKey uchun (1 ga 1 yoki ko'p ga 1)
        # prefetch_related — ManyToMany va reverse FK uchun (1 ga ko'p)
```

### 19.2 CORS sozlash

Frontend va Backend alohida serverlarda bo'lganda CORS kerak:

```bash
pip install django-cors-headers
```

```python
# settings.py
INSTALLED_APPS = ['corsheaders', ...]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # ENG TEPAGA qo'ying!
    # ...
]

# Development uchun:
CORS_ALLOW_ALL_ORIGINS = True

# Production uchun:
CORS_ALLOWED_ORIGINS = [
    "https://mysite.com",
]
```

### 19.3 .env fayl — maxfiy ma'lumotlar

```bash
pip install python-decouple
```

```env
# .env fayl (settings.py bilan bir xil papkada)
SECRET_KEY=juda-maxfiy-kalit-12345
DEBUG=True
DATABASE_URL=postgres://user:pass@localhost/dbname
```

```python
# settings.py
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

### 19.4 Xulosa nasihatlar

1. ✅ **Har doim** Custom User model yarating (loyihaning BOSHIDA)
2. ✅ `select_related` / `prefetch_related` ishlating
3. ✅ Maxfiy kalitlarni `.env` faylda saqlang
4. ✅ Yozish va o'qish uchun alohida serializer'lar yarating
5. ✅ API versiyalash ishlating (`/api/v1/`, `/api/v2/`)
6. ✅ Testlar yozing
7. ❌ `fields = '__all__'` ishlatmang
8. ❌ `DEBUG = True` ni production'da QOLDIRMANG

---

## 20. Loyiha strukturasi

```
project/
├── config/               # Asosiy sozlamalar
│   ├── settings.py
│   ├── urls.py           # Asosiy URL'lar
│   ├── wsgi.py
│   └── asgi.py
│
├── core/                 # Umumiy kodlar (barcha applar ishlatadi)
│   ├── permissions.py    # Custom permission'lar
│   ├── pagination.py     # Custom pagination
│   └── exceptions.py     # Xatolarni boshqarish
│
├── accounts/             # Foydalanuvchilar bilan bog'liq
│   ├── models.py         # Custom User model
│   ├── serializers.py    # Register, Login, Profile serializer'lar
│   ├── views.py          # Auth view'lar
│   ├── urls.py
│   └── tests.py
│
├── products/             # Mahsulotlar (misol app)
│   ├── models.py         # Product, Category modellari
│   ├── serializers.py    # Mahsulot serializer'lari
│   ├── views.py          # ViewSet'lar
│   ├── urls.py           # Router
│   ├── filters.py        # Filtrlash
│   ├── admin.py          # Admin panel sozlamalari
│   └── tests.py
│
├── media/                # Foydalanuvchilar yuklagan fayllar
├── requirements.txt      # pip freeze > requirements.txt
├── .env                  # Maxfiy sozlamalar
├── .gitignore
└── manage.py
```

---

## 📋 DRF bilan ishlash ketma-ketligi

Yangi loyiha boshlayotganda shu tartibda ishlang:

```
1.  ✅ Virtual environment yaratish
2.  ✅ pip install django djangorestframework
3.  ✅ django-admin startproject config .
4.  ✅ Custom User model yaratish (BIRINCHI!)
5.  ✅ settings.py sozlash
6.  ✅ Modellar yaratish → makemigrations → migrate
7.  ✅ Serializer'lar yozish
8.  ✅ ViewSet'lar yaratish
9.  ✅ URL'lar sozlash (Router bilan)
10. ✅ Permission'lar qo'shish
11. ✅ Filter, Search, Ordering qo'shish
12. ✅ Swagger qo'shish
13. ✅ CORS sozlash
14. ✅ Test'lar yozish
```

---

