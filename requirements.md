# Backend Requirement Specifications for Airbnb Clone

This document outlines the detailed technical and functional requirement specifications for key backend features of the Airbnb Clone project.

---

## Table of Contents

1.  [User Authentication](#1-user-authentication)
    * 1.1. User Registration
    * 1.2. User Login
    * 1.3. Token Refresh 
    * 1.4. User Profile Retrieval
    * 1.5. User Profile Update
2.  [Property Listings Management](#2-property-listings-management)
    * 2.1. Create Property Listing
    * 2.2. Get Property Listing Details
    * 2.3. Update Property Listing
    * 2.4. Delete Property Listing
    * 2.5. Search/Filter Property Listings
3.  [Booking System](#3-booking-system)
    * 3.1. Create Booking
    * 3.2. Get Booking Details
    * 3.3. Cancel Booking
    * 3.4. List User Bookings (Guest/Host)

---

## 1. User Authentication

This feature handles user sign-up, login, and management of user sessions.

### 1.1. User Registration

* **Description:** Allows new users (guests or hosts) to create an account.
* **API Endpoint:** `POST /api/v1/auth/register`
* **Input:**
    * `firstName` (String, required)
    * `lastName` (String, required)
    * `email` (String, required, unique)
    * `password` (String, required, min 8 characters)
    * `role` (String, optional, default: 'guest', enum: ['guest', 'host'])
* **Output (Success - 201 Created):**
    * `userId` (String)
    * `email` (String)
    * `firstName` (String)
    * `lastName` (String)
    * `role` (String)
    * `message`: "User registered successfully. Please check your email for verification."
* **Output (Error - 400 Bad Request):**
    * `error`: "Validation error message(s)" (e.g., "Email already exists", "Password too short")
* **Validation Rules:**
    * `firstName`, `lastName`: Must not be empty.
    * `email`: Must be a valid email format and unique in the database.
    * `password`: Must be at least 8 characters long, preferably with complexity requirements (e.g., uppercase, lowercase, number, special character - handled by frontend and backend).
    * `role`: If provided, must be 'guest' or 'host'.
* **Security:**
    * Passwords must be securely hashed (e.g., bcrypt, Argon2) before storing.
    * Consider email verification flow to activate the account.
* **Performance Criteria:**
    * Response time: < 500ms under normal load.

### 1.2. User Login

* **Description:** Allows registered users to log in and obtain an access token.
* **API Endpoint:** `POST /api/v1/auth/login`
* **Input:**
    * `email` (String, required)
    * `password` (String, required)
* **Output (Success - 200 OK):**
    * `accessToken` (String, JWT)
    * `refreshToken` (String, JWT, optional for token refresh mechanism)
    * `user`: { `userId`, `email`, `firstName`, `lastName`, `role` }
* **Output (Error - 401 Unauthorized):**
    * `error`: "Invalid email or password"
* **Validation Rules:**
    * `email`, `password`: Must not be empty.
* **Security:**
    * Compare provided password with the hashed password in the database.
    * Implement rate limiting to prevent brute-force attacks.
* **Performance Criteria:**
    * Response time: < 300ms under normal load.

### 1.3. Token Refresh

* **Description:** Allows users to obtain a new access token using a refresh token.
* **API Endpoint:** `POST /api/v1/auth/refresh-token`
* **Input:**
    * `refreshToken` (String, required)
* **Output (Success - 200 OK):**
    * `accessToken` (String, JWT)
* **Output (Error - 401 Unauthorized):**
    * `error`: "Invalid or expired refresh token"

### 1.4. User Profile Retrieval

* **Description:** Allows an authenticated user to retrieve their own profile details.
* **API Endpoint:** `GET /api/v1/users/me`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header)
* **Output (Success - 200 OK):**
    * `userId` (String)
    * `email` (String)
    * `firstName` (String)
    * `lastName` (String)
    * `role` (String)
    * `profilePictureUrl` (String, optional)
    * `bio` (String, optional)
    * `createdAt` (Timestamp)
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required" or "Invalid token"
* **Performance Criteria:**
    * Response time: < 200ms.

### 1.5. User Profile Update

* **Description:** Allows an authenticated user to update their profile details.
* **API Endpoint:** `PUT /api/v1/users/me`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header)
    * `firstName` (String, optional)
    * `lastName` (String, optional)
    * `profilePictureUrl` (String, optional, URL or reference to uploaded file)
    * `bio` (String, optional)
* **Output (Success - 200 OK):**
    * Updated user profile object (similar to 1.4 Output)
* **Output (Error - 400 Bad Request):**
    * `error`: "Validation error message(s)"
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required" or "Invalid token"
* **Validation Rules:**
    * Input fields must meet basic type and length requirements if provided.
* **Performance Criteria:**
    * Response time: < 400ms.

---

## 2. Property Listings Management

This feature allows hosts to create, manage, and display their property listings.

### 2.1. Create Property Listing

* **Description:** Allows authenticated hosts to create a new property listing.
* **API Endpoint:** `POST /api/v1/properties`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header, user must have 'host' role)
    * `title` (String, required, max 255 chars)
    * `description` (String, required, text)
    * `address` (Object, required):
        * `street` (String, required)
        * `city` (String, required)
        * `state` (String, required)
        * `zipCode` (String, required)
        * `country` (String, required)
        * `latitude` (Number, optional)
        * `longitude` (Number, optional)
    * `pricePerNight` (Number, required, positive)
    * `maxGuests` (Integer, required, positive)
    * `numBedrooms` (Integer, required, positive)
    * `numBathrooms` (Number, required, positive)
    * `amenities` (Array of Strings, optional, e.g., ["wifi", "pool", "kitchen"])
    * `propertyType` (String, required, e.g., "Apartment", "House", "Condo")
    * `images` (Array of Strings - URLs or references to uploaded files, optional)
    * `availability` (Array of Objects, optional - for specific available dates/ranges)
        * `startDate` (Date)
        * `endDate` (Date)
