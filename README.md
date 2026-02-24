# 📘 Event Registration System
### Laravel + Inertia.js + React + Tailwind + Prisma

---

## ⚡ Quick Start (Dummy Mode)

**Mau langsung test UI tanpa setup database?**

```bash
composer install && npm install
cp .env.example .env && php artisan key:generate
npm run dev    # Terminal 1
php artisan serve # Terminal 2
```

**Buka:** `http://localhost:8000`

Semua controller sudah pakai **dummy data**. Perfect untuk testing UI!  
👉 Detail: [SETUP.md](SETUP.md)

---

## 🎯 Project Overview

Sistem manajemen event kampus berbasis web yang memungkinkan:

- ✅ **Admin** membuat dan mengelola event
- ✅ **Peserta** mendaftar event dengan mudah
- ✅ **Data peserta** tersimpan terstruktur di database
- ✅ **Monitoring & Export** data dalam format CSV/Excel
- ✅ **Dashboard Statistik** dengan visualisasi data
- ✅ **Role-based Access Control** (Admin & User)

---

## 🏗 Tech Stack

| Layer | Technology |
|-------|------------|
| **Backend** | Laravel 10+ |
| **Frontend** | React (via Inertia.js) |
| **Styling** | TailwindCSS |
| **ORM** | Prisma |
| **Database** | MySQL / PostgreSQL |
| **Auth** | Laravel Breeze (Inertia React) |

---

## ⚙️ System Architecture

```
Client (React + Tailwind)
        ↓
Inertia.js (Bridge)
        ↓
Laravel Controller
        ↓
Prisma ORM
        ↓
Database (MySQL/PostgreSQL)
```

**Laravel** tetap jadi HTTP handler dan routing.  
**Prisma** handle database layer dan queries.  
**React** render semua UI via Inertia.js.

---

## 🚀 Installation Guide

### 1️⃣ Prerequisites

- PHP 8.1+
- Composer
- Node.js 18+ & npm
- MySQL or PostgreSQL

### 2️⃣ Clone & Setup

```bash
# Clone repository (if applicable)
git clone <your-repo-url>
cd event-system

# Install PHP dependencies
composer install

# Install Node dependencies
npm install
```

### 3️⃣ Environment Configuration

```bash
# Copy environment file
cp .env.example .env

# Generate application key
php artisan key:generate
```

Edit `.env` file:

```env
APP_NAME="Event Registration System"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

# Database Configuration (Laravel)
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=event_system
DB_USERNAME=root
DB_PASSWORD=

# Database URL (Prisma)
DATABASE_URL="mysql://root:@127.0.0.1:3306/event_system"
```

**Note:** Pastikan `DB_*` dan `DATABASE_URL` sesuai dengan database Anda.

### 4️⃣ Database Setup

```bash
# Create database
mysql -u root -p
CREATE DATABASE event_system;
EXIT;

# Run Prisma migrations
npx prisma migrate dev --name init

# Generate Prisma Client
npx prisma generate
```

### 5️⃣ Build Assets

```bash
# Development
npm run dev

# Production
npm run build
```

### 6️⃣ Run Application

```bash
php artisan serve
```

Open browser: `http://localhost:8000`

---

## 🗄 Database Schema (Prisma)

### **User Model**
```prisma
model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  password  String
  role      Role     @default(USER)
  events    Event[]  @relation("EventCreator")
  createdAt DateTime @default(now())
}
```

### **Event Model**
```prisma
model Event {
  id          Int       @id @default(autoincrement())
  title       String
  description String    @db.Text
  date        DateTime
  location    String
  quota       Int
  poster      String?
  status      EventStatus @default(DRAFT)
  creator     User      @relation("EventCreator", fields: [creatorId], references: [id])
  creatorId   Int
  participants Participant[]
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

### **Participant Model**
```prisma
model Participant {
  id        Int      @id @default(autoincrement())
  name      String
  nim       String
  email     String
  phone     String
  jurusan   String?
  angkatan  String?
  status    ParticipantStatus @default(REGISTERED)
  event     Event    @relation(fields: [eventId], references: [id], onDelete: Cascade)
  eventId   Int
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  @@unique([eventId, email])
}
```

### **Enums**
```prisma
enum Role {
  ADMIN
  USER
}

enum EventStatus {
  DRAFT
  PUBLISHED
  CLOSED
}

enum ParticipantStatus {
  REGISTERED
  ATTENDED
  CANCELLED
}
```

---

## 🔐 Authentication & Roles

### **Role System**

1. **ADMIN**
   - Create, edit, delete events
   - View all participants
   - Export data
   - Mark attendance
   - Access dashboard statistics

2. **USER** (Mahasiswa/Peserta)
   - View published events
   - Register for events
   - View registration history

### **Creating Admin User**

```bash
php artisan tinker
```

```php
$user = User::create([
    'name' => 'Admin',
    'email' => 'admin@example.com',
    'password' => bcrypt('password'),
    'role' => 'admin'
]);
```

---

## 🎨 Frontend Structure

```
resources/js/
├── Pages/
│   ├── Dashboard/
│   │   ├── Admin.jsx       # Admin dashboard with stats
│   │   └── User.jsx        # User dashboard
│   ├── Events/
│   │   ├── Index.jsx       # Event listing
│   │   ├── Create.jsx      # Create event form
│   │   ├── Show.jsx        # Event detail & registration
│   │   └── Edit.jsx        # Edit event form
│   └── Participants/
│       └── Index.jsx       # Participant management
├── Components/
│   └── (shared components)
└── Layouts/
    └── AuthenticatedLayout.jsx
