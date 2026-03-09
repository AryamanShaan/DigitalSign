# Digital Sign n Verify

Digital Sign n Verify is a Django web application for creating and verifying digital signatures for PDF documents. The project was built to demonstrate practical backend engineering skills with Django, PostgreSQL, authentication, CRUD-oriented data handling, and cryptographic workflows.

## Project Goal

This project showcases:

- Django app structure and request handling
- User authentication and session-based access control
- PostgreSQL integration through Django's ORM
- Form handling and file uploads
- CRUD-style persistence for user-linked cryptographic profile data
- Backend logic for hashing, signing, encryption, and verification

## What the App Does

Users can register for an account, log in, and generate a digital signature for an uploaded PDF. During registration, the system creates an RSA key pair for the user. The public key is stored for later verification, and the private key is encrypted with AES before being saved.

Once logged in, a user can:

- Upload a PDF to generate a base64-encoded digital signature
- Share that signature along with their username
- Verify a signature later by re-uploading the PDF and providing the username and signature

The verification flow confirms whether:

- the uploaded file matches the originally signed file bytes
- the provided signature is valid
- the signature corresponds to the given user's public key

## Tech Stack

- Python
- Django 5
- PostgreSQL
- `cryptography` library
- HTML templates
- `python-dotenv` for environment variable loading

## Project Structure

```text
mysite/     Django project settings and root URL configuration
user/       Registration, login, logout, and user cryptographic profile storage
home/       Logged-in landing page
sign/       PDF upload and digital signature generation
verify/     PDF upload and signature verification
```

## Core Flow

### 1. Registration

When a user registers:

- a Django `User` is created
- an RSA public/private key pair is generated
- the private key is encrypted using an AES key from environment variables
- the encrypted private key, IV, and public key are stored in `UserProfile`

### 2. Signing

When a logged-in user uploads a PDF:

- the file bytes are read
- a SHA-256 hash of the PDF is generated
- the user's encrypted private key is decrypted
- the hash is signed using RSA-PSS with SHA-256
- the resulting signature is returned as a base64 string

### 3. Verification

When a logged-in user verifies a signature:

- the uploaded PDF is hashed again
- the signer is identified by username
- the stored public key for that user is loaded
- the provided base64 signature is decoded
- the signature is checked against the uploaded file hash

## Database

The project is configured to use PostgreSQL through Django settings loaded from environment variables.

The main custom model is:

- `UserProfile`: stores a one-to-one mapping with Django's built-in `User`, along with the user's public key, encrypted private key, and IV

## Environment Variables

Create a `.env` file with values similar to:

```env
DJANGO_SECRET_KEY=your-django-secret-key
AES_SECRET_KEY=your-base64-encoded-32-byte-aes-key
DB_NAME=your_db_name
DB_USER=your_db_user
DB_PASSWORD=your_db_password
DB_HOST=localhost
DB_PORT=5432
```

You can generate a random AES key by running:

```bash
python generate_random_aes_key.py
```

## Local Setup

Install dependencies, configure PostgreSQL, and run the Django server:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
```

If you do not already have a `requirements.txt`, install the main packages manually:

```bash
pip install django psycopg2-binary cryptography python-dotenv
```

## Routes

- `/user/register/` - create an account
- `/user/login/` - log in
- `/user/logout/` - log out
- `/` - home page
- `/sign/` - generate a digital signature for a PDF
- `/verify/` - verify a PDF signature


## Summary

Digital Sign n Verify is a Django + PostgreSQL project that demonstrates backend development, authentication, data persistence, and cryptographic document workflows in a single application. It was built as a practical showcase of Django, CRUD-oriented development, and database-backed application design.
