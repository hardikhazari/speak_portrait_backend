# Speak Portrait Backend

A comprehensive Node.js backend API for managing multimedia projects with Firebase authentication, AWS S3 storage, and PostgreSQL database integration.

## 🚀 Features

- **User Authentication**: Firebase Admin SDK integration for secure user management
- **Project Management**: Create, read, update, and delete multimedia projects
- **Media Upload**: Support for image, video, and audio file uploads to AWS S3
- **Database**: PostgreSQL with Prisma ORM for robust data management
- **Cloud Storage**: AWS S3 integration for media file storage
- **RESTful API**: Clean and intuitive API endpoints
- **CORS Support**: Cross-origin resource sharing enabled
- **Environment Configuration**: Secure environment variable management

## 🛠️ Technology Stack

- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: PostgreSQL with Prisma ORM
- **Authentication**: Firebase Admin SDK
- **Cloud Storage**: AWS S3
- **File Upload**: Multer middleware
- **Development**: Nodemon for hot reloading

## 📋 Prerequisites

Before running this application, make sure you have:

- Node.js (v14 or higher)
- PostgreSQL database or Prisma Postgres (recommended)
- AWS S3 bucket with proper permissions
- Firebase project with Admin SDK credentials

## 🔧 Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/yasharyasaxena/speak_portrait_backend.git
   cd speak_portrait_backend
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Environment Setup**

   Create a `.env` file in the root directory with the following variables:

   ```env
   # Server Configuration
   PORT=8000
   CORS_ORIGIN=*

   # Database Configuration
   DATABASE_URL=your_postgresql_connection_string

   # AWS S3 Configuration
   AWS_ACCESS_KEY_ID=your_aws_access_key
   AWS_SECRET_ACCESS_KEY=your_aws_secret_key
   AWS_REGION=your_aws_region
   AWS_S3_BUCKET_NAME=your_s3_bucket_name
   AWS_S3_BUCKET_URL=your_s3_bucket_url
   ```

4. **Firebase Setup**

   Place your Firebase Admin SDK service account key file in the root directory and name it according to your configuration.

5. **Database Setup**

   ```bash
   # Generate Prisma client
   npx prisma generate

   # Run database migrations
   npx prisma migrate dev
   ```

6. **Start the server**
   ```bash
   npm start
   ```

The server will start on `http://localhost:8000` (or your configured PORT).

## 📁 Project Structure

```
speak_portrait_backend/
├── prisma/
│   ├── migrations/          # Database migration files
│   └── schema.prisma       # Database schema definition
├── src/
│   ├── config/             # Configuration files
│   │   ├── db.js
│   │   ├── env.js
│   │   ├── firebase.js
│   │   └── prisma.js
│   ├── controllers/        # Request handlers
│   │   ├── authController.js
│   │   ├── projectController.js
│   │   └── uploadController.js
│   ├── middleware/         # Custom middleware
│   │   └── auth.js
│   ├── models/             # Data models
│   │   ├── Project.js
│   │   └── User.js
│   ├── routes/             # API routes
│   │   ├── auth.js
│   │   ├── index.js
│   │   ├── project.js
│   │   └── upload.js
│   ├── services/           # Business logic
│   │   ├── awsService.js
│   │   ├── projectServices.js
│   │   ├── uploadService.js
│   │   └── userServices.js
│   └── app.js              # Express app configuration
├── index.js                # Application entry point
├── package.json
└── README.md
```

## 🔗 API Endpoints

### Health Check

- `GET /api/health` - API health check

### Authentication

- `POST /api/auth/login` - User login
- `POST /api/auth/register` - User registration
- `POST /api/auth/logout` - User logout

### Projects

- `GET /api/projects/active` - Get user's active projects
- `GET /api/projects/:id` - Get project by ID
- `POST /api/projects` - Create new project
- `PUT /api/projects/:id` - Update project
- `DELETE /api/projects/:id` - Delete project

