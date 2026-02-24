🎟 Event Registration System

Laravel + Inertia.js + React + TailwindCSS + Prisma

Sistem manajemen event berbasis web untuk membuat, mengelola, dan memonitor pendaftaran event secara terstruktur dengan role Admin dan User.

📌 Overview

Event Registration System memungkinkan:

Admin membuat dan mengelola event

User mendaftar event secara online

Monitoring peserta secara real-time

Export data peserta (CSV)

Dashboard statistik

Role-based access control

🏗 Tech Stack

Backend: Laravel 10+

Frontend: React (Inertia.js)

Styling: TailwindCSS

ORM: Prisma

Database: MySQL / PostgreSQL

Auth: Laravel Breeze (Inertia React)

Build Tool: Vite

⚡ Quick Start (UI Only / Dummy Mode)

Jika ingin fokus ke UI tanpa database:

composer install
npm install
cp .env.example .env
php artisan key:generate

Jalankan:

# Terminal 1
php artisan serve

# Terminal 2
npm run dev

Akses di:

http://localhost:8000
⚙️ Full Installation (Database Mode)
1. Requirements

PHP 8.1+

Composer

Node.js 18+

MySQL / PostgreSQL

2. Install Dependencies
composer install
npm install
3. Setup Environment
cp .env.example .env
php artisan key:generate

Edit .env:

APP_NAME="Event Registration System"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=event_system
DB_USERNAME=root
DB_PASSWORD=

DATABASE_URL="mysql://root:@127.0.0.1:3306/event_system"

Buat database:

CREATE DATABASE event_system;
4. Setup Prisma
npx prisma migrate dev --name init
npx prisma generate
5. Run Application
php artisan serve
npm run dev
🧠 System Architecture
Client (React + Tailwind)
        ↓
Inertia.js
        ↓
Laravel Controller
        ↓
Prisma ORM
        ↓
Database

Laravel menangani routing & logic.
Prisma menangani query database.
React merender UI melalui Inertia.

👥 Role & Permissions
Admin

Create / Edit / Delete event

Publish / Close event

Manage participants

Export CSV

View dashboard statistics

User

Browse events

View event details

Register for event

Receive confirmation

🔄 System Flow
Admin Flow
Login → Dashboard → Create Event → Publish → Monitor Participants → Export Data
User Flow
Browse Events → View Detail → Fill Registration Form → Submit → Confirmation Message
🗄 Database Structure
User

id

name

email (unique)

password

role (ADMIN / USER)

Event

id

title

description

date

location

quota

status (DRAFT / PUBLISHED / CLOSED)

creatorId

Participant

id

name

nim

email

phone

status (REGISTERED / ATTENDED / CANCELLED)

eventId

Constraint:

Satu email hanya bisa mendaftar satu kali per event.

📂 Project Structure
app/
├── Http/Controllers/
├── Services/PrismaService.php
├── Data/ (Dummy Mode)

resources/js/
├── Pages/
│   ├── Dashboard/
│   ├── Events/
│   ├── Participants/
├── Components/
└── Layouts/

prisma/
└── schema.prisma
🛠 Development Commands
# Laravel
php artisan serve
php artisan test

# Frontend
npm run dev
npm run build

# Prisma
npx prisma studio
npx prisma generate
npx prisma migrate dev
npx prisma migrate reset
