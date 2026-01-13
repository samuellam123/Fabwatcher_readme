# FabWatcher - 3D Printer Farm Management System

A comprehensive web application for real-time management, monitoring, and booking of a 3D printer farm in a FabLab environment. The system consists of a FastAPI backend and a React-based frontend dashboard.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Backend Setup](#backend-setup)
- [Frontend Setup](#frontend-setup)
- [Running the Applications](#running-the-applications)
- [Environment Variables](#environment-variables)
- [API Documentation](#api-documentation)
- [Technology Stack](#technology-stack)
- [Deployment](#deployment)
- [Testing](#testing)
- [Contributing](#contributing)

## Overview

FabWatcher is a cloud-native application designed to manage and monitor a fleet of 3D printers. It provides:

- **User Dashboard**: Printer booking system, job tracking, and status monitoring
- **Admin Dashboard**: Fleet management, health logs, maintenance tracking, and analytics
- **Real-time Monitoring**: Live printer status and telemetry data
- **Authentication**: JWT-based authentication with role-based access control
- **Health Monitoring**: Comprehensive printer health logs and diagnostics

## Architecture

The application follows a microservices-based, cloud-native architecture:

- **Backend**: FastAPI (Python) REST API with AWS DynamoDB
- **Frontend**: React SPA with Tailwind CSS
- **Database**: AWS DynamoDB (NoSQL)
- **Storage**: AWS S3 for static assets and images
- **Authentication**: JWT tokens with bcrypt password hashing
- **Deployment**: Containerized with Docker, deployable on AWS ECS

## Prerequisites

Before setting up the project, ensure you have the following installed:

- **Python 3.11+** (for backend)
- **Node.js 18+** and npm (for frontend)
- **pip** (Python package manager)
- **Git**
- **Docker** (optional, for containerized deployment)
- **AWS Account** (for DynamoDB and S3, can use local alternatives for development)

## Project Structure

```
Cloud-Project/
├── fabwatcher-main-backend/    # FastAPI backend
│   ├── app/
│   │   ├── routers/            # API route handlers
│   │   ├── models/             # Pydantic models
│   │   ├── services/           # Business logic
│   │   └── main.py            # Application entry point
│   ├── tests/                  # Backend tests
│   ├── requirements.txt        # Python dependencies
│   ├── Dockerfile             # Backend container config
│   ├── BACKEND.md             # Backend documentation
│   └── README.md              # This file
│
└── fabwatcher-dashboard/       # React frontend
    ├── src/
    │   ├── components/        # React components
    │   ├── pages/             # Page components
    │   ├── services/          # API service layer
    │   ├── context/           # React context (state)
    │   └── App.jsx            # Main app component
    ├── public/                # Static assets
    ├── package.json           # Node dependencies
    ├── tailwind.config.js     # Tailwind CSS config
    ├── vite.config.js         # Vite build config
    └── README.md   # This file
```

## Backend Setup

### 1. Navigate to Backend Directory

```bash
cd fabwatcher-main-backend
```

### 2. Create Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

Create a `.env` file in the `fabwatcher-main-backend` directory:

```bash
# AWS Configuration
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=your_access_key_here
AWS_SECRET_ACCESS_KEY=your_secret_key_here

# DynamoDB (use local endpoint for development)
DYNAMODB_ENDPOINT=http://localhost:8000

# Security
SECRET_KEY=your-secret-key-change-in-production-min-32-chars
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# CORS
ALLOWED_ORIGINS=["http://localhost:5173", "http://localhost:3000"]

# S3
S3_BUCKET=fabwatcher-images
```

### 5. Verify Installation

```bash
# Run the backend server
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Test the health endpoint
curl http://localhost:8000/health
```

Expected response: `{"status": "healthy", "service": "fabwatcher-api"}`

## Frontend Setup

### 1. Navigate to Frontend Directory

```bash
cd fabwatcher-dashboard
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Configure Environment Variables

Create a `.env` file in the `fabwatcher-dashboard` directory:

```bash
VITE_API_BASE_URL=http://localhost:8000/api
```

### 4. Verify Installation

```bash
# Start development server
npm run dev
```

The application will be available at `http://localhost:5173`

## Running the Applications

### Development Mode

#### Terminal 1: Backend

```bash
cd fabwatcher-main-backend
source venv/bin/activate
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Backend will run on: `http://localhost:8000`

API Documentation (Swagger UI): `http://localhost:8000/docs`

#### Terminal 2: Frontend

```bash
cd fabwatcher-dashboard
npm run dev
```

Frontend will run on: `http://localhost:5173`

### Production Mode

#### Backend

```bash
cd fabwatcher-main-backend
source venv/bin/activate
uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 4
```

#### Frontend

```bash
cd fabwatcher-dashboard
npm run build
npm run preview
```

## Environment Variables

### Backend (.env)

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `AWS_REGION` | AWS region for services | `us-east-1` | Yes |
| `AWS_ACCESS_KEY_ID` | AWS access key | - | Yes (for AWS) |
| `AWS_SECRET_ACCESS_KEY` | AWS secret key | - | Yes (for AWS) |
| `DYNAMODB_ENDPOINT` | DynamoDB endpoint URL | - | No (for local dev) |
| `SECRET_KEY` | JWT secret key (min 32 chars) | - | Yes |
| `ALGORITHM` | JWT algorithm | `HS256` | No |
| `ACCESS_TOKEN_EXPIRE_MINUTES` | Token expiration time | `30` | No |
| `ALLOWED_ORIGINS` | CORS allowed origins | `[]` | Yes |
| `S3_BUCKET` | S3 bucket name | - | Yes |

### Frontend (.env)

| Variable | Description | Default | Required |
|----------|-------------|---------|----------|
| `VITE_API_BASE_URL` | Backend API URL | - | Yes |

## API Documentation

### Key Endpoint Categories

#### Authentication (`/api/auth`)
- `POST /api/auth/register` - Register new user
- `POST /api/auth/login` - User login (returns JWT token)
- `POST /api/auth/logout` - User logout
- `GET /api/auth/me` - Get current user info

#### Printers (`/api/printers`)
- `GET /api/printers/` - List all printers
- `GET /api/printers/{printer_id}` - Get printer details
- `GET /api/printers/{printer_id}/availability` - Check availability
- `GET /api/printers/{printer_id}/telemetry` - Get telemetry data
- `GET /api/printers/{printer_id}/health` - Get health status

#### Bookings (`/api/bookings`)
- `GET /api/bookings/` - List user bookings
- `POST /api/bookings/` - Create booking
- `PUT /api/bookings/{booking_id}` - Update booking
- `DELETE /api/bookings/{booking_id}` - Cancel booking

#### Jobs (`/api/jobs`)
- `GET /api/jobs/` - List user jobs
- `POST /api/jobs/` - Create print job
- `GET /api/jobs/{job_id}/status` - Get job status

#### Admin (`/api/admin`) - Requires admin role
- `GET /api/admin/dashboard` - System statistics
- `GET /api/admin/printers` - All printers (admin view)
- `GET /api/admin/jobs` - All jobs
- `GET /api/admin/logs` - System logs
- `GET /api/admin/analytics` - Analytics data

For complete API specification, refer to:
- Backend: `fabwatcher-main-backend/README.md`
- Frontend: `fabwatcher-dashboard/API_SPECIFICATION.md`

## Technology Stack

### Backend
- **Framework**: FastAPI (Python)
- **Database**: AWS DynamoDB
- **Authentication**: JWT with bcrypt
- **Server**: Uvicorn (ASGI)
- **Cloud Services**: AWS (DynamoDB, S3, IoT Core)

### Frontend
- **Framework**: React 18+
- **Build Tool**: Vite
- **Styling**: Tailwind CSS
- **Routing**: React Router v6
- **State Management**: React Context API
- **HTTP Client**: Axios
- **Icons**: Lucide React

## Contributing

### Development Workflow

1. Read the project documentation:
   - `fabwatcher-dashboard/README.md` - Project context guide

2. Create a new branch for your feature:
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. Make your changes following the project conventions

4. Test your changes thoroughly

5. Commit with clear, descriptive messages

6. Push and create a pull request

### Code Style Guidelines

#### Backend (Python)
- Follow PEP 8 conventions
- Use type hints for function parameters and return values
- Write docstrings for all functions and classes
- Keep functions focused and modular

#### Frontend (React)
- Use functional components with hooks
- Follow Tailwind CSS utility-first approach
- Refer to `STYLES.md` for design system
- Use meaningful component and variable names
- Keep components small and reusable

## Troubleshooting

### Common Issues

#### Backend won't start
- Verify Python version is 3.11+
- Check that all dependencies are installed: `pip install -r requirements.txt`
- Ensure `.env` file exists with required variables
- Check if port 8000 is already in use

#### Frontend won't start
- Verify Node.js version is 18+
- Delete `node_modules` and run `npm install` again
- Check that `.env` file exists with `VITE_API_BASE_URL`
- Ensure backend is running before starting frontend

#### Authentication fails
- Verify `SECRET_KEY` in backend `.env` is set (min 32 characters)
- Check that JWT token is included in Authorization header
- Verify token has not expired (default: 30 minutes)

#### DynamoDB connection fails
- For local development, ensure DynamoDB Local is running
- For AWS, verify AWS credentials are correct
- Check `DYNAMODB_ENDPOINT` in `.env` file

#### CORS errors
- Verify `ALLOWED_ORIGINS` in backend `.env` includes frontend URL
- Check that frontend is making requests to correct backend URL
- Ensure both services are running

## Support

For issues or questions:
- Check the documentation files listed above
- Review the interactive API docs at `/docs`
- Create an issue in the project repository

## License

ISC

## Acknowledgments

Built for the FabLab community to streamline 3D printer farm management and monitoring.

---

**Last Updated**: 2025-12-03