### Project Media Management

- `GET /api/projects/bucket/:id` - Get media files for project
- `DELETE /api/projects/bucket/:id/:fieldname` - Delete specific media type
- `DELETE /api/projects/bucket/:id?fieldname=type` - Delete media (query param)

### File Upload

- `POST /api/upload` - Upload media files

## 📊 Database Schema

The application uses three main entities:

### User

- `id` (String, Primary Key)
- `email` (String, Unique)
- `emailVerified` (Boolean)
- `displayName` (String, Required)
- `imageUrl` (String, Optional)
- `phoneNumber` (String, Optional)
- `providerId` (String, Required)
- `disabled` (Boolean)
- `createdAt` (DateTime)
- `lastLogin` (DateTime)
- `updatedAt` (DateTime)

### Project

- `id` (String, Primary Key)
- `name` (String)
- `userId` (String, Foreign Key)
- `status` (ProjectStatus: ACTIVE/COMPLETED)
- `createdAt` (DateTime)
- `updatedAt` (DateTime)

### Media

- `id` (String, Primary Key)
- `url` (String)
- `fileName` (String)
- `fileType` (MediaType: IMAGE/VIDEO/AUDIO)
- `projectId` (String, Foreign Key)
- `metadata` (JSON)
- `createdAt` (DateTime)
- `updatedAt` (DateTime)

## 🔐 Authentication

The API uses Firebase Authentication for secure user management. All protected routes require a valid Firebase ID token in the Authorization header:

```
Authorization: Bearer <firebase_id_token>
```

## 📤 File Upload

The upload endpoint supports multipart/form-data with the following structure:

```javascript
// Form data structure
{
  projectId: "optional_existing_project_id",
  projectName: "Project Name",
  files: {
    audio: [file1, file2],
    video: [file1],
    image: [file1, file2, file3]
  }
}
```

**File Organization in S3:**

```
bucket/
└── {userId}/
    └── {projectId}/
        ├── audio/
        │   └── filename.mp3
        ├── video/
        │   └── filename.mp4
        └── image/
            └── filename.jpg
```

## 🚦 Error Handling

The API returns standardized error responses:

```json
{
  "error": "Error message",
  "details": "Detailed error description",
  "timestamp": "2025-08-02T10:30:00.000Z"
}
```

Common HTTP status codes:

- `200` - Success
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `404` - Not Found
- `500` - Internal Server Error

## 🔧 Configuration

### AWS S3 Permissions

Your IAM user needs the following permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket", "s3:GetBucketLocation"],
      "Resource": "arn:aws:s3:::your-bucket-name"
    },
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

### Firebase Configuration

1. Create a Firebase project
2. Enable Authentication
3. Generate Admin SDK private key
4. Download the service account JSON file
5. Place it in your project root directory

## 🧪 Development

### Running in Development Mode

```bash
npm start
```

### Database Operations

```bash
# Generate Prisma client
npx prisma generate

# Create and apply migration
npx prisma migrate dev --name your_migration_name

# Reset database (development only)
npx prisma migrate reset

# Open Prisma Studio
npx prisma studio
```

### Environment Variables for Development

```env
NODE_ENV=development
DEBUG=true
LOG_LEVEL=debug
```

## 🚀 Deployment

### Production Environment Variables

```env
NODE_ENV=production
DATABASE_URL=your_production_database_url
# ... other production configurations
```

### Build and Deploy Steps

1. Set up production database
2. Configure environment variables
3. Run migrations: `npx prisma migrate deploy`
4. Start the application: `npm start`

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Commit your changes: `git commit -am 'Add some feature'`
4. Push to the branch: `git push origin feature/your-feature-name`
5. Submit a pull request

## 📝 License

This project is licensed under the ISC License.

## 👥 Authors

- **Yash Arya Saxena**
- **Hardik Hazari**
- **Divyae Arya**

---

**Happy Coding! 🎉**