* **Output (Success - 201 Created):**
    * The created property listing object (including `propertyId`, `hostId`, `createdAt`).
* **Output (Error - 400 Bad Request):**
    * `error`: "Validation error message(s)"
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required"
* **Output (Error - 403 Forbidden):**
    * `error`: "User must be a host to create listings"
* **Validation Rules:**
    * All required fields must be present and valid.
    * `pricePerNight`, `maxGuests`, `numBedrooms`, `numBathrooms` must be positive numbers.
    * `images`: If provided, URLs should be valid.
* **Performance Criteria:**
    * Response time: < 700ms (may be longer if image processing/linking is involved).

### 2.2. Get Property Listing Details

* **Description:** Retrieves details for a specific property listing. Publicly accessible.
* **API Endpoint:** `GET /api/v1/properties/{propertyId}`
* **Input:**
    * `propertyId` (String, path parameter)
* **Output (Success - 200 OK):**
    * The property listing object (including details, images, amenities, host information - limited).
* **Output (Error - 404 Not Found):**
    * `error`: "Property not found"
* **Performance Criteria:**
    * Response time: < 300ms (excluding image loading by client).

### 2.3. Update Property Listing

* **Description:** Allows the host who owns the listing to update its details.
* **API Endpoint:** `PUT /api/v1/properties/{propertyId}`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header, user must be the host who owns the property)
    * `propertyId` (String, path parameter)
    * Fields to update (same as in 2.1, all optional).
* **Output (Success - 200 OK):**
    * The updated property listing object.
* **Output (Error - 400 Bad Request):**
    * `error`: "Validation error message(s)"
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required"
* **Output (Error - 403 Forbidden):**
    * `error`: "User is not authorized to update this property"
* **Output (Error - 404 Not Found):**
    * `error`: "Property not found"
* **Performance Criteria:**
    * Response time: < 500ms.

### 2.4. Delete Property Listing

* **Description:** Allows the host who owns the listing to delete it.
* **API Endpoint:** `DELETE /api/v1/properties/{propertyId}`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header, user must be the host who owns the property)
    * `propertyId` (String, path parameter)
* **Output (Success - 204 No Content):**
    * Empty response.
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required"
* **Output (Error - 403 Forbidden):**
    * `error`: "User is not authorized to delete this property"
* **Output (Error - 404 Not Found):**
    * `error`: "Property not found"
* **Performance Criteria:**
    * Response time: < 400ms.
* **Considerations:**
    * Soft delete vs. hard delete.
    * What happens to existing bookings for a deleted property? (Usually prevent deletion if active bookings exist).

### 2.5. Search/Filter Property Listings

* **Description:** Allows users to search and filter property listings. Publicly accessible.
* **API Endpoint:** `GET /api/v1/properties`
* **Input (Query Parameters):**
    * `location` (String, e.g., city, zip code)
    * `checkInDate` (Date, YYYY-MM-DD)
    * `checkOutDate` (Date, YYYY-MM-DD)
    * `numGuests` (Integer)
    * `minPrice` (Number)
    * `maxPrice` (Number)
    * `amenities` (Comma-separated string, e.g., "wifi,pool")
    * `propertyType` (String)
    * `sortBy` (String, e.g., "price_asc", "price_desc", "rating_desc")
    * `page` (Integer, default: 1)
    * `limit` (Integer, default: 10, max: 50)
* **Output (Success - 200 OK):**
    * `properties`: [Array of property listing objects (summary view)]
    * `pagination`: { `currentPage`, `totalPages`, `totalItems`, `itemsPerPage` }
