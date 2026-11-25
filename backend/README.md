# StudyShare Backend

A comprehensive backend API for StudyShare - an online platform for sharing exam papers and study materials. Built with Node.js, Express.js, and MongoDB.

## 🚀 Features

- **User Authentication**: JWT-based authentication with bcrypt password hashing
- **Role-based Access Control**: Admin and User roles with different permissions
- **File Upload**: Secure file upload with validation (PDF, DOC, DOCX, PPT, PPTX, JPG, JPEG, PNG)
- **Material Management**: CRUD operations for study materials with approval workflow
- **Rating System**: Users can rate and comment on materials
- **Admin Panel**: Complete admin management system for users and materials
- **Statistics Dashboard**: Comprehensive analytics for admins
- **Input Validation**: Joi-based request validation
- **Error Handling**: Global error handling with meaningful error messages
- **Pagination**: Efficient pagination for all list endpoints
- **File Management**: Automatic file cleanup and organization

## 📋 Prerequisites

- Node.js (v14 or higher)
- MongoDB (local or cloud instance)
- npm or yarn package manager

## 🛠️ Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd studyshare-backend
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Environment Setup**
   ```bash
   # Copy the example environment file
   cp env.example .env
   
   # Edit .env with your configuration
   nano .env
   ```

4. **Configure Environment Variables**
   ```env
   NODE_ENV=development
   PORT=5000
   MONGO_URI=mongodb://localhost:27017/studyshare
   JWT_SECRET=your-super-secret-jwt-key-here
   CLIENT_URL=http://localhost:3000
   ```

5. **Start the server**
   ```bash
   # Development mode
   npm run dev
   
   # Production mode
   npm start
   ```

## 📚 API Documentation

### Base URL
```
http://localhost:5000/api
```

### Authentication Endpoints

#### Register User
```http
POST /api/auth/register
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

#### Login User
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

#### Get Current User
```http
GET /api/auth/me
Authorization: Bearer <token>
```

#### Logout User
```http
POST /api/auth/logout
Authorization: Bearer <token>
```

### Material Endpoints

#### Get All Materials
```http
GET /api/materials?page=1&limit=10&sort=newest&subject=Mathematics&semester=1
```

#### Get Single Material
```http
GET /api/materials/:id
```

#### Upload Material
```http
POST /api/materials/upload
Authorization: Bearer <token>
Content-Type: multipart/form-data

{
  "title": "Calculus Exam 2023",
  "subject": "Mathematics",
  "semester": "1",
  "file": <file>
}
```

#### Download Material
```http
GET /api/materials/:id/download
```

#### Get My Uploads
```http
GET /api/materials/my-uploads
Authorization: Bearer <token>
```

### Rating Endpoints

#### Add/Update Rating
```http
POST /api/materials/:id/rate
Authorization: Bearer <token>
Content-Type: application/json

{
  "rating": 5,
  "comment": "Excellent material!"
}
```

#### Get Material Ratings
```http
GET /api/materials/:id/ratings?page=1&limit=10
```

#### Get My Rating
```http
GET /api/materials/:id/ratings/my-rating
Authorization: Bearer <token>
```

#### Delete My Rating
```http
DELETE /api/materials/:id/ratings
Authorization: Bearer <token>
```

### Admin Endpoints

#### Get All Users
```http
GET /api/admin/users?page=1&limit=10&role=user&search=john
Authorization: Bearer <admin-token>
```

#### Get User by ID
```http
GET /api/admin/users/:id
Authorization: Bearer <admin-token>
```

#### Update User Role
```http
PUT /api/admin/users/:id/role
Authorization: Bearer <admin-token>
Content-Type: application/json

{
  "role": "admin"
}
```

#### Delete User
```http
DELETE /api/admin/users/:id
Authorization: Bearer <admin-token>
```

#### Approve Material
```http
PUT /api/admin/materials/:id/approve
Authorization: Bearer <admin-token>
```

#### Delete Material
```http
DELETE /api/admin/materials/:id
Authorization: Bearer <admin-token>
```

#### Get Pending Materials
```http
GET /api/admin/materials/pending?page=1&limit=10
Authorization: Bearer <admin-token>
```

#### Get Statistics
```http
GET /api/admin/stats
Authorization: Bearer <admin-token>
```

### Health Check
```http
GET /api/status
```

## 🔧 Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `NODE_ENV` | Environment mode | `development` |
| `PORT` | Server port | `5000` |
| `MONGO_URI` | MongoDB connection string | `mongodb://localhost:27017/studyshare` |
| `JWT_SECRET` | JWT secret key | Required |
| `CLIENT_URL` | Frontend URL for CORS | `http://localhost:3000` |

### File Upload Configuration