```

---

## 🛣 API Routes

### **Public Routes**
```php
GET  /events              # List all published events
GET  /events/{id}         # View event detail
```

### **Authenticated Routes**
```php
POST /events/{id}/register   # Register for event
```

### **Admin-Only Routes**
```php
GET    /events/create                 # Show create form
POST   /events                        # Store new event
GET    /events/{id}/edit              # Show edit form
PUT    /events/{id}                   # Update event
DELETE /events/{id}                   # Delete event
POST   /events/{id}/publish           # Publish event
POST   /events/{id}/close             # Close event

GET    /events/{id}/participants                  # List participants
GET    /events/{id}/participants/export           # Export CSV
POST   /events/{id}/participants/mark-attendance  # Mark attendance
PUT    /events/{id}/participants/{pId}/status    # Update status
```

---

## 📊 Dashboard Features

### **Admin Dashboard**
- 📈 Total Events
- 👥 Total Participants
- ✅ Active Events Count
- 📊 Registration Statistics (Chart)
- 🔔 Recent Registrations

### **User Dashboard**
- 📋 My Registered Events
- 🎫 Available Events
- ✅ Registration Status

---

## 🎯 Core Features

### ✅ Event Management
- Create event dengan poster upload
- Set quota & location
- Publish/Draft/Close status
- Edit & delete event

### ✅ Registration System
- Simple registration form
- Validation (no duplicate email per event)
- Auto-check quota availability
- Confirmation message

### ✅ Participant Management
- Search by name, NIM, email
- Filter by status (Registered/Attended/Cancelled)
- Bulk mark attendance
- Export to CSV

### ✅ Statistics & Monitoring
- Real-time participant count
- Registration trends
- Status breakdown
- Event activity tracking

---

## 🔧 Development Commands

```bash
# Run development server
php artisan serve
npm run dev

# Run Prisma Studio (Database GUI)
npx prisma studio

# Generate Prisma Client after schema changes
npx prisma generate

# Create new migration
npx prisma migrate dev --name migration_name

# Reset database (WARNING: deletes all data)
npx prisma migrate reset

# Run tests
php artisan test
```

---

## 📦 Deployment Guide

### **1. Environment Setup**

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com

DATABASE_URL="mysql://user:password@host:3306/database"
```

### **2. Build Assets**

```bash
npm install
npm run build
```

### **3. Optimize Laravel**

```bash
php artisan config:cache
php artisan route:cache
php artisan view:cache
```

### **4. Run Migrations**

```bash
npx prisma migrate deploy
npx prisma generate
```

### **5. Set Permissions**

```bash
chmod -R 775 storage bootstrap/cache
chown -R www-data:www-data storage bootstrap/cache
```

---

## 🧪 Testing

```bash
# Run all tests
php artisan test

# Run specific test file
php artisan test tests/Feature/EventTest.php

# With coverage
php artisan test --coverage
```

---

## 🔥 System Flow

### **Admin Flow**
```
Login → Dashboard → Create Event → Set Details → Publish → Monitor Participants → Export Data
```

### **User Flow**
```
Browse Events → View Details → Register → Fill Form → Submit → Confirmation → Receive Email
```

---

## 📝 Key Files

| File | Purpose |
|------|---------|
| `prisma/schema.prisma` | Database schema |
| `routes/web.php` | Application routes |
| `app/Http/Controllers/EventController.php` | Event logic |
| `app/Http/Controllers/ParticipantController.php` | Participant logic |
| `app/Services/PrismaService.php` | Prisma integration |
| `resources/js/Pages/Events/` | Event UI components |
| `app/Http/Middleware/EnsureUserIsAdmin.php` | Admin middleware |

---

## 🐛 Troubleshooting

### **Prisma Client Not Found**
```bash
npx prisma generate
```

### **Database Connection Error**
- Check `.env` file
- Verify `DATABASE_URL` format
- Ensure database exists

### **Assets Not Loading**
```bash
npm run build
php artisan config:clear
```

### **Permission Denied**
```bash
chmod -R 775 storage
chmod -R 775 bootstrap/cache
```

---

## 🎓 Usage Examples

### **Creating an Event**
1. Login as admin
2. Click "Create Event"
3. Fill in event details
4. Upload poster (optional)
5. Choose status (Draft/Published)
6. Click "Create Event"

### **Registering for an Event**
1. Browse events
2. Click on an event
3. Click "Register for This Event"
4. Fill registration form
5. Submit

### **Managing Participants**
1. Go to event detail (as admin)
2. Click "View Participants"
3. Search/filter participants
4. Select participants
5. Mark as attended or export

---

## 🚀 Future Enhancements

- [ ] Email notifications (Laravel Mail/Queue)
- [ ] QR Code for check-in
- [ ] Certificate auto-generation
- [ ] Multi-event organizer support
- [ ] Advanced analytics with charts
- [ ] Mobile responsive optimization
- [ ] API for mobile app
- [ ] Payment integration

---

## 📞 Support

Kalau ada error atau butuh bantuan:
1. Check dokumentasi ini dulu
2. Lihat error log di `storage/logs/laravel.log`
3. Run Prisma Studio: `npx prisma studio`
4. Clear cache: `php artisan config:clear`

---

## 📜 License

This project is open-source under the MIT License.

---

## 👨‍💻 Credits

Built with:
- Laravel Framework
- Inertia.js
- React
- TailwindCSS
- Prisma ORM

---

**Selamat ngoding! 🚀**
#   e v e n t - g u e s t  
 