* **Performance Criteria:**
    * Response time: < 1000ms for typical searches, depends heavily on database indexing and query complexity.
    * Efficient pagination is crucial.

---

## 3. Booking System

This feature handles the process of guests booking properties.

### 3.1. Create Booking

* **Description:** Allows an authenticated guest to book an available property for specific dates.
* **API Endpoint:** `POST /api/v1/bookings`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header, user must have 'guest' role)
    * `propertyId` (String, required)
    * `checkInDate` (Date, YYYY-MM-DD, required)
    * `checkOutDate` (Date, YYYY-MM-DD, required)
    * `numGuests` (Integer, required, positive)
    * `paymentDetails` (Object, required - e.g., payment method token from Stripe/PayPal)
* **Output (Success - 201 Created):**
    * The created booking object (including `bookingId`, `guestId`, `propertyId`, `status: 'confirmed'`, `totalPrice`).
    * Payment transaction details (or reference).
* **Output (Error - 400 Bad Request):**
    * `error`: "Validation error message(s)" (e.g., "Dates not available", "Number of guests exceeds limit", "Invalid payment details")
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required"
* **Output (Error - 403 Forbidden):**
    * `error`: "User must be a guest to create bookings"
* **Output (Error - 404 Not Found):**
    * `error`: "Property not found"
* **Output (Error - 409 Conflict):**
    * `error`: "Property is not available for the selected dates"
* **Validation Rules:**
    * `propertyId` must exist.
    * `checkInDate` must be before `checkOutDate`.
    * `checkInDate` must be in the future.
    * `numGuests` must be positive and not exceed property's `maxGuests`.
    * Dates must be available for the property (no overlapping bookings).
    * Payment must be successfully processed.
* **Process Flow:**
    1.  Validate input.
    2.  Check property existence and availability.
    3.  Calculate total price.
    4.  Process payment via payment gateway.
    5.  If payment successful, create booking record, update property availability.
    6.  Send confirmation notifications to guest and host.
* **Performance Criteria:**
    * Response time: < 1500ms (includes payment gateway interaction).

### 3.2. Get Booking Details

* **Description:** Retrieves details for a specific booking. Accessible by the guest who made the booking or the host of the property.
* **API Endpoint:** `GET /api/v1/bookings/{bookingId}`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header)
    * `bookingId` (String, path parameter)
* **Output (Success - 200 OK):**
    * The booking object (including property details, guest details - limited, dates, status, price).
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required"
* **Output (Error - 403 Forbidden):**
    * `error`: "User is not authorized to view this booking"
* **Output (Error - 404 Not Found):**
    * `error`: "Booking not found"
* **Performance Criteria:**
    * Response time: < 300ms.

### 3.3. Cancel Booking

* **Description:** Allows a guest or host to cancel a booking (subject to cancellation policies).
* **API Endpoint:** `PUT /api/v1/bookings/{bookingId}/cancel`
* **Input:**
    * `Authorization: Bearer <accessToken>` (Header, user must be the guest or host associated with the booking)
    * `bookingId` (String, path parameter)
    * `reason` (String, optional)
* **Output (Success - 200 OK):**
    * Updated booking object with `status: 'cancelled'`.
    * Details about any applicable refund.
* **Output (Error - 400 Bad Request):**
    * `error`: "Booking cannot be cancelled" (e.g., past check-in date, policy violation)
* **Output (Error - 401 Unauthorized):**
    * `error`: "Authentication required"
* **Output (Error - 403 Forbidden):**
    * `error`: "User is not authorized to cancel this booking"
* **Output (Error - 404 Not Found):**
    * `error`: "Booking not found"
* **Process Flow:**
    1.  Verify user authorization.
    2.  Check if booking can be cancelled based on policy and timing.
    3.  Update booking status.
    4.  Process refund if applicable.
    5.  Update property availability.
    6.  Send cancellation notifications.
* **Performance Criteria:**
    * Response time: < 1000ms (may include refund processing).

### 3.4. List User Bookings (Guest/Host)

* **Description:** Retrieves a list of bookings for the authenticated user.
* **API Endpoint:** `GET /api/v1/bookings/my-bookings` (for guests) OR `GET /api/v1/bookings/my-listings-bookings` (for hosts)
* **Input (Query Parameters):**
    * `Authorization: Bearer <accessToken>` (Header)
    * `status` (String, optional, e.g., "confirmed", "cancelled", "completed", "upcoming", "past")
    * `page` (Integer, default: 1)
    * `limit` (Integer, default: 10)
* **Output (Success - 200 OK):**
    * `bookings`: [Array of booking objects (summary view)]
    * `pagination`: { `currentPage`, `totalPages`, `totalItems`, `itemsPerPage` }
* **Performance Criteria:**
    * Response time: < 500ms.

---

