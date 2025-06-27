 User Authentication
1.1. Endpoints
Method	Endpoint	Description
POST	/api/auth/register	Register a new user
POST	/api/auth/login	Authenticate user and return token
GET	/api/auth/profile	Get current user’s profile (JWT required)
PUT	/api/auth/profile	Update user profile
POST	/api/auth/reset-password	Send password reset email
POST	/api/auth/reset-password/confirm	Confirm password reset
________________________________________
1.2. Input/Output
 /api/auth/register
•	Input (JSON):
{
  "email": "user@example.com",
  "password": "secure1234",
  "role": "user"  // or "owner"
}
Validation Rules:
•	Email must be unique and valid format.
•	Password: min 8 characters, 1 number, 1 uppercase.
•	Role must be user or owner.
{
  "message": "User registered successfully",
  "token": "<JWT>"
}
Output (Failure):
{
  "error": "Email already exists"
}
1.3. Performance Criteria
•	Max response time: 500ms under 100 concurrent requests.
•	Secure endpoints with rate limiting (e.g. 5 login attempts per minute).
•	Passwords hashed with bcrypt/scrypt.
________________________________________
2. Property Management
2.1. Endpoints
Method	Endpoint	Description
POST	/api/properties/	Add a new property
GET	/api/properties/	List all properties
GET	/api/properties/{id}	View a specific property
PUT	/api/properties/{id}	Update property info
DELETE	/api/properties/{id}	Delete a property
POST	/api/properties/{id}/images	Upload property images
________________________________________
2.2. Input/Output
/api/properties/ (POST)
•	Input:
{
  "title": "3 Bedroom Flat",
  "location": "Abuja, Nigeria",
  "price_per_night": 25000,
  "available_from": "2025-07-01",
  "available_to": "2025-12-31",
  "amenities": ["WiFi", "Air Conditioning"]
}
•	Validation:
o	Price must be positive.
o	Title/location must not be empty.
o	Dates must be valid and available_to >= available_from.
•	Output:
{
  "id": 101,
  "message": "Property created"
}
________________________________________
2.3. Performance Criteria
•	Upload images to S3 or cloud storage, store only URL.
•	Pagination for property list (default 20/page).
•	Cache common queries using Redis (e.g. featured listings).
________________________________________
3. Booking System
3.1. Endpoints
Method	Endpoint	Description
POST	/api/bookings/	Create a new booking
GET	/api/bookings/	View user’s bookings
GET	/api/bookings/{id}	View specific booking
PUT	/api/bookings/{id}/cancel	Cancel booking
________________________________________
3.2. Input/Output
 /api/bookings/ (POST)
•	Input:
{
  "property_id": 101,
  "start_date": "2025-07-10",
  "end_date": "2025-07-15"
}
•	Validation:
o	Dates must be available for the property.
o	User must be authenticated.
o	Cannot book overlapping dates.
•	Output:
{
  "booking_id": 2001,
  "total_amount": 125000,
  "status": "pending"
}
________________________________________
3.3. Performance Criteria
•	Ensure booking creation checks for availability with locking/transactions.
•	Max response time: 600ms.
•	Notifications sent via email/SMS (asynchronous queue).

