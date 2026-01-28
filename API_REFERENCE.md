<div dir="rtl" align="right">

<p align="center">
  <img src="https://img.shields.io/badge/📡_مرجع_API-Mazzady-orange?style=for-the-badge" alt="API Reference"/>
</p>

<h1 align="center">🔌 مرجع الـ API الكامل</h1>

---

## 📋 معلومات عامة

### Base URL

```
http://localhost:3000
```

### Headers المطلوبة

```http
Content-Type: application/json
Authorization: Bearer <token>  # للـ endpoints المحمية
```

### رموز الحالة

| الكود | المعنى         |
| ----- | -------------- |
| 200   | نجاح           |
| 201   | تم الإنشاء     |
| 400   | طلب غير صالح   |
| 401   | غير مصرح       |
| 403   | ممنوع          |
| 404   | غير موجود      |
| 500   | خطأ في السيرفر |

---

## 🔐 Authentication

### تسجيل مستخدم جديد

```http
POST /auth/register
```

**Body:**

```json
{
  "email": "user@example.com",
  "password": "Password123",
  "firstName": "أحمد",
  "middleName": "محمد",
  "lastName": "علي",
  "nickname": "ahmed_ali",
  "phone": "01012345678",
  "nationalId": "29001011234567"
}
```

**Response (201):**

```json
{
  "message": "User registered successfully",
  "user": {
    "_id": "...",
    "email": "user@example.com",
    "nickname": "ahmed_ali"
  }
}
```

---

### تسجيل الدخول

```http
POST /auth/login
```

**Body:**

```json
{
  "email": "user@example.com",
  "password": "Password123",
  "rememberMe": true
}
```

