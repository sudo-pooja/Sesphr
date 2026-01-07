# Sesphr - Project Analysis & Requirements

## Project Overview
**Sesphr** is an encrypted health record management system built with Flask. It enables secure sharing of medical records between patients, doctors, hospitals, pharmacies, and laboratories with encrypted access controls.

---

## Technology Stack

### Backend Framework
- **Flask 2.3.3** - Web application framework
- **Flask-CORS 4.0.0** - Cross-Origin Resource Sharing support

### Database
- **MySQL** - Primary database (sesphr)
- **MySQL Clone Database** - Secondary database (clone) for data replication
- **mysql-connector-python 8.1.0** - MySQL database connector

### Cryptography & Security
- **cryptography 41.0.4** - Cryptographic primitives
  - Fernet symmetric encryption
  - PBKDF2 key derivation
  - SHA256 hashing
- **hashlib** - SHA512 password hashing
- **secrets** - Secure random number generation

### Built-in Libraries
- **base64** - URL-safe base64 encoding/decoding
- **datetime** - Date/time operations
- **math, random** - Mathematical and random utilities
- **time** - Time-related functions

---

## Project Structure

```
Sesphr/
├── app.py                 # Flask application & route handlers
├── utils.py              # Database operations & encryption utilities
├── settings.py           # Configuration settings
├── requirements.txt      # Python dependencies (frozen)
├── clone.sql            # SQL schema for clone database
├── sesphr.sql           # SQL schema for main database
├── README.md            # Project documentation
├── static/              # Static assets
│   ├── css/            # Stylesheets (Bootstrap, Material Dashboard)
│   ├── js/             # JavaScript files (jQuery, custom scripts)
│   ├── fonts/          # Font files
│   └── img/            # Images
└── templates/          # HTML templates
    ├── index.html
    ├── doctor.html
    ├── doctor_dashboard.html
    ├── patient.html
    ├── patient_dashboard.html
    ├── hospital.html
    ├── hospital_admin_dashboard.html
    ├── pharm_dashboard.html
    └── lab_dashboard.html
```

---

## Core Features

### 1. User Management (Multi-role System)
- **Doctors** - Register, login, manage patient access
- **Patients** - Register, login, generate access codes, control access to records
- **Hospital Staff** - Admin, Pharmacy, Lab technicians with role-based access
- **Database-driven UID generation** - Auto-incrementing unique identifiers

### 2. Authentication & Security
- **Email-based login** - Email serves as unique identifier
- **SHA512 password hashing** - For database comparison
- **Fernet encryption** - Symmetric encryption for sensitive data at rest
- **PBKDF2 key derivation** - 1000+ iterations for key hardening
- **Session management** - Flask session cookies for authenticated users

### 3. Access Control Mechanism
- **PIN-based access codes** - Patients generate and share PINs with doctors
- **Dynamic permission system** - Patients can allow/block access dynamically
- **Multi-code validation** - Verification of both patient UID and access code

### 4. Data Management
- **Patient health records** - Diagnosis, prescriptions, test results, lab data
- **Doctor records** - Hospital affiliation, license information
- **Hospital employee management** - Role-based (Admin/Pharmacy/Lab)
- **Dynamic table creation** - Per-patient data tables for record isolation

### 5. Cross-Hospital Access
- **Hospital Admin dashboard** - View patient basic info (name, phone, email, DOB)
- **Pharmacy dashboard** - Access prescription data
- **Lab dashboard** - Access lab test data and results

---

## Database Schema Overview

### Primary Database Tables (sesphr)
- `login_doctor` - Doctor authentication (email, hashed password, uid)
- `doctor_details` - Doctor information (encrypted name, email, phone, license, hospital)
- `login_patient` - Patient authentication
- `patient_details` - Patient information (encrypted name, email, phone, DOB)
- `login_hospital` - Hospital staff authentication
- `hospital_details` - Hospital staff details (encrypted name, email, phone, type, hospital name)
- `t{uid}` - Dynamic patient health record tables