- **Max file size**: 10MB
- **Allowed file types**: PDF, DOC, DOCX, PPT, PPTX, JPG, JPEG, PNG
- **Upload directory**: `./uploads`

## 🗂️ Project Structure

```
src/
├── config/
│   └── db.js              # Database configuration
├── controllers/
│   ├── authController.js  # Authentication logic
│   ├── materialController.js # Material management
│   ├── ratingController.js # Rating system
│   └── adminController.js # Admin operations
├── middlewares/
│   ├── authMiddleware.js  # Authentication middleware
│   ├── errorMiddleware.js # Error handling
│   └── validationMiddleware.js # Input validation
├── models/
│   ├── User.js           # User model
│   ├── Material.js       # Material model
│   └── Rating.js          # Rating model
├── routes/
│   ├── authRoutes.js      # Authentication routes
│   ├── materialRoutes.js  # Material routes
│   └── adminRoutes.js     # Admin routes
├── utils/
│   └── fileHelper.js      # File handling utilities
├── app.js                 # Express app configuration
└── server.js             # Server entry point
```

## 🔐 Security Features

- **Password Hashing**: bcrypt with salt rounds
- **JWT Authentication**: Secure token-based authentication
- **Input Validation**: Joi schema validation
- **File Type Validation**: Strict file type checking
- **CORS Protection**: Configurable CORS settings
- **Role-based Access**: Admin and user role separation
- **File Size Limits**: Prevents large file uploads
- **Error Handling**: Secure error messages without sensitive data

## 📊 Database Schema

### User Model
```javascript
{
  name: String (required, max 50 chars),
  email: String (required, unique, lowercase),
  password: String (required, min 6 chars, hashed),
  role: String (enum: ['user', 'admin'], default: 'user'),
  createdAt: Date (default: Date.now)
}
```

### Material Model
```javascript
{
  title: String (required, max 100 chars),
  subject: String (required, max 50 chars),
  semester: String (required, max 20 chars),
  uploaderId: ObjectId (ref: 'User'),
  filePath: String (required),
  fileName: String (required),
  fileType: String (enum: ['pdf', 'doc', 'docx', 'ppt', 'pptx', 'jpg', 'jpeg', 'png']),
  fileSize: Number (required),
  uploadDate: Date (default: Date.now),
  approved: Boolean (default: false),
  approvedBy: ObjectId (ref: 'User'),
  approvedAt: Date,
  downloadCount: Number (default: 0),
  averageRating: Number (default: 0, min: 0, max: 5),
  totalRatings: Number (default: 0)
}
```

### Rating Model
```javascript
{
  userId: ObjectId (ref: 'User', required),
  materialId: ObjectId (ref: 'Material', required),
  rating: Number (required, min: 1, max: 5),
  comment: String (max 500 chars),
  createdAt: Date (default: Date.now)
}
```

## 🚀 Deployment

### Production Setup

1. **Environment Configuration**
   ```bash
   NODE_ENV=production
   MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/studyshare
   JWT_SECRET=your-production-secret-key
   CLIENT_URL=https://your-frontend-domain.com
   ```

2. **Deploy to Render/Railway**
   - Connect your GitHub repository
   - Set environment variables
   - Deploy automatically

3. **MongoDB Atlas Setup**
   - Create a MongoDB Atlas cluster
   - Whitelist your server IP
   - Get connection string

### Docker Deployment (Optional)

```dockerfile
FROM node:16-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build
EXPOSE 5000
CMD ["npm", "start"]
```

## 🧪 Testing

### Manual Testing with Postman

1. Import the provided Postman collection
2. Set up environment variables
3. Test all endpoints systematically

### API Testing Checklist

- [ ] User registration and login
- [ ] Material upload and download
- [ ] Rating system functionality
- [ ] Admin operations
- [ ] Error handling
- [ ] Authentication and authorization
- [ ] File validation
- [ ] Pagination

## 📝 API Response Format

### Success Response
```json
{
  "success": true,
  "message": "Operation successful",
  "data": { ... },
  "count": 10,
  "total": 100,
  "page": 1,
  "pages": 10
}
```

### Error Response
```json
{
  "success": false,
  "message": "Error description",
  "error": "Detailed error message"
}
```

## 🔧 Development

### Available Scripts

```bash
npm start          # Start production server
npm run dev        # Start development server with nodemon
npm test           # Run tests (if implemented)
```

### Code Style

- Use ES6+ features
- Follow RESTful API conventions
- Implement proper error handling
- Use meaningful variable names
- Add comments for complex logic

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License.

## 🆘 Support

For support and questions:
- Create an issue in the repository
- Check the API documentation
- Review the error logs

## 🔄 Version History

- **v1.0.0**: Initial release with complete backend functionality
- Authentication system
- Material management
- Rating system
- Admin panel
- File upload system
- Comprehensive API documentation