**Response (200):**

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "_id": "...",
    "email": "user@example.com",
    "nickname": "ahmed_ali"
  }
}
```

---

### إرسال كود التحقق

```http
POST /auth/send-verification-code
```

**Body:**

```json
{
  "email": "user@example.com"
}
```

---

### التحقق من الكود

```http
POST /auth/verify-email-code
```

**Body:**

```json
{
  "email": "user@example.com",
  "code": "123456"
}
```

---

### التحقق من توفر البيانات

```http
GET /auth/check-user?email=user@example.com
GET /auth/check-nickname?nickname=ahmed_ali
GET /auth/check-phone?phone=01012345678
GET /auth/check-national-id?nationalId=29001011234567
```

**Response:**

```json
{
  "exists": false
}
```

---

## 🏆 Auctions (المزادات)

### جلب جميع المزادات

```http
GET /auctions
GET /auctions?status=active
GET /auctions?category=electronics
GET /auctions?page=1&limit=10
```

**Response:**

```json
{
  "auctions": [
    {
      "_id": "...",
      "productName": "iPhone 15 Pro",
      "startingPrice": 15000,
      "highestBid": 16500,
      "status": "active",
      "endDate": "2026-02-01T10:00:00.000Z",
      "sellerId": { "nickname": "seller1" }
    }
  ],
  "total": 100,
  "page": 1,
  "pages": 10
}
```

---

### جلب مزاد واحد

```http
GET /auctions/:id
```

---

### إنشاء مزاد جديد

```http
POST /auctions
Content-Type: multipart/form-data
Authorization: Bearer <token>
```

**Form Data:**

```
productName: "iPhone 15 Pro"
startingPrice: 15000
minBidIncrement: 100
durationInSeconds: 86400
category: "electronics"
mainImage: <file>
additionalImages: <files[]>
isFeatured: false
```

---

### تعديل إعدادات المزاد (Admin)

```http
PUT /auctions/:id/settings
Authorization: Bearer <admin_token>
```

**Body:**

```json
{
  "productName": "iPhone 15 Pro Max",
  "minBidIncrement": 150,
  "durationInSeconds": 172800,
  "category": "electronics",
  "isFeatured": true
}
```

---

### تفعيل مزاد (Admin)

```http
PUT /auctions/:id/activate
Authorization: Bearer <admin_token>
```

---

### حذف مزاد (Admin)

```http
DELETE /auctions/:id
Authorization: Bearer <admin_token>
```

---

## 💰 Bids (المزايدات)

### تقديم مزايدة

```http
POST /bids
Authorization: Bearer <token>
```

**Body:**

```json
{
  "auctionId": "auction_id",
  "amount": 17000
}
```

---

### جلب مزايدات مزاد

```http
GET /bids/auction/:auctionId
```

---

### جلب مزايداتي

```http
GET /bids/my-bids
Authorization: Bearer <token>
```

---

## 🤖 Auto-Bid (المزايدة التلقائية)

### إنشاء مزايدة تلقائية

```http
POST /auto-bid
Authorization: Bearer <token>
```

**Body:**

```json
{
  "auctionId": "auction_id",
  "maxBidAmount": 20000,
  "bidIncrement": 500
}
```

---

### إلغاء مزايدة تلقائية

```http
DELETE /auto-bid/:auctionId
Authorization: Bearer <token>
```

---

## 💳 Wallet (المحفظة)

### جلب رصيد المحفظة

```http
GET /wallet/balance
Authorization: Bearer <token>
```

**Response:**

```json
{
  "balance": 1500.0,
  "pendingBalance": 200.0
}
```

---

### جلب المعاملات

```http
GET /wallet/transactions
Authorization: Bearer <token>
```

---

## 💵 Money Requests (طلبات المال)

### طلب إيداع

```http
POST /money-requests/deposit
Authorization: Bearer <token>
Content-Type: multipart/form-data
```

**Form Data:**

```
amount: 500
method: "instapay"
proofImage: <file>
```

---

### طلب سحب

```http
POST /money-requests/withdraw
Authorization: Bearer <token>
```

**Body:**

```json
{
  "amount": 200,
  "method": "instapay",
  "accountNumber": "01012345678"
}
```

---

### الموافقة على طلب (Admin)

```http
PUT /money-requests/:id/approve
Authorization: Bearer <admin_token>
```

---

### رفض طلب (Admin)

```http
PUT /money-requests/:id/reject
Authorization: Bearer <admin_token>
```

**Body:**

```json
{
  "reason": "صورة الإثبات غير واضحة"
}
```

---

## 👤 Users (المستخدمين)

### جلب البروفايل

```http
GET /users/profile
Authorization: Bearer <token>
```

---

### تحديث البروفايل

```http
PUT /users/profile
Authorization: Bearer <token>
```

**Body:**

```json
{
  "firstName": "أحمد",
  "bio": "مهتم بالتقنية"
}
```

---

### تحديث صورة البروفايل

```http
PUT /users/profile/image
Authorization: Bearer <token>
Content-Type: multipart/form-data
```

---

## 🪪 Identity Verification (التحقق من الهوية)

### رفع صور الهوية

```http
POST /identity-verification
Authorization: Bearer <token>
Content-Type: multipart/form-data
```

**Form Data:**

```
frontImage: <file>
backImage: <file>
```

---

### حالة التحقق

```http
GET /identity-verification/status
Authorization: Bearer <token>
```

---

### الموافقة على التحقق (Admin)

```http
PUT /identity-verification/:userId/approve
Authorization: Bearer <admin_token>
```

---

## 🔔 Notifications (الإشعارات)

### جلب الإشعارات

```http
GET /notifications
Authorization: Bearer <token>
```

---

### تحديد كمقروء

```http
PUT /notifications/:id/read
Authorization: Bearer <token>
```

---

### تحديد الكل كمقروء

```http
PUT /notifications/read-all
Authorization: Bearer <token>
```

---

## ⭐ Watchlist (قائمة المتابعة)

### إضافة للمتابعة

```http
POST /watchlist/:auctionId
Authorization: Bearer <token>
```

---

### إزالة من المتابعة

```http
DELETE /watchlist/:auctionId
Authorization: Bearer <token>
```

---

### جلب قائمة المتابعة

```http
GET /watchlist
Authorization: Bearer <token>
```

---

## 🛒 Cart (السلة)

### إضافة للسلة

```http
POST /cart
Authorization: Bearer <token>
```

**Body:**

```json
{
  "productId": "product_id",
  "quantity": 1
}
```

---

### جلب السلة

```http
GET /cart
Authorization: Bearer <token>
```

---

## 💬 Chat (المحادثات)

### إرسال رسالة

```http
POST /chat
Authorization: Bearer <token>
```

**Body:**

```json
{
  "receiverId": "user_id",
  "message": "مرحباً"
}
```

---

### جلب المحادثات

```http
GET /chat
Authorization: Bearer <token>
```

---

## ⭐ Ratings (التقييمات)

### تقييم بائع

```http
POST /ratings
Authorization: Bearer <token>
```

**Body:**

```json
{
  "sellerId": "seller_id",
  "rating": 5,
  "comment": "تجربة ممتازة"
}
```

---

## 👍 Seller Likes (إعجابات البائعين)

### إعجاب ببائع

```http
POST /seller-likes/:sellerId
Authorization: Bearer <token>
```

---

### إلغاء الإعجاب

```http
DELETE /seller-likes/:sellerId
Authorization: Bearer <token>
```

---

## 📊 User Statistics (إحصائيات المستخدم)

### جلب إحصائياتي

```http
GET /user-statistics
Authorization: Bearer <token>
```

**Response:**

```json
{
  "totalBids": 25,
  "wonAuctions": 5,
  "totalSpent": 15000,
  "totalEarned": 8000,
  "activeAuctions": 2
}
```

---

## 🎫 Customer Support (خدمة العملاء)

### إنشاء تذكرة

```http
POST /customer-support
Authorization: Bearer <token>
```

**Body:**

```json
{
  "subject": "مشكلة في الإيداع",
  "message": "لم يتم إضافة الرصيد",
  "priority": "high"
}
```

---

### جلب تذاكري

```http
GET /customer-support
Authorization: Bearer <token>
```

---

## 📝 Job Applications (طلبات التوظيف)

### تقديم طلب

```http
POST /job-applications
Content-Type: multipart/form-data
```

**Form Data:**

```
name: "أحمد محمد"
email: "ahmed@example.com"
phone: "01012345678"
position: "مطور Backend"
cv: <file>
```

---

## 👨‍💼 Admin (الإدارة)

### جلب جميع المستخدمين

```http
GET /admin/users
Authorization: Bearer <admin_token>
```

---

### حظر مستخدم

```http
PUT /admin/users/:id/ban
Authorization: Bearer <admin_token>
```

---

### إلغاء حظر مستخدم

```http
PUT /admin/users/:id/unban
Authorization: Bearer <admin_token>
```

---

### إرسال رسالة لمستخدم

```http
POST /admin-messages
Authorization: Bearer <admin_token>
```

**Body:**

```json
{
  "userId": "user_id",
  "subject": "تنبيه مهم",
  "message": "محتوى الرسالة"
}
```

---

## 🏠 Home (الصفحة الرئيسية)

### جلب بيانات الصفحة الرئيسية

```http
GET /home
```

**Response:**

```json
{
  "featuredAuctions": [...],
  "categories": [...],
  "stats": {
    "totalAuctions": 500,
    "totalUsers": 1200,
    "completedAuctions": 350
  }
}
```

---

## 📦 Shipping Tracking (تتبع الشحن)

### تحديث حالة الشحن

```http
PUT /shipping-tracking/:orderId
Authorization: Bearer <token>
```

**Body:**

```json
{
  "status": "shipped",
  "trackingNumber": "EG123456789"
}
```

---

### جلب حالة الشحن

```http
GET /shipping-tracking/:orderId
Authorization: Bearer <token>
```

</div>