### Clone Database
- Mirror of primary database for replication/backup purposes

---

## API Endpoints

### Doctor Routes
- `POST /registerdoctor` - Register new doctor
- `POST /logindoctor` - Doctor login
- `POST /patinetaccess` - Validate patient access codes
- `GET /patinetaccess` - Display patient data entry form
- `POST /insertdata` - Insert diagnosis/prescription
- `GET /showdata` - View patient records

### Patient Routes
- `POST /registerpatient` - Register new patient
- `POST /loginpatient` - Patient login
- `GET /loginpatient` - Patient logout
- `GET /generate_pin` - Generate access PIN
- `GET /allow_access` - Allow doctor access
- `GET /block_access` - Block doctor access

### Hospital Routes
- `POST /registerhospital` - Register hospital staff
- `POST /loginhospital` - Hospital staff login
- `POST /admindata` - Hospital admin access patient data
- `POST /pharmdata` - Pharmacy access prescription data
- `POST /labdata` - Lab access test data
- `GET /labdata` - Lab data entry form
- `POST /insertdatalab` - Insert lab results

### Public Routes
- `GET /` - Landing page
- `GET /doctor` - Doctor login page
- `GET /patient` - Patient login page
- `GET /hospital` - Hospital login page

---

## Security Considerations

### Implemented
✅ Encryption at rest (Fernet symmetric encryption)
✅ Password hashing (SHA512 + session validation)
✅ PBKDF2 key derivation with iterations
✅ Database isolation per patient
✅ Multi-level access control (PIN-based)
✅ Session-based authentication

### Notes
- Hardcoded encryption key: "everythingissafe" should be moved to environment variables
- CORS enabled for all origins - should be restricted
- Secret key in app.py should be moved to environment variables
- Database credentials hardcoded in utils.py

---

## Configuration Requirements

### Environment Variables (To Be Set)
```bash
FLASK_SECRET_KEY=<random-secure-key>
ENCRYPTION_KEY=<secure-encryption-key>
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=<password>
DB_NAME=sesphr
DB_CLONE_NAME=clone
FLASK_ENV=production
FLASK_DEBUG=False
```

### Flask Configuration
- Host: 127.0.0.1 (local only)
- Port: 5001
- Debug: True (should be False in production)
- Threaded: True (handles concurrent requests)

---

## Installation & Setup

```bash
# Install dependencies
pip install -r requirements.txt

# Database setup
mysql -u root < sesphr.sql
mysql -u root < clone.sql

# Run application
python app.py
```

---

## Dependencies Frozen
**Date**: January 7, 2026
**Python Version**: 3.8+ recommended

### Pinned Versions
- Flask==2.3.3
- flask-cors==4.0.0
- mysql-connector-python==8.1.0
- cryptography==41.0.4

---

## Development Notes

### Naming Inconsistencies Found
- Typo: `/patinetaccess` should be `/patientaccess`
- Variable naming: `patinet_*` should be `patient_*`
- Typo: `patinet_prescription` should be `patient_prescription`

### Code Quality Issues
- SQL injection vulnerable to parameterized queries (partially mitigated)
- Hardcoded credentials and keys
- Missing input validation and sanitization
- Missing error handling and logging
- No CSRF protection
- No rate limiting on authentication endpoints

### Future Improvements
1. Migrate to environment-based configuration
2. Implement proper error handling and logging
3. Add CSRF protection
4. Add rate limiting on auth endpoints
5. Implement audit logging
6. Add input validation
7. Use SQLAlchemy ORM instead of raw SQL
8. Implement role-based access control (RBAC)
9. Add API documentation (Swagger/OpenAPI)
10. Implement data encryption at rest for all sensitive fields

---

## Database Initialization
The project requires two MySQL databases:
1. **sesphr** - Main encrypted health records database
2. **clone** - Replica database for data redundancy

Both databases follow the same schema and are synchronized.